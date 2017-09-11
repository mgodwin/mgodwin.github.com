---
layout: post
title: Complex SQL Queries in Rails
---

If you would have talked to me three years ago, I would have fought pretty hard
against custom SQL queries inside your rails code, but after spending extensive
time optimizing certain queries for Firepoint, I've come to realize that you
**have** to write SQL sometimes.  The Rails Guides point you to certain methods
that say, "Here's how you *can* write custom SQL" but they don't tell you where
it should go (e.g. `find_by_sql`).  It's left up to the implementor to determine
where it should best fit in the application.

Whilst Googling for a solution, I didn't really find a straightforward answer
that I liked.  Some examples I found simply left the SQL in the model as a
method... But when your query is 40+ lines long, that's really polluting what
your model looks like!  Plus a lot of times, these custom SQL queries can reach
across multiple tables, so it starts getting really gray as to where the code
*really* should live.  Example:

```ruby
class Office < ActiveRecord::Base

  def load_report_query
    <<-SQL
    ..50 lines going across 5 tables
    .. kill
    .. meeee
    .. it
    .. goes
    .. on
    .. forever
    SQL
  end
end
```

My solution:

* Create a Ruby object to hold the enormous query.
* Pass in any objects that the query depends on.
* Run query using `ActiveRecord::Base.connection.exec_query`
* You can sanitize data using `ActiveRecord::Base.sanitize` ([check it out](https://github.com/rails/rails/blob/master/activerecord/lib/active_record/sanitization.rb))

You end up with an object that looks more like:

```ruby
class MyCoolReport

  def initialize(office)
    @office = office
  end

  def retrieve_data
    ActiveRecord::Base.connection.exec_query(query)
  end

  private

  def query
    <<-SQL
    A metric ass ton of SQL
    SQL
  end
end
```

What's cool about this, is that usage in a controller then looks like:

```ruby
# reports_controller.rb
def my_cool_report
  office = Office.find(1)
  @data = MyCoolReport.new(office).retrieve_data
end
```

Nice!  Pretty concise, but then we can write tests around this query too:

```ruby
# my_cool_report_test.rb
test 'query executes successfully' do
  data = MyCoolReport.new(offices(:my_office)).retrieve_data
  assert_not data.empty?
  # ...other assertions
end
```

So then your tests are more well organized around your complex query, and aren't
taking up space in your model test file!  High fives all around!

It's worth mentioning that `exec_query` returns an [ActiveRecord::Result](http://api.rubyonrails.org/classes/ActiveRecord/Result.html), so you'll
have to deal with some intricacies around that, but generally these queries are
going to be returning data in a super custom format anyways, so I would argue that
the extra work is worth it.
