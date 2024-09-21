I love Game Development, and the part I love the most is Networking.

My personal passion is to code cooperative networking for games. Why? because I love to play with games, and sometimes I find games that I feel like "I wish this one had cooperative so I could play with my Friends". All the little games I made last years had cooperative networking, and I also did a very big project bringing cooperative to a fork of Doom 3 called *Dhewm3*. The project name is *Librecoop*.

So after years of making networking for My games and mods, I want to share my personal tips and experiences.

## General tips about networking.

Games multiplayer is mostly about sending packets, and there are two common ways of sending packets: Reliable packets *(TCP)* and Unreliable Packets *(UDP)*.

## Packets via TCP

Packets via TCP are ensured to reach the client/server, so they may take more time to reach it's destination because if the packet is not received or data is lost in the middle of the way, then the packet will be sent again to ensure it's reach the destination at some point.

This way of sending packets is ideal for crucial things in our games that we must ensure that the server/client receives for the correct functionality of the multiplayer session.
