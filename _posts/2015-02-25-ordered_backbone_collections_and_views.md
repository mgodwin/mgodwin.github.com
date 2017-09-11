---
layout: post
title: Ordered Backbone Collections and Views
---

Holy cow.  The more I use Backbone.js the more I develop a love-hate
relationship with it.  More on that another time.  It's ridiculous to me how
complex some things can be, and that there aren't some out of the box solutions
to address some of these really repetitive tasks.  Frankly, this one comes
up far too often to not have the code readily available.

If you have an ordered collection of Backbone models (perhaps when you are using the
`comparator` function), then you'll need to insert newly added models *typically*
somewhere in an existing collection of elements in the DOM.  Backbone doesn't
offer a solution for this because the framework delegates to jQuery to handle
all DOM manipulation.  I've done this enough times that I'm tired of coming up
with similar code everywhere I want my view to maintain the same order as my
collection when models are added.

A pretty good pattern that I've discovered is providing my own set of custom
*lightweight* jQuery extensions that I can litter throughout my Backbone views.
It's a pretty nice approach for DRYing up repetitive view code and not
polluting the `Backbone.View` prototype.

I originally found this code [here](http://upshots.org/javascript/jquery-insert-element-at-index)
but I ported it to coffeescript and reversed the order of the params.

```coffee
$.fn.insertAt = (index, elements) ->
  children = this.children()
  if index >= children.size()
    this.append(elements)
    return this
  before = children.eq(index)
  $(elements).insertBefore(before)
  return this
```

Example usage is as follows:

```coffeescript
// Insert at index 2
$('ul').insertAt(2, $('<li/>'))
```

If you have any alternative approaches to this, I would love to hear about them!
