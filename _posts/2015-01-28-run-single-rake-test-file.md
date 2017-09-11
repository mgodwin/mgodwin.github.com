---
layout: post
title: Run a single test file with rake
---

Lately I've been on a binge of trying to leverage as much of the stock functionality provided by rails.  When trying to write tests using minitest, it's not exactly obvious how to just run tests for a single file.  Sure you can run tests for individual groupings, e.g. `rake test:units` or `rake test:functionals` but that still can be too slow!  If you google around, a bunch of people talk about using an environment variable to specify a file path... To that I say *barf*.  Instead...

    $ rake test path/to/test_file.rb
