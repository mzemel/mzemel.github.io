---
layout: post
title:  "Game development: retrospective"
categories: ruby
---

![missile command](https://media.giphy.com/media/3oz8xXZGdxiYkNmNCE/giphy.gif)

This weekend, I played around with [gosu](http://libgosu.org) after a talk at RubyConf, making a copy of [Missile Command](https://en.wikipedia.org/wiki/Missile_Command) in Ruby.  Without some important points from the talk, I would be up the creek, but there were some design patterns not brought up that felt emergent when writing game code.

## Use constants for "magic numbers"

You'll use numbers everywhere.  How many pixels should something move each frame?  What is the Z-index of assets?  I found it useful to put every number into a constant and put them in the same place (where reasonable).  My `Utility` module ended up looking like this:

```ruby
module Utility

  FIRING_RATE = 500 # Lower equals higher firing rate
  PROJECTILE_SPEED = 5  # If this gets too high and bullets don't arrive, increase PROJECTILE_PROXIMITY
  PROJECTILE_PROXIMITY = 2
  CURSOR_SPEED = 4
  EXPLOSION_SPEED = 2
  SPACESHIP_SPEED = 1

  module ZIndex
    BACKGROUND = 0
    GROUND     = 1
    HEALTH_BAR = 1
    BUNKER     = 2
    SPACESHIP  = 2
    DEFENDER   = 2
    EXPLOSION  = 3
    CURSOR     = 4
    PROJECTILE = 5
    SCORE      = 6
    DEBUG      = 99
  end
end
```

When parts of the game feel unrealistic (how fast an enemy moves, how big explosions get), all my numbers are in one place for tweaking.

## Draw your hit boxes when debugging collision

I wrote a collision library and, like all parts of my game, I did not test it.  When I couldn't figure out why two objects weren't registering a collision when they were overlapping, I added a utility function to take an object and draw its width/height around it.

```ruby
module Utility
  module Debug
    def self.trace(obj, c = nil)
      c ||= Explosion::COLOR
      # Left
      Gosu.draw_line(*obj.top_left, c, obj.top_left[0], obj.bottom_right[1], c, ZIndex::DEBUG, mode = :default)
      # Top
      Gosu.draw_line(*obj.top_left, c, obj.bottom_right[0], obj.top_left[1], c, ZIndex::DEBUG, mode = :default)
      # Right
      Gosu.draw_line(*obj.bottom_right, c, obj.bottom_right[0], obj.top_left[1], c, ZIndex::DEBUG, mode = :default)
      # Bottom
      Gosu.draw_line(*obj.bottom_right, c, obj.top_left[0], obj.bottom_right[1], c, ZIndex::DEBUG, mode = :default)
    end
  end
end
```

## Use keyword args everywhere

Using keyword args makes the message fail in the sender, not the receiver.  Usually it's the sender that's responsible for sending the wrong arguments, so this shifts the responsibility to the sender by ensuring that every expected argument will be present.  Avoid default arguments and nil checks as well.

## Abstract common behaviors into modules

This is a given in programming for all applications, but can be super useful when multiple classes behave in more or less the exact same way.  If you have 3 types of enemies that are all starships -- they change direction when they hit the end of the screen, they fire at a certain rate, they have a particular speed -- that can all be abstracted into a module and methods overwritten as needed.

```ruby
module Mixins
  module Ship
    def self.included(base)
      base.class_eval do
        def damage!(projectile)
          @damage += projectile.damage_value
          if damage >= health
            # Handle being destroyed
          end
        end

        private

        def speed
          # Look up speed from CONSTANT in Utility module
          table_name = "Utility::" + "#{self.class}".split('::').last.upcase + "_SPEED_TABLE"
          Object.const_get(table_name)[@health]
        end

        def health
          # Look up health from CONSTANT in Utility module
          # ...
        end


        def move
          # Move back and forth at speed in direction
        end

      end
    end
  end
end
```

Then when I make a new class that behaves like a `Ship`, I just have to include the module, add a speed entry in the `Utility` CONSTANT, and overwrite methods as necessary:

```ruby
module Enemy
  class Fortress
    include Mixins::Ship

    WIDTH = 60
    HEIGHT = 60

    private

    # Health in this case is a fixed value, always
    def health
      @health ||= 100
    end

    # Change the image
    def image
      @image ||= Gosu::Image.new("assets/death_star.png")
    end
  end
end
```

## Store levels in a collection

In your main loop, delegate the current action (`update`, `draw`) to the `current_level` variable.  Levels should all have a common API, like a `.done?` method, so in your main loop you say:

```ruby
current_level = levels.shift if current_level.done?
```

## Generate levels from a config file

Use YML, JSON, or some other format to store information for each level.  When the game loads, instantiate a collection of levels that correspond to entries in the file.  This way, you can easily change what's in a particular level by modifying the config file.  Like the idea of constants in a `Utility` module, it's one place where you can modify far away behavior.

```ruby
class Levels::Collection

  def initialize(game:)
    @levels = YAML
              .load_file("./config/levels.yml")
              .collect do |_, details|
                Levels::Base.new(game: game, details: details)
              end
  end
end
```

```yaml
one:
  spaceships:
    number: 3
  battleships:
    number: 0
  fortresses:
    number: 0
  bunkers:
    number: 1
    ammo: 10
    health: medium
  defenders:
    number: 0

two:
  spaceships:
    number: 5
    height: medium
    weapons: easy
  battleships:
    number: 0
  fortresses:
    number: 0
  bunkers:
    number: 1
    ammo: 10
    health: medium
  defenders:
    number: 0
```

The main loop of my game just looks like this:

```ruby
class Game < Gosu::Window
  # ...

  # Called once each frame
  def update
    return if game_over
    current_level.update
    check_game_over
    if !game_over && current_level.over?
      @current_level = levels.shift 
    end
  end

  # ...
end
```

The code for my game is available [on my Github](http://github.com/mzemel/missile_command) and thanks for reading!