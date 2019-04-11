---
layout: posts
title:  "RubyConf: Day Two"
categories: ruby rails
---

1. [Implementing Virtual Machines](#implementing-virtual-machines)
2. [Ruby 3 Concurrency](#ruby-3-concurrency)
3. [C Extensions](#c-extensions)
4. [The Little Server That Could](#the-little-server-that-could)

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

#### Goals

* How to implement object membership: `copy` and `transfer_membership`
  * Global variables become guild variables
  * Constant and Method tables introduce mutual exclusions

## C Extensions

#### Background

There are many ways to hook into C extensions from MRI.  The API really just exposes the Ruby internals.  Often times, the C implementation supposes particular extensions which leaves JRuby, Rubinius, etc. unable to proceed.

Methods calls in C extensions are slower than in Ruby, because Ruby invisibly caches the method.  C does not offer that.

C extensions cannot be optimized since they are already compiled.

FFI is simple but most people are already writing C extensions.

#### Truffle

From the [website](https://github.com/jruby/jruby/wiki/Truffle):

> The Truffle runtime of JRuby is an experimental implementation of an interpreter for JRuby using the Truffle AST interpreting framework and the Graal compiler. It’s an alternative to the IR interpreter and bytecode compiler. The goal is to be significantly faster, simpler and to have more functionality than other implementations of Ruby.

Oracle wrote a VM called Graal VM (JVM + Graal compiler) and framework Truffle on top of that.

Truffle compiles AST to a single module, turns that into a graph, apply optimizations, and turns that into machine code.

Author wrote a C interpreter that runs alongside the Ruby interpreter.  C code is given to LLVM interpreter and turned into simplified C code, which is interpreted and optimized at the same time as Ruby.  Truffle/Graal can take both Ruby and C files at this point and optimize them both the same way.

```
C extension -------> [ LLVM ] -------> file.ll ------- [ interpreter] \
                                                                       --------> [ optimizer ]
Ruby file   -------------------------> file.rb ------- [ interpreter] /
```

#### Downside

* Need the source code; can't use a closed-source C extension
* FFI still has widespread support

## The Little Server That Could

An engineering skill is knowing when to dive into an abstraction vs. when to accept constraints and just use it.

Servers communicate with outside world via OS and conform to a specific API.

### The outside world

Server runs in a process that gives you scope to define variables and set state without affecting other programs.

1. Open a socket
  * Everything on UNIX is a file.  Sockets are no exception.
  * OS uses a File Descriptor for the socket but Ruby abstracts that so we don't have to pass it around.
  * Choose address format: UNIX (`tmp/something.sock`) vs. INET (192.0.1.1:22)
  * Something
    - Stream sockets: bi-directional, uses TCP 3-way handshake (SYN-SYNACK-ACK).  TCP protocol sends packets with an order flag; client assembles them in order.
    - Datagram sockets: uni-directional, uses UDP that doesn't wait for acknowledgement of receipt.  Client may or may not receive packets.
2. Listen for requests
3. Handle requests
4. Reply to client

```ruby
require 'socket'

def server
  socket = create_server_socket
  loop_and_listen_for_client_requests(socket)
end

def create_server_socket
  Socket.new(:INET, :STREAM)
  # ???
end
```

### The application

#### Parser

Parser extracts header, body, URL, etc.  Ex. Ragel Parser

#### Rack

Implements `#call` which takes 1 arg, `env`, and returns `[status, headers, body]`

#### Improvements

Blocking network calls in the normal response means that multiple clients are blocked.  We can use UNIX forks to handle the blocking code and allow us to serve multiple concurrent requests.

Forking contains references to the same socket.  If one doesn't exit, the socket will be unavaible to processes longer.  When you fork a process, the only memory copied to the new process is that which is unique to the process; otherwise it takes memory from parent process.  This copy-on-write.

Pre-2.0 Ruby marked objects as different in child processes so their memory footprint would swell up to the size of parent processes.

Post-2.0 Ruby does not do this, allowing C.O.W. processes to remain small.

#### Threading

Threads share process resources (like memory); they use less memory and die when the process dies, so no zombie processes.  Faster to communicate as you don't have to go through a UNIX socket.

Downsides: non-atomic operations are not thread-safe.

Example:

```ruby
def buy_toy
  if @available_cat_toys > 0 # CPU executes this, then hops over to another thread (see below)
    buy_cat_toy
  else
    dont_buy_toy
  end
end

def buy_toy
  if @available_cat_toys > 0 # These 3 lines
    buy_cat_toy              # are executed on
    @available_cat_toys -= 1 # a separate thread
  else
    dont_buy_toy
  end
end
```

By the time we get back to line 3, there are no more available toys (race condition)

### You, the Developer

Throw and trap signals to control flow of application.  You can shovel code into trapping an interrupt signal, for example, to exit gracefully.
