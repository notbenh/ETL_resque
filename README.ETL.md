Resque backed demo as a replacement to powells current ETL process
=========================

* `Rakefile` -- run `rake work` to start Resque worker, `rake log` to display log messages
* `lib/greeter.rb` -- run `rake greeter:enqueue` to enqueue a Greeter job
* `lib/pingpong.rb` -- run `rake ping:enqueue` to enqueue a Ping job
* `lib/redis_eg.rb` -- run `rake redis_eg` to run Redis examples




Theory:
==========================
E: 
   -> accepts 'remote' path
   == based on config will attempt to prune path to list of acceptable files and will copy 'remote' file to 'E/working' dir
   == will validate file (if not valid will move file to 'E/failed' dir, if possible, and email, HALT)
   == will enqueue T jobs for each file, likely 'T/working' path, one job per file
   == will move 'E/working' file(s) to 'E/resolved'
   <- number of jobs enqueued ( could be 0 do not accept this value as true/false)

T: 
   -> 'T/working' path
   == will move file to 'T/work_in_process' dir
   == will validate file (if not valid will move file to 'T/validate/failed' dir and email, HALT)
   == will read thru file splitting each bit to a single unit
   == will build serialized data structure to pass to L, will likely be write JSON to file 'L/working'
   == will enqueue L jobs for each single unit data structure
   == will move 'T/working' file to 'T/resolved'
   <- number of jobs enqueued ( could be 0 do not accept this value as true/false)

L: 
   -> 'L/working' path
   == will move file to 'L/work_in_progress' dir
   == will validate file (if not valid will move file to 'L/validate/failed' dir and email, HALT)
   == for each storage locations will wrap all inserts in transaction
      == attempt to insert, if failed email and COPY file to 'L/storage_location/failed', !! DO NOT HALT !!
   == will move 'L/working' file to 'L/resolved'
   <- number of successful inserts made ( could be 0 do not accept this value as true/false)

C: (scheduled)
   -> 'root' path
   == will build dated archive of all 'E/resolved' files (possible 'T/resolved' and 'L/resolved') stored in 'archive' dir
   == will remove any '*/resolved' that were are archived
   == will email about any '*/working' file that is older then N days
   == will email about any '*/failed' file that is older then N days
   <- number of archives built ( could be 0 do not accept this value as true/false)

TODO: 
  ? how to automate a retry
  ? it would be nice if there was a service that watched for files to be dropped in to dirs and trip jobs vs the 'previous' event starting jobs, as it would allow for retrys to be a simple move from '*/failed' to '*/working'
