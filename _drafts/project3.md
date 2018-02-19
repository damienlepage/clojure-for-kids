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

# Reveal parts of a word

The next step consists in processing an entire word with a function that we can call `reveal-letters`. Here is how it's going to be used:

```clojure
guess-the-word.core=> (reveal-letters "fantastic" #{\a \t \g \y \c})
"_a_ta_t_c"
```

You probably guessed already that this function `reveal-letters` will need to call the previous function `reveal-letter` for each letter of the word. Remember that a string is just a collection of characters and some functions exist to extract items from a collection, we are going to discover 3 of them with a small REPL session:

```clojure
guess-the-word.core=> (first "word")
\w
guess-the-word.core=> (rest "word")
(\o \r \d)
guess-the-word.core=> (next "word")
(\o \r \d)
guess-the-word.core=> (rest "a")
()
guess-the-word.core=> (next "a")
nil
```

Unsurprisingly, `first` returns the first character of the string. What's the difference between `rest` and `next`? There doesn't seem to be any when called with a word that has multiple letters, but there is a major difference when called with a 1-letter word. The fact that `next` returns nil instead of an empty collection makes it a function of choice when we want to combine it with a test expression, see for yourself:

```clojure
guess-the-word.core=> (if (not (empty (rest "a"))) "there is more" "this is the last letter")
"this is the last letter"
guess-the-word.core=> (if (next "a") "there is more" "this is the last letter")
"this is the last letter"
```

After splitting and replacing letters as needed, we also need to put them back together. This is can be done very simply with the function `str`:

```clojure
guess-the-word.core=> (str \b "_zz")
"b_zz"
```

With this knowledge in mind, can you create the function `reveal-letters`? Remember to use `let` to keep track of intermediate results.

<div class="solution closed"></div>
```clojure
(defn reveal-letters [word known-letters]
  (let [letter (first word)
        more (next word)
        current-letter (reveal-letter letter known-letters)]
    (if more
      (str current-letter (reveal-letters more known-letters))
      current-letter)))
```

<div class="solution closed"></div>
```clojure
(defn reveal-letters [word known-letters]
  (let [letter (first word)
        more (next word)
        current-letter (reveal-letter letter known-letters)]
    (if more
      (str current-letter (reveal-letters more known-letters))
      current-letter)))
```

The solution above can be improved by using a nice feature of the Clojure language which is called destructuring. This is just a trick to save some lines of code. A collection like the argument `word` can be destructured into its different parts directly where it is declared as an argument. This is best illustrated with an example:

```clojure
guess-the-word.core=> (defn destruct [[one two & more]] (println "one is" one "; two is" two "and more is" more))
#'guess-the-word.core/destruct
guess-the-word.core=> (destruct [1 2 3 4])
one is 1 ; two is 2 and more is (3 4)
nil
guess-the-word.core=> (destruct "amazing")
one is a ; two is m and more is (a z i n g)
nil
```

Notice how the function `destruct` takes only one argument, but this argument is decomposed into 3 parts. When called with a collection, the first element of the collection becomes `one`, the second becomes `two` and the rest becomes the collection `more`.

By the way, what type of collection is `[1 2 3 4]`? This is called a vector and you've already seen it many times because the vector is the data structure that is used to declare the argument of a function. When using destructuring, we use another vector inside the vector of arguments.

Can you now improve the `reveal-letters` function by using destructuring?

<div class="solution closed"></div>
```clojure
(defn reveal-letters [[letter & ammore] known-letters]
  (let [current-letter (reveal-letter letter known-letters)]
    (if more
      (str current-letter (reveal-letters more known-letters))
      current-letter)))
```
