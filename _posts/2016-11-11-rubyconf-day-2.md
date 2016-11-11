---
layout: post
title:  "RubyConf: Day Two"
categories: ruby rails
---

1. [Implementing Virtual Machines](#implementing-virtual-machines)
2. [Ruby 3 Concurrency](#ruby-3-concurrency)

## Implementing Virtual Machines

Examples of virtual machines: .NET runtime, JVM, C.

Moving between the CPU, stack, heap, and buffers are orders of magnitude faster than network calls or disk writes.

Using a stack abstraction (Array in Ruby), we can push things onto the stack.  Given a program:

```ruby
vm = VM.new(
  :push, 13,
  :push, 28,
  :add,
  :print,
  :exit
)
```

We can define instructions on how to perform the actions.  Pass through a compiler first to reject methods that are dangerous.

```ruby
def load(program)
  @program = compile(program)
end

def compile(program)
  program.collect do |v|
  case
  when v.is_a?(Method)
    # check if it's blacklisted
    # return method
  when methods.include?(v)
    # check if it's blacklisted
    # return method
  else
    # return methjod
  end
end
```

At this point she put a bunch of C code on the screen, talked about direct vs. indirect threading and said, "Now this is the part that makes my brain hurt."  I gave up trying to understand it all.

#### Resources

* Programming Languages, An Interpreter-Based Approach - Samuel N. Kamin
* Java Virtual Machine - Jon Meyer & Troy Downing
* Threaded Interpretive Languages

## Ruby 3 Concurrency

Concurrency exists in Ruby threads but threads are not executed in parallel.

**Guilds**: Proposal to add restriction for sharing mutable objects.  Eliminates race conditions.

Multi-threading programming is difficult.  Hard to debug/replicate, possible deadlocks, and hard to tune performance.

#### Data Race vs. Race Condition

```ruby
def transfer1(amount, account_from, account_to)
  if (account_from.balance < amount) return NOPE
  account_to.balance += amount
  account_from.balance -= amount
  return YEP
end
```

* Problem 1: Data Race: lines 3 and 4 do not happen in a single transaction
* Problem 2: Race Condition: lines 2 and 3/4 can happen in the wrong order.

Highlights need to synchronize sharing mutable objects.

Comparison to other languages:

* Shell/DRuby: Everything is copied to send; which is slow
* Erlang/Elixir/FP: Prohibits mutable objects; big incompatibility
* Place/Racket: Share only immutable objects; we want to share all objects
* Clojure: allow sharing with special protocol; can't accept a special protocol

Goals for Ruby 3:

* Keep compatibility with Ruby 2
* Parallel program
* Don't care about locks
* Share objects with a fast copy
* Share immutable objects
* Special objects allow sharing mutable objects

#### Guild Proposal

1. Multiple Guilds
2. Each Guild has a Thread
3. Each Thread has at least one Fiber

Threads in different guilds can run in parallel; threads in the same guild cannot run in parallel.

Mutable objects have a membership to a particular guild.  Guilds cannot access mutable objects in other guilds.

#### Mutable objects

`Guild::Channel` is an intercommunication library.  Provides 2 communication methods:

1. `#copy`
  * Objects now exist in both guilds.
2. `#transfer_membership`
  * Transferred objects are invalidated from original guild.

```ruby
  obj = "foo"
  ch.move(obj)
  puts obj # Error
```

#### Immutable objects

Small objects can be sent as messages and copied.  Large objects can be passed by reference.

#### Applications

Use guilds for pipelines, computational functions (like computing Fibonacci outside the main context of a program)
