+++
title = "Regarding problems which can be solved by dynamic programming techniques"
author = ["Anton Erholt"]
description = "It's all about 42, (defn foobarbaz []) and cheeseburgers."
date = 2015-01-22
lastmod = 2018-10-03T22:26:27+02:00
tags = ["dynprog", "recursion", "bfs"]
categories = ["blog"]
draft = false
weight = 2009
+++

I have recently watched quite a few videos on dynamic
programming. What is dynamic programming? It's a method for
constructing efficient solutions to some specific problems. In order
to apply the method, there are some criteria needed to be met.

-   The problem should have a brute-force solution/exhaustive search
    solution.
-   The problem should be dividable into sub-problems with optimal solutions
    (explained further down)
-   The problem should have a recursive definition.

<span class="underline">NOTE:</span> I'm not doing this in a formal manner, I simply think out
loud in order to understand the subject better.

Let's use these criteria to make up a problem which we could solve.

**Definition of optimal**

I'm thinking of the second criteria, that the problem should be
dividable into sub-problems with **optimal** solutions. What do I mean
by that? Well, being optimal means that if the original problem is
split into parts, and you find a solution for a part, you know that
the solution is the best possible solution for that part. Simple as
that.

**A recursive thought**

You can use these "smaller" solutions to construct a "bigger"
solution for the original problem. This is obviously a recursive
thought.

-   "I start with a lot of small simple problems, and I use
    them to construct a bigger solution."

It is however a lot more common to state (or think about) recursion
the other way around.

-   "In order to find a solution to this problem, I need to build it
    up from smaller solutions by dividing the problem into
    sub-problems. When the sub-problems become small enough, the
    solutions will be trivial."

The second statement is a lot longer when expressed in text, but
when expressed with math, it becomes a lot more concise as well as
more beautiful.

**Brute force?**

Well, this part I am not so sure about. Something in the back of my
mind tells me that the problems in question should be solvable by
performing some kind of exhaustive search (trying all variants of
solutions which could possibly be generated) in a (what computer
scientists would call a) non-efficient manner (referring to that it
would take A LOT of time if the problem is big - usually exponential
compared to the size of input).

**Do you have a problem?**

I think before I dwell any further into what the dynamic programming
method is all about, I'd like to try and see if I can come up with a
good problem. I've recently come to realize that constructing
problems can be just as efficient as a learning tool as actually
solving them. Conversation with myself follows:

-   A1: Let's pick a subject!
-   A2: Sports!
-   A1: Okay, what now&#x2026; Where's the recursion?
-   A2: I'm thinking a football league&#x2026; NO! Ice-hockey!
-   A1: Okay, keep going.
-   A2: Well, let's say there are 12 teams like in the [Swedish Hockey
    League](http://www.shl.se/statistics/standings/summary/2014/All). They will all play against each other twice per season.

\\(\mathbf{I}\\): This would result in \\(nPr(12,2) = 132\, matches\\).

\\(\mathbf{II}\\): It could probably also be expressed like \\(nCr(12,2)\*2
  = 132\\).

In fact, \\(\mathbf{I}\\) is a special case of permutations when we only have two
matches per season. It makes more sense to use \\(\mathbf{II}\\) if we
for any reason would like to increase the number of matches per
season. Hmm&#x2026; Interesting!

-   A1: Enough with the combinatorics, go on!
-   A2: Okay, let's think of a property of the matches we can use. The
    first one I can think of is the total number of goals made per
    match.

<span class="underline">That's enough. I'll continue this some other time&#x2026;</span>
