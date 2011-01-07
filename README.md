First thank you to igal, this is all built directly from his examples from https://github.com/igal/resque_and_redis_eg.


Resque and Redis examples
=========================

* `Rakefile` -- run `rake work` to start Resque worker, `rake log` to display log messages
* `lib/greeter.rb` -- run `rake greeter:enqueue` to enqueue a Greeter job
* `lib/pingpong.rb` -- run `rake ping:enqueue` to enqueue a Ping job
* `lib/redis_eg.rb` -- run `rake redis_eg` to run Redis examples
