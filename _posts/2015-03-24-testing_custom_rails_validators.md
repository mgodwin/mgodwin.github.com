---
layout: post
title: Testing Custom Rails Validators
---

[Custom Validators](http://guides.rubyonrails.org/active_record_validations.html#custom-validators) are one of the best ways to DRY up your code, but
there's little to no documentation on how to test them.

After writing a validator, I had the thought that it would be tested as a side
effect of writing unit tests for models that leveraged the custom validator.
However with that approach, basically every single model test file would have
some variation of the same tests, leading to more or less copy pasta'd code
all throughout the codebase.  Not to mention validation logic can get really
complex - are you sure you want to test the same 10-20 cases in each model?

_I think it makes the most sense to have tests directly around the validator,
make sure they're rock solid, and then test simple cases in model unit tests._

For this example, we will making a custom validator that verifies a string only
has characters that might appear in a phone number:

```ruby
# app/validators/phone_validator.rb
class PhoneValidator < ActiveModel::EachValidator
  def validate_each(record, attribute, value)
    unless value.to_s =~ /\A[\d \-\(\)\+\.]+\z/i
      record.errors[attribute] << (options[:message] || 'is invalid')
    end
  end
end
```

In order to test this, wouldn't it be awesome if we had a model that only had
this one validator declared and we could just call the normal `model.valid?` and
`model.errors` methods that we might call in a regular model unit test?

It just so happens that we can make that happen by creating an anonymous class
in ruby and mixing in the `ActiveModel::Validations` module. The syntax is
pretty easy, and if we declare our anonymous class inside of a `setup` block, we
can ensure that the object is empty before each test.

```ruby
#test/unit/validators/phone_validator_test.rb

setup do
  phone_model = Class.new do
    include ActiveModel::Validations

    attr_accessor :phone
    validates :phone, phone: true
  end
  @model = phone_model.new
end
```

The presence of `attr_accessor` is so that we have an attribute to validate, and
obviously the `validates :phone` line is what we actually are trying to test!

The line immediately following the class definition is simply instantiating one
of our anonymous classes and storing it to an instance variable (`@model`) so we
can use it in our tests.

What do our tests end up looking like?  Here's a couple of examples:

```ruby
test 'spaces dashes and parenthesis are valid' do
  @model.phone = '(303) 555 - 1234'
  assert @model.valid?
end

test 'periods are valid' do
  @model.phone = '303.555.1234'
  assert @model.valid?
end

test 'letters are invalid' do
  @model.phone = 'abcdefghijk'
  assert @model.invalid?
end
```

Pretty sweet right?  Like most good things, anonymous classes can be abused, and
can add complexity and degrade the maintainability of your tests.   For this
particular instance, I feel like they make testing your validation code dead
simple, so it's worth the trade off.  Best of luck!
