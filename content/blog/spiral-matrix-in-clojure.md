+++
title = "Spiral matrix in Clojure"
author = ["Anton Erholt"]
description = "It's all about 42, (defn foobarbaz []) and cheeseburgers."
date = 2016-04-15
lastmod = 2018-10-03T22:26:26+02:00
tags = ["code", "problem", "clash"]
categories = ["blog"]
draft = false
weight = 2002
+++

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
