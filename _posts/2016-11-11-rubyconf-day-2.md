---
layout: post
title:  "RubyConf: Day 2"
categories: ruby rails
---

# RubyConf 2016: Day Two

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