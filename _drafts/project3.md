---
layout: post
title:  "Project 3 - Guess the word"
---

# The project

In our third project, the player will need to propose letters to try and find the name of a country. The game can be modified to play with cities or animals or common words or any other list of words that you may be able to find.  

![Demo]({{ "/assets/project3.png" | absolute_url }})

# Characters

Strings are composed of characters, nothing surprising with that but how do we represent a single character outside of a string? The answer is to prefix it with `\`. And in order to transform a string into a sequence of characters, the function `seq` can be used. Here is a REPL session to demo this:

```
user=> \a
\a
user=> (seq "this is wonderful")
(\t \h \i \s \space \i \s \space \w \o \n \d \e \r \f \u \l)
```
 
Notice how the space character is fully spelled out as a character.

# Sets

Another data structure that we haven't seen yet will be very useful for this project, in order to keep track of the letters proposed by the player. This is a type of collection called a set, which is very good at working with unique elements. Its notation is `#{}`.

Let's have a REPL session to discover its properties.

```clojure
user=> #{1 2 3}
#{1 3 2}
user=> (#{1 2 3} 3)
3
user=> (#{1 2 3} 4)
nil
user=> (conj #{1 2 3} 4)
#{1 4 3 2}
user=> (into #{} "hello")
#{\e \h \l \o}
```

As you can see the order of the elements is not maintained. Also, the set can be used as a function to look-up an element it contains, you'll see later how it can be useful (hint: remember the test conditions that are not `false` or `nil`?). Finally, note the useful functions `conj` to add an element into a set and `into` to load another collection (here a string) into a set. Notice how the character `\l` appears only once in the set.

# Reveal or hide a letter

Time to create the project, follow the same procedure as previously and remember to not use any space in your project name. Then let's start writing some code inside your new `core.clj`.

The first part of the project is to create a function called `reveal-letter` that will take 2 arguments, the argument `letter` which is a character, and the argument `known-letters` which is a set of characters already played. This function will return the character `letter` if it's a known one, or replace it with `_` if it's unknown. Here is a REPL session to illustrate:
 
```clojure
guess-the-word.core=> (reveal-letter \a #{})
\_
guess-the-word.core=> (reveal-letter \b #{\a \b \c})
\b
guess-the-word.core=> (reveal-letter \z #{\a \b \c})
\_
```

Try writing this function yourself then check the solution if needed.

<div class="solution closed"></div>
```clojure
(defn reveal-letter [letter known-letters]
  (if (known-letters letter) 
    letter
    \_))
```

Good, however this function hides special characters such as spaces. We only want letters from the alphabet to be replaced. Can you do that? Here are two tips:
 
* An easy way to get the alphabet into a set is to write it into a string before loading it into a set.
* The function `not` is useful to test the opposite of something.

Now give it a shot and try not to peak at the solution before you have something working.

<div class="solution closed"></div>
```clojure
(def alphabet (into #{} "abcdefghijklmnopqrstuvwxyz"))

(defn reveal-letter [letter known-letters]
  (cond 
    (known-letters letter) letter
    (not (alphabet letter)) letter
    :default \_))
```

