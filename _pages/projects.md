---
layout: page
title: Projects
subtitle: Side projects are fun.
desc: Side projects are fun.
permalink: /projects/
---

Oh goodness is this page a work in progress. Well, let me give you a quick list
of things I'm currently working on:

- A couple of game prototypes are always in the pipeline for me. My current one
is a metroidvania-style game with some survival, crafting, and heavy exploration
and resource management components.

- At ViaSat, I'm currently working on a complete redesign of how we approach
CI/CD by making it auditable, replicable, highly-available, and brainlessly easy
to use. This is based around Jenkins as well as several custom webservices which
integrate with Jenkins and our source code repositories to provide a variety of
services to make the build/test/deploy cycle easier. Check back for updates, I'm
gunning to get some of this work open-sourced one day. No guarantees on that
though.

- I have a couple of "big ideas" I'm working on: one is a SaaS platform on top
of Docker that would take immutable infrastructure to the next level: basically,
you would deploy a set of Docker hosts that would be only for serving your apps
in a performant, secure manner. No extra management tools, no nothing, just
great monitoring out of the box and autogenerated templates for deploying to
AWS/Openstack. The idea is to make immutable infrastructure go down to the
compute level rather than stopping at the configuration level; if you want to
update your app, you would be able to deploy new container versions directly,
but if you wanted to stand up another app it would get its own Docker cluster
with its own networking and management. This will hopefully prevent a lot of the
barriers to starting with Docker and reduce the heartburn with getting
developers started using container technology. The other is a new paradigm for
building REST API's that I don't want to give too much away about right now.
