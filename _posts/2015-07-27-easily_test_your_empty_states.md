---
layout: post
title: Easily Test Your Empty States
---

Empty states are all the rage these days!  Seriously, just check out this [tumblr](http://emptystat.es/).  When developing in rails applications, it can be a bit annoying trying to test your empty state conditional blocks.  Many times you'll have an erb template like:

```erb
<% if @collection.any? %>
  # Lots of markup
  <% @collection.each do |item| %>
    # More markup...
  <% end %>
<% else %>
  There's no items here!  <a> Do something about it! </a>
<% end %>
```

And in your controller, you'll usually have something along the lines of:

```ruby
@collection = current_user.items
```

Rather than testing empty state by stubbing out an empty array, you can use the [none()](http://api.rubyonrails.org/classes/ActiveRecord/QueryMethods.html#method-i-none) `QueryMethod` on your `WhereChain` (mm talk nerdy to me), so you can just tack it on and test away!

```ruby
@collection = current_user.items.none
```

There's limitations (as with anything), you can't use it when you're doing raw SQL or anything... but it does help for some of that vanilla rails goodness. :)
