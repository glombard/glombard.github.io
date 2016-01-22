---
layout: post
title: How to structure app packages - Modularity Part 1
tags: modularity, architecture, android
---

How should you structure your code into separate directories/packages/namespaces?

Another way to phrase that question: How should you modularize your code?

Even if your medium-sized app is small enough that you don't need physically separate modules,
you likely still need to organize your code into virtual modules by using namespacing,
eg. Java package names. The first and easiest thing you can do to prevent any sufficiently large
codebase from becoming a huge unmaintainable mess is to keep the code modular.
Keep separate things separate and only keep related things together.

This is not a controversial idea. Everyone knows about
"[Separation of Concerns](https://en.wikipedia.org/wiki/Separation_of_concerns)" (Soc).
I'm sure we can all agree that SoC is a good idea, however where we may start disagreeing
very quickly once we dig into the details, is exactly what "concerns" should we separate?
How do you decide where to draw the boundary lines between the classes/components of your code?

A very common pattern that I see in Android, AngularJS and other platforms, is to organize
classes by their "type". For example many, if not most, AngularJS sample apps split the
`.js` files into directories like these:

- `controllers`
- `directives`
- `services`

As another example, let's take a hypothetical Android "TODO list app", which might be split
up into directories like this:

- `adapters`
    + TaskListAdapter
- `interfaces`
    + LoginResultHandler
- `model`
    + Task
    + LoginResult
- `presenters`
    + AddEditTaskPresenter
    + LoginPresenter
    + TaskListPresenter
- `utils`
    + LoginAPI
    + TodoDatabase
- `views`
    + LoginActivity
    + TaskListActivity
    + AddTaskActivity
    + EditTaskActivity

(Note: my class names above are contrived, a real application will likely need more and different classes.
My point here is the directory naming: `adapters`, `views`, etc.)

To me the above naming approach seems absurdly arbitrary, because it gives you no useful insight or
visibility into the structure, features, size or scope or any other characteristic of the code.
It only tells you what kind of components (e.g. activities, services, adapters) were used, which
is pretty uninteresting by itself. You might as well separate the classes alphabetically or by date
or by size or any other equally meaningless aspect.

The logical areas / features of the application, namely "Login", "List tasks" and "Add task" are
scattered all over the place. If I need to make a change to the "Add Task" code, the structure of
the packages don't assist me at all, in fact they only obscure the structure of the application unnecessarily.

An analogy I like to use: how do we apply SoC when we organize the books in a library? Would it make
sense to organize all the books in a library by the color of their cover? Or by their size?
Thickness? Alphabetically by title or author? No, of course not: books are mainly arranged by *subject*.

Why would I need `LoginActivity` to be in the same package or namespace as the completely unrelated
`TaskListActivity`, while their related partner classes, say `LoginPresenter` and `TaskListPresenter`
respectively, are in a separate package?

Java packages in conjunction with `protected` or
[package-private](https://docs.oracle.com/javase/tutorial/java/javaOO/accesscontrol.html)
access control (or C# namespaces in conjunction with the `internal` keyword) provide a mechanism
to isolate and hide related code that should not be accessed externally (albeit a slightly weak
boundary when compared with using physically separate modules, especially in C# where there is no
package-private concept). Consider this when separating code by using packages - do the classes in
the same package really need implicit mutual visibility? In other words, do they really belong together?

It makes a lot more sense to structure the root level of the code according to the separate logical
units, or "modules", of the app:

- `data`
    + Task
    + TodoDatabase
- `edit`
    + EditTaskActivity
    + AddTaskActivity
    + AddEditTaskPresenter
- `login`
    + LoginActivity
    + LoginAPI
    + LoginPresenter
    + LoginResult
    + LoginResultHandler
- `list`
    + TaskListActivity
    + TaskListAdapter
    + TaskListPresenter

(Of course, you're still free to arbitrarily namespace the levels below that into whatever sub-packages
you like, like `views`, `services`, `utils` etc.)

So how did I decide on these directories? Isn't this also arbitrary? No, the code is now divided into
three logically independent modules: login, add/edit and list. (The `data` module is also separated
out because it's shared by both the `edit` and `list` modules.)

A module is a logical standalone unit of the application. For example, at least in principle,
the “Add/edit task” functionality can exist and operate independently without having to consider
the "Login" and "List tasks" functionality at all. If I'm a developer tasked to work on the
"Edit task" functionality, I know where I have to focus my attention in the code. I don't need
to build a mental model of the `login` or `list` code in order to understand and work on the `edit` code.

In John Papa’s Angular Style Guide, he describes the
[LIFT](https://github.com/johnpapa/angular-styleguide#application-structure-lift-principle) and
[Folders-by-Feature Structure](https://github.com/johnpapa/angular-styleguide#style-y152)
guidelines for naming the directories in a project:

- *"Create folders named for the feature they represent... Do not structure your app using
  folders-by-type. This requires moving to multiple folders when working on a feature and
  gets unwieldy quickly as the app grows to 5, 10 or 25+ views and controllers (and other features),
  which makes it more difficult than folder-by-feature to locate files."*
- LIFT principle: structure your app such that you can
    + **L**ocate your code quickly
    + **I**dentify the code at a glance
    + keep the **F**lattest structure you can
    + **T**ry to stay DRY (Don’t Repeat Yourself)

In this post, I’ve only considered using directories/packages as a way to divide code into logical
modules, but in a larger application (especially when working with a team or even multiple teams)
the same concepts would apply when deciding how to break the application up into physically
separate modules, e.g. separate `.jar` or `.dll` files. I will discuss modularization more in
future posts, e.g. how do we define a module, what are the benefits of modularization,
how does modularization relate to coupling and cohesion.

Links:

- [Separation of Concerns](https://en.wikipedia.org/wiki/Separation_of_concerns)
- [On the Criteria To Be Used in Decomposing Systems into Modules](https://www.cs.umd.edu/class/spring2003/cmsc838p/Design/criteria.pdf) by [David Parnas](https://en.wikipedia.org/wiki/David_Parnas)
