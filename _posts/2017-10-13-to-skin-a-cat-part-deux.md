---
layout: post
title: To Skin a Cat, part deux
subtitle: "Functional programming outside Wonderland"
---
I've been writing a lot of Node.JS these past couple of months; even more
weirdly, almost all of it has been self-imposed. Basically, I had a project come
up at work, and I was curious about the ecosystem, and so I said "Why not learn
Node?" and was off to the races.

The project itself has actually been pretty pleasant in terms of picking up the
(admittedly really, really strange) semantics of the Node environment; I chose
Hapi.js as my web framework of choice thanks to its support for autogenerating
Swagger docs from your route definitions[^1]. I've especially enjoyed how
concise ES6/next make the language.

However, given that I've been on a quest to better understand functional
programming (and given that I had the very good luck to be working on a solo
project where I could make these kinds of sweeping decisions), I also decided to
make this project adopt "the functional style" (in the words of John Carmack). I
wanted to spend a little time today going over how this works[^2].

## The traditional model, un-traditional-ified.

So, the standard Hapi.js (really Node.JS (really recent web development, in
general)) API project layout looks something like this:

```
my-awesome-project
├── LICENSE.txt
├── README.md
├── app.js
├── controllers
│  ├── BarController.js
│  ├── BazController.js
│  └── FooController.js
├── models
│  ├── BarModel.js
│  ├── BazModel.js
│  └── FooModel.js
├── routes.js
└── settings.js
```

This is a common pattern, inspired by such greats as Ruby on Rails and Django,
who (to a greater or lesser extent) also follow a similar setup. The idea is to
enshrine at the structural level both separation of concerns and easy testing;
since, ideally, each model is simply a serializer/deserializer of data along
with some methods to directly manipulate said data, it can be tested separately
from your controllers ("views" for all you Django folk), which handle such
details as translating user requests into information your system understands
and can manipulate.

As it turns out, this model can be applied functionally if we make the following
affordances to stretch the definition of `model` and `controller` a little
farther than normal:

- we treat models as simply a single serializer and deserializer function, and 
then a collection of completely stateless transforms on our data
- we adopt a simple pattern for our controllers: deserialize, transform, serialize,
respond

These rules are a little stricter than the standard wisdom of API development,
in which we adopt object-oriented design to couple the behavior of a piece of
data with its associated transformations, but it's actually not a significant
cognitive burden to adopt. It's even relatively easy to take an existing
application and adapt it to this style!

(A quick note: in the following code examples, I'm ignoring asynchronous
execution of, e.g. database calls. Call it pseudocode if you need to, or assume
I use ES6 and `async/await` everywhere.)

For example, instead of this:

```js
let ThingSchema = Mongoose.Schema({ /* Mongoose schema def */ });

ThingSchema.methods.updateValue = function(newVal) {
  return this.model('Thing').find({ id: this.id })
    .then((thing) => {return thing.update({val: newVal }) });
};
```

You could write something like this:
```js
// This is part of the secret sauce
const { Map } = require('immutable');

// This returns an *immutable* JS object. To modify, you have to copy it.
let getThingPublic = function(id) { 
  return Immutable.Map(getThing(id)) 
}

// Saves a thing
let saveThingPublic = function(thing) { 
  validateThing(thing); 
  saveThing(thing.toObject()); 
}

let updateValue = function(thing, value) {
  return thing.set({val: newVal})
}
```

The basic mechanics of the code stay the same, but you concentrate all of the
machinery for actually doing the "boring bits" (serialization and
deserialization) into a couple of functions build to serialize and deserialize
your data; then you create a library of functions that applies manipulations and
transforms to your data.

Our route handler might then look something like this:
```js
function routeHandler(req, res) {
  res(saveThingPublic(updateValue(getThingPublic(req.id)))); // that was easy!
}
```

Or, if written in a more functional style:
```js
function routeHandler(req, res) {
  // `flow` is available in many functional programming libraries. It just
  // takes the output of the first function and plugs it into the second, etc.
  // Note that your favorite lodash-flavored library might do something
  // different, but the basic semantics are avaialable.
  _.flow(getThingPublic(id), updateValue, saveThingPublic, res)([req.id]);
}
```

I love this pattern because it's so ridiculously explicit in what's happening,
and it's obvious exactly where the state mutation is happening.  Additionally,
since each and every function just speaks the "language" of our application,
functions are nearly infinitely composable.

## Now hold on just a second...

This looks longer, doesn't it? You're probably thinking "Why in the heck is this
a better idea?". And *that's a good thing!* As it turns out, functional
programming is hard to wrap your head around, and it requires a little extra
work on the front end to make it make any sense. But, if you're willing to humor
me, I can prove to you this is the better alternative.

### The boring bits
Rich Hickey (BDFL of Clojure) likes to say that software is "situated" if it
deals with the real world[^3]. This is to say, situated programs are those that
deal with the realities of talking to a database, interacting with
~~morons~~users, etc. The functional pattern allows us to isolate the "bits" of
our code that deal with that unfortunate side effect of living in the real
world; since our serialization and deserialization code is written once and in
such a manner that it just takes/returns a JSON blob, it's replaceable and
totally uninteresting.

### The interesting bits, and a word on testing

One of the main advantage functional programming advocates like to harp on about
writing pure code is that pure code is *dramatically* easier to test than
potentially stateful code, and this method forces your hand in some sense to
keep all your "interesting business logic" in stateless transforms. Testing
becomes an exercise in passing in a constructed example and verifying the output
is exactly what you expect; this is the easiest kind of unit test to write by
far, and the only integration tests required are those to do the
(de)serialization (i.e. the boring bits). If you write the boring code well, and
test it throroughly once, you can treat it as library code and drop it on other
projects, and your testing surface is decreased dramatically.

## What have we learned?

The advantages of this pattern are many:
- "library-izing" your boring, difficult, error-prone database code
- Making your business logic the only thing that matters
- Dramatically simplifying your testing procedure
- Making all your route code dead simple
- Simplifying understanding of your code by others. Everyone can understand
"load, modify, save, respond" even if they aren't well spun up on your code
base. Since there's no "magic" anywhere, understanding of your code isn't
hampered by your architectural patterns so long as you name and document well.

Granted, the initial effort of creating what is essentially a database
serialization/deserialization library that behaves in this way is significant.
However, I posit that spending the time to do this right at the beginning of
your project will pay itself back in spades later on.

For more (dubious) functional programming wisdom, check out some of my other
posts!

---
[^1]: I'm a HUGE believer in Swagger. I think that documentation is the most important thing we can do as developers who build tools for others, but I hate writing it almost as much as I believe in its importance. Swagger bridges the gap between docs and code by autogenerating both from a YAML-based declarative spec language. It's, in concept, the best way I've ever seen to build APIs, and the best use of a DSL I've ever used. It does, however have its warts, and in this case, my Swagger definition is actually being generated from my Hapi route definitions, and then the docs from that. That's a minor semantic detail, but may be an interesting article in its own right in the future.

[^2]: In case you haven't read the inspiration for this post, you should definitely [check it out](https://galbacarys.github.io/2017/am-i-living-in-a-time-warp/).

[^3]: The example he gave at clojure/conj this year was awesome. ([link](https://youtu.be/2V1FtfBDsLU?t=638))
