---
layout: post
title: Drawing tools for developers
tags: tools
comments: true
---

If you're like me, then you hate doing documentation. I'd much rather draw a block diagram on a whiteboard and a photo of it. But sometimes you need to create a more official looking diagram, and then you probably want a tool that helps you get the job done as quickly as possible, without having to learn how to use Visio or some complex graphics / drawing application.

## seqdiag

[seqdiag][1] allows you to generate UML sequence diagrams using a simple text file format.

## draw.io

[draw.io][2] is a very nice online drawing tool that allows you to download your drawings, including UML diagrams etc, as .png or lots of other file formats.

## graphviz

[GraphViz][3] with its simple .dot file syntax makes it very easy to create directed graphs. It's easy to write a script that reads some input and generates the .dot file, and then you use graphviz to generate a .png or .pdf file. For example, I've used it to generate a graph to visualize the dependencies between projects in a Visual Studio C++ solution etc.

## Visual Studio

Recent versions of Visual Studio support generating a [.dgml][4] graph to show project dependencies.

But you can also write your own script (PowerShell?) to generate a .dgml file from any hierarchical or graph source and view the .dgml file in Visual Studio.

## And...

There are so many other cool free graphics tools I've used over the years, I can't even remember half of them. Also check out Gimp and Inkscape...

[1]: http://blockdiag.com/en/seqdiag/ "seqdiag"
[2]: https://www.draw.io/
[3]: http://www.graphviz.org/ "graphviz / dot"
[4]: http://en.wikipedia.org/wiki/DGML "Directed graph markup language"
