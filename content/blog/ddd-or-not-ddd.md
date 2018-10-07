+++
title = "To use DDD or not (| !) to use DDD"
author = ["Anton Erholt"]
description = "It's all about 42, (defn foobarbaz []) and cheeseburgers."
date = 2014-01-07
lastmod = 2018-10-07T11:23:50+02:00
tags = ["gamedevstory"]
categories = ["blog"]
draft = false
weight = 2013
+++

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
