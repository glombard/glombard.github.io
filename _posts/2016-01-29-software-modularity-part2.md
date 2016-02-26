---
layout: post
title: Software Modularity Part 2
tags: modularity, architecture
comments: true
---

In the [previous post]({% post_url 2016-01-21-software-modularity-part1 %}), I described one aspect of modularity: how should you split your app into multiple packages/directories? In this post, I'm continuing on the topic of software modularity...

**Single Responsibility Principle**

We are all very familiar with the concepts of
[Separation of Concerns](http://c2.com/cgi/wiki?SeparationOfConcerns>) and the
[Single Responsibility Principle](http://blog.8thlight.com/uncle-bob/2014/05/08/SingleReponsibilityPrinciple.html>)
(SRP) as they are applied on the class and method level.

Too often, not enough thought is given to applying SRP on the module level. In other words,
a large application should be broken into many small modules instead of growing it into a
large monolithic application over time.

**Why?**

Some people object to splitting an application into physically separate modules using
arguments like *"that's what packages/namespaces are for"*, but that's not necessarily
enough. Properly structuring the code into packages is definitely a good idea, but we
need to do more. A large application easily becomes a monolithic mess that is
hard to maintain, because changes in one area affect other unrelated areas when
components are so intertwined that it's difficult to see the boundary between layers.

A modular design allows us to grow the application by adding new modules, with very
little impact to existing modules. That means that separate teams can focus on the
modules applicable to their areas of the application, without being distracted by the
complexity inside unrelated modules.

**What is modularity?**

[Modularity](http://en.wikipedia.org/wiki/Modularity) is the degree to which a
system's components may be separated and recombined. A module is a collection of
related concerns/components. Modules are independent and typically communicate in
a loosely-coupled fashion, e.g. using an [event bus](http://square.github.io/otto/)
or well-defined interfaces.

One big advantage of modular design is that it limits the amount of code you need
to consider (i.e. the size of the model you need to load into your brain) when working
on something. You don't need to understand the entire codebase to see how your change
is going to affect the entire system, you can focus on the module alone, and as long as
the module's API/interface doesn't change you can be fairly confident that you're not
breaking something else.

**Goals**

What are we trying to accomplish? Some random goals of a modular design:

- Maintainability: a smaller code base is simply easier to understand and therefore
  easier for maintain.
- Facilitates teamwork by breaking system down into smaller pieces. A team doesn't have
  to understand or consider the entire application, only their own module(s) and the
  interfaces of external modules. Separate teams can work on separate modules without
  stepping on each others toes.
- Flexibility: can replace/refactor a module's implementation without affecting the
  rest of the system.
- Composition: can re-configure an application to include/remove modules as necessary.
  Composition of modules can either happen at run-time or statically during compile-time,
  for example modules are merged into a single project before compiling. For example,
  ideally, new large features would be encapsulated in its own module. When the feature
  (experiment) is removed, the separation is clear.
- Re-usability: clearly defining and limiting a module's responsibility means it's more
  likely to be usable in other applications.
- Enforces architectural layer integrity: by separating concerns using modules, it's
  easier to enforce the
  [Dependency Rule](http://blog.8thlight.com/uncle-bob/2012/08/13/the-clean-architecture.html)
  which says that "source code dependencies can only point inwards", for example, your
  "networking layer" should not know or care about UI concepts like Activities etc, and
  similarly, the Activity code should not attempt to do networking stuff directly. By having
  physically separate modules, it makes it easier to visualize the architectural layers of
  the application and to understand where a change in the code needs to go.

What's your thoughts on modularity? [Let me know on Twitter](https://twitter.com/codeblast)!