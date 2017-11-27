---
layout: post
title: Towards a theory of DevOps
---

This is part 1 of an ongoing series in which I intend to create a sort of
"spellbook of DevOps practices".

[Standard disclaimer: all of the opinions expressed herein are my own and are in
no way reflective of the views and policies of my employer]

# What even is DevOps?

Imagine a project where you know ahead of time exactly what your requirements 
entail and which are most important.

Imagine a project where you have an actionable plan at the beginning of each day
for the work you're going to perform, and metrics to measure yourself against
that plan.

Imagine a project where every single line of code you push to production has 
been vetted by every test you could possibly want. 

Imagine a project where the moment something breaks, you know and have a plan 
for how to fix it. 

Imagine a project where responsibility for your app is distributed amongst a 
diverse group of experts, each with their own domain expertise, but each of 
which is perfectly capable of handling any situation that may arise.

These "five fantasies" encompass my understanding of DevOps, namely 
requirements, planning, testing, monitoring, and cross-training. Each is
important in their own way, and all point towards one simple idea: *to reduce
risk*. In the simplest terms, DevOps is the practice[^1] of reducing risk to
better deliver a software project on time, within the stated requirements, and
with operational preparedness to match expected service agreements and
expectations.

Now, there are clear reasons one might want to reduce risk in a project: it
allows for easier control of costs, better understanding of the problem at hand,
a clearer path forward for addressing feature requests and bug reports, etc. But
the reason you should care is that reduced risk means reduced stress on you as a
software engineer. DevOps provides a path for you to prevent working harder than
you need to and stop all work that isn't the most important possible thing you
could be doing right now.

## General philosophies

The most difficult thing to understand about DevOps is actually its philosophy;
this is something that I find many teams struggling with as they attempt to
understand why and how to start operating in a "modern" or "DevOps-y" manner.
Many engineers on a team moving to DevOps will fall into one of three
categories:

1. DevOps means I'm going to have to go on call all the time and I'll never get
   to have a night to myself again! (This is only partially true.)
2. I've got automated deployments and a dashboard, which means I'm doing DevOps,
   right? (Nope.)
3. All this stuff is baloney and a waste of my time. I just want to get back to
   coding (There's actually evidence to show that you get MORE time to work with
   DevOps!)

It's instructive to break down each of these points and explain how they fall
short of understanding the DevOps paradigm, so at the risk of boring the reader
I'm going to take the time to do so here.

### 1. On "going on call"

DevOps does not (exclusively) mean you'll be on call more. It's true that
many members of a DevOps team will find themselves responsible for their work
outside work hours, which to many is undesireable and feels like a step
backwards for their work life balance. However, going on call allows for a few
very important things to happen. 

First of all, code quality necessarily improves, as engineers are naturally lazy
and would rather not respond to on call issues if they don't need to; monitoring
and alerting naturally improve when they wake people up in the middle of the
night, and annoyins issues are fixed more quickly and more completely than they
would be otherwise.

Second, being on call is a responsibility shared by all team members, whether
they be more development or operations focused[^2]. This forces cross-functional
training (a huge aspect of DevOps practice, discussed later in this article) and
discourages silo-forming, or the harmful practice of information being cached in
such a way that portions of the team aren't able to access it easily.

Finally, going on call is not the burden many envision, especially not after
other DevOps practices have been adopted. By going on call, we accept
responsibility for our work and ensure that we improve on our ability to manage
our product. Over time, the goal is naturally to reduce the amount of
out-of-hours work we have to do, and on call rotations help encourage us towards
that goal.

### 2. Tooling does not imply DevOps

This one is especially insiduous. Many teams have convinced themselves that they
are "DevOps" once they have a code packagin pipeline (and perhaps automated
testing and/or deployment) in place. In reality, DevOps has nothing to do with
the tools you use. Or, rather, DevOps is not defined by a toolset, but instead
by a methodology for using your tools.

A "standard DevOps toolkit" might involve:
- a ticketing and work tracking system like JIRA, 
- code management software like GitLab,
- an automated distributed task runner like Jenkins,
- an automated deployment tool like Ansible,
- And a metrics and log gathering tool like Splunk

but all of these tools can (and in many successful organizations, are often)
be subsituted for any manner of other tool, or none at all. The goal is not to
be prescriptive about which tools are "good" or "bad" for a DevOps environment,
although I will discuss some of the tools I prefer later on in this "handbook".

---

[^1]: practice here denotes a deliberate and continuous effor to improve upon a principle, in this case the application of DevOps.

[^2]: In fact, this is where the name "DevOps" comes from, Dev(elopment) + Op(eration)s
