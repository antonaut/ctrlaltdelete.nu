+++
title = "A flawed idea of the halting problem"
author = ["Anton Erholt"]
description = "It's all about 42, (defn foobarbaz []) and cheeseburgers."
date = 2014-03-16
lastmod = 2018-10-07T11:23:50+02:00
tags = ["programming", "computer", "science"]
categories = ["blog"]
draft = false
weight = 2011
+++

The halting problem is fairly interesting even though someone (Alan
Turing) some time ago (1936) has proved that it can not be solved by a
general algorithm. It is undecidable. Despite the fact, today when I woke
up I had an idea.

Let us use a FSM to describe the program. Think of it as a FSM with A
LOT OF STATES. Almost as if it is an Infinite State Machine (which
would not be of much use), but rather a very large FSM.

**Program A**

```c
int main()
{
  int a;
  a = function(0, 0);
  return a;
}

int function(a, b)
	int a,b;
{
  a = 1;
  b = 2;

  return a == b ? 0 : function(a, b);
}
```

Consider this Program A. It is written in a very small subset of C
which only handles:

-   integer assignment
-   ternary if statement based only on integer equality comparison
-   function call
-   function return
-   probably something else which I've forgotten

The idea is to convert these statements into a set of transitions
\\(\mathbf{T}\\) between the set of states \\(\mathbf{S}\\).

For example, we take the integer assignment and turn it into
corresponding 2<sup>32</sup> - 1 possible transitions from the current state
(and one more for assigning the same value again).

The function call flattens out to whatever the function is supposed to
do and turns into an assignment.

The equality check performs either a transition to the 'then'
state or a transition to the 'false' state.

Et voilà! With a bit more convincing, we can build ourselves a FSM of
the program. When looking at the State-Transition graph (STG) we
simply need to perform a Breadth-First-Search of the graph in order to
tell if the program can halt or not. Right?!?

**WHAT ABOUT INFINITE RECURSION?** Well, if we perform some kind of lazy
check if we have already entered the function a lot of times with the
same parameters, we should have already visited the states before the
function call. Or I mean we should somehow flatten out the
func&#x2026; **HANG ON! IS YOUR 'SUBSET OF C' UNIVERSALLY COMPUTABLE?** Meh,
universally computable? I havn't really thought about it \`[insert
ellipsis here]\`

**OH MY GOD, AND YOU MUST KNOW THAT YOUR INTEGERS HAVE LIMITED
PRECISION AND PRECISELY WHAT IMPLICATIONS THAT HAVE ON YOUR IDEA!**

Well yes, I kind of do. I mean, the STG will be a lot smaller if you
use uint8's instead. There might actually be something of use here,
since a lot of programs don't use unlimited precision for absolutely
everything. Perhaps there's a lot to gain if we use other types than
simply a 32-bit integer (since that what the ints precision is on my
machine).

**SO WHAT YOU ARE SAYING IS THAT IF YOU LIMIT YOUR PROGRAM SO IT ONLY
USES VERY FEW INSTRUCTIONS IT MEANS WHAT?**&#x2026; \* _catching breath_ \*
**&#x2026;AAAND YOU CAN ONLY** **EXPRESS A <span class="underline">VERY</span> GENERAL IDEA ABOUT HOW THAT
MIGHT BE USEFUL? YOU ARE TOO VAGUE!** **REALLY GOOD IDEAS<sup>TM</sup> SHOULD BE
MORE EXPLICIT, AND YOU KNOW IT!**

I'm not listening to you&#x2026; What if we remove infinite recursion
and/or infinite loops. I mean the stack isn't infinitely large and we
can always keep track of how much we have looped. Let's remove this
annoying feature of the language instead and see what that does!

Sigh&#x2026; Computer science can be tough. But this is what I love about
it. The science part. Even though my idea is filled with flaws it
might inspire other me and other people to look into new
things. Someone reading this not knowing that much C may discover that
you can list your arguments like I have done for 'function'. It might
not be the 'Next Big Thing' but this way I feel that I may somehow
contribute with a bit of knowledge, simply by letting my thoughts flow
freely.

Even though I get turned down a lot by both my own and others
**CRITICISM**, I really love how it may lead me somewhere else and take
me into new orbits of reasoning. The criticism is the fuel of my
science engine. For instance, now I do wonder if the 'small subset of
C' I chose is universally computable (or at least if I could write a
Fibonacci generator in it?). I think that science being tough is not a
bad thing. It is exactly how it should be. The interesting thing here
is how you choose to percieve it. I choose to see it as way of
generating new insights.

**OH YEA? WELL I DON'T!**

_UPDATE (2014-09-28): Realised it won't compile. Function definitions
are supposed to be declared before they're used._
