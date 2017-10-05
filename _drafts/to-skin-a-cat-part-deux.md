---
layout: post
title: To Skin a Cat, part deux
subtitle: "Functional programming outside Wonderland"
---
# "Functional" Node
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
project where I could make these kinds of sweeping decisions)

---
[^1]: I'm a HUGE believer in Swagger. I think that documentation is the most
important thing we can do as developers who build tools for others, but I hate
writing it almost as much as I believe in its importance. Swagger bridges the
gap between docs and code by autogenerating both from a YAML-based declarative
spec language. It's, in concept, the best way I've ever seen to build APIs, and
the best use of a DSL I've ever used. It does, however have its warts, and in
this case, my Swagger definition is actually being generated from my Hapi route
definitions, and then the docs from that. That's a minor semantic detail, but
may be an interesting article in its own right in the future.
