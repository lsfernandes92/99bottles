# 99 Bottles

## The Code

* [Chapter 1](https://github.com/sandimetz/99bottles/commits/chapter-1)
* [Chapter 2](https://github.com/sandimetz/99bottles/commits/chapter-2)
* [Chapter 3](https://github.com/sandimetz/99bottles/commits/chapter-3)
* [Chapter 4](https://github.com/sandimetz/99bottles/commits/chapter-4)
* [Chapter 5](https://github.com/sandimetz/99bottles/commits/chapter-5)
* [Chapter 6](https://github.com/sandimetz/99bottles/commits/chapter-6)

## Installing Ruby

### Windows

There's an installer, it's easy.
http://rubyinstaller.org/

### Mac

Newer macs ship with a usable version of Ruby.

Try `ruby -v` in a terminal window, and if it's 1.9.x or 2.x you're fine.

http://www.railstutorial.org/book/beginning#sec-install_ruby
http://tutorials.jumpstartlab.com/topics/environment/environment.html
http://docs.railsbridge.org/installfest/macintosh

### Linux

Ubuntu: http://docs.railsbridge.org/installfest/linux
https://www.ruby-lang.org/en/installation/

## My understanding of the book

For the time I am reading this booking I will write some resume for the things that understand and for what I jugde to be importants. This is only and only for me to fix the concept.

### 1. Rediscovering Simplicity

As we growth into programming we tend to write code more complex hoping that it will ultimately be easier to maintain. Unfortunately do such abstraction is hard even with the best intentions. That's because design decisions inevitably involve trade-offs, for example:

When you try to DRY your code you may pay the cost of being total ignorant on know what is happening to method and only seeing the result of it.
Or when you divide your classes into minor others. Now it's no longer know how the models fits together, you may be injecting a dependency, and so on...

So the main objective here, as the title said, is to resist for abstraction at first until they absolutely insist on being created.

#### 1.1.1. Incomprehensibly Concise

```
class Bottles
  def song
    verses(99, 0)
  end

  def verses(hi, lo)
    hi.downto(lo).map {|n| verse(n) }.join("\n")
  end

  def verse(n)
    "#{n == 0 ? 'No more' : n} bottle#{'s' if n != 1}" +
    " of beer on the wall, " +
    "#{n == 0 ? 'no more' : n} bottle#{'s' if n != 1} of beer.\n" +
    "#{n > 0 ? "Take #{n > 1 ? 'one' : 'it'} down and pass it around"
             : "Go to the store and buy some more"}, " +
    "#{n-1 < 0 ? 99 : n-1 == 0 ? 'no more' : n-1} bottle#{'s' if n-1 != 1}"+
    " of beer on the wall.\n"
  end
end
```

This example come with some problems:
* **Inconsistent**. of the use of the conditionals. Some times using ternary than in others using if. This also forces the readers to press a mental reset button each time it changes.
* **Duplication**. For example: `'s' if n != 1` and `'s' if n-1 != 1`. It suggest that are concepts hidden in the code that are no visible, because haven't been isolated and named correct.
* **Names**. The `verse` method contains embedded logic and aren't any situable name.

Summary about this example:

1. How difficult was it to write?
It's appear to be easy in a way that following a simple logic. It may be confused because of the ternary possibles follow by other ternaries possibles, and so on...

2. How hard is it to understand?
Although it's confusing to read if you take time it's easy to understand. But the problem here is to find the core concepts of the problema that it's solving.

3. How expensive will it be to change?
May not concern the aspect of this example because it must be refactoring in so better ways.

#### Method versus Message

```
The method is defined on an object, and contains behavior. Like the method named `song` in `Bottles`.

A message is sent by an invoker behavior. Like the `song` method sends the `verses` message to the implicit receiver `self`.

There is often a confusing between these terms because when you named the method as the same of the message it makes them synonymous, but the are not. In the example above the method `song` sending the message `verses` which results the invocation of the `verses` method.

Named method is like writing a book. U may take every effort if you wish your work to survive to be read.
```

#### 1.1.2. Speculatively General

```
class Bottles
  NoMore = lambda do |verse|
    "No more bottles of beer on the wall, " +
    "no more bottles of beer.\n" +
    "Go to the store and buy some more, " +
    "99 bottles of beer on the wall.\n"
  end

  LastOne = lambda do |verse|
    "1 bottle of beer on the wall, " +
    "1 bottle of beer.\n" +
    "Take it down and pass it around, " +
    "no more bottles of beer on the wall.\n"
  end

  Penultimate = lambda do |verse|
    "2 bottles of beer on the wall, " +
    "2 bottles of beer.\n" +
    "Take one down and pass it around, " +
    "1 bottle of beer on the wall.\n"
  end

  Default = lambda do |verse|
    "#{verse.number} bottles of beer on the wall, " +
    "#{verse.number} bottles of beer.\n" +
    "Take one down and pass it around, " +
    "#{verse.number - 1} bottles of beer on the wall.\n"
  end

  def song
    verses(99, 0)
  end

  def verses(finish, start)
    (finish).downto(start).map {|verse_number|
      verse(verse_number) }.join("\n")
  end

  def verse(number)
    verse_for(number).text
  end

  def verse_for(number)
    case number
    when 0 then Verse.new(number, &NoMore)
    when 1 then Verse.new(number, &LastOne)
    when 2 then Verse.new(number, &Penultimate)
    else        Verse.new(number, &Default)
    end
  end
end

class Verse
  attr_reader :number
  def initialize(number, &lyrics)
    @number = number
    @lyrics = lyrics
  end

  def text
    @lyrics.call self
  end
end
```

The problems here:

* All lambdas takes an argument on `verse` method but only `Default` actually refers to it.
* One hundred of instances of the `Verse` will be created.
* Each of the lambdas hold on template could merely have put into a method and than invoke in the proper place

Well... this example is far more better in exposing the core concepts, but the code is overly complicated, is harder to understand without being easier to change, and while complexity is not forbidden, it is required to pay its own way. In this case, complexity does not.

Programmers love clever code. You must resist being clever for its own sake. It is incumbent upon you to accept the harder task and write simpler code.

Summary of this example:

1. How difficult was it to write?
There’s far more code here than is needed to pass the tests. This unnecessary code took time to write.

2. How hard is it to understand?
The many levels of indirection are confusing. Their existence implies necessity, but you could study this code for a long time without discerning why they are needed.

3. How expensive will it be to change?
The mere fact that indirection exists suggests that it’s important. You may feel compelled to understand its purpose before making changes.

#### 1.1.3. Concretely abstraction

```
class Bottles

  def song
    verses(99, 0)
  end

  def verses(bottles_at_start, bottles_at_end)
    bottles_at_start.downto(bottles_at_end).map do |bottles|
      verse(bottles)
    end.join("\n")
  end

  def verse(bottles)
    Round.new(bottles).to_s
  end
end

class Round
  attr_reader :bottles
  def initialize(bottles)
    @bottles = bottles
  end

  def to_s
    challenge + response
  end

  def challenge
    bottles_of_beer.capitalize + " " + on_wall + ", " +
    bottles_of_beer + ".\n"
  end

  def response
    go_to_the_store_or_take_one_down + ", " +
    bottles_of_beer + " " + on_wall + ".\n"
  end

  def bottles_of_beer
    "#{anglicized_bottle_count} #{pluralized_bottle_form} of #{beer}"
  end

  def beer
    "beer"
  end

  def on_wall
    "on the wall"
  end

  def pluralized_bottle_form
    last_beer? ? "bottle" : "bottles"
  end

  def anglicized_bottle_count
    all_out? ? "no more" : bottles.to_s
  end

  def go_to_the_store_or_take_one_down
    if all_out?
      @bottles = 99
      buy_new_beer
    else
      lyrics = drink_beer
      @bottles -= 1
      lyrics
    end
  end

  def buy_new_beer
    "Go to the store and buy some more"
  end

  def drink_beer
    "Take #{it_or_one} down and pass it around"
  end

  def it_or_one
    last_beer? ? "it" : "one"
  end

  def all_out?
    bottles.zero?
  end

  def last_beer?
    bottles == 1
  end
end
```

The problems here:

* If changes need to be taken, it will changes in lot of places
* The hard high level of abstraction is not a good thing
* Name methods too poor not telling exactly what they do
* Too short methods returning only few things like the string method that only return a string `beer`. Certainly that could been just written in the string

Summary of this example:

1. How difficult was it to write?
Difficult. This clearly took a fair amount of thought and time.

2. How hard is it to understand?
The individual methods are easy to understand, but despite this, it’s tough to get a sense of the song. The parts don’t seem to add up to the whole.

3. How expensive will it be to change?
While changing the code inside any individual method is cheap, in many cases, one simple change cascade and force many other changes.

#### 1.1.4. Shameless Green

```
class Bottles

  def song
    verses(99, 0)
  end

  def verses(starting, ending)
    starting.downto(ending).map {|i| verse(i)}.join("\n")
  end

  def verse(number)
    case number
    when 0
      "No more bottles of beer on the wall, " +
      "no more bottles of beer.\n" +
      "Go to the store and buy some more, " +
      "99 bottles of beer on the wall.\n"
    when 1
      "1 bottle of beer on the wall, " +
      "1 bottle of beer.\n" +
      "Take it down and pass it around, " +
      "no more bottles of beer on the wall.\n"
    when 2
      "2 bottles of beer on the wall, " +
      "2 bottles of beer.\n" +
      "Take one down and pass it around, " +
      "1 bottle of beer on the wall.\n"
    else
      "#{number} bottles of beer on the wall, " +
      "#{number} bottles of beer.\n" +
      "Take one down and pass it around, " +
      "#{number-1} bottles of beer on the wall.\n"
    end
  end
end
```

The most immedietely apparent quality of this code is how very simple it is. That example shall duplicates strngs and contains few named abstractions, but it clearly the best solution here because is easy to understand and fullfill the most cost-effective way event if the problems mention, because of the pay-offs here. In this particular case, the song is so unlikely to change that betting that the code is "good enough" should pay off.

"Most programmers have a powerful urge to do more, but sometimes it’s best to stop right here."

"One of the biggest challenges of design is knowing
when to stop, and deciding well requires making judgments about code."

Summary of this example:

1. How difficult was this to write?
It was easy to write.

2. How hard is it to understand?
It is easy to understand.

3. How expensive will it be to change?
It will be cheap to change. Even though the verse strings are duplicated, if one verse changes it’s easy to keep the others in sync.
