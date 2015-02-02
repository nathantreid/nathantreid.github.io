---
title: Ratling Trader - developing a rogue-like game in JavaScript
---

Ratling Trader - developing a rogue-like game in JavaScript
---

I love rogue-like games. In my youth, I spent innumerable hours delving in the dungeons of [ADOM](http://www.ancientdomainsofmystery.com/) and [NetHack](http://www.nethack.org/), and for a long time have wanted to create my own rogue-like game. Naturally, the game I have in my head is very expansive, so I've put it off in favor of other projects. However, recently my desire has resurged, and I decided to start with a less ambitious rogue-like.
To that end, and with a bit of a hat-tip to ADOM, I have begun working on Ratling Trader. The player will take on the role of a ratling (a member of a humanoid rat race) trader who is just trying to hawk enough wares every day to provide for his family. *If you've played ADOM, I'm sure you'll remember the ratling traders from the arena. If not, well, go play it! It's a great, challenging adventure.*
Unfortunately, the local crime boss has taken a disliking to you and demanded that you pay a ridiculously large sum of protection money - or else. The game will focus on the player's attempt to deal with this issue within a certain number of turns, whether it be through selling enough of your wares to make the payment, or by other means...

Of course, as a personal project, the game is a playground for me to experiment. The game is being written in JavaScript, and I plan to package it with node-webkit for PC releases, as well as hosting it on a server with Node.JS and potentially building iPad and Android versions as well using Cordova.

I started writing the game by loosely following [this excellent tutorial](http://www.codingcookies.com/2013/04/01/building-a-roguelike-in-javascript-part-1/). Having made my way through most of the tutorial, and having gained a better feel for what I want to do and how I want to go about it, I've decided to start over with a Test-First approach.

As another experiment, I've decided to try Ian Cooper's approach to test-driven development - that is, treating each behavior of the application as a unit instead of each class. Right now, that looks like this:
![Unit Tests Output](/images/2015-01-01-ratling-trader/unit-tests.png)
So far, it's going well. It feels like I'm writing tests to implement features, as opposed to writing tests for the sake of... writing tests. In some of the tests, I've used mocks, although they often disappear as I flesh out the implementation. This does mean that my tests are more coupled to my implementation, although not as much as you might think. My goal is to set up each test to interact with the most external point of the application that makes sense for a given behavior, inject whatever I need to hook into the output, and ignore the details of what happens in between. This means that my tests will break if my interface changes, or if the behavior changes, which is the same as it always has been. The difference is that I'm no longer updating tons of mocks when a method signature changes - instead, almost all of the updates will directly relate to behaviors with in the app.
It feels good right now, so I'll see how it goes and post more detail as I go along.

The source code is on GitHub: [https://github.com/nathantreid/ratling-trader/tree/fresh-start-with-tests](https://github.com/nathantreid/ratling-trader/tree/fresh-start-with-tests).
