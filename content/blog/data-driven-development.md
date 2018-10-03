+++
title = "Data-driven approach"
author = ["Anton Erholt"]
description = "It's all about 42, (defn foobarbaz []) and cheeseburgers."
date = 2013-11-15
lastmod = 2018-10-03T22:26:29+02:00
tags = ["game", "design"]
categories = ["blog"]
draft = false
weight = 2024
+++

A data-driven approach is shifted from Object Oriented approaches.
The data-driven approach seems like a really good fit if you are
geared towards making a state-of-the art game engine for making really
high performing games. Bitsquid is certainly outstanding in that sense

It's always a bit tricky when you're learning a new programming
paradigm. Usually you learn a new programming language together with
the paradigm which can help you understand difficult patterns and
ideas more easily. However when shifting to the data driven paradigm,
you haven't really got the advantage of changing language as well.

According to wikipedia&nbsp;[^fn:1], there are a few languages which have
a more data driven approach but I have unfortunately not used any of
these (except for one line of awk, but that's it). I'm guessing what
we're after is to learn the concept and then embrace it in whatever
language we are most confident. Techniques like loop unrolling and
cache optimizations through padding are worth noting in the back of
your head.

I'm still kind of eager not to do this first. Since I'm already pretty
much into the object oriented paradigm, I'll probably prototype my
idea with OOP on beforehand, then afterwards evolve it into something
a bit more suitable to the profiling tools. I feel that the most
important thing is that the game gets created in the first place and
then one can make gradual improvements.

[^fn:1]: <http://en.wikipedia.org/wiki/Data-driven%5Fprogramming>
