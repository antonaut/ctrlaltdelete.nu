+++
title = "CtrlAltDelete.nu"
author = ["Anton Erholt"]
description = "It's all about 42, (defn foobarbaz []) and cheeseburgers."
lastmod = 2018-10-02T14:27:29+02:00
draft = false
+++

## Blog {#blog}


### <span class="org-todo done DONE">DONE</span> About {#about}

This started out as a blog for written answers to assignments from a
course in Computer Game Design&nbsp;[^fn:1] at the Royal Institute of
Technology (KTH) in Sweden. Its purpose is to serve as a diary of
sorts. It is a place where I jot down ideas, thoughts and reflections
about my life and computers.

It is solemnly written by me, Anton Erholt (twitter: [@aerholt](http://twitter.com/aerholt), github:
[@antonaut](https://github.com/antonaut/)). I am 28 years old and live in Stockholm, Sweden. Currently I am
working as a Data engineer at Tele2 IoT and finishing my master's
thesis.

This blog is in chronological order, with the oldest post at
bottom. Yes if you want to read it, you have to scroll. This may seem
a bit old-fashioned, but it is just the way I like it. I have started
using proper links as well and omitted the footnote style for the
newer posts.

Dear reader, hopefully it will be to your liking.


### <span class="org-todo done DONE">DONE</span> Portfolio {#portfolio}

These are some of the projects I have been involved in, one way or
another. Most of the projects are related to school, but some have
I worked on during my free time.


#### Aerial {#aerial}

[Aerial](http://aerial.lindstrm.se/about) is a project where you control an airplane by moving a
smartphone around. The goal of the project was to make something which
somebody paralyzed from the neck and downwards could interact
with. The smartphone is supposed to be attached to your head and you
control the plane by tilting your head back and forth or side to side.

I worked mostly on project management and fixing the issues we had, as
well as some of the mathematical modeling needed.

This project was part of the KTH course AGI-15.

{{< figure src="./img/aerial.png" caption="Figure 1: Gameplay of Aerial." >}}


#### Padawan101 {#padawan101}

[Padawan101](http://www.nada.kth.se/~aerholt/padawan101/) is a light saber simulator made for Oculus DK2, a Microsoft
Kinect and a smart phone. The project was built with Unity. We
combined a lot of technologies to get body and light saber tracking
working correctly. Worked on a lot of stuff during the project. What I
worked the most on was actually networking, to create even more views
so that people could join and look at the jedi fighting. I was also
responsible for the project web page.

The code is available [here](https://github.com/stefanseibert/padawan101). This project was also part of the KTH
course AGI-15.


#### Put put glorify! {#put-put-glorify}

[Bm-putputglorify](https://github.com/mold/bm-putputglorify) is a multiplayer game to be played on a big screen
using smart phones as controllers. You spawn as a shell, the objective
is to crash into other shells. Me and some of my friends built it
as part of a 'pizza & javascript' - hackathon.

{{< figure src="https://raw.githubusercontent.com/mold/bm-putputglorify/master/put-put.png" caption="Figure 2: Gameplay of bm-putputglorify" >}}


#### Rendering with ray casting and rasterization {#rendering-with-ray-casting-and-rasterization}

I built two renderers using ray tracing and rasterization as part of a
introduction course to computer graphics.

<iframe width="420" height="315"
src="https://www.youtube.com/embed/_Y4V6jAptpA" frameborder="0"
allowfullscreen></iframe>

<iframe width="420" height="315"
src="https://www.youtube.com/embed/8R8CTFd2PSc" frameborder="0"
allowfullscreen></iframe>

<!--


#### Commercial projects {#commercial-projects}

noaxlab


#### Go projects -> posts? {#go-projects-posts}

fractalz
game-of-life


#### Old projects {#old-projects}

-->


### <span class="org-todo done DONE">DONE</span> Moves where a Queen can't take (8x8 chess board) {#moves-where-a-queen-can-t-take--8x8-chess-board}

```clojure
;;; Finds okay positions where a single queen can't take the piece (8x8
;;; board) It sure took a while to write, but now it's sort of understandable
;;; and done in a functional style.
;; TOO SLOW! (1h 10min)

;; Learned about generation with lazy-seq and map-indexed together with
;; list comprehension (for).


;; Simulates input.
(def one-Q-board
  [["." "." "." "." "." "." "." "."]
   ["." "." "." "." "." "." "." "."]
   ["." "." "." "." "." "Q" "." "."]
   ["." "." "." "." "." "." "." "."]
   ["." "." "." "." "." "." "." "."]
   ["." "." "." "." "." "." "." "."]
   ["." "." "." "." "." "." "." "."]
   ["." "." "." "." "." "." "." "."]])

;; Will be used as a base board to build on.
(def all-p-board
  [["P" "P" "P" "P" "P" "P" "P" "P"]
   ["P" "P" "P" "P" "P" "P" "P" "P"]
   ["P" "P" "P" "P" "P" "P" "P" "P"]
   ["P" "P" "P" "P" "P" "P" "P" "P"]
   ["P" "P" "P" "P" "P" "P" "P" "P"]
   ["P" "P" "P" "P" "P" "P" "P" "P"]
   ["P" "P" "P" "P" "P" "P" "P" "P"]
   ["P" "P" "P" "P" "P" "P" "P" "P"]])

;; Direction functions for taking a step in the next direction.

(def dirs {:topleft   (fn [[x y]] [(dec x) (dec y)])
	   :top       (fn [[x y]] [x (dec y)])
	   :topright  (fn [[x y]] [(inc x) (dec y)])
	   :left      (fn [[x y]] [(dec x) y])
	   :right     (fn [[x y]] [(inc x) y])
	   :downleft  (fn [[x y]] [(dec x) (inc y)])
	   :down      (fn [[x y]] [x (inc y)])
	   :downright (fn [[x y]] [(inc x) (inc y)])})

(defn outside-board?
  "Stop the lazyness with this when you fall off the board."
  [[x y]]
  (or (< x 0)
      (> x 7)
      (< y 0)
      (> y 7)))

(defn advance
  "Take a step forward."
  [dir pos]
  ((dir dirs) pos))


(defn advanced-positions
  "Lazily take several steps forward."
  [dir pos]
  (lazy-seq
   (cons pos
	 (advanced-positions dir (advance dir pos)))))

(defn take-positions
  "Find all the positions where a queen can take a piece."
  [[x y]]
  (for [dir (keys dirs)
	pos (take-while
	     #(not (outside-board? %))
	     (advanced-positions dir [x y]))]
    pos))

(defn find-string-on-board
  [board string]
  (for [row   (map-indexed vector board)
	col   (map-indexed vector (second row))
	:when (= string (second col))]
    [(first row) (first col)]))

(defn find-queen [board]
  (find-string-on-board board "Q"))


;; Functions for updating a board / 2d-seq

(defn update-row [row x val]
  (map-indexed (fn [xidx col]
		 (if (= x xidx) val col)) row))

(defn update-board [board [x y] val]
  (map-indexed (fn [yidx row]
		 (if (= y yidx)
		   (update-row row x val)
		   row)) board))


;; (update-board one-Q-board [2 3] "X")

(defn ok-positions
  [board]
  (let [qpos       (first (find-queen board))
	tpositions (take-positions qpos)]
    (reverse
     (map reverse
	  (loop [tpositions tpositions
		 board      all-p-board]
	    (if (empty? tpositions)
	      (update-board board qpos "Q")
	      (recur (rest tpositions)
		     (update-board board
				   (first tpositions)
				   "."))))))))

(ok-positions one-Q-board)
```

Results in:

```clojure
(("P" "P" "P" "." "P" "." "P" ".")
 ("P" "P" "P" "P" "." "." "." "P")
 ("." "." "." "." "." "Q" "." ".")
 ("P" "P" "P" "P" "." "." "." "P")
 ("P" "P" "P" "." "P" "." "P"pp ".")
 ("P" "P" "." "P" "P" "." "P" "P")
 ("P" "." "P" "P" "P" "." "P" "P")
 ("." "P" "P" "P" "P" "." "P" "P"))
```


### <span class="org-todo done DONE">DONE</span> Spiral matrix in Clojure {#spiral-matrix-in-clojure}

I had a bit of a struggle with this problem at first. I decided to
generate the path with a few small functions and then sort and print.
Enjoy this beatiful piece!

```clojure
;;; Prints numbers in a spiral matrix going ⟳.
(ns first.spiral)

(defn output-number [n]
  (if (< n 10)
    (str "0" n)
    (str n)))

(defn right [[x y]]
  [(inc x) y])

(defn left [[x y]]
  [(dec x) y])

(defn up [[x y]]
  [x (dec y)])

(defn down [[x y]]
  [x (inc y)])

(def dirs {:right right
	   :up    up
	   :down  down
	   :left  left})

(def rotate {:right :down
	     :down  :left
	     :left  :up
	     :up    :right})

(defn advance-dir
  [pos
   current-dir
   visited]
  (if (contains? visited ((current-dir dirs) pos))
    (current-dir rotate)
    current-dir))

(defn spiral [W H]
  (let [stop (* W H)]
    (loop [pos            [1 1]
	   next-dir       :right
	   current-number 1
	   visited        #{[(inc W) 1]
			    [W (inc H)]
			    [0 H]}
	   matr           #{}]
      (if (= stop current-number)
	(conj matr [pos current-number])
	(recur ((next-dir dirs) pos)
	       (advance-dir ((next-dir dirs) pos)
			    next-dir
			    visited)
	       (inc current-number)
	       (conj visited pos)
	       (conj matr [pos current-number]))))))

(defn cmp-pos [[[x1 y1] _]
	       [[x2 y2] _]]
  (cond (< y1 y2) -1
	(> y1 y2) 1
	:else     (cond (< x1 x2) -1
			(> x1 x2) 1
			:else     0)))

(defn print-row
  [row]
  (loop [row row]
    (if (= 1 (count row))
      (println (output-number (first row)))
      (do
	(print (output-number (first row)) " ")
	(recur (rest row))))))

(defn main
  "Width and height of matrix to be printed.
  Looks really bad if either of them > 9."
  [W H]
  (let [xs   (sort cmp-pos (spiral W H))
	mat  (partition W xs)
	rows (map #(map second %) mat)]
    (map print-row rows)))

;;(main 5 3)
(main 9 9)
```

Results in:

```nil
01  02  03  04  05  06  07  08  09
32  33  34  35  36  37  38  39  10
31  56  57  58  59  60  61  40  11
30  55  72  73  74  75  62  41  12
29  54  71  80  81  76  63  42  13
28  53  70  79  78  77  64  43  14
27  52  69  68  67  66  65  44  15
26  51  50  49  48  47  46  45  16
25  24  23  22  21  20  19  18  17
```


### <span class="org-todo done DONE">DONE</span> Improving my Clojure skills, Apr 15 {#improving-my-clojure-skills-apr-15}

Over at [CodinGame](http://codingame.com) they have something called clashes of code where you
compete with other developers to solve a certain problem in a given
amount of time. I know there are quite a few sites where you can
sharpen your coding skills, but somehow I got stuck at this one. Maybe
it provides me with just enough challenge for me with my current skills.

I've tried to use the clashes to learn more about Clojure. So far I've
come to realize that there are a **TON** of functions I don't know of. I
feel like I learn something new on (almost) every problem. Even though
I can't finish most of them on time, I still try to complete them. I'll put
some of my solutions to a few of the more interesting solutions here.


### <span class="org-todo done DONE">DONE</span> End of my game dev story? Apr 09 {#end-of-my-game-dev-story-apr-09}

We worked on this for a while, but it just ran out in the sand when I
went to France for exchange studies. Nevertheless, it was a great
learning experience for me. I think I mostly learned a bunch of
javascript. We built the client side of a somewhat advanced game. The
coolest part was a small window system built mostly by my friends in
WebGL.

Hopefully we can publish the code with some small examples.


### <span class="org-todo done DONE">DONE</span> Sphere packing on a sphere? Circle packing papers, Mar 08 {#sphere-packing-on-a-sphere-circle-packing-papers-mar-08}

The idea of a tool giving you access to finding relevant papers in a
certain field of research is pretty interesting. Let's imagine all the
papers as a [planar graph](https://en.wikipedia.org/wiki/Planar%5Fgraph) depicted on a sphere. The edges
(arrows/links) of the graphs are keywords or subjects. A certain
'theme' (a set of papers) would then be a 'continent' on the sphere.

{{< figure src="http://jeffe.cs.illinois.edu/teaching/comptop/2009/Fig/circlepack.png" caption="Figure 3: Found on a course web page for computational topology, originally from <http://www.math.utk.edu/~kens/BookInfo.html>. I should totally read that book." >}}

Imagine this thing spinning and showing you the papers you've found,
then expanding those with related papers - pushing the old papers
aside. Found an example done in 2D with d3.js for dependencies
<https://bl.ocks.org/mbostock/7607535>. Would it be interesting to see
this in 3D? Maybe in an AR setting. The zooming effect would probably
come naturally if the spheres grow to big. I sort of like the concept
of several virtual globes for organizing stuff. Not so sure of how I
would feel about them in reality though.


### <span class="org-todo done DONE">DONE</span> Tools for completing a master's thesis, Feb 25 {#tools-for-completing-a-master-s-thesis-feb-25}

A master's thesis is an intense 18 weeks of total focus and devotion
to a research question. So far, I've only been able to invest about
one weeks worth of studies during a four week period. My personal life
is taking its toll on my work at the moment. I don't feel like it's
that frustrating, but I'd really like to get more stuff done.

In order to increase my productivity I've come up with a plan.

1.  Devote more time to planning in the morning
2.  Use a pomodoro (from _Italian:_ means tomato) timer when working to
    minimize distractions

To keep track of my pomodoros, I use a simple app called [Productivity
Challenge Timer](https://play.google.com/store/apps/details?id=com.wlxd.pomochallenge).

These two simple things should allow me to plan the process more
smoothly.

Step one in the process of the thesis is to write a specification
document. The document should provide detailed information of the
following things:

-   The goals of the thesis, what I hope to achieve
    -   Why it is important
    -   To whom is it important
-   My own background and why I'm eligible to write it
-   The research question this thesis will try to explore and answer
-   A detailed timeplan for when everything should be done

Just like this blog, I'll largely write my thesis in [emacs](https://www.gnu.org/software/emacs/) with
[org-mode](http://orgmode.org/). I've started to organize some of the references I've found
in [Mendeley Desktop](https://www.mendeley.com/) and in an org-mode file. I also found a nice
latex-package for doing gantt-charts called _pgfgantt_.


### <span class="org-todo done DONE">DONE</span> Concerning idea debt and investment, Feb 25 {#concerning-idea-debt-and-investment-feb-25}

I read [this](http://jessicaabel.com/2016/01/27/idea-debt/) article about idea debt. Not sure which affected me the
most. What she stated about idea investment, or the fact that she too
doesn't know how to use the french subjunctive correctly. Nonetheless,
I'm truly inspired to put my old ideas to the side and work with what
I've got at the moment. I want to start documenting stuff
properly. This is my blog. My life.


### <span class="org-todo done DONE">DONE</span> 2016 {#2016}

<h1>Happy new year 2016!</h1>

2015 was not a very bloggy year. Who knows? Maybe 2016 will bear more
blog fruit.


### <span class="org-todo done DONE">DONE</span> Regarding problems which can be solved by dynamic programming techniques, Jan 22 {#regarding-problems-which-can-be-solved-by-dynamic-programming-techniques-jan-22}

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


### <span class="org-todo done DONE">DONE</span> 2015 {#2015}

<h1>Happy new year 2015!</h1>


### <span class="org-todo done DONE">DONE</span> A flawed idea of the halting problem, Mar 16 {#a-flawed-idea-of-the-halting-problem-mar-16}

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


### <span class="org-todo done DONE">DONE</span> HACKATHON! {#hackathon}

A lot has happened since we started developing the game.
Our initial goal was to do these tasks during the hackathon:

-   WebSockets and API
-   TouchEvents
-   GameLoop and GameStates

Afterwards, we now have a somewhat working APICallSystem with Secure
WebSockets. Unfortunately havn't our exportation framework for Android
and iOS supported the wss protocol yet, but it'll hopefully be up and
running soon. During the hackathon I had a great time. We put up a
stream at Twitch.tv to stream everything while making tacos and
listening to jazz from a jar (using something similar to
[this](http://www.origaudio.com/shop/index.php?dispatch=products.view&product%5Fid=29778)).


### <span class="org-todo done DONE">DONE</span> To use DDD or not (| !) to use DDD {#to-use-ddd-or-not-----to-use-ddd}

My initial thoughts on Data Driven Development was that it to some
extent might have been unneccessary (mainly due to the fact that I
didn't understand it fully). I thought that when making a smaller
game, you probably wouldn't benefit from using a DDD code style at
all. However, after reading
[this](http://www.richardlord.net/blog/why-use-an-entity-framework)
post on why one should use an entity-component-system composition of a
game/gameengine, I have changed my mind.

Our initial approach to writing the game was with OOP (Object-Oriented
Principles) which gave us a massive Character class, which as of right
now only seems unneccessary. At the moment, the entity-relation
software model seems like a great way for us to develop, since it will
give us the benefit of partitioning almost everything into tiny
beautiful pieces which almost magically works together.

As of right now we have a lot of coding to do in order to get our game
playable (which of course is our current goal). Hopefully we'll be
able to show off something soon.


### <span class="org-todo done DONE">DONE</span> The beginning of Anton's Game Dev Story, Jan 7 {#the-beginning-of-anton-s-game-dev-story-jan-7}

About a year ago, me and two classmates ([johntu](http://www.nada.kth.se/~johntu) & [dmol](http://www.nada.kth.se/~dmol))
participated in a programming competition called [Escape from
/dev/null](http://swdc.se/devnull/). The competition is based around having to program something
that assembles a client to a web-based API revolving different
things. The API gets further uncovered as you get further into the
competition. It's a game in a game. Very meta and
Oh-So-Lovely&trade; if you are into these kinds of things.

Back to the contest! As young and somewhat novice web programmers, little did we
know what to expect as we entered the contest. Not entirely without
effort, we smacked our heads together, right down in the free pizza, chunked
it down hard with some delicious beer and ended up at somewhere in the
order of second to last.

{{< figure src="img/lost_in_space.png" caption="Figure 4: Our client for the space themed /dev/null" >}}

Needless to say, time passed and we participated in a few more
similar contests. The game we are building is in fact a client for
this particular game. Peter Svensson ([@psvensson](https://twitter.com/psvensson)) has made a fabulous
job of writing the game mechanics and providing it as a web service
for us to write a client from. We use web technologies in the core and
hope to deliver an exclusive fullscreen, WebGL experience to all of
our players. Are you getting excited? No? Then, maybe a bit
interested? Aha&#x2026; You continue reading because of pity and
self-recognition? Ah well, just wait until I tell you what the game is
about&#x2026;


### <span class="org-todo done DONE">DONE</span> 2014 {#2014}

<h1>Happy new year 2014!</h1>


### <span class="org-todo done DONE">DONE</span> Presentation of game demo, Dec 13 {#presentation-of-game-demo-dec-13}

We made a presentation of a game demo we developed during the
course&nbsp;[^fn:1]. Here follows the script and some comments of the final
presentation (NOTE: This might be a tedious read since&#x2026; Well. You
have been warned).


#### Presentation Script {#presentation-script}

**Welcome**
Hi everyone and welcome to this presentation. We’re the group “Fat
ninja” a.k.a. Kitten with Mittens. We are here to present a charming
puzzle game with&#x2026;

**Description**

&#x2026;an adorable Kitten. But is it so cute after all?
Kitten with Mittens is a game where you lead Cat through a series of
levels by using his moving abilities. The levels are puzzles where you
need to think and plan your route beforehand if you want to complete
the level successfully.

As you probably can tell by our awesome posters, the game is currently
available on Android and iOS platforms, but today we will be demoing
on the Windows platform (Microsoft Surface tablet) and a smart
phone. Without spoiling anything more about the game, we are going to
present a story to you.

**Story**

In a warm and cozy house, far up in icy
and cold Sweden, lives Cat the clumsy kitten.

One early winter morning, Cat is lying in a warm bed under a
blanket. Sleeping and dreaming of catnip with not a single thought of
ever going up.

-   “Aaaah, this will be a lovely day of doing nothing.”

All of a sudden, Cat feels the need to scratch behind the ear. As Cat
moves the paw back-and forth on the head, Cat tries to unfold a claw
for that extra bit of OOMPH in the scratch. Cat feels that all of the
fingers are completely restrained and Cat can’t unfold them at
all. What is this?! Cat panics and tumbles over, whelps the bed over
and lands in an otherwise yummilicious bowl of morning milk.

Cat slowly lift the eyelids and discovers a pair of mittens on top of
the paws. As if that wasn’t enough, already having the morning ruined
by a non-scratched itch and immobilization of paws, Cat also discovers
a long scarf tied way too hard around the neck. What used to be a warm
kitty, soft kitty, little ball of fur - is now unhappy kitty, awake
and wet kitty, no purr, purr, purr!

Will you help Cat rid the mittens?

**Game play**
_Showed a short video of actual gameplay_

So what does the game play look like?
All-in-all you can move the cat around by swiping left or right, jump by tapping and swing by pressing and holding when near a swinging platform. These abilities can be combined in order to create mind blowing and dazzling puzzles.

**Art**
Original art hand-made by Kajsa. The cat is modeled and animated by Veronica in Blender.

**Project plan**
On time.

**Trailer, Thanks & Questions**


#### Comments About the Game {#comments-about-the-game}

**Positive Feedback**

The first thing that struck people was how cute
and pleasing to the eyes the game was, even though it was not the most
technically advanced game presented during the seminar. The prototype
was completely made up from primitives that was at our disposal in
Unity, a self made model (the cat) from Blender and watercolour made
paintings that had been scanned in for the textures in the background
as well as the textures on walls and the ground. They really liked the
art direction as well and thought that the whole presentation
(textures, level design and models) looked consistent as a whole.

The consensus about the game mechanics and level progression itself
from the people who tried it was that it was addicting, since the
reward of clearing a stage made you want to play the game for
longer. They liked the rope mechanic too even though it is hard to
master.

Another thing that people really liked was the original oral
presentation that we made and the posters that we had put up on the
walls in the seminar room. It definitely raised interest in our
product, so from a managerial point of view it was a successful game
announcement. Finally we got some nice comments from one of the
persons from Swedish Game Awards who thought that we definitely should
participate in their event. One of his comments were that he did not
get as frustrated as he could have been when he failed to complete one
of our hard levels. He said it was because you can never count on a
kitten to behave as you want it to. We think it is a good comment,
since it is hard to say the same thing about another game.

**Constructive Criticism**

The prototype levels got very hard a little bit too fast. This is due
to us trying to cram in the rope functionality in an early stage,
which is a hard technique to master. Especially if the player is not
completely familiar with the rest of the control scheme.

People thought that the game was hard and frustrating at times due to
too few descriptions of the controls. However, that was something that
we were already aware of, but due to time constraints we did not have
enough time to implement as many “tutorial elements” in the prototype
version of the game as we would have liked.


### <span class="org-todo done DONE">DONE</span> Mobile games, Dec 6 {#mobile-games-dec-6}

When playing a game on a mobile device, you have very little time to
make the player interested. This is a very similar problem to the one
where websites have only a few seconds to present the right content to
the visitor or else the visitor will browse away from the site. These
problems are hard to solve (their respective decision problems are
probably way more than NP-complete [Comp. Sci. humor] ).

I don't believe for a second that there is an ultimate answer to how
to keep players stuck in the game. One idea is to allow them to leave
and then force them to come back. If you develop for mobile, it can
probably be a good thing. To enforce the player out of the game
wanting to play more. The player will then return later, eager to face
new challenges for a while. Games which operates in this manner are
Quizkampen, Wordfeud and Candy Crush Saga. I'd say that it is probably
a good concept to incorporate in a mobile game.

If you focus on the specific part of just getting the player hooked, I
would say that a good idea is probably to try to give the player a
feeling of greatness. In one of my favorite mobile games,
Stardash&nbsp;[^fn:2] by OrangePixel, you probably manage to clear two
levels in under 30 seconds. You feel that it was pretty easy, but not
too easy so you keep playing. Making the first few levels good are
essential if you want players to stay in game.

In order to really strengthen the inferred feeling of greatness, it
would be good to perhaps infer an achievement system. Perhaps an
achievement for the first death is a good idea so it actually turns
into something positive?


### <span class="org-todo done DONE">DONE</span> The game producer - Game business models, Dec 3 {#the-game-producer-game-business-models-dec-3}

Now I'll briefly discuss some business models. Please note that I'm
probably a bit biased.


#### Pay-to-play (P2P) {#pay-to-play--p2p}

The "standard" model. You pay for a game once and then it is
yours. For all eternity&#x2026; Oh, you want an example? Hmm&#x2026; Let's
see. Oh! Project Zomboid&nbsp;[^fn:3] mentioned in an earlier post is a good example.

-   **Pros**
    -   You don't have to keep track of all the players

-   **Cons**
    -   The eventual income will spike and then drastically fall.
    -   Typically means there will be sequels if the devs are successful.


#### Subscription {#subscription}

Well, the most obvious example I can come up with is World of
Warcraft&nbsp;[^fn:4], where you pay a fee each month to continue playing.

-   **Pros**
    -   Continuous cashflow means an ability to have continuous development
    -   Makes scalability easier

-   **Cons**
    -   Player database needed to keep track of payments etc.

-   **Comments**
    -   I think this model is perfectly suited for the MMO type of games
        which take a lot of time and effort to maintain and develop. You
        already do have a player database with accounts, so this should
        not be that much of a problem.


#### Free-to-play (F2P) {#free-to-play--f2p}

A great example is the giant in the MOBA-genre&nbsp;[^fn:5], DotA 2. In
this game you can buy skins and new visual styles for the characters
in the game.

-   **Pros**
    -   Players get to play, haters get to hate and lovers get to
        love. You don't have to feel bad for trying to trick money out of
        someone&#x2026;

-   **Cons**
    -   &#x2026; or yes you do. But how!? This business model requires
        something else to generate income, such as ads or in-game
        purchases.

A thing to keep in mind is that it is not completely impossible to
switch business model, even though it might hurt some of your
playerbase.  One example of where a switching between business models
has been successful is Heroes of Newerth&nbsp;[^fn:6]. The game is F2P
since the 20th of July 2012&nbsp;[^fn:7] (is used to be P2P).


### <span class="org-todo done DONE">DONE</span> Dataspelsbranschen: the trade organization, Nov 29 {#dataspelsbranschen-the-trade-organization-nov-29}

Today we had a guest lecture from Per Strömbäck of Dataspelsbranchen,
the trade organization for Swedish game development. He wanted us to
discuss two specific questions which all in all are quite important
when it comes.

**How can the game developer interact with the audience?**

There are multiple ways of interacting with your target audience as a
game dev. The easiest to come up with are probably through public
forums, a wiki, social media and/or some other kind of web based
communcation. However, I think the most important interaction comes
from other settings though.

Perhaps you perform some usability tests on your game, meaning you
pick some testers and perform interviews of them. Perhaps you play the
game together with them and communicate through the game you have
made? Perhaps your dev studio attends fairs or arranges workshops of
different kinds (hackathons?), where you need to physically interact
with players etc.

Usually when it comes to games, there is always a lot of feelings
involved. Therefore I think it is really important to be honest with
what goals you have with the game and what purpose it has. Otherwise
you might end up having a really let-down playerbase since you
introduced PvP in starting towns and did not discuss it with your
player. Sure it is your game since you made it, but don't you have
some kind of responsibility towards your playerbase?

**Why does not in-game ads work?**

I think In-game ads don't work because the amount of time a player spends in a
game is minimal. At least when it comes to mobile games. I do think
in-app purchases are a lot better. At least if they are done in a good way.

British technology magazine Wired has released an article on in-app
purchases in Plants-vs-Zombies 2&nbsp;[^fn:8]. The article explains
extremly well how I think in-app purchases shouldn't work. I do not
think that in-app purchases should give the player any advantages
compared to when not having done them. I usually end up quitting these
games immediately since they ultimately favor the player with the
largest wallet. Which is insane.


### <span class="org-todo done DONE">DONE</span> Short on game physics and animation, Nov 26 {#short-on-game-physics-and-animation-nov-26}

Some games are really nothing but great combination of physics,
animation and astonishing scenery. One example I would like to discuss
is a game called flower&nbsp;[^fn:9] (note, no capital f). In flower one
controls a single petal flying in the wind over an enormous field. As
you fly across the field, if you hit other flowers, some of its petals
will join you in your flight. In order to complete a level you need to
collect a certain number of petals and reach the end (although I don't
think it is specified how many you need).

The game per se has not the intentions of being difficult or puzzly
but rather to give the player a joyful experience. The game does not
contain any dialogs or any specific interfaces. You simply control the
wind (and thereby the petals) with your chosen controller.

In flower, I think the developers (Thatgamecompany) have made a great
effort of using flow dynamics and beautiful scenery in order to make
the game into what it is. This is a great use of flow physics and I'm
really happy to see that games like these makes it onto consoles and
do not just stay in the swamp of indie PC games.

<iframe width="560" height="315" src="//www.youtube.com/embed/NAftDQN4SNg" frameborder="0" allowfullscreen></iframe>

Lastly, I'd like to say that there are times when you find out things
which give you an advantage in the game due to physics being used in a
"clever" way (some might argue glitchy or cheaty). I have currently
got two good examples of this. The first one is very well known but
still very funny. In The Elder Scrolls V: Skyrim, you can climb almost
any mountain when riding a horse (as opposed to when you are not riding).
I remember someone making a funny picture out of it:

<blockquote class="imgur-embed-pub" lang="en" data-id="5xgVBRU"><a href="//imgur.com/5xgVBRU">View post on imgur.com</a></blockquote><script async src="//s.imgur.com/min/embed.js" charset="utf-8"></script>

My second example is from a 2D platformer called Cave
Story&nbsp;[^fn:10].

**NOTE: Might contain some spoilers**

Cave Story is a free action adventure independently developed by Daisuke Amaya
(a.k.a. Pixel -> Pixel Studios). Originally released in December 2004
for PC, an upgraded version (Cave Story+) was released on Steam and it
was later ported to the Nintendo 3DS by Nicalis. You play a robot
called Quote (or Mr. Traveler) which you guide through the
world.

Depending on the path you take, you may come across a machine
gun. Leveling up said gun will make it more powerful and thus make
more damage and a greater recoil. Without prior knowledge, the
undersigned happened to find out by mistake that firing the machine
gun downwards allowed for continuous hovering/floating. This made
certain areas of the game a breeze to play through. Delightful,
amusing and totally unexpected.


### <span class="org-todo done DONE">DONE</span> Using a game engine or building your own? Nov 22 {#using-a-game-engine-or-building-your-own-nov-22}

This post will be filled with spontaneous questions and answers
related to whether one should use an existing game engine or create
one from scratch.

**What budget are you on?** If I'm really low on a budget I'll probably
use an engine if it can save me some time, since we all know; Time is
money. The time saving aspect is also present when it comes to
maintaining the game and/or improving it. It is important that the
engine gives you a good workflow so you can easily work with it. A
good idea might be to implement some simple games/demos/ideas in the
few engines you are interested of and see which ever suits you the
best.

**Are you lacking a feature in the engine?** You can probably extend the
engine if it doesn't have all the features you require for a game
project. Also remember that there are several other engines to choose
from which might give you more features for free. This is obviously
something you should consider when you make the actual choice.

**What platforms should we target?**
Different engines allow us to target different platforms. This is
obviously an important choice to make as well. It is kind of connected
to the monetization issue. Do more platforms equal more profit?

**So, Anton since you're into web apps. What about them?**

Well, I personally really like the web, but I am not sure it is well
suited for everyone. John Carmack mentions in a keynote from QuakeCon
that he does not really believe in using javascript for high
performant graphics in the browser&nbsp;[^fn:11]. I am thinking that means
it would be hard to get a triple-A title running as smooth in the
browser as it does natively. However a lot of "smaller" games run
perfectly in the browser with great graphics. I remember specifically
when Supergiant Games released Bastion&nbsp;[^fn:12] on the Chrome Web
Store. Beautiful.


### <span class="org-todo done DONE">DONE</span> The Sims - a short presentation, Nov 20 {#the-sims-a-short-presentation-nov-20}

We made a short presentation on the Sims in class. Here are the slides and the
script.


#### HISTORY - Slide {#history-slide}

-   The Sims is the first game in the series. Developed by Maxis and
    published by Electronic Arts, it was released for Microsoft Windows
    in February 2000.
-   The Sims 2 are released on September 14, 2004. The sequel, developed
    by Maxis, takes place in a full 3D environment with every sim having
    an aspiration system.
-   The Sims 3 are released on June 2, 2009. Game has open, seamless
    neighbourhood, with improved tools for sim creation, enhanced build
    and buy mode functions, and the introduction of wishes and goals.
-   The Sims 4 will be available in 2014. It allows creating sims with
    intelligence and emotion. It provides new intuitive creative tools
    to sculpt sims and build unique homes. Game gives control over the
    mind, body, and heart of sims to bring stories to life.
-   The Sims Online, The Sims Stories, MySims, The Sims Carnival, The
    Sims Medieval, The Sims Social …


#### HISTORY - Script/notes {#history-script-notes}

-   Sims 1 - Seven expansion packs and two bonafide deluxe editions with
    exclusive content have been released for this game. By March 22,
    2002, The Sims had sold more than 2 million copies worldwide, making
    it the best-selling P.C. game in history

-   The Sims 2 is set some 25 years after the original game. For
    instance, the Goth family has aged significantly with Bella Goth
    mysteriously vanishing in the 25-year period. Because the entire
    game has progressed from 2D sprites to 3D models, all content in The
    Sims 2 had to be created from the ground up. Electronic Arts has
    released eight expansion packs and nine stuff packs for The
    Sims 2. Over 400 exclusive items have also been released for this
    game via The Sims 2 Store

-   The Sims 3 sold 1.4 million copies in the first week, making it the
    largest release in PC gaming history at the time. The game has sold
    over 10 million copies worldwide since its release. Eleven expansion
    packs and nine stuff packs have been released for the third
    generation of the series. Notably, Maxis was no longer involved in
    the production

-   The Sims 4 is built upon an entirely new technology. The content
    from The Sims 3 will not be transferable to The Sims 4. The Sims 4
    will be available for PC.


#### EFFECTS OF THE GAME - Slide {#effects-of-the-game-slide}

-   The Sims is one of first full-commercial strategic life-simulation
    video games that still is most successful artificial life game
-   Inspired other games
-   Has real life applications


#### EFFECTS OF THE GAME - Script/notes {#effects-of-the-game-script-notes}

Artificial life games and life simulations find their origins in
artificial life research, including Conway's Game of Life from 1970.
But one of the first commercially viable artificial life games was
Little Computer People in 1985, a Commodore 64 game that allowed
players to type requests to characters living in a virtual house. The
game is cited as a little-known forerunner of virtual-life simulator
games to follow. One of the earliest dating sims, Tenshitachi no gogo,
was released for the 16-bit NEC PC-9801 computer that same year,
though dating sim elements can be found in Sega's earlier Girl's
Garden in 1984. In 1986, the early biological simulation game Bird
Week was released. In the mid-1990s, as artificial intelligence
programming improved, true AI virtual pets such as Petz and Tamagotchi
began to appear. Around the same time, Creatures became "the first
full-blown commercial entertainment application of Artificial Life and
genetic algorithms".


#### BEHAVIOUR OF THE PLAYER - Slide {#behaviour-of-the-player-slide}

-   Cheats - Infinite money - Maximum skills
-   Different objectives
    -   Dream house
    -   Killing
    -   Woo Hoo
-   Aspects of life (University, Seasons, Pets…)


#### BEHAVIOUR OF THE PLAYER - Script/notes {#behaviour-of-the-player-script-notes}

What’s the most memorable thing about the Sims? We think it depends
greatly on how the player has behaved when playing. What do we
remember the most?  The cheats and the ability to play God. There are
a lot of different cheats, and most of you remember ‘motherlode’ or
‘rosebud’ to get more money;!;!;!; We all have different objectives
when playing the Sims such as acting out your dream of becoming an
architect and designing your dream house, or perhaps playing God and
killing your Sims off in different ways. There are several ways of
doing this, like trapping your Sims in a room with fire, removing the
ladder when they’re in the pool so they drown or simply by letting
them age. Another way of behaving in game, could of course be to do
“the right thing” and let their Sims create a family and live their
life as intended. Also are several different aspects of life which the
player can focus on, for instance “University, Seasons, Pets… etc.”


### <span class="org-todo done DONE">DONE</span> Future of procedural generation of content, Nov 19 {#future-of-procedural-generation-of-content-nov-19}

I really think that map generation or procedural content generation is
something which is becoming more and more common in games. Instead of
playing linear, story based games we get to experience
multidimensional games where we create our own unique worlds (or have
our own worlds created for us). However, generating good maps is a
really difficult problem which really hasn't been available until more
recently so I think that there are many improvements to be made here
in the future.

I think we will see even more of this in the future. Perhaps a lot
more free-roaming, sandbox games like Minecraft, Terraria or perhaps
Project Zomboid&nbsp;[^fn:3] (video included since it's not that heard of and
'only' in beta). I think the procedural generation of content adds
greatly to the sandbox survival genre of games. These games often add
a bit of a RPG element to them, making it extra sad when your
heroine/hero dies.

<iframe width="560" height="315" src="//www.youtube.com/embed/qoHm2vxRBKc" frameborder="0" allowfullscreen></iframe>

Taking it one step further, I'd love to see what happens when not only
the maps are generated, but also the creatures within them. I imagine
something like Spore&nbsp;[^fn:13] for monsters/creatures in the
world. Perhaps someday one will be able to generate quests and dialogs
aswell. It all comes down to breaking it into small enough parts I
guess.

Hopefully we will se a lot more AR-/VR integration in games as
well. Games played on devices such as Oculus Rift, Google Glass and so
forth, are games I'm already greatly looking forward to, since
procedural creation might be very applicable to such scenarios. For
instance generating content based on whatever you are currently
looking at.


### <span class="org-todo done DONE">DONE</span> Data-driven approach, Nov 15 {#data-driven-approach-nov-15}

A data-driven approach is shifted from Object Oriented approaches.
The data-driven approach seems like a really good fit if you are
geared towards making a state-of-the art game engine for making really
high performing games. Bitsquid is certainly outstanding in that sense

It's always a bit tricky when you're learning a new programming
paradigm. Usually you learn a new programming language together with
the paradigm which can help you understand difficult patterns and
ideas more easily. However when shifting to the data driven paradigm,
you haven't really got the advantage of changing language as well.

According to wikipedia&nbsp;[^fn:14], there are a few languages which have
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


### <span class="org-todo done DONE">DONE</span> Software development model, Nov 12 {#software-development-model-nov-12}

I think the model used to develop a game is really depending on what
type of game you make. I'd like to address the fact that not every
type of game needs to be high-end performing. As a guideline though,
whatever type of development model which allows you to be productive
is useful.

Do you have a ton of ideas which needs to be tested before you can
decide upon stuff? Like /u/RukiTanuki suggested on Reddit to the girl
making a science-based dragon MMO&nbsp;[^fn:15]; There are several ideas to
take into account.

The one i liked the most of his proposals is where he suggests that
you make some prototypes/mini-games before you make an actual
game. This allows you to evaluate concepts early and decide how they
feel/what they add to the game in general.

I think another important thing is the interaction between the asset
management and the development team is crucial. Using an internal wiki
as a design document might be suitable for a small game making
team. This might extend into a public wiki aswell which can help
players in the game.

Me and some friends will probably start working on a game sometime
soon, and we're currently discussing this a lot. I'll definitely
recall and reuse the idea about getting a head start and something
playable as soon as possible.


### <span class="org-todo done DONE">DONE</span> Haven and Hearth, Nov 8 {#haven-and-hearth-nov-8}

Today I played a game by originally made by Seatribe called Haven and
Hearth&nbsp;[^fn:16]. It's a MMORPG where death, Permanent death(!), is
imminent. The scenario takes place in a world quite similar to real
nature. There are no fictive elements in the game in that sense. It's
really like a simulation of a subset of the real world. Typically you
travel around in a forest of some sorts. There are mountains, rivers
and lakes for you to explore. I starved to death pretty early but on
my second character I managed to find some apple/hazel/mullberry
trees which I could forage for edibles.

Other than permadeath, another unique thing about this game is that
the world is dynamic. For instance can everything be razed and reused
much like in the real world. I think this adds a bit of spice to the
game, as MMORPGS tend to get very repetitive with new characters, as
you do the same types of quests over and over again.

In order to make it more enjoyable and so on, Seatribe have added a
quality system to the game. The quality systems gives value to the
items much like an in-game currency. All of the items have quality
values. If you combine items of different quality level, you get an
item with some weighted average quality.

Their business model is quite interesting. The game is F2P&nbsp;[^fn:17] and
doesn't focus on money making at all, which is quite a relief when
playing. No ads, no worries. In a sense, it can get very "zen" as you
stroll through the forest, enjoying the view. A meditation of sorts.
This is really a good game for whenever you feel like escaping reality.

I've never played a game like this before. I've only played MMORPG's
with a static world like Runescape&nbsp;[^fn:18]. Playing was really fun, I
managed to find my friend there and we chopped down some trees
together and built a simple shelter. Perhaps we should've played some
more, since we didn't get that far in the game.


### <span class="org-todo done DONE">DONE</span> What type of gamer are you? Nov 5 {#what-type-of-gamer-are-you-nov-5}

My name's Anton Erholt and I've just started my master's programme
in Computer Science at KTH. I'm 23 years old and live in a suburb of
Stockholm together with two of my friends.

I've been playing computer games for about as long as I can
remember. I remember the Christmas when I got my first console, the
Nintendo Game Boy&nbsp;[^fn:19] together with Super Mario Land&nbsp;[^fn:20].

Ever since then, I guess I've been kind of hooked on handheld
games. Somehow, I really prefer the compact format and (the usually)
somewhat simple controls. I can recall playing in the backseat of the
car as a kid almost every vacation trip we ever made.

Throughout the years, I've owned a few different handheld consoles,
mostly from the Nintendo series. IIRC, I currently own a Game Boy, a
Game Boy Advanced (GBA), a PlayStation Pocket (PSP) and well, no PS
VITA, but a PlayStation Certified smartphone&nbsp;[^fn:21]. Together with my
flatmate, we have some other consoles at home as well (NES, SNES, N64,
PS3, XBOX [original] and a&#x2026; Commodore 64!). As of right now I mainly
play on my smartphone or my PC if I play something.

My favorite genre of games usually varies a bit, but over the years I
seem to return to the single player RPG-genre. Games such as Golden
Sun&nbsp;[^fn:22], The Legend of Zelda: Links Awakening&nbsp;[^fn:23] and Final
Fantasy VII&nbsp;[^fn:24] are a few of my favorites. Another genre which I've
grown a bit attached to, is the roguelike, dungeon-crawling
genre. Permadeath, tons of monsters and weird actions. Dungeons of
Dredmor&nbsp;[^fn:25] is one of the games I'm currently playing a lot.

One of my greatest game experiences must have been while playing
Pokémon Blue&nbsp;[^fn:26], getting the 151st Pokémon. I remember being
extremely happy about having "Caught them all". The feeling was almost
indescribable after having spent so many hours completing that
task. Too bad it only took 'til a few months until they released about a
hundred more Pokémons in a new game.

One of the worst gaming experiences I've had was probably when I
played one of the Zenonia&nbsp;[^fn:27] games on my phone. The game was so focused
on trying to sell me stuff and incorporating ads that it was almost
unplayable. I uninstalled it very quickly since it wasn't that
fun. I'd rather had spent some money on buying the game instead of
having a f2p business model.

I've not got that much experience of developing games. My main
development work has been of web programming nature (part front-end,
part back-end), so this is a bit of a new field for me.

[^fn:1]: <https://www.kth.se/social/course/DH2650>
[^fn:2]: <https://play.google.com/store/apps/details?id=com.orangepixel.stardash>
[^fn:3]: <http://pzwiki.net/wiki/Main%5FPage>
[^fn:4]: <http://eu.battle.net/wow/en/game/guide/>
[^fn:5]: <http://en.wikipedia.org/wiki/Multiplayer%5Fonline%5Fbattle%5Farena>
[^fn:6]: <http://www.rockpapershotgun.com/2012/07/20/dota-lol-all-heroes-of-newerth-heroes-going-free/>
[^fn:7]: <http://forums.heroesofnewerth.com/showthread.php?436758-Attention-Neweth!-All-Heroes-Going-Free>!
[^fn:8]: <http://www.wired.co.uk/news/archive/2013-08/15/plants-vs-zombies-2-review>
[^fn:9]: <http://en.wikipedia.org/wiki/Flower%5F(video%5Fgame)>
[^fn:10]: <http://cavestory.wikia.com/wiki/Cave%5FStory%5FWiki>
[^fn:11]: John Carmack on WebGL&#x2026; <http://www.youtube.com/watch?v=ozxRRPJzZ78>
[^fn:12]: <http://en.wikipedia.org/wiki/Bastion%5F(video%5Fgame)>
[^fn:13]: <http://sv.wikipedia.org/wiki/Spore>
[^fn:14]: <http://en.wikipedia.org/wiki/Data-driven%5Fprogramming>
[^fn:15]: <http://www.reddit.com/r/gaming/comments/p1ssv/dear%5Finternet%5Fim%5Fa%5F26%5Fyear%5Fold%5Flady%5Fwhos%5Fbeen/>
[^fn:16]: <http://havenandhearth.com>
[^fn:17]: <http://en.wikipedia.org/wiki/Free-to-play>
[^fn:18]: <https://www.runescape.com/game?html5=1>
[^fn:19]: <http://en.wikipedia.org/wiki/Game%5Fboy>
[^fn:20]: <http://en.wikipedia.org/wiki/Super%5FMario%5FLand>
[^fn:21]: <http://www.sonymobile.com/global-en/products/phones/xperia-play/>
[^fn:22]: <http://en.wikipedia.org/wiki/Golden%5FSun>
[^fn:23]: <http://en.wikipedia.org/wiki/Link%27s%5FAwakening>
[^fn:24]: <http://en.wikipedia.org/wiki/Final%5FFantasy%5FVII>
[^fn:25]: <http://dungeonsofdredmor.com/>
[^fn:26]: <http://en.wikipedia.org/wiki/Pokemon%5FBlue>
[^fn:27]: <https://play.google.com/store/apps/details?id=com.gamevil.zenonia4.global&hl=sv>
