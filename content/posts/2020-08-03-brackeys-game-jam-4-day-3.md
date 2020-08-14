+++ 
draft = false
date = 2020-08-03T07:59:07-04:00
title = "Brackeys Game Jam 2020.2 Day 3"
description = "Devlog for a game jam"
slug = "brackeys-game-jam-2020-2-day-3" 
tags = ["game-programming", "game-jam"]
categories = []
externalLink = ""
series = ["Brackeys Game Jam 2020.2", "DevLog: Nilrem and the Hourglass of Time"]
+++

## 2020-08-03 0759H EDT
Today working on a lot of the generation algorithms. Primary dungeon and path generation will be complete today. Need to get as far as the REWIND mechanic.

## 2020-08-03 0847H EDT
Have initial room spawning complete. Need to get some work done at the office (remotely of course).

## 2020-08-03 2016H EDT
Actually not spawning Nilrem in the `GameController`. Nilrem and the first room always spawn at the same location. So Nilrem is just a `GameObject` in the main dungeon scene.

Behold Nilrem!

![Behold Nilrem](/images/brackeys-game-jam-2020-2-day-3/behold-nilrem.png)

He's the purple guy in the scene. He'll soon look a little better. This is the quick prototype for getting spawning and basic movement down.

I'm actually pretty happy with today's progress so far. However, it is today that I realize I won't be able to submit this project on Saturday for the jam. That's too bad; I do think this will be a fun game. I would actually, like to continue this project knocking out a couple of items a day on the backlog until I release this first version as a prototype. This is going to be a pretty unique game in my opinion, and if it works out I'd even like to Kickstarter it to raise money to add custom stylized assets, music, and some mo-cap animations. Animations that help tell a story and add humor in this game would really make it something more than a prototype.

That being said, I still want to submit something for the game jam. Time for a Pivot.

The name of the new game is: _"Rewind Maestro"_. This is inspired by the game [Ridiculous Fishing](https://www.youtube.com/watch?v=d27JTsAGks4&feature=emb_logo). But instead of a fish line and fishing and shooting fish, you'll start as a conductor at the end of a staff of music. You'll _REWIND_ through a staff of music. As soon as you touch the first "note" you'll fast-forward through the score, you have to collect the right notes. At the end of the score, the notes right, wrong, or missing will be played and you'll receive a score based on how perfect the song is.

The Game begins and the Maestro is standing to the right of a staff of music. He holds up his baton. The REWIND control is shown activating at the top left of the screen. You guide a treble clef backwards through the staff. There are notes, instruments, and some other clefs. Clicking above the treble causes the clef to move up (or up arrow). Clicking below the treble causes the clef to move down (or the down arrow). Your goal is to get to the start of the score. As soon as you touch a note or some other obstacle, the direction changes to fast-forward. Now you have to collect the correct notes. You'll know they're correct because they'll stand out visually somehow. When you reach the end of the staff, The notes that you've captured will Play above the Maestro's head, and you'll receive points for each correct note and bonuses for streaks etc...

It should be noted that this is going to be programmer art in 2D. If I keep it simple enough that shouldn't be a problem. I need to only make a few sprites and the "animation" is pretty simple.

Maybe I'll sleep on it and see if I can come up with anything better.
