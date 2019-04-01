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

For the time I am reading this booking I will write some resume for the things that understand. This is only and only for me to fix the concept.

### 1. Rediscovering Simplicity

As we growth into programming we tend to write code more complex hoping that it will ultimately be easier to maintain. Unfortunately do such abstraction is hard even with the best intentions. That's because design decisions inevitably involve trade-offs, for example:

When you try to DRY your code you may pay the cost of being total ignorant on know what is happening to method only the result of it.
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

#### Method versus Message

The method is defined on an object, and contains behavior. Like the method named `song` in `Bottles`.

A message is sent by an invoker behavior. Like the `song` method sends the `verses` message to the implicit receiver `self`.

There is often a confusing between these terms because when you named the method as the same of the message it makes them synonymous, but the are not. In the example above the method `song` sending the message `verses` which results the invocation of the `verses` method.

Named method is like writing a book. U may take every effort if you wish your work to survive to be read.
