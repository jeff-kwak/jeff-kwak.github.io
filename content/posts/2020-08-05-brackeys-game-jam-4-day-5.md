+++ 
draft = false
date = 2020-08-05T12:08:05-04:00
title = "Brackeys Game Jam 2020.2 Day 5"
description = "Devlog for a game jam"
slug = "brackeys-game-jam-2020-2-day-5" 
tags = ["game-programming", "game-jam"]
categories = []
externalLink = ""
series = [ "Brackeys Game Jam 2020.2" ]
+++

## 2020-08-05 1209H EDT
Today is the day the game will be finished. Putting a little "backlog" into the repo (a to-do list). Decided to rename the game to "Relax and Rewind". Want to give it a sort of Zen Garden aesthetic. Decided against scoring and timers. Just put the puzzle back together, and if you can't just rewind.

## 2020-08-05 1713H EDT
Figured out a nuance with the Input System. The orbit camera is best served by listening to something called `Delta [Pointer]` bindings. The axis still have to be interpreted into Cinemachine however.

Now this raises a new requirement for keyboard and mouse control. `OnLook` is essentially the equivalent of the right stick on a controller, and `OnMove` is essentially the equivalent of the left stick. Basically, I need another control mechanism for keyboard and mouse, one to control the mouse cursor for object and menu selection and another to do `OnLook`. Maybe, hold down right-mouse button.

## 2020-08-05 2121H EDT
I spent all my time on the jam today working on the camera controls. They came out beautiful just what I wanted. Using the Input System and Cinemachine together has been challenging. I think today did me in. That's part of it though isn't it. I learned a lot. I have some killer tools for the next jam. Every day there's less I have to learn and more I know. I might try to finish this game as well. It'll probably take me until next week to get the first version out.
