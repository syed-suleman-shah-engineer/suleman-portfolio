---
title: "I Had 10 Days, a Theme, and No Team — So I Built a 1v1 Car Battle Game From Scratch"
description: "How I designed, modeled, coded, and shipped Marduga — a 1v1 multiplayer car battle game with custom vehicle physics, lock-on missiles, and a single-texture art style — solo in a game jam. What I built, what broke, and why I'm remaking it for the Play Store."
tags: [Game Jam, Unity, Game Development, WebGL, Blender, Multiplayer, Car Game, Indie Game, WitShells Studio, GameDevJS]
date: 2026-06-27
---

<!-- INSERT HERO SCREENSHOT: Marduga — two cars facing off in the arena, missile locked on, nitro trail visible -->
![marduga-cover-pic](/images/marduga-cover.png)


*Play it here: [Marduga on WaveDash](https://wavedash.com/games/marduga)*

---

## 10 Days. Solo. No Brief. Just a Theme.

Client work teaches you a lot.

It teaches you how to cooperate with someone who changes their mind three times before lunch. How to rebuild an environment you spent two weeks on because the client saw something on Pinterest. How to stay professional when the review doesn't match the work.

What client work doesn't teach you is what you're actually capable of when nobody can change the brief.

The GameDevJS Jam gave me that.

The theme: **Machine.**

The timeline: 10 to 13 days.

The team: me.

---

## Day 1 and 2: The Plan

Before a single asset gets made or a single line of code gets written — the plan.

Two days. Just thinking.

I had to make a WebGL game. Browser-based. Which immediately rules out anything too heavy, too complex, anything that pushes polygons too hard. I needed something that ran fast, looked good at a reasonable poly count, and could be completed by one person in under two weeks.

I landed on **1v1 car battle.**

Third-person. Two players. One arena. Weapons, pickups, collisions. The kind of game that's instantly readable — you see what it is in three seconds and you know whether you want to play it.

The theme was Machine. Cars are machines. Simple enough.

But I didn't want simple. I wanted a challenge inside the challenge.

So I added a constraint nobody asked for:

**Single color palette texture. One material. Every asset in the entire game.**

---

## The Art Style Nobody Asked Me to Use
![marduga-gameplay-view](/images/marduga-s1.png)

<!-- INSERT SCREENSHOT: Marduga cars and environment showing the flat color palette texture applied across all assets -->

I found Imphanzia on YouTube. If you've never seen his work — he's the person who popularized the single-texture color palette approach for low-poly 3D in Blender. One image. A grid of flat colors. You UV-unwrap every face of every mesh onto whichever color you want. One texture. One material. Unlimited colors across your entire scene.

The result has a distinctive look. Clean. Graphic. Recognizable.

It also looked like exactly what I wanted Marduga to be.

The trade-off: you set UV coordinates on every face manually. Every car panel, every weapon piece, every ground tile — each one gets its UV positioned precisely on the palette. Time-consuming. Unforgiving when you forget and a face maps to the wrong color.

I did it anyway.

I generated AI reference images for the car shapes and attachment designs to work from. Then modeled everything in Blender myself — bodies, wheels, the machine gun, the missile launcher — all UV-unwrapped to the same palette texture.

One material. Entire game. Every asset.

---

## The Vehicles: Custom Physics From Scratch

<!-- INSERT SCREENSHOT: Third-person view of car drifting in arena, 360 rotation visible from above -->
![marduga-gameplay-view](/images/marduga-s2.png)


I did not use Unity's built-in `WheelCollider`.

I built the vehicle controller myself. Every steering response, every acceleration curve, every friction calculation — custom C#.

The inspiration was GTA San Andreas car handling. Specifically the drift mechanic — if you hold acceleration and brake simultaneously while steering, the car rotates. Momentum decreases but the car spins. A full 360 if you hold it long enough.

I wanted that. Exactly that.

The feel of a car that rewards aggression and punishes caution. A vehicle that slides when you push it and snaps back when you release. Not realistic — fun. Those are different things and most car game developers confuse them.

Getting the custom physics to feel right took most of Day 3. But when it clicked — when the car started rotating on its own axis under acceleration and brake — I knew the game would be playable.

---

## The Weapons: Four Systems, Four Days

Each attachment got one day. That was the budget.

**Machine Gun — Day 1**

Front-mounted. Fires straight. No lock-on.

The constraint is the mechanic — to shoot someone with the machine gun, you have to be facing them directly. No spray. No tracking. You earn the hit by positioning yourself correctly.

This made it a weapon of aggression. You use it when you're confident, when you've chased someone down and have them in your sights.

**Missile Launcher — Day 2**

This one I'm most proud of.

Hold the fire key. The missile system starts scanning for a target in front of the vehicle. A lock-on indicator appears on screen — filling as the lock strengthens. When you have a full lock, release the key.

The missile fires.

The hold-to-lock, release-to-fire mechanic inverts the usual button logic in a way that felt immediately natural during testing. It also creates tension — you're holding the key, the lock is building, and the enemy is trying to break your line of sight before you can release.

**Nitro — Day 3**

Straightforward. Pickup activates a speed boost. Visual effect on the exhaust. The car surges forward.

Simple mechanics are only simple if they feel good. I spent most of Day 3 on the force curve — how fast the boost kicks in, how long it lasts, how the speed trails off. Too instant and it feels fake. Too gradual and it feels weak.

**Shield — Day 4**

The shield was always designed to do two things. Not one.

First: absorbs incoming damage while active. Standard defensive mechanic. But a shield that only blocks is boring — it rewards passivity and passive mechanics slow down car combat.

So I added the second function deliberately: if an enemy vehicle collides with you while your shield is active, the collision force gets reflected back — amplified, not absorbed. Hard enough to send them airborne if they were moving fast.

This turns the shield into an offensive trap. You bait the charge. You activate at the last second. They fly.

The timing window is tight. The risk is real — activate too early and the enemy reads it, adjusts, retreats. Activate too late and the collision hits before the shield is up. But get it right and it's the most satisfying play in the game.

Highest-risk, highest-reward. Designed that way from day one.

---

## The Arena

<!-- INSERT SCREENSHOT: Arena overview — tunnel visible, obstacles placed, both spawn points marked -->
![marduga-game-destructionr-pic](/images/marduga-game-destruction.png)


The arena design was the part I got most wrong.

I built it with the weapons in mind — open enough for missile lock-ons, tight enough for machine gun engagements, a tunnel for cover. Mini obstacles to drive around. Two clear sides for spawning.

What I didn't account for adequately was the surface geometry at a fine level.

Custom vehicle physics are sensitive to micro-collision offsets in ways that Unity's `WheelCollider` handles automatically. Certain surfaces in my arena had tiny geometry gaps — invisible to the eye, but the physics engine treated them as small ramps. The car would lose speed unpredictably on those surfaces, breaking the feel of the controls exactly when momentum mattered most.

Players noticed. The feedback was "controls feel off."

The controls weren't off. The environment was.

I know that now. I didn't fix it during the jam because I ran out of days.

---

## The Multiplayer

1v1. Real-time. Browser-based WebGL.

Two players. Same arena. First to destroy the other wins.

I used **Unity Netcode for GameObjects** for the sync layer — but Netcode alone doesn't solve the WebGL problem. Browsers can't open raw TCP or UDP sockets. You need a relay.

WaveDash — the platform hosting the game — provides a **WebRTC transport layer** that sits between Unity Netcode and the browser's networking constraints. Players connect through WaveDash's relay rather than peer-to-peer, which handles NAT traversal and browser compatibility automatically.

I had never used WaveDash before this jam. First time integrating their transport. First time working with WebRTC in Unity. First time shipping multiplayer to a browser target. The research and integration happened inside the same 10-day window as everything else.

It worked. Two players, same arena, real-time sync in a browser tab.

WebGL multiplayer is a specific technical challenge most developers avoid entirely. Getting it working inside a jam timeline — with a transport layer I'd never touched before — is the part of this project I'm most technically proud of.

---

## The Results — Honest Assessment

The game shipped. On time. Playable. Online.

My wife played it. My family played it. They came back and played it again — which matters more to me as a signal than any review score.

My friends played it and told me what was wrong. Which is also fine. That's what friends are for.

The jam voting didn't go my way. I won't pretend otherwise.

But I watched the other entries. Some of them were genuinely excellent — creative, polished, memorable. Games that deserved recognition. They also didn't win. The jam distributed prizes based on votes, and votes in game jams are partly about community size, partly about visibility, partly about luck.

I was disappointed. That's honest.

I was also proud. That's also honest.

---

## What 10 Days of Uninterrupted Building Taught Me

### 1. Constraints produce creativity
The single-texture art style was a self-imposed constraint that nobody asked for. It also became the most distinctive thing about the game visually. Limitations force decisions. Decisions produce character.

### 2. Custom physics are only better if you finish them
The vehicle controller felt great. The environment undermined it. A game with imperfect physics but a well-designed environment would have played better than what I shipped. Next time the environment gets built alongside the physics, not after.

### 3. Jam voting is not quality assessment
It is a popularity contest with a game as the subject. If you enter a jam for the prize, enter with a large community behind you. If you enter for the experience, enter with a challenge that pushes you. Conflating the two leads to disappointment.

### 4. Your own project gives you something client work can't
When I'm working for a client, I cooperate. I rebuild when they pivot. I adjust when they change direction. That's the job and I do it professionally.

Marduga had no client. Every decision was mine. The machine gun mechanic, the hold-to-lock missile system, the shield collision effect — all of it came from my own judgment and stayed because I wanted it to. That feeling is different from anything client work produces.

### 5. A shipped game with known flaws beats an unshipped game with none
Marduga has a collision offset problem in the arena that I haven't fixed. It's also live, playable, online, and being played. Perfection is the enemy of shipping. Ship. Fix later.

---

## What's Next for Marduga

I'm not updating the jam version. That submission is what it is.

But I'm remaking Marduga for the **Play Store** when I get the time. Proper arena design built from day one alongside the physics. Refined vehicle controller. More attachment types. The same single-texture art style because I genuinely like how it looks.

The 1v1 car battle format is good. The core mechanics are good. The environment needs a complete redesign and the multiplayer needs proper mobile networking.

When that version is ready — I'll write the next chapter of this story.

---

## Play It

The current version is live on WaveDash:

**[Play Marduga](https://wavedash.com/games/marduga)**

Lock on. Release. Send them flying with the shield. See if the arena gets you first.

---

## Building a Game and Need a Developer?

If you're building a multiplayer game, a simulation, or any interactive experience in Unity — this is what I do. Custom physics. Custom networking. WebGL, Android, VR. Full builds from concept to deployment.

📩 [sayedsulaiman607@gmail.com](mailto:sayedsulaiman607@gmail.com) · [cto@witshells.com](mailto:cto@witshells.com)
💬 [WhatsApp Me →](https://wa.me/923093023289)

---

**Tags:** *Game Jam, Unity Game Development, WebGL Game, Indie Game, Car Battle Game, Custom Vehicle Physics, Low Poly Game, Blender 3D, Multiplayer Unity, GameDevJS, WitShells Studio, Game Developer Pakistan*

---

### About the Author

**Syed Suleman Shah** is CTO of WitShells Studio — a game development and VR studio delivering interactive experiences for education, defense, and entertainment clients across Pakistan and the Middle East.

*© 2026 WitShells Studio (SMC-Private) Limited. All rights reserved.*