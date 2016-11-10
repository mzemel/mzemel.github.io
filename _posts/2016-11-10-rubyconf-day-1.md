---
layout: post
title:  "RubyConf: Day 1"
categories: ruby rails
---

# RubyConf 2016

1. [Matz's Keynote](#matzs-keynote)
2. [Video Games in Ruby](#video-games-in-ruby)
3. [Go Ruby Go](#ruby--go-gorb---ruby-native-extensions-for-go)
4. [A Look at Hooks](#a-look-at-hooks)
5. [Keyword Args](#keyword-args)
6. [Logic Programming](#logic-programming)
7. [Computer Science: The Good Parts](#computer-science-the-good-parts)

## Matz's Keynote

Guilds in Ruby 2.4 allow true concurrency; objects can only belong to one Guild at a time

## Video games in Ruby

#### Gosu 

Library provides single class to inherit from; overload 3 methods:

- `initialize()` - game then enters loop
- `update()` - called each frame to update state
- `draw()` - called each from to draw game

Create private functions to abstract out behaviors, while keeping the 3 necessary game functions.

```ruby
class Game < Gosu::Window
  def initialize
    # game details here
  end

  def update
    # change state here
    handle_action
  end

  def draw
    # draw frame here
  end

  def handle_action
    # encapsulate complicated logic here
  end
end
```

Author demonstrated animation strategies using `Gosu::load_tiles`, collision detection using rectangular boxes, and refactoring code into standalone classes

```ruby
class Enemy
  def initialize
    # ...
  end

  def update
    # ...
  end

  def draw
    # ...
  end
end
```

And in the game class:

```ruby
class Game < Gosu::Window
  # ...

  def update
    @enemies.each(&:draw)
  end
end
```

#### Resources

* `opengameart.org` - Images
* `PikoPixel` - Pixel editor
* `Tiled Map Editor` - build maps and export to JSON/XML
* `libgosu.org`

## Ruby & Go (gorb - Ruby native extensions for Go)

### Go language

Data and behavior are separated into structs and functions, respectively.  Ruby combines these in classes.

#### `gorb`
 
Tool that takes Go code and generates the CRuby API wrapper code to get it loaded into the VM.

Steps:

1. Write Go structs and functions
2. `gorb` creates CRuby wrapper files (.go)
3. `gorb` loads Go to compile them into native extensions (.so)
4.  Require native extensions in Ruby code and use them as Ruby classes

```go
package fib

type Fibonacci struct {
}

func (f *Fibonacci) Fib(n int) int {
  return f.fib(n)
}

func (f *Fibonacci) fib(n int) int {
  if n < 2 {
    return n
  }
  return f.fib(n-1) + f.fib(n-2)
}

func IsPrime(n int) bool {
  return true
}
```

```bash
gorbgen test/fib/fib.go
# => test/fib/fib.so
```

```ruby
require 'test/fib/fib.so'
Test::Fibonacci.new.fib(10)
# => 55
```

#### Questions

Does the C code in the VM dispatch to Go?  If so, are there cases where we'd see penalties like loading the Go environment?

> `.so` files include Go runtime environment

What might some implementations be in business-driven Ruby app development?  Computationally intensive things like matrix operations?

#### Resources

* `github.com/lsegal/gorb`
* `gobyexample.com`

## A look at hooks

Hook: a method called implicitly by Ruby, never explicitly

Ex.

```ruby
class Foo
  def initialize
  # ...
  end
end

Foo.new # never call #initialize directly
```

* `BasicObject#method_missing` - called when method does not exist
* `Object#respond_to_missing` - called with `obj.respond_to?(:meth_name)`
* `Module#const_missing` - don't use?
* `Module#included` - called when module is included
* `Module#extended` - called when module is extended
* `Module#prepended` - called when module is prepended (added to start of method ancestry)
* `Class#inherited` - called when class is subclassed/inherited from a class
* `Module#method_added`, `Module#method_removed` - not used very often as we don't dynamically add methods often
* `BasicObject#singleton_method_added`, etc - called when you dynamically add methods to an object
* `Kernel#at_exit` - calls when an exception is raised, possibly during normal exit as well?

Objects are implictly tried to be converted during binary operations.  Implement things to `#to_str` to add a hook when the object is converted.

Coercion tells you how to [coerce classes](https://www.mutuallyhuman.com/blog/2011/01/25/class-coercion-in-ruby) to avoid:

```ruby
> 'foo' + 1
TypeError: no implicit conversion of Fixnum into String
```

## Keyword Args

#### Background

**Pros**:

* Behaves like hashes.
* Tells the user what the arguments are going to be used for.
* Position doesn't matter when you omit certain ones.
* Dependency injection is better

**Cons**:

* Non-keyword arguments must be before keyword arguments

#### Usage

```ruby
# Bad
Net::HTTP.new('server.com', 732, 'admin', 'monkey72')

# Better
Net::HTTP.new(server: 'server.com', port: 732, username: 'admin', password: 'monkey72')

# Best
Net::HTTP.new(
  server: 'server.com',
  port: 732,
  username: 'admin',
  password: 'monkey72'
)
```

#### Splat operator

You can use the splat operator (`**`) to explode hashes into keyword arguments.  This lets the arguments be dynamic depending on a particular case.

```ruby
@salary = calculate_salary(
  base: 20_000,
  per_mile: 10,
  **user.overrides
)
```

## Logic Programming

#### Intro

Logic programming is a paradigm alongside object-oriented, imperative, and functional.  Tell the computer how the world works and let it generate the solution.

Generally have 3 features: declarative, relational, and inferential.

**Declarative** aspect is easy in Ruby with method chaining.  You declare what functions are going to be performing the work and expose a single interface.  E.g. `has_many` -- don't care about the implementation, it "just does it".

**Relational** aspect focuses on intersections of data.  SQL is a great example of a relational tool (envision a Venn diagram)

```ruby
18 + 42 = ?  # Easy
18 + ? = 42  # Doesn't work
```

```
sum_r(18, 24, ?) # match ? with 42
sum_r(18, ?, 42) # match ? with 24
```

#### Logic programming in Ruby

`russell` library creates a `Logic::Solver` class that allows you to assert certain constraints and then call `solve`.

It seems like this talk hid all of the magic of actually solving the problem in the library and focused more on the public interface of the library.  "Look, you have to write very little code", but it would be interested to see what is going on under the hood.

He created a Sudoku solver that looked like it was using a generic `Logic::Solver` class not tailored for Sudoku and, by passing in the right constraints, he could solve the Sudoku puzzle.  It would be very impressive if he can use the same class extensibly to solve other problems assuming you set the correct constraints.a

#### Mechanics

Unification - pattern matching of how two things are related to each other.  Example: `concat_r([m, ?, t], [?]), [m, a, t, z]) #=> [m, a, t, z]`

Substitution List - Transitive property where you can point variables at each other and they keep those references.  If `q == r` and `r == 8`, then `q == 8`

You devise a strategy of attempting to unify through the substitution list and backtracking/branching when you violate a constraint.

#### Resources

* __The Art of Prolog__, Leon Sterling & Ehud Shapiro
* [Russell](https://gitlab.com/gavinmcg/russell) by the speaker, Gavin McGimpsey

## Computer Science: The Good Parts

#### Linked Lists

The very minimum amount of information you can have.  You can only start with the first element, and each element contains the first data and a reference to the next element.  In order to get the 5th element, you need to start at the first and walk the list to the 5th.

#### Binary Tree

Instead of having 1 connection between elements, you have 2.  If you pre-sort the data as you go, you approach log^2(n) to find a sorted element.

#### Graph

Generic term for connected nodes with as many connections as you want.  Implemented in maps, social networks, electrical grids, etc.

#### Big-O Notation

Describes complexity of an algorithm.  Using a sorted list, for example, improves search time from O(n) to O(log(n)).

Nested loops are a code smell of a bad algorithm.