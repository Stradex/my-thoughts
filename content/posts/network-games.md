---
date: 2024-09-21
draft: false
params:
  author: Stradex
title: Networking for game development.
---

## My personal experience

I love Game Development, and the part I love the most is Networking.

My personal passion is to code cooperative networking for games. Why? because I love to play with games, and sometimes I find games that I feel like "I wish this one had cooperative so I could play with my Friends". All the little games I made last years had cooperative networking, and I also did a very big project bringing cooperative to a fork of Doom 3 called *Dhewm3*. The project name is ["Librecoop"](https://github.com/Stradex/librecoop).

Also I did a cooperative game called ["Space Schump"](https://stradex.itch.io/space-schmup-coop) in Godot Engine. And an unfinished turn based gamed called "Powerfool", also in Godot engine.

So after years of making networking for My games and mods, I want to share my personal tips and experiences.

## General tips about networking.

Games multiplayer is mostly about sending packets, and there are two common ways of sending packets: Reliable packets *(TCP)* and Unreliable Packets *(UDP)*.

## Packets via TCP

Packets via TCP are ensured to reach the client/server, so they may take more time to reach it's destination because if the packet is not received or data is lost in the middle of the way, then the packet will be sent again to ensure it's reach the destination at some point.

This way of sending packets is ideal for crucial things in our games that we must ensure that the server/client receives for the correct functionality of the multiplayer session.

### Examples when you could use TCP to send packets in a multiplayer game.

* Update the scoreboard of the game. We want to make sure that all clients keep a sync scoreboard probably.
* An important event. For example the opening of a door, the start of a cutscene, etc.. if a packet with that information does not reach the client, then the game experience could break.
* When a client connect or disconnects.
* In a turn based game, the start of a new round for example.

## Packets via UDP

Sometimes you want some information to be synced very frequently but the delivery must be fast. Packets sent via UDP are ideal for a more intense in real time syncronization but they can fail to reach it's destiny and there will be no double check to send the packet again if it fails. So one must consider when this kind of packet can be useful, since sometimes data can be lost.

I would choose this way of sending packets for non-crucial aspects parts of the games but that are necessary to ensure a smooth experience between the players.

### Examples when you could use UDP to send packets in a multiplayer game.

* Clients position in a game where players can move around the world. Usually you can compensate for the loss of data or delay between packets with some interpolation taking in account current player speed and position.

* Non-crucial information that could be useful to be synced: Another entities position that we want players to see at the same place but that can interact with physics and the players can move them, I mean syncing moveable objects *(Usually, if you can, you want moveable non-crucial objects to don't sync if possible and let each client to handle that locally. As long as you are OK with that one player see an small decoration moveable object in one place, and another in other place)*


## Choosing when to use UDP packets, when to use TCP and when to let the client to handle things.

One of the most crucial parts of networking in multiplayer videogames is to decide what MUST sync perfectly, what we must somehow sync but is not crucial to be perfectly synced and what to let the client to handle locally without the need of sync at all.

For me this is the most important part when you are designing your multiplayer game. It depends a lot in what kind of game you are making, if the multiplayer will be competitive, cooperative. If the cooperative will be also competitive or just to play for fun with friends. 

Let's try analize different games to see what strategies we can choose for different kind of games:

### Example 1: Cooperative FPS game *(Like Doom, Quake, Left 4 dead 2, etc..)*

In this scenario we have a game that will be cooperative and we will design it mostly so people can have fun with friends. We can make some sacrifices in order to boost the experience and reduce the sensation of latency.

I would use TCP packets only for very few things like: A server map change, the death of a player, the death of a monster/enemy, an event like the opening a door, the movement of a bridge from position A to B *(do not sync all the movement but the action that says "This bridge will move" and let the client locally handle the movement. The same with the door)* etc...

I would use UDP packets for syncing players position and enemies position. I would put more priority in the position of closer enemies over far away enemies from the current players. This is because may we have a game with thousands of enemies and trying to syncing everything at the same time will overflow the server and could cause a collapse in the server network.

In my mod, Librecoop, I had a list of all active enemy entities sorted by distance to each players *(and more things, but let's just simplify for the sake of the explanation)* and I would give more priority to sync info about enemies that were closer and engaging with players over other enemies far more away or that they were not engaging directly with the player. For example, enemies currently fighting with players and being more close would always try to sync for every time a packet was sent, enemies that were far away are at the bottom of the queue and will sync less frequently to avoid overflowing the server network.

In a game like this, I would also try to let as much things as possible to be handled clientside. 

* Individual player movements?, handle it clientside without waiting for the server to auth my position and just sending my current position to the server and doing all collision checks in my local machine. 

* Handling player damage locally: In a game like this, waiting for the server to inform me "You received some damage" could affect the experience and give a sensation of delay each take we take damage, mostly if the distance between clients to the server is far. Usually when you exceed 100ms of ping with the server, it start affect the game experience. For example, instead of waiting the server to confirm that I received a damage from a projectile, just handle locally that a projectile reached the current player positiona and handle that damage locally and just inform the server "I received damage". Yes, this could allow some players to do hacks, or cheat, but we are talking about a cooperative game for fun, not a competitive game.
