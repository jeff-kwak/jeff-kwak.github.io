+++ 
draft = false
date = 2020-08-01T10:02:05-04:00
title = "Brackeys Game Jam 2020.2 Day 1"
description = "Devlog for a game jam"
slug = "brackeys-game-jam-2020-2-day-1" 
tags = ["game-programming", "game-jam"]
categories = []
externalLink = ""
series = ["Brackeys Game Jam 2020.2", "DevLog: Nilrem and the Hourglass of Time"]
+++

### 2020-08-01 0800H EDT
Woke up and the jam has been going on for 2 hours already. The theme is _**"REWIND"**_. Time for coffee and to noodle on some ideas for the game.

### 2020-08-01 1000H EDT
I have a couple of ideas around the theme. In no particular order:

  * A maze game where you can only go backwards by a "REWIND". You only get so many rewinds per level.
  * A time traveler game. Maybe like the old arcade holograph game.
  * Physics puzzle that works like a backwards 8mm movie.

  I've loosely monitored the Brackeys Discord, and watched a couple of videos mostly for inspiration:

  * ([Rick Dyer/SEGA - Hologram Time Traveler (Full Playthrough via Daphne/Singe Emulator)](https://www.youtube.com/watch?v=ZI6h0Sg73yw)
  * [REWIND TIME in Unity](https://www.youtube.com/watch?v=eqlHpPzS22U&list=LLSy96YSvT3AFn904zr0dKuQ&index=4540)
  * [Dragon's Lair 2: Time Warp playthrough](https://www.youtube.com/watch?v=-lwOzgFJxaQ)

### 2020-08-01 1047H EDT
So the game is going to be called _Nilrem and the Hourglass of Time_. You will play a wizard on a quest to obtain the powerful Hourglass of Time. An evil wizard, Daryll, through powerful dark magic has stolen the Hourglass and is controlling time as ransom for control of the Kingdom.

It will be heavily inspired by the laser disc games.

The game is divided into seamless "Turns" (maybe each Turn is ~2s). Each turn will have a _problem phase_, a _choose phase_, and a _results phase_. The _problem phase_ will present the player with some problem to solve. During the _choose phase_ the correct choice will be highlighted briefly. You must pick the correct action within the _choose phase_ or die. You have within the _result phase_ to cast "REWIND" (if you have enough casts left). If you do not rewind a deadly result, you die and your game is over.

Problems:
  * something is requiring you to move: duck, jump, move left, move right, move forward, move backwards.
  * something requires you to cast a spell: fireball, lightning, freeze, transform, shield, teleport

There are potentially cutscenes at some point to break-up the gameplay and give the player a rest, and also to advance some semblance of a "story", but at least for the purpose of game play pacing. The "cutscenes" could be something as simple as a monster having a dramatic death animation.

The game will be played in a randomly generated dungeon. This dungeon will actually just be a simple tree. Each room in the dungeon will be the same size and square. Rooms will connect wall-to-wall. There must be at least one, and at most 3 exit doors. Exit doors cannot loop back into an already created room.

The turns themselves form a graph from entrance to exit. If there is more than one exit, there's a chance that one of the exits will be death. Potentially during the _choose phase_ you can also cast REWIND, and it will put you in the previous turn.

Here's an example. The room (in this example) is a 3x3 grid with spaces marked as `o` and Nilrem's location marked by `@`. There's a single entrance door on the West, through which Nilrem has entered. There's a single exit door on the East. The turn graph must construct move actions that lead from the entrance space to the exit door. So the correct choices in each turn might be as simple as ` Move(Right)->Move(Right)->Move(Right # opens the exit door, enters next room") `. This is "simple" enough, in that it's a basic path algorithm and it doesn't have to be efficient. The _problem phase_ presented here could be some kind of trap: spikes, saws, swinging pendulum, lava, acid, freeze, falling rock, or a pending attack from an enemy. After the correct choice path is calculated, the problem can be randomized that goes along with it.

For example, on Turn 1, during the _problem phase_, Nilrem is standing on a spike trap (` ^ `), and that is shown somehow (e.g., a brief animation). The _choose phase_ highlights that moving East is the correct action. The player chooses `Move(East)`, and the _result phase_ shows Nilrem moving East and the Spikes triggering. If the player chose some other action, or no action, the _result phase_ would show Nilrem dying. If the player casts "REWIND", the Turn starts over. If the player doesn't or can't cast "REWIND", the player dies and it's game over.

In this example on Turn 2, the _problem phase_ reveals a big ogre (` # `) getting ready to attack. The _choose phase_ reveals the correct action by highlighting the `Move(East)` choice. Important note, in "real life" other choices may have been equally correct. For example, `Cast(Shield)`, `Cast(Teleport)`, `Move(North)` could all potentially be choices that save Nelrim from death. However, for this game you have to choose the one "correct choice". For example, choosing `Cast(Some Spell)` would result in Nelrim failing to cast the spell in time and him getting crushed.

On the final Turn in this example, the _problem phase_ might reveal a swinging axe (` x `) from the ceiling. The _choose phase_ reveals the correct action by highlighting the exit door.

```text
Turn 1     Turn 2     Turn 3     Enters the next room
+-------+  +-------+  +-------+     --+-- 
| o o o |  | o o o |  | o o o |     o | o 
] @ o o [  ] ^ @ o [  ] ^ # @ [     x ] @ 
| o # o |  | o # o |  | o   o |     o | o 
+-------+  +-------+  +-------+     --+-- 
Spikes     Monster    Axe           Next room  
```

After the main Turn path has been determined. Other non-move turns can be inserted. For example, before Turn 2, a Turn could be inserted that is a monster attack, that requires a shield spell be cast.

After some number of rooms, the final room with the Hourglass of Time is given. This will be a pre-determined room with the final action showing Nilrem obtaining the Hourglass of Time and defeating Daryll. The player wins. Game over. Roll the credits.

I think that's the general game design. I'm considering doing a paper prototype to codify the algorithms:
  1. Room-door generation.
  2. Turn generation
    a. Correct Choice Graph
    b. Problem Selection
    c. Augmented Turns

It took about 2.5 hours to write this design document. For play testing, you'll probably want some cheat codes to slow down the turn timer, give you  more rewinds, etc...

### 2020-08-01 2014H EDT
I spent a few hours paper prototyping the game. This was valuable.

![Paper Prototype Setup](/images/brackeys-game-jam-2020-2-day-1/paper-prototype.jpg)

I drew a room. Got some tokens for Nelrim, some monsters, and some randomizers from a Pathfinder Beginner Box set. I used stickies to add doors, and exits. I then ran through the algorithms from the design and learned a few things.

Namely randomly, the rooms can crash into each other. Which is actually not what I want for this game. I'll have to keep a list of rooms visited and their virtual coordinates and prevent an exit into an already visited room. It might also be noted that, you can still get stuck by creating a room that fits like a Tetris piece in the middle of 3 previously visited room. Then you're essentially stuck. That didn't happen in the prototype, but I might have to prune those from the tree.

I think the rule of not allowing Nelrim to rewind into an already visited room, or to revisit existing rooms is a good one. It gives the flow of the game more urgency, more like a runner, and closer to the laser disc games. It makes it less roguelike; which is neither good nor bad.

Calculating the path with move actions worked out really well in the prototype.

I liked having an odd number of spaces because it makes a space in the center of the room. The prototype used a room of 7x7 but I think that's too large. Even 5x5 might be too large. If the rooms were 3x3, at a minimum you would require 3 turns to leave the room. A quicker pace might be better.

I wanted to get through some of the coding today, but I'm actually so tired I don't think I can do anything more. That's it for day 1.