---
layout: post
title: To Skin a Cat, part 1
subtitle: "Adventures in learning Clojure: Simple is hard"
---

I've got a friend at work named Hans. Hans is our Clojure evangelist, insofar as someone can be. he's always waxing philosophical about the joys and virtues of Functional Programming, and why every project can benefit from laziness and immutability. Now, I've always been interested in functional programming and have attempted several times to learn FP-based languages (Haskell, Scheme, the functional bits available in JavaScript and Ruby), but I've never quite "gotten" it, and so I decided to give Hans's favorite functional language a try to see if it would fit me better.

Boy howdy has it.

# The cult of Functional Programming[^1]

Hans gave me one of his copies of the O'Reilly book on Clojure (of course he has multiple) and had me "sign in" to the front of the book-he keeps a record of every "conversion" he's accomplished by having people write their names in the front of the copy they use. I grabbed the book from him on a Friday and by the next Tuesday I had finished it. I still feel like a complete n00b when it comes to doing anything *useful* with the language, but for probably the first time ever, I feel capable of at least expressing problems in a functional manner, even if I'm not good at it yet. And for me, that's progress beyond my wildest expectations.

I now get what people mean when they say immutability makes problems easier; I see why STM is the coolest thing to have ever happened to software engineering. It's just a matter of getting it all integrated now. One of my most interesting experiences to date has been trying to solve a simple problem, coming up with a perfectly valid, if verbose, answer, being unsatisfied, and becoming enlightened once I found the "correct" answer online.

## The 4Clojure problem

Since I finished the O'Reilly book, I've been using the 4Clojure problem set as a way to practice my skills and get a better practical understanding of writing Clojure code. I ran across this problem earlier today:

> Write a function which checks if a sequence is a palindrome:
```clojure
(false? (__ '(1 2 3 4 5)))
(true? (__ "racecar"))
; etc.
```

My original solution was structured in much the same way I learned to solve this problem in high school APCS: 
```clojure
(fn [string-or-seq]
  (loop [s (seq string-or-seq)]
    (if s
      (if (= (last s) (first s))
        (recur (butlast (rest s)))
        false)
       true)))
```

This passed all the test cases without a problem; it was somewhat naive in its application of functional programming, but I treated the loop iteration as a recursive function call, which is the "standard" "canonical" way to do it, and I did some neat bits with the input sequence (I'm especially a fan of the line `(recur (butlast (rest s)))`, this illustrates to me the elegant built-in power of functional languages' handling of sequences). 

## Okay, so we're done right?

Well, not quite.

I wasn't actually *happy* with this solution, and people whose Clojure more regularly than I do and/or have more experience than I do will know why. Essentially, the fact that I had 2 nested `if` forms and both the `true` and `false` literals in my solution made me feel uncomfortable. The solutions shown for the problems in the O'Reilly book were *so elegant*[^2], and this didn't resemble those at all. I knew there had to be a "one-liner" for this, but I didn't see it until I started googling around for other peoples' palindrome finders in Clojure online.

This is the "better" solution:

```clojure
(fn [string-or-seq]
  (let [s (seq string-or-seq) rs (reverse s)]
    (= s rs)))
```

I didn't even think about the fact that a palindrome would be identical forwards and back, and that that was a perfectly valid way of solving the problem, simply by checking that the sequence was identical to itself but reversed.

## Towards a "best" solution

So, the question then becomes which solution is best. I'm going to try to talk about the three major optimization targets in programming: speed, memory usage, and maintainability.

### Speed

I think that in practice my solution would be fastest when sequences are (relatively) very short, while for longer sequences the internet's solution wins out. While my solution would only require one allocation of the incoming sequence (saving time during startup) it would also require accessing the tail end of a sequence at every iteration, which is markedly slower than accessing the head. Thus, mine would grow in `n*log(n)` in the length of the sequence while the internet's solution would only increase linearly.

### Memory usage

This is an easy win for my solution: using `recur` doesn't take up extra stack space, so the stack usage wouldn't spiral out of control, and there would be only one allocation of the array since a new sequence does not need to be created (internally, Clojure can map many sequences onto a single allocation of memory so long as you don't modify the sequence in any way). On the other hand, `reverse` requires completely re-allocating the incoming sequence, but in reverse order, which means double the memory usage.

### Maintainability

Ultimately, this is actually the most important thing to optimize for in your code unless performance is a requirement you aren't currently meeting. It's better to write clean slow code than ugly fast code in almost every scenario. In this case, I lose hands-down. While my solution is *readable* and with minimal effort understandable, I could hand the internet solution to a 9 year old and they would "get it". 

## Clojure: Simple is hard

This is the key insight about what makes Clojure awesome, I think: since code is read 100 times as much as it is written, optimizing code for its readability is crucial to developing complex systems. It's hard to find the right way to express things sometimes, and while Clojure provides a gigantic toolbox of sequence manipulation goodies to make it easier to reason about a solution, the "interesting part" of programming in Clojure (or any other functional language really) is to find a way to express the problem in terms of a problem you already know how to solve. Thus, when you learn how to solve that problem, it gets added to the "body" of ready-made solutions you have to work with, which allows you to solve yet more complex problems than before.

That isn't to say that Clojure is *easy*: finding those elegant, completely understandable solutions takes work (often very mind-bending work at that). Part of this stems from the fact that imperative programming has ruled the world for the last 50 years, and the vast majority of our computer science education is based around understanding the machinery required to force the computer to do what we want. Clojure, on the other hand, *gives* you the machinery and makes that code *completely uninteresting*, which frees your mind up to solve the actual problem at hand, as opposed to the (often equally difficult, but much less interesting) task of building up the tools to even describe the problem to the computer.

This is what makes me really excited about Clojure: solving problems with it may be harder off-the-bat than in other languages, but if you really get those simple problems right, then the simple can interact with the simple to form something more complex; but, since we can implicitly trust a solution we understand completely, and which is built out of trusted components, our trust in our system never decreases even as its complexity increases. The smaller each "next level" is, the more concisely we can express each level, and the better we can understand it. Ultimately, this will (hopefully) lead to better-constructed software.

---
[^1]: The cult of functional programming (AKA the Holy Church of lambda) is one of the most evangelical movements I've ever seen in software engineering, perhaps topped only by the followers of St. iGNUcius himself.

[^2]: A brief aside, this is actually an extremely common problem in programming textbooks. The solution presented is always the absolute most elegant one, using methods of manipulating the problem that I (and many other readers) would have never even begun to think of. It almost feels like the solution was known before the problem, so all the steps taken from problem to solution were built up completely intentionally. I almost wish programming textbooks would show the full thought process of solving a problem, e.g. "First we'll try <solution>; this is non-optimal because of <reason>; what are some other ways we can think of this problem?", etc.
