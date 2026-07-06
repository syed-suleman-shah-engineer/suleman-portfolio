---
title: "A Client Wanted His VR Game Polished. We Ended Up Rebuilding It From Scratch."
description: "How a $800 Meta Quest performance optimization contract turned into a six-month journey rebuilding a detective VR game entirely — new story, ReadyPlayer.me avatars, hand magnifier, backpack gadgets, zombie sequences, and 90+ FPS on Quest hardware."
tags: [VR Game Development, Meta Quest, Unity, VR Optimization, Detective Game, ReadyPlayer Me, Quest 2, Fiverr, WitShells Studio, Case Study]
date: 2026-07-06
---

![Sure Lock — Detective VR Game running on Meta Quest](https://img.youtube.com/vi/HHZiCeuiAbw/0.jpg)

*Watch the gameplay: [Sure Lock VR on YouTube](https://www.youtube.com/shorts/HHZiCeuiAbw)*

---

## It Started as an Optimization Job

The brief was simple.

A client had a detective VR game for Meta Quest. It worked. But it didn't run well — frame rates dropping, performance inconsistent, the experience stuttering in ways that break immersion on standalone VR hardware.

He wanted 90+ FPS. Sustained. On device.

On Fiverr, we agreed on the first contract. Performance optimization only. One month.

I hit the target. Delivered a stable, optimized build running consistently above 90 FPS.

He came back.

*"Can we rebuild the whole game?"*

---

## Why 90 FPS Matters on Meta Quest

<!-- INSERT SCREENSHOT: Unity profiler showing frame time before and after optimization -->

Before I get into the rebuild — the optimization work deserves its own moment, because Quest performance is a specific discipline that most Unity developers underestimate.

Meta Quest is standalone hardware. No PC. No GPU. A mobile chipset doing everything — rendering, physics, audio, game logic — while maintaining enough thermal headroom to run for an hour without throttling.

90 FPS on Quest means each frame must complete in **11.1 milliseconds**. Every millisecond matters.

What kills Quest performance:

**Overdraw** — transparent materials layering on top of each other. The GPU redraws the same pixels multiple times. On mobile hardware this is catastrophic.

**Dynamic lighting** — real-time shadows from multiple light sources. Baked lighting is the answer on Quest. Always.

**Draw calls** — every unique material is a separate draw call. Combine meshes. Atlas textures. Reduce materials per scene.

**Physics complexity** — mesh colliders on every object. Replace with primitive colliders. Simplify anything the player never directly interacts with.

I audited the original game, identified every bottleneck, and worked through them systematically. Baked the lighting. Reduced overdraw. Combined meshes where possible. Replaced complex colliders with capsule and box primitives.

The frame rate went from inconsistent to locked above 90.

That was month one. Then the real work began.

---

## The New Story

<!-- INSERT SCREENSHOT: Sure Lock opening scene — detective lying in hospital bed, dream world beginning to form around him -->

The client had a new vision for the game. Not just polished — reinvented.

A detective falls into a coma.

Inside his coma, he's trapped in a dream world. And in that dream, cases still need solving. Evidence still needs finding. The mystery still needs unraveling.

His sister appears as his guide — a voice and presence moving him through the dream, explaining what's real, what's memory, and what the next mission requires.

Three distinct investigative scenes. And one sequence that breaks from the detective format entirely.

A zombie attack. Inside the dream, because in a coma dream the rules don't hold — and sometimes the horror of the subconscious pushes through.

It was a genuinely interesting premise. The kind of brief that makes you want to build it well.

---

## ReadyPlayer.me Avatars as Actors

<!-- INSERT SCREENSHOT: ReadyPlayer.me avatar as the detective, standing in the dream environment, sister avatar visible in background -->

I'd been watching ReadyPlayer.me for a while. The platform lets you create fully rigged, customizable 3D avatars — complete humanoid characters with facial features, clothing, accessories — that export directly into Unity.

The client's game needed characters. The detective. The sister. NPCs in the dream world.

Instead of modeling characters from scratch — which would have consumed weeks of the timeline — I brought in ReadyPlayer.me avatars and integrated them as the game's actors.

The result was immediately more convincing than low-poly placeholder characters. The detective felt like a person. The sister felt like a presence. The dream world felt inhabited.

ReadyPlayer.me avatars aren't a shortcut — they're a production decision. The right tool for what this game needed at this stage of development.

---

## The Gadgets: Building the Detective's Toolkit

<!-- INSERT SCREENSHOT: Backpack open in VR — gun, magnifier, and other tools visible as grabbable objects -->

A detective game lives or dies on its interaction design. If the player can't feel like an actual detective — picking up clues, examining evidence, using tools — the mystery falls flat.

We built three core pieces of the detective's toolkit, each as a physical VR object.

### The Hand Magnifier

The magnifier is the most used tool in the game.

Pick it up. Hold it over a clue. The lens zooms in — magnifying the detail, making small evidence readable, rewarding players who look carefully at the environment.

Building a magnifier in VR that feels physically real is more nuanced than it sounds. The zoom needs to respond to how close the lens is to the object. The rendering needs to stay sharp at the magnified scale. The hand tracking needs to feel like you're actually holding glass, not a UI panel.

I built it as a physical object with a custom render texture — the magnified view renders to a separate camera and displays inside the lens. In VR, through a Quest headset, it feels like a real object in your hand.

### The Backpack

Every tool lives in the backpack — mounted on the player's back, reachable over the shoulder.

Reach back. Grab the object you need. The backpack opens as your hand enters it and closes when you pull away.

This is a mechanic that VR does uniquely well. The physical gesture of reaching over your shoulder to grab something engages your spatial awareness in a way that a menu or quick-select wheel never can. You feel like you're equipped, not scrolling through inventory.

### The Gun and Shooting Mechanics

The zombie sequence needed combat.

A gun with proper Quest controller feel — trigger pull, recoil feedback, reload gesture. The zombie AI chases the detective through the dream environment. The player has to move, aim, and fire while the world is falling apart around them.

The zombie sequence is a deliberate tonal break from the investigative scenes. You've been solving a mystery. Now you're running from something your unconscious mind conjured. The shift in mechanics mirrors the shift in emotional stakes.

---

## Three Investigative Scenes

<!-- INSERT SCREENSHOT: Crime scene in dream world — evidence markers glowing, magnifier in player's hand, sister's voice indicator visible -->

Each of the three scenes is a contained mystery.

The player arrives in a location inside the dream — an apartment, a building, an outdoor space shaped by the detective's sleeping mind. Evidence is placed throughout the environment. Some obvious. Some hidden. Some requiring the magnifier to identify.

The sister guides the player through each scene — narrating context, reacting to discoveries, steering attention when the player gets stuck. She's the thread connecting the dream logic to the investigative goal.

Complete the scene. Find the evidence. Piece together what happened.

Then wake up to the next one.

The three scenes gave the game a structure — a beginning, middle, and end — that the original version didn't have. A detective game needs a narrative spine. Without it, you're just looking at objects in rooms.

---

## Optimization Running Through Everything

<!-- INSERT SCREENSHOT: Three scenes side by side showing lighting bake, texture atlases, and LOD settings in Unity -->

The rebuild wasn't just new content on top of the old optimization pass. Optimization was a constraint that shaped every decision from day one.

Every environment asset was evaluated for Quest compatibility before it went into the scene. Lighting was baked — not dynamic — across all three investigative areas. Texture atlases kept draw calls controlled as the scene complexity grew.

The zombie sequence was the hardest to optimize. Multiple AI agents moving through an environment, reacting to the player, playing animations — all simultaneously on Quest hardware.

AI pathfinding was the main cost. I simplified the navmesh, reduced update frequency on agents that weren't in the player's immediate area, and batched animation updates. The sequence held above 90 FPS even with multiple zombies active.

90+ FPS. On device. Sustained. The original contract target — maintained through an entire game rebuild.

---

## March to August 2023

Six months.

From a performance optimization order that was supposed to take one month — to a full game with new story, new characters, new mechanics, three investigative scenes, a zombie sequence, and a complete technical rebuild targeting Quest hardware.

The project grew because the client's vision grew. That happens in game development — especially when the first deliverable works well enough that the client starts seeing what's actually possible.

What I remember most from this project isn't the technical work. It's the moment the detective's dream world started feeling coherent — when the sister's voice, the magnifier in your hand, and the evidence scattered through a dreamed apartment all worked together to make you feel like you were actually inside someone else's unconscious.

That's what VR does when it works. It doesn't show you a world. It puts you inside one.

---

## What This Project Taught Me

### 1. Optimization is a discipline, not a checklist
Quest performance work isn't about running through a list of tips. It's about profiling, identifying the actual bottleneck, fixing it, profiling again. Every scene has different constraints. Every optimization decision has trade-offs. You can't bake your way out of a bad mesh or atlas your way out of a bad shader.

### 2. Third-party avatar systems are a production decision, not a shortcut
ReadyPlayer.me gave the game characters in a fraction of the time it would have taken to model them. The integration work was real — rigging, animation, facial expression systems — but the result was a game that looked inhabited. The right tool used well is always better than the wrong tool executed perfectly.

### 3. Physical VR interactions earn their complexity
The magnifier render texture, the backpack reach mechanic, the gun feel — each one took significantly longer to build than a menu equivalent. Each one also made the game feel like VR, not a PC game running in a headset. The investment in physical interaction design is always worth it on Quest.

### 4. A contract can grow if the first delivery builds trust
The rebuild happened because the optimization work landed well. The client saw what was possible when the frame rate was stable and the performance was clean. Trust built in month one created six months of continued work. That's the value of delivering exactly what you promise, on time.

---

## The Result

A fully rebuilt detective VR game for Meta Quest — three investigative scenes, a zombie combat sequence, ReadyPlayer.me avatar characters, a hand magnifier with custom render texture, a physical backpack inventory system, and sustained 90+ FPS on standalone Quest hardware.

From a $800 optimization contract to a six-month rebuild.

That's the arc this project followed. And it started because a frame rate target was met.

---

## Building a VR Game for Meta Quest?

Whether you need performance optimization to hit Quest's frame rate targets, a full game built from concept to deployment, or an existing project rebuilt properly — this is the work we do at WitShells Studio.

Quest optimization. Physical VR interaction design. Avatar integration. Full game development.

📩 [sayedsulaiman607@gmail.com](mailto:sayedsulaiman607@gmail.com) · [cto@witshells.com](mailto:cto@witshells.com)
💬 [WhatsApp Me →](https://wa.me/923093023289)

---

**Tags:** *Meta Quest, VR Game Development, Unity VR, Quest Optimization, Detective VR Game, ReadyPlayer Me, VR Interaction Design, 90 FPS Quest, Standalone VR, WitShells Studio, Fiverr Unity Developer*

---

### About the Author

**Syed Suleman Shah** is CTO of WitShells Studio — a VR and interactive experience studio delivering immersive environments for education, defense, and entertainment clients across Pakistan and the Middle East.

*© 2026 WitShells Studio (SMC-Private) Limited. All rights reserved.*