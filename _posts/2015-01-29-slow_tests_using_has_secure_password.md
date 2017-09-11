---
layout: post
title: has_secure_password could be slowing you down
---

Encrypting passwords in rails is pretty simple when using `has_secure_password`.  However,
if you don't know what it's doing, it could potentially be slowing down your tests, or other scripts.

Under the hood, `has_secure_password` is using the awesome `BCrypt` library to actually
generate your *super secure password hash* to store in your database.  The `BCrypt::Password.create()` method
takes two parameters, your `password` (the value you want to encrypt), and the `cost`.  What is the cost?

Well if you check out the [docs](http://www.ruby-doc.org/gems/docs/b/bcrypt-ruby-maglev--3.0.1/BCrypt/Password.html) for `BCrypt::Password.create`:

> Hashes a secret, returning a BCrypt::Password instance. Takes an optional :cost option, which is a logarithmic variable which determines how computational expensive the hash is to calculate (a :cost of 4 is twice as much work as a :cost of 3). The higher the :cost the harder it becomes for attackers to try to guess passwords (even if a copy of your database is stolen), but the slower it is to check users’ passwords.

So what is the `cost` parameter set to by default?  Well in order to figure that out
we can check out the [source](https://github.com/rails/rails/blob/869a90512f36b04914d73cbf58317d953caea7c5/activemodel/lib/active_model/secure_password.rb#L53) for `has_secure_password`.

The method we're interested in is the `password=` method, lines 119-127.

```ruby
def password=(unencrypted_password)
  if unencrypted_password.nil?
    self.password_digest = nil
  elsif !unencrypted_password.empty?
    @password = unencrypted_password
    cost = ActiveModel::SecurePassword.min_cost ? BCrypt::Engine::MIN_COST : BCrypt::Engine.cost
    self.password_digest = BCrypt::Password.create(unencrypted_password, cost: cost)
  end
end
```

Hey, there's a cool variable called `ActiveModel::SecurePassword.min_cost`!  That's what we're interested in.
It looks like on line 13, it's set to `false`.  So that if statement is going to default to the BCrypt::Engine.cost, which (after you go hunt down the BCrypt source) you will find to be 10!

A cost of 10 takes quite a while (greater than 1 sec!) so if you have a script that is creating hundreds of users or test data that is calling out to BCrypt with the higher cost you'll want to make sure that you set `ActiveModel::SecurePassword.min_cost` to be `true`.
