!SLIDE 
# Concurrency and Parallelism in Ruby #
## Past, Present, and Future ##

!SLIDE smbullets incremental
# What you've heard #

"Ruby threading sucks because..."

* 1.8 green threads are useless
* MRI 1.9 GIL ruins performance
* Threading is too hard anyway

!SLIDE smbullets

# Why threads? #

Certain types of work are better suited for threads:

* Fast
* Homogenous
* Isolated
* CPU-bound

.notes Fast: jobs complete in less than a second or so. Homogenous: jobs take around the same time to complete. Isolated: work can be completed without any shared state. CPU-bound: versus IO-bound.

!SLIDE smbullets

# Why threads? #

Applications more suited for threads:

* Web servers
* Background job queues
* Parallel processing

!SLIDE subsection

# Concurrency Past #

!SLIDE smbullets incremental

## Ruby 1.8: Green Threads ##

* Implemented in Ruby interpreter
* Low overhead
* Only one CPU
* Yields during IO

.notes Ruby threading got ignored because of the performance issues with green threads

!SLIDE
 
         @@@ Ruby
         # Green threads are easy to control!
         while command = next_command do
           case command
             when :pause
               Thread.stop
               # ...
         
          def unpause
            @player_thread.run
          end

.notes roombox, a ruby music player daemon I wrote in 2004. Green threads greatly simplified the implementation.

!SLIDE smbullets incremental
# The Mongrel era #
## Multiprocess ##

* Multiple processes
* One thread per process
* Ensures isolation
* Uses all CPUs
* Gobbles RAM

.notes Once Rails started going into production, clustering implementations became the thing. Rails was single-threaded until 2.2

!SLIDE smbullets incremental
# Passenger
## Multiprocess + COW ##

* Saves RAM through forking
* Requires custom garbage collector
* Prevents most effective GC methods
* Memory savings are limited

!SLIDE smbullets incremental
# JRuby #
## Real threads at last ##

* Implemented Ruby threads on top of Java threads
* Java threads = OS threads
* No global locking
* Adoption slow

.notes Adoption was slow because Rails multithreading was imperfect, no commonly deployed app servers were threaded, people were scared of Java

!SLIDE smbullets incremental
# Ruby 1.9/YARV #
## Real threads, with a catch ##

* Implemented Ruby threads on top of OS threads
* Global interpreter lock prevents parallelism
* GIL is released during IO - `rb_thread_select`
* Not all libraries properly release GIL
* Like `mysql` (but not `mysql2`)

.notes YARV threading not much faster than green threads, but the libraries have improved

!SLIDE section
# Concurrency Present #

!SLIDE
# Ruby threading today

<table>
  <thead>
    <tr>
      <th></th>
      <th>OS threads?</th>
      <th>GIL?</th>
    </tr>
  </thead>
  <tbody>
    <tr>
      <td>MRI 1.8</td>    <td>No</td>   <td>N/A</td>
    </tr>
    <tr>
      <td>MRI 1.9</td>    <td>Yes</td>  <td>Yes</td>
    </tr>
    <tr>
      <td>JRuby</td>      <td>Yes</td>  <td>No</td>
    </tr>
    <tr>
      <td>Rubinius 1</td> <td>Yes</td>  <td>Yes</td>
    </tr>
    <tr>
      <td>Rubinius 2</td> <td>Yes</td>  <td>No</td>
    </tr>
    <tr>
      <td>MacRuby</td>    <td>Yes</td>  <td>No</td>
    </tr>
    </tr>
  </tbody>
</table>

!SLIDE smbullets
# Threaded web servers #

* Puma
* Rainbows
* Trinidad (JRuby)

!SLIDE smbullets
# Threading abstractions #

* Celluloid
* ???

.notes And that leads us to...

!SLIDE subsection
# Concurrency Future #

!SLIDE smbullets
# Ruby stdlib threading #

* `mutex`
* `monitor`
* `queue`
* `thwait`
* `gserver`

!SLIDE smbullets
# Can't we do better? #

* Puma, released in 2012, implements its own thread pool!
* Java has executors, transparent thread pooling, futures...
* Clojure has transactional memory, agents, futures, promises...

!SLIDE smbullets incremental 
# Ruby devs don't use threads... #

* ... because they don't know they work well
* ... because of long-standing FUD
* ... because of evented IO hype
* ... because of poor ecosystem support

!SLIDE smbullets
# Poor ecosystem support #

* Non-threadsafe libraries
* Rails default is to prevent threading
* Few abstractions on top of Thread/Mutex

!SLIDE
# What can we do? #
## Documentation! ##

http://ruby.io

!SLIDE 
# What can we do? #
## Encourage library authors to write threadsafe code ##

!SLIDE smbullets
# What can we do? #
## Start building threading abstractions ##

* Futures
* Executors
* Thread pools
* Actors (Celluloid)
* Develop a standard toolkit

!SLIDE subsection
# Questions/Discussion #
