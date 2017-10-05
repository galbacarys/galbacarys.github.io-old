---
layout: post
title: Am I living in a time warp?
subtitle: "What's old is new again: State machines in web development"
---

Warning: this article may offend you. I have some serious opinions and use some
colorful language to express them.

# My biases
If you've been keeping track of the kind of work I'm doing and the kind of
religion I'm espousing on this blog, you'll be well aware of the fact that I am
a huge believer in functional programming. Between its application in my recent
projects and the learnings I'm doing in SML and Clojure, it's perhaps a bit
obvious that I've been pushing that agenda in all parts of my software
development career as of late.

That's not to say that I've cargo-culted functional programming. I can say
absolutely that it has its warts, and there are plenty of things I would love to
fix about it. For one, as I'm reading through the excellent [Category Theory for
Programmers](https://bartoszmilewski.com/2014/10/28/category-theory-for-programmers-the-preface/)
by Bartosz Milewski[^1], I am slowly realizing just how incomplete functional
programming is without a solid type system underneath it (and Clojure's optional
typing is starting to bother me-clojure.spec be damned, it seems bolted on like
Python's type annotations). On top of that, functional programming is
*weird*-it's hard to find easy ways to integrate it into your daily workflow
without going part and parcel into a functional-first language like Clojure or
Haskell because it requires so many facilities that aren't available by default
in most languages (immutable data structures, lazy sequences, STM, etc.). But,
I've found some success in being honest with myself about how much I can take
on and adapting functional concepts to whatever application I'm working on. 

# Functional Programming in practice
In my latest project (a set of moderately-sized Node.JS microservices) I've 
been keeping in mind the stateless nature of functional programming. I've been 
modeling endpoints as simply transforms on data: I have a library containing all
the manipulations required for a particular object in my application, the
objects themselves (saved in DynamoDB using the balls-to-the-wall amazing
library [Dynamoose.JS](https://dynamoosejs.com/)) handle their own
serialization/deserialization logic, and all my endpoint definitions end up
looking like:

```js
function endpointHandler(request, reply) {
  let thing = Things.get(request.objectId);
  thing = ThingTransforms.frombulate(thing);
  let errors = thing.save();
  reply(genErrorCode(errors));
}
```

This has a couple of super important properties: first of all, it makes all of
my endpoints essentially boilerplate code (which opens up an opportunity for
autogeneration, which is something I'm pondering how best to approach), it makes
all of the application logic completely stateless
(`ThingTransforms.frombulate()` can internally do whatever the heck it wants to
the `thing` object, so long as it returns the new state of `thing`; I am using
immutability here whenever possible, but it isn't a hard and fast rule), and the
ugliness of database validation/serialization is no longer my problem. It also
makes ThingTransforms (the only part of my code that I need worry about from a
testing perspective) brainlessly easy to test since I can construct arbitrary
objects to achieve coverage of any and every edge case I care about. Things like
users' context can either be modeled as separate objects in a database (I've
been investigating using Redis for session storage in this way) or encoded in
the response to the client application and then sent over each request (which is
the "right way" to do REST APIs, if we're being pedantic).

# The Nineties
Let's contrast this to how web programming was done in the age of flannel and
grunge: back in the before before times, we would create a temporary state
object for each user and track their state as they moved from one page to
another in the application. The theory went that we would be able to more
efficiently present the user with what they needed and we would require less
network bandwidth to, e.g. transmit the entire state of the application over
the wire for each request. We would wait for the user to hit the "next" button,
and then send them the next bit of the workflow. "APIs" as we know them today
weren't really a thing, and we expected users to behave, or else we presented
them with the ever-hated "You can't hit the back button, you stooge!" message.

Clearly, this approach was in no way optimal: as it turns out, users aren't well
behaved, and some of them will even intentionally mess with your workflow just
to see what happens. These state machine-based web applications have been out of
fashion for a good long while, and many people have adopted something akin to
what I've presented above. They often don't adopt the moderately-strict
functional style I'm using, but they generally have some concept of an
"application controller" which at least encapsulates the database calls and
transition logic away from request validation.

The justification for this asinine design decision "back-in-the-day" was that
most users didn't have a concept of their browsers' history functionality, and
network bandwidth and computing resources were scarce enough that these crazy
ideas were seen as "optimizations" rather than "terrible fucking ideas you
should never, ever consider".

# Nowadays...
I've seen two articles and a presentation in the last week that explicitly
mention "state machines" as a viable way to build web applications. The state
machines these new voices espouse are somewhat different than those of the past:
they purport to handle invalid state transitions and entering the machine at
random locations; one common pattern I've seen is to model the state machine by
defining all possible states that, for example, an order for cassette tapes and
vintage sneakers could be in:

- Not yet placed
- Placed but not yet fulfilled
- Rejected due to credit card bounce
- Rejected due to inventory issues
- Rejected due to bad address
- Rejected due to vintage sneakers and cassette tapes being banned in your country for being too 90's
- Rejected due to [...]
- Validated
- Shipped
- Received

And then spelling out which states cannot exist at once (e.g., placed but not
yet fulfilled cannot coexist with shipped, but can coexist with validated or
rejected).

While this may seem like a pedantic exercise ("well, you picked some *oddly
specific* things there, friend…"), I posit that this pattern is a waste of time.
Rather than modeling states, we should be modeling *transitions*; this allows us
to define explicitly all the possible transforms that could be applied to an
object at any given moment, and detect invalid states more directly (e.g. if
there is no connection from rejected to validated, it is literally impossible
for that state transition to happen since we don't have a code path for it).

And yet, developers are more and more turning to the state machine pattern and
using one database object to track current state, with another one to model each
and every transition. This forces you to define every possible state from the
very beginning (or design your system so very carefully that adding a new state
is easy enough that it doesn't significantly burden the developers maintaining
your code later), and also to handle the fact that the data about each state
transition you want to save may not conform to a neat data format (e.g., a SQL
table may not be able to hold all of your transition data efficiently).

Now, I will be clear: both this approach and the approach I outlined above model
state machines in some way; however, the key difference is my approach's focus
on transitions rather than state; by making state simply a consequence of the 
behavior of your application rather than the driver, you can more easily adopt 
the functional style, which is a worthy goal in any case.

# And now for some math to prove my point
Here's the facts:

A graph of possible states can have at most `n(n-1)/2` possible edges
(transitions)[^2]. This means that in my system, you could need to define as
many as `n(n-1)/2` state transition functions. This sounds like a nightmare, but
most systems don't actually require this many transitions. The "new state
machine" concept I've outlined above requires `n(n-1)/2` definitions as well,
but this time for which states can coexist with others, and this is most
certainly required for complete definition of *any* state machine. Moreover,
adding a new state to the system I've outlined requires as few as 2 new state
transition functions (one to enter that state, and one to leave it), but is
dependent on the complexity of the behavior; the state machine requires the
redefinition of every state in relation to this new one, which is `n` updates.
*For every new state*.

# Conclusion
The advantage is obvious: by modeling transitions instead of states, you can
rest assured your program is never in an invalid state. Furthermore, the
addition of new behavior scales directly to the complexity of the behavior,
rather than requiring an update to the entire codebase. And finally, as someone
who really believes in the functional style I'm going to try and adopt as
stateless as system as possible: by modeling the application as a series of
transitions, state is simply the input and output of the application, not its
core behavior, and this has advantages too numerous to list here.

It seriously disturbs me to see this new style becoming more popular, and all I
can do is hope that people begin to see the light once more.

---
[^1]: I'm actually legitimately shocked I enjoyed this as much as I did-usually abstract mathy stuff isn't really my thing. You owe it to yourself as a programmer to at least give this a shot at some point in your career.

[^2]: I *definitely* didn't need to look that up, promise!
