+++
title = "Moves where a Queen can't take (8x8 chess board)"
author = ["Anton Erholt"]
description = "It's all about 42, (defn foobarbaz []) and cheeseburgers."
date = 2016-04-17
lastmod = 2018-10-07T11:23:49+02:00
tags = ["code", "problem", "clash"]
categories = ["blog"]
draft = false
weight = 2001
best = true
+++

This piece finds okay positions where a single queen can't take the
piece (on a 8x8 chess board). It took a little while to write, but I
think it reads pretty well and is done in a functional style. I was
TOO SLOW to finish this in a single clash! (1h 10min)

Learned about generation with lazy-seq and map-indexed together with
list comprehension (for).

```clojure

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
 ("P" "P" "P" "." "P" "." "P" ".")
 ("P" "P" "." "P" "P" "." "P" "P")
 ("P" "." "P" "P" "P" "." "P" "P")
 ("." "P" "P" "P" "P" "." "P" "P"))
```
