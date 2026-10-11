---
title: "Lesson 13: Build Your 100-Unit Adventure — Pick a Theme, Design Obstacles and Puzzles, and Connect Menu to Game"
order: 13
description: "A recap-and-tips lesson for your 100-Unit Obstacle Course. Your player moves freely with the third-person controller, so learn how to pick a theme, design fair obstacles, build puzzle gates (including combination puzzles), add a pause menu, save the player name with PlayerPrefs, and move between scenes."
tags: [Unity, Unity6, Third Person Controller, Obstacle Course, Puzzles, Pause Menu, PlayerPrefs, SceneManager, Game Design, Game Theme, Menu, Leaderboard, CSharp, Game Development, Beginner Guide, Unity Tutorial]
---

You have learned a lot in 12 lessons. Now it is time to put it all together in one real game.

This lesson is different from the others. It is **not** a step-by-step build. It is a **recap with tips and tricks**. I will build the examples live in class. This page is your guide to follow along, and to come back to when you are working on your own game.

> **Big idea of this lesson:** A good game is not only code. It has a **theme**, **fair challenges**, and a **clean flow** from the menu to the game to the game over screen. Code is the tool. These three things make the game feel real.

## What We Build in Class

A game with a **100-unit path**. You use the **third-person controller** I gave you. Your player can walk in any direction, jump, dash, and look around with the camera.

This is **not** a game where the player runs forward without stopping. The player can stand still, look around, go back, and **pause**. That changes how you design your game.

- The player starts on one side and must reach the other side.
- On the way there are **obstacles**, and **gates** that stay locked until the player solves a **puzzle**.
- Challenges get harder as the player moves forward.
- A timer counts how fast the player finishes.
- A leaderboard shows the player name and the shortest time.

Here is the full game flow:

```
MENU SCENE                     GAME SCENE
+--------------------+         +--------------------------------+
| Enter your name    |         | Explore the path               |
| [ Start ] [ Quit ] | ------> | Dodge obstacles                |
+--------------------+         | Solve puzzles, open gates      |
          ^                    | Pause any time                 |
          |                    | Reach the finish               |
          |                    +----------------+---------------+
          |                                     |
          |                                     v
          |                    +--------------------------------+
          |                    | GAME OVER PANEL                |
          +------------------- | Your time + Leaderboard        |
            [ Back to Menu ]   | [ Restart ] [ Menu ] [ Quit ]  |
                               +--------------------------------+
```

In this lesson we cover seven things:

1. Why you must pick a **theme**, and how to use it
2. How to design **obstacles** for a player who can move freely
3. How to build **puzzle gates**, including **combination puzzles**
4. How to add a **pause menu**
5. How to build the **menu** and save the player name with **PlayerPrefs**
6. How to **change scenes**
7. Quick tips for the **timer** and the **leaderboard**

## You Already Know Most of This

Look at the table. Almost every part of this game comes from a lesson you have already finished.

| Lesson | What you learned                                  | Where you use it in this game                              |
| ------ | ------------------------------------------------- | ---------------------------------------------------------- |
| 3      | Materials and colors                              | Your theme colors                                          |
| 4      | Colliders, Rigidbody, triggers, physics materials | Obstacles, pressure plates, pushable crates, slippery ice  |
| 5      | Awake, Start, Update, `OnTriggerEnter`            | Every script in the game                                   |
| 6      | C# basics: variables, lists, conditions           | Puzzle counters, sequences, leaderboard                    |
| 7      | New Input System                                  | Player movement, pause key (old input is **not** accepted) |
| 8      | Cinemachine and the third-person controller       | Camera and player                                          |
| 9      | Player mechanics: jump, dash                      | How the player crosses obstacles                           |
| 10     | Animation and Animators                           | Run, jump, and **death animation**                         |
| 11     | Canvas, layouts, planning a screen                | Menu, Pause, and Game Over screens                         |
| 12     | MVC and the worksheet                             | UI scripts for menu, timer, leaderboard                    |

> **Try it:** Pick two rows from this table. Open that old lesson and read the part again. A five-minute refresh now saves an hour of confusion later.

---

## Part 1: Pick a Theme First

### Why a theme matters

Imagine two games. Both have a path, three obstacles, and a locked gate.

- **Game A** has grey cubes on a grey floor. A grey gate. A grey button that says "Start".
- **Game B** is a collapsing lava temple. The floor glows orange. The gate is a heavy stone door. The button says "Enter the Temple".

The code can be exactly the same. But Game B feels like a real game. The player remembers it. Your teacher remembers it too.

A theme gives you three gifts:

| Gift                      | What it means                                                                      |
| ------------------------- | ---------------------------------------------------------------------------------- |
| **Decisions become easy** | You stop asking "what color should this be?" The theme answers for you.            |
| **Everything matches**    | The world, the obstacles, the puzzles, the menu, and the sound feel like one game. |
| **Ideas appear**          | If you know your world, you know what is dangerous in it, and what is locked.      |

### Write one theme sentence

Before you build anything, write **one sentence**. Use this pattern:

> *A [who] travels through a [place] that is [mood]. They must reach [goal].*

Examples:

- A lone explorer travels through a **collapsing lava temple**. They must reach the sun door.
- A robot travels through a **broken space station**. It must reach the escape pod.
- A kid travels through a **haunted forest** at midnight. They must reach the old cabin.

The **goal** in your sentence becomes your finish line. Pick your theme in **10 minutes**. Do not spend a whole day. A simple theme that you finish is better than a perfect theme that you never build.

### Six theme ideas

You can use one of these, or invent your own.

| Theme               | Look and mood                                 | Obstacle ideas                                                                             | Puzzle and gate ideas                                                                       |
| ------------------- | --------------------------------------------- | ------------------------------------------------------------------------------------------ | ------------------------------------------------------------------------------------------- |
| **Lava Temple**     | Dark stone, orange glow, red fog              | Spinning fire blades, crumbling bridge over lava, rolling boulder                          | Push a stone block onto a plate to open the stone door. Light 3 torches in the right order. |
| **Space Station**   | Dark blue and white, cyan lights, stars       | Laser gates that turn on and off, sliding airlock doors, moving platforms over empty space | Collect 3 power cells to open the airlock. Type a door code you found on a wall screen.     |
| **Haunted Forest**  | Dark green and purple, thick fog, moonlight   | Swinging logs, thorn walls that rise and fall, patrolling ghost lights                     | Find 3 lanterns to open the iron gate. Step on grave stones in the right order.             |
| **Candy Factory**   | Pink, mint, and yellow, bright and soft light | Rolling candy balls, conveyor belts, spinning lollipop arms                                | Collect 5 candies to melt the chocolate door. Push the red crate onto the red plate.        |
| **Frozen Mountain** | White and ice blue, cold light, snow          | Falling icicles, slippery ice floor, rolling snowball                                      | Pick up a torch to melt the ice wall. Slide a block across the ice onto a plate.            |
| **Cyber City**      | Black with neon pink and blue, rain and fog   | Laser grids, cars crossing the road, electric floor tiles                                  | Press 3 terminals before a timer ends. Match a code from a street sign.                     |

> **Tip:** **Frozen Mountain** uses something from Lesson 4. A **Physics Material** with low friction makes the floor slippery. Old lessons come back in new ways.

### How to apply your theme

Once you have your sentence, use this checklist. Go from top to bottom.

| Step               | What to do                                           | How                                                                           |
| ------------------ | ---------------------------------------------------- | ----------------------------------------------------------------------------- |
| 1. **Palette**     | Choose 3 main colors and 1 accent color              | Make Materials (Lesson 3). Use only these colors.                             |
| 2. **Light**       | Set the mood with light color and brightness         | Change the Directional Light color and angle.                                 |
| 3. **Fog and sky** | Add fog and a skybox that match the mood             | Window → Rendering → Lighting → Environment tab.                              |
| 4. **Props**       | Fill the world with simple decoration                | Use cubes, cylinders, and spheres in smart ways, or free assets.              |
| 5. **Challenges**  | Make obstacles and puzzles that belong in this world | Ask: "What is dangerous here? What is locked here?"                           |
| 6. **Gates**       | Make every gate look like part of the world          | A stone door, an airlock, a thorn wall, a chocolate door.                     |
| 7. **UI**          | Use the same palette and font in your menu           | Change the colors on your Canvas panels and buttons.                          |
| 8. **Words**       | Write button text in the voice of your world         | "Enter the Temple" instead of "Start". "You Escaped!" instead of "Game Over". |
| 9. **Sound**       | Add one music loop and a few sound effects           | One loop, plus sounds for a hit, a solved puzzle, and a win.                  |
| 10. **Death**      | Make the death feel like the theme                   | Burn in lava, freeze in ice, fade like a ghost.                               |

> **Tip: less is more.** Three colors that match look better than ten colors that fight. If something looks wrong, remove a color. Do not add one.

> **Try it:** Take your plain grey test scene. Do only steps 1 to 3. Change the colors, the light, and the fog. Look at how different the same scene feels after five minutes.

### Let AI build your world

You can ask AI to write a script that builds your environment with **one right-click**. This is the same idea as the screen generator in Lesson 11, now for your game world. Use this prompt. Replace the parts in `[brackets]` with your theme.

```text
Write a Unity 6 C# script called EnvironmentGenerator (MonoBehaviour).

Theme: [your theme sentence, for example "a collapsing lava temple"]
Palette: [3 main colors and 1 accent color]

It must have two [ContextMenu] methods:
1. "Generate Environment"
2. "Clear Environment"

Generate Environment creates everything under a parent object named "Environment":
- A flat ground area, 100 units long and 16 units wide, made from cubes
- Two side walls, 6 units high, along the path, so a player who jumps cannot leave
- A Start zone at one end and a Finish zone at the other end
- The Finish zone has a BoxCollider set to Trigger
- Three empty marker objects named GateSpot_1, GateSpot_2, GateSpot_3
  at 25, 55, and 85 units along the path
- A large trigger box under the whole path, named KillZone
- A directional light, ambient color, and fog that match the theme
- Simple props that match the theme, made only from Unity primitives
- New Materials created in code using the palette (no outside assets)

Rules:
- Use public fields for length, width, and wall height
- Clear Environment deletes the old Environment before generating a new one
- Use Undo.RegisterCreatedObjectUndo so I can press Ctrl+Z
- Short comments that a beginner can understand
```

> **Remember:** AI builds the boring part. **You** choose the theme, review the result, and fix what looks wrong. Just like Lesson 12.

---

## Part 2: Design Obstacles for a Free Player

Your assignment needs **at least 3 obstacles**. If you build fewer than 3, they do not count. An obstacle can be a **hazard** (a spinning blade) or a **puzzle gate** (a door that needs a solution). But three bad obstacles are still bad. Here is how to build good ones.

### Five rules of a good obstacle

| Rule                    | What it means                                       | Example                                                                               |
| ----------------------- | --------------------------------------------------- | ------------------------------------------------------------------------------------- |
| **1. Easy to read**     | The player understands the challenge in one second. | A red spinning blade says "do not touch". A closed door says "find a way to open me". |
| **2. Fair**             | A careful player can always get past it.            | There is always a gap, a safe moment, or a clue.                                      |
| **3. Teach, then test** | The first one of each type is easy and alone.       | One slow blade first. Two fast blades later.                                          |
| **4. One idea each**    | Each obstacle tests one skill.                      | Timing, or jumping, or thinking. Not all at once.                                     |
| **5. Quick to retry**   | Failing must not be a long wait.                    | Respawn in about 2 seconds or less.                                                   |

> **Tip:** If you fail and think "that was unfair", the obstacle is broken. If you fail and think "I can do it next time", the obstacle is great.

### Your player is free, so plan for it

In a runner game, the player is pushed forward. In **your** game, the player can do anything the controller allows. Think like a player who wants to break your level.

| The free player can...                      | Your fix                                                            |
| ------------------------------------------- | ------------------------------------------------------------------- |
| Jump or dash over a low wall                | Make walls **taller** than the highest jump plus dash.              |
| Walk around an obstacle                     | Make the path narrow at that spot, or use walls.                    |
| Stand still and wait                        | Timing obstacles repeat forever, with a fair pattern.               |
| Go back to the start                        | That is fine. Gates that are open stay open.                        |
| Fall off the edge                           | Put a **KillZone** trigger under the path, then respawn the player. |
| Jump over a locked gate                     | The gate must reach **wall to wall**, and be taller than any jump.  |
| Push a crate into a bad place and get stuck | Add walls around it, and a way to reset the crate.                  |

> **Try it:** Play your own level and try to cheat. Jump the walls. Dash past the gate. Every cheat you find is a bug to fix.

### Four building blocks

Almost every hazard in every game is made from these four blocks. Mix them and you get endless ideas.

| Block       | What it does                                | How to build it                             |
| ----------- | ------------------------------------------- | ------------------------------------------- |
| **Move**    | The obstacle slides, swings, or rolls       | `Mathf.PingPong`, or a Rigidbody with force |
| **Rotate**  | The obstacle spins                          | `transform.Rotate(...)` in `Update()`       |
| **Timing**  | The obstacle waits, then acts               | A timer, `Invoke`, or a Coroutine           |
| **Trigger** | The obstacle starts when the player is near | A trigger collider with `OnTriggerEnter`    |

Here is a small script for the **Move** block. It slides any object back and forth. Put it on a wall, a platform, or a laser, and it just works.

```csharp
using UnityEngine;

public class PingPongMover : MonoBehaviour
{
    [SerializeField] private Vector3 direction = Vector3.right;
    [SerializeField] private float distance = 4f;
    [SerializeField] private float speed = 2f;

    private Vector3 startPosition;

    private void Start()
    {
        startPosition = transform.position;
    }

    private void Update()
    {
        // PingPong goes 0 -> distance -> 0 -> distance ...
        float offset = Mathf.PingPong(Time.time * speed, distance);
        transform.position = startPosition + direction.normalized * offset;
    }
}
```

The fields `direction`, `distance`, and `speed` show up in the Inspector. This is how one script becomes many obstacles. A slow wall, a fast wall, and a wide wall are all the same script with different numbers.

> **Try it:** Add `PingPongMover` to a cube. Set the direction to `(0, 1, 0)`. Now it moves up and down. Same script, new obstacle.

### From theme to hazard

Do not start with "what obstacle can I make?" Start with this question:

> **What is dangerous in my world?**

Then turn the answer into one of the four blocks.

| Theme           | What is dangerous?      | Block            | Obstacle                            |
| --------------- | ----------------------- | ---------------- | ----------------------------------- |
| Lava Temple     | The floor is burning    | Timing + Trigger | Crumbling stone tiles over lava     |
| Space Station   | Broken machines         | Timing           | Laser gate that turns on and off    |
| Haunted Forest  | Things hide in the dark | Move             | A ghost light that patrols the path |
| Candy Factory   | Machines never stop     | Move + Rotate    | A conveyor belt with spinning arms  |
| Frozen Mountain | Ice is slippery         | Physics Material | A narrow ice bridge                 |
| Cyber City      | Traffic and electricity | Move + Timing    | Cars crossing, electric tiles       |

### A special idea: the Crumbling Bridge

Most hazards try to hit the player. This one tests **timing** instead. It works in almost any theme: stone tiles over lava, ice over water, metal plates over a void.

- There is a gap in the path.
- A row of tiles makes a bridge over the gap.
- When the player steps on a tile, it **shakes** for a short time, then **falls**.
- The player must keep moving, or use jump and dash, to cross.

| Part                       | What it does                     | Concept                  |
| -------------------------- | -------------------------------- | ------------------------ |
| Trigger on top of the tile | Detects the player               | `OnTriggerEnter`         |
| Short wait                 | Gives the player a moment to run | Coroutine or timer       |
| Shake                      | Warns the player                 | Move the tile a tiny bit |
| Fall                       | The tile drops                   | Rigidbody gravity        |
| Kill zone under the gap    | Catches a falling player         | Trigger and respawn      |

> **Try it:** Change the wait time from 1 second to 0.3 seconds. How does the game feel now?

### Death and respawn tips

When the player is hit or falls, do not just teleport them back. Show what happened.

1. Play the **death animation** (Lesson 10).
2. Wait a short time (1 to 2 seconds).
3. Move the player back to the **last checkpoint**.
4. Reset the player so it can move again.

Three tips:

- **Use the gates as checkpoints.** When the player opens a gate, save that spot as the respawn point. Nobody wants to walk the whole path again.
- **Do not reload the scene when the player dies.** Reloading resets every puzzle. Only move the player.
- If your player uses a **CharacterController**, it can ignore a new position. Turn the component **off**, change the position, then turn it **on** again. If it uses a **Rigidbody**, set the `velocity` to zero when you reset.

---

## Part 3: Puzzle Gates

A **puzzle gate** is a simple idea:

> A gate blocks the path. The player must **solve a puzzle** to open it.

Puzzles slow the player down and make them think. This is a good thing, as long as the puzzle is fair and fun.

### Think in three parts

Use the worksheet from Lesson 12 for every puzzle.

| Question                                | Example: the pressure plate                             |
| --------------------------------------- | ------------------------------------------------------- |
| **1. What is the data?**                | Is the puzzle solved? Yes or no.                        |
| **2. What is the reasoning?**           | When the plate is pressed, the gate opens.              |
| **3. What does the player see and do?** | A plate on the floor, a gate, a light that turns green. |

Notice that a puzzle does not need a screen. The **world** is the UI. A light turning green, a door sliding, and a sound are the feedback.

### Seven puzzle types

| Puzzle             | How it works                                                            | You build it with                                |
| ------------------ | ----------------------------------------------------------------------- | ------------------------------------------------ |
| **Pressure plate** | Stand on a plate, or push a crate onto it                               | Trigger, `OnTriggerEnter` and `OnTriggerExit`    |
| **Collect**        | Find a number of items to open the gate                                 | A counter, and pickups like the Coin in Lesson 5 |
| **Sequence**       | Step on or press things in the right order. The wrong order resets.     | A list or array (Lesson 6)                       |
| **Code**           | A clue is in the world. The player types it on a keypad.                | UI (Lessons 11 and 12), comparing text           |
| **Timed**          | A plate opens the gate for a few seconds. The player must dash through. | Timer or Coroutine, jump and dash (Lesson 9)     |
| **Push (physics)** | Push a heavy block to a spot                                            | Rigidbody mass, Physics Material (Lesson 4)      |
| **Combination**    | Two or more puzzles must **all** be solved                              | A puzzle group (see below)                       |

> **Tip: for the Interact key.** If a puzzle needs the player to press a key (a lever, a button), add an **Interact** action in your Input Actions (Lesson 7). Use a trigger zone to know when the player is close.

### Your first puzzle: plate and gate

We need three small scripts. First, the **gate**. It slides up to open and down to close.

```csharp
using System.Collections;
using UnityEngine;

public class Gate : MonoBehaviour
{
    [SerializeField] private Vector3 openOffset = new Vector3(0f, 6f, 0f);
    [SerializeField] private float moveTime = 1.5f;

    private Vector3 closedPosition;
    private Coroutine moving;

    private void Awake()
    {
        closedPosition = transform.position;
    }

    public void Open()  { MoveTo(closedPosition + openOffset); }
    public void Close() { MoveTo(closedPosition); }

    private void MoveTo(Vector3 target)
    {
        if (moving != null) StopCoroutine(moving);
        moving = StartCoroutine(Slide(target));
    }

    private IEnumerator Slide(Vector3 target)
    {
        Vector3 start = transform.position;
        float t = 0f;

        while (t < 1f)
        {
            t += Time.deltaTime / moveTime;
            transform.position = Vector3.Lerp(start, target, t);
            yield return null;
        }

        transform.position = target;
    }
}
```

Second, the **pressure plate**. It does not know about the gate. It only **announces** "I was pressed" and "I was released". This is the same idea as events in Lesson 12: *don't ask, get told*.

```csharp
using UnityEngine;
using UnityEngine.Events;

[RequireComponent(typeof(Collider))]
public class PressurePlate : MonoBehaviour
{
    [SerializeField] private string requiredTag = "Player";
    [SerializeField] private UnityEvent onPressed;
    [SerializeField] private UnityEvent onReleased;

    private int objectsOnPlate;

    private void Reset()
    {
        GetComponent<Collider>().isTrigger = true;
    }

    private void OnTriggerEnter(Collider other)
    {
        if (!other.CompareTag(requiredTag)) return;

        objectsOnPlate++;
        if (objectsOnPlate == 1) onPressed.Invoke();
    }

    private void OnTriggerExit(Collider other)
    {
        if (!other.CompareTag(requiredTag)) return;

        objectsOnPlate--;
        if (objectsOnPlate == 0) onReleased.Invoke();
    }
}
```

Look at the `UnityEvent` fields. They look just like the **On Click ()** list on a Button. You connect them in the Inspector, without writing more code.

**Set it up:**

1. Make a **Gate**: a tall cube that fills the path from wall to wall. Add the `Gate` script.
2. Make a **plate**: a flat cube with a Box Collider. Add `PressurePlate`. (The collider becomes a trigger.)
3. Select the plate. In **On Pressed ()** click **+**. Drag in the Gate. Choose **Gate → Open**.
4. In **On Released ()** click **+**. Drag in the Gate. Choose **Gate → Close**.
5. Press Play and step on the plate.

The gate opens while you stand on the plate and closes when you leave. That is a "hold" puzzle. To make it a **crate puzzle**, change `requiredTag` to `Crate`, tag a pushable cube as `Crate`, and push it onto the plate. The crate holds the plate down, and the player walks through.

> **Tip:** Your player needs a **Rigidbody** or a **CharacterController** for triggers to work, and the right **Tag**. If the player's collider is on a child object, tag that child.

### Combination puzzles

A combination puzzle means **more than one puzzle must be solved**. For example:

> The gate opens only when the plate is pressed **and** all 3 keys are collected.

We make this with a small **puzzle group**. Each puzzle reports "I am solved". When enough puzzles are solved, the group opens the gate.

```csharp
using UnityEngine;
using UnityEngine.Events;

public class PuzzleGroup : MonoBehaviour
{
    [SerializeField] private int requiredCount = 2;
    [SerializeField] private UnityEvent onAllSolved;
    [SerializeField] private UnityEvent onNoLongerSolved;

    private int solvedCount;

    public void ReportSolved()
    {
        solvedCount++;
        if (solvedCount == requiredCount) onAllSolved.Invoke();
    }

    public void ReportUnsolved()
    {
        bool wasSolved = solvedCount >= requiredCount;
        solvedCount = Mathf.Max(0, solvedCount - 1);
        if (wasSolved && solvedCount < requiredCount) onNoLongerSolved.Invoke();
    }
}
```

**The wiring looks like this:**

```
Plate  ---- On Pressed ---->  PuzzleGroup.ReportSolved
Key 1  ---- On Collected -->  PuzzleGroup.ReportSolved
Key 2  ---- On Collected -->  PuzzleGroup.ReportSolved

PuzzleGroup (Required Count = 3)
   |-- On All Solved ---->  Gate.Open
```

The same group works for every kind of combination. Two plates at the same time. Five collectibles. A plate plus a code. You only change the **Required Count** and the wiring.

> **Try it:** Make a gate with a group of 2 plates, placed far apart. How do you press both at once? (Hint: a crate on one, you on the other.)

### Puzzle design rules

| Rule                           | What it means                                                                                       |
| ------------------------------ | --------------------------------------------------------------------------------------------------- |
| **Show the goal**              | The player must see that the gate is locked. Use a red light for locked and a green light for open. |
| **Give feedback**              | Play a sound or a light when progress happens (a key collected, a plate pressed).                   |
| **Put the clue near the gate** | The player should not have to guess. A clue on a wall is fair. A clue 80 units away is not.         |
| **No dead ends**               | A pushed crate must never block the level forever. Add walls or a reset.                            |
| **Keep it short**              | The **timer keeps running** while the player thinks. A good puzzle takes 15 to 60 seconds.          |
| **Teach, then combine**        | First gate: one simple puzzle. Last gate: a combination.                                            |

### Plan your gates along the path

Use the four zones. Each zone has obstacles **and** a gate. The challenge grows in both.

| Zone          | Distance | Obstacles                                        | Gate                                           |
| ------------- | -------- | ------------------------------------------------ | ---------------------------------------------- |
| **Learn**     | 0 – 25   | One easy hazard, alone, with plenty of space     | **Gate 1:** one simple puzzle (a plate)        |
| **Practice**  | 25 – 55  | Two or three of the same hazard, a little faster | **Gate 2:** a different type (collect 3 items) |
| **Challenge** | 55 – 85  | Two different hazards close together             | **Gate 3:** a combination puzzle               |
| **Master**    | 85 – 100 | Fast and tight. The last test before the finish. | The finish line                                |

The layout looks like this:

```
Start --- hazard --- [Gate 1] --- hazards --- [Gate 2] --- hazards + puzzle --- [Gate 3] --- final hazards --- Finish
  0                    25                       55                                 85                           100
```

Always leave a **small safe spot** after each gate, so the player can breathe.

To make a hazard or a puzzle harder, turn one of these knobs. Change **one** at a time.

| Knob              | Easier                     | Harder                                     |
| ----------------- | -------------------------- | ------------------------------------------ |
| **Speed**         | Slow                       | Fast                                       |
| **Gap**           | Wide space to pass         | Narrow space                               |
| **Timing window** | Long wait                  | Short wait                                 |
| **Combination**   | One obstacle or one puzzle | Two close together, or two puzzles at once |
| **Clues**         | A big clear clue           | A small clue, or no clue                   |

> **Tip: playtest it.** Play your own level at least 10 times. Then give it to a friend. Where does your friend get stuck the longest? That might be too hard. Where does your friend never struggle? It might be too easy.

---

## Part 4: Add a Pause Menu

Because the player moves freely and thinks about puzzles, a **pause menu** makes your game feel finished. The player can stop, take a breath, and come back.

### How pause works

Unity has one setting that controls the speed of time: `Time.timeScale`.

| Value | What happens                                             |
| ----- | -------------------------------------------------------- |
| `1`   | Normal time                                              |
| `0`   | Time stops. Anything that uses `Time.deltaTime` freezes. |

Because the gate script and your timer use `Time.deltaTime`, they **freeze by themselves** when the game is paused. This is why we do not use `Time.unscaledDeltaTime` for the game timer.

### A simple pause script

Make a **Pause** action in your Input Actions (Lesson 7), and bind it to the **Escape** key (and the **Start** button on a gamepad).

```csharp
using UnityEngine;
using UnityEngine.InputSystem;

public class PauseController : MonoBehaviour
{
    [SerializeField] private InputActionReference pauseAction;
    [SerializeField] private GameObject pausePanel;

    private bool isPaused;

    private void OnEnable()
    {
        pauseAction.action.Enable();
        pauseAction.action.performed += OnPausePerformed;
    }

    private void OnDisable()
    {
        pauseAction.action.performed -= OnPausePerformed;
    }

    private void Start()
    {
        SetPaused(false);   // always start the game unpaused
    }

    private void OnPausePerformed(InputAction.CallbackContext context)
    {
        SetPaused(!isPaused);
    }

    // Call this from the Resume button too
    public void SetPaused(bool paused)
    {
        isPaused = paused;
        Time.timeScale = paused ? 0f : 1f;
        pausePanel.SetActive(paused);

        // Show the mouse while paused so the buttons can be clicked
        Cursor.lockState = paused ? CursorLockMode.None : CursorLockMode.Locked;
        Cursor.visible = paused;
    }
}
```

Your pause panel can have three buttons: **Resume**, **Restart**, and **Back to Menu**. Build it like the menu in Part 5, with a View and a Controller.

### Pause tips

| Tip                                               | Why                                                                                                         |
| ------------------------------------------------- | ----------------------------------------------------------------------------------------------------------- |
| Reset `Time.timeScale = 1f` when any scene starts | If you leave the game while paused, the next scene stays frozen.                                            |
| Your controller may already lock the cursor       | If it does, remove the two `Cursor` lines for the "unpaused" case, or the two scripts will fight.           |
| Test that the **camera** stops turning            | Some camera inputs keep working at `timeScale = 0`. If yours does, disable the player's input while paused. |
| The timer stops while paused                      | Count time with `Time.deltaTime` and only in the **Playing** state.                                         |

---

## Part 5: Build the Menu

### Plan on paper first

Remember Lesson 11: plan before you build. Remember Lesson 12: start with the worksheet, not with the UI.

| Question                              | Menu answer                                                                                                 |
| ------------------------------------- | ----------------------------------------------------------------------------------------------------------- |
| **1. What is the data?**              | The player name (text)                                                                                      |
| **2. What is the reasoning?**         | The name is never empty. It has a maximum length (for example, 12 letters). We remember the last name used. |
| **3. What does the user see and do?** | A title, a name input, a Start button, a Quit button                                                        |

### Build checklist

Use the Canvas checklist from Lesson 11. Do it every time.

- [ ] A **Canvas** exists, set to **Screen Space – Overlay**.
- [ ] Canvas Scaler is on **Scale With Screen Size**, **1920 × 1080**.
- [ ] There is exactly **one EventSystem**.
- [ ] Your buttons live inside a panel with a **Vertical Layout Group**.

Then add your elements. For the name box, right-click the Canvas → **UI → Input Field – TextMeshPro**.

| Element          | Type (Lesson 11) |
| ---------------- | ---------------- |
| Game title       | Display          |
| Name input field | Text Input       |
| Start button     | Action           |
| Quit button      | Action           |

> **Tip:** Apply your theme now. Same palette, same font, and button words in the voice of your world. A menu is the first thing the player sees.

### Save the player name with PlayerPrefs

The player types a name in the Menu scene. But the leaderboard needs that name in a **different scene**. When Unity loads a new scene, all objects in the old scene are destroyed. So where does the name live?

The easy answer is **PlayerPrefs**.

> **Do not mix them up.** It is **PlayerPrefs** (short for *Player Preferences*). It is **not** *Prefabs*. A Prefab is a saved GameObject. PlayerPrefs is a small saved value, like a name or a number.

PlayerPrefs is a tiny storage box on the player's computer. You save a value with a **key** (a label), and you get it back later with the same key. It stays there even after the game closes.

| Method                                    | What it does                                      |
| ----------------------------------------- | ------------------------------------------------- |
| `PlayerPrefs.SetString("key", value)`     | Save text                                         |
| `PlayerPrefs.GetString("key", "default")` | Load text. If nothing is saved, give the default. |
| `PlayerPrefs.SetInt` / `GetInt`           | Save and load a whole number                      |
| `PlayerPrefs.SetFloat` / `GetFloat`       | Save and load a decimal number                    |
| `PlayerPrefs.Save()`                      | Write it to disk now                              |
| `PlayerPrefs.DeleteKey("key")`            | Remove one saved value                            |
| `PlayerPrefs.DeleteAll()`                 | Remove everything (great for testing)             |

Use PlayerPrefs for **small things**: a name, volume, a best time. Do not use it for big data.

Remember the rule from Lesson 12: **the Model holds the data and its rules.** Our rule is "never empty, never too long". So we put that rule in one small script.

```csharp
using UnityEngine;

public static class PlayerNameStore
{
    private const string Key = "player_name";
    public const int MaxLength = 12;

    public static string Get()
    {
        return PlayerPrefs.GetString(Key, "Player");
    }

    public static void Set(string playerName)
    {
        // Rule 1: remove spaces at the start and end
        playerName = (playerName ?? "").Trim();

        // Rule 2: never empty
        if (playerName.Length == 0) playerName = "Player";

        // Rule 3: never too long
        if (playerName.Length > MaxLength)
            playerName = playerName.Substring(0, MaxLength);

        PlayerPrefs.SetString(Key, playerName);
        PlayerPrefs.Save();
    }
}
```

Any script, in any scene, can now call `PlayerNameStore.Get()` and know the name. The leaderboard will use it. The Game Over screen will use it. Nobody needs to touch the UI.

### The menu in MVC

Now the View and the Controller. The **View** holds the UI and reports what the user did. The **Controller** decides what happens.

**MenuView.cs** — it only shows and reports.

```csharp
using System;
using TMPro;
using UnityEngine;
using UnityEngine.UI;

public class MenuView : MonoBehaviour
{
    [SerializeField] private TMP_InputField nameInput;
    [SerializeField] private Button startButton;
    [SerializeField] private Button quitButton;

    // The View reports what the user did. It does not decide anything.
    public event Action<string> StartPressed;
    public event Action QuitPressed;

    private void Awake()
    {
        startButton.onClick.AddListener(OnStartClicked);
        quitButton.onClick.AddListener(OnQuitClicked);
    }

    private void OnDestroy()
    {
        startButton.onClick.RemoveListener(OnStartClicked);
        quitButton.onClick.RemoveListener(OnQuitClicked);
    }

    private void OnStartClicked() => StartPressed?.Invoke(nameInput.text);
    private void OnQuitClicked() => QuitPressed?.Invoke();

    public void ShowName(string playerName)
    {
        nameInput.SetTextWithoutNotify(playerName);
    }
}
```

**MenuController.cs** — it connects the View, the saved name, and the scene change.

```csharp
using UnityEngine;
using UnityEngine.SceneManagement;

public class MenuController : MonoBehaviour
{
    [SerializeField] private MenuView view;
    [SerializeField] private string gameSceneName = "Game";

    private void Start()
    {
        // The menu needs a free mouse cursor and normal time
        Cursor.lockState = CursorLockMode.None;
        Cursor.visible = true;
        Time.timeScale = 1f;

        view.StartPressed += OnStartPressed;
        view.QuitPressed += OnQuitPressed;

        // Show the last saved name
        view.ShowName(PlayerNameStore.Get());
    }

    private void OnDestroy()
    {
        view.StartPressed -= OnStartPressed;
        view.QuitPressed -= OnQuitPressed;
    }

    private void OnStartPressed(string typedName)
    {
        PlayerNameStore.Set(typedName);          // save the name
        SceneManager.LoadScene(gameSceneName);   // go to the game
    }

    private void OnQuitPressed()
    {
        Application.Quit();

#if UNITY_EDITOR
        // Application.Quit() does nothing inside the Editor,
        // so we stop Play Mode ourselves.
        UnityEditor.EditorApplication.isPlaying = false;
#endif
    }
}
```

Look at the pattern. It is the same as Lesson 12: **Awake creates, Start connects, OnDestroy disconnects.**

> **Try it:** Press Play, type a name, press Start, and stop the game. Press Play again. Your name is already in the box. That is PlayerPrefs working. Now call `PlayerPrefs.DeleteAll()` once and see the name disappear.

---

## Part 6: Change Scenes

A **scene** is one screen of your game: the menu, the level, and so on. For this game you need two scenes:

| Scene    | What is inside                                                                                   |
| -------- | ------------------------------------------------------------------------------------------------ |
| **Menu** | Menu Canvas, MenuController                                                                      |
| **Game** | The path, player, camera, obstacles, gates, puzzles, timer, a Pause panel, and a Game Over panel |

### Step 1: Add the scenes to the build

Unity can only load a scene that is in the build list. If you forget this, you get an error.

1. Save both scenes in a folder called `Scenes`.
2. Open **File → Build Profiles** and find the **Scene List**. (In older Unity versions this is **File → Build Settings**.)
3. Click **Add Open Scenes** for each scene.
4. Put **Menu** first. The first scene is the one that opens when the game starts.

### Step 2: Load a scene with code

You need one `using` line, and then one method.

```csharp
using UnityEngine.SceneManagement;

SceneManager.LoadScene("Game");   // load by name
SceneManager.LoadScene(0);        // load by number in the build list
```

For the Game Over and Pause buttons, you need three actions.

| Button           | Code                                                          |
| ---------------- | ------------------------------------------------------------- |
| **Restart**      | `SceneManager.LoadScene(SceneManager.GetActiveScene().name);` |
| **Back to Menu** | `SceneManager.LoadScene("Menu");`                             |
| **Quit**         | `Application.Quit();`                                         |

> **Tip:** Show the Game Over screen as a **panel inside the Game scene**. It is simpler than a third scene. Hide it at the start, and show it when the player reaches the finish. **Restart** reloads the scene, so every puzzle and gate resets by itself. This is different from dying, where we do **not** reload.

### Scene problems everyone meets

| Problem                                                                        | Why it happens                                                                | Fix                                                                                     |
| ------------------------------------------------------------------------------ | ----------------------------------------------------------------------------- | --------------------------------------------------------------------------------------- |
| "Scene couldn't be loaded because it has not been added to the build settings" | The scene is not in the Scene List                                            | Add it (Step 1)                                                                         |
| Nothing loads, no error                                                        | The name has a typo or a different capital letter                             | Copy the exact scene name                                                               |
| The mouse cursor is missing in the menu                                        | The game scene locked the cursor                                              | Set `Cursor.lockState = None` and `Cursor.visible = true` in menu, pause, and game over |
| The game is frozen after restart                                               | You set `Time.timeScale = 0` for pause or game over and never changed it back | Set `Time.timeScale = 1f` when a scene starts                                           |
| Buttons do nothing                                                             | No EventSystem, or the wrong input module                                     | See the troubleshooting table at the end                                                |
| The name is empty in the Game scene                                            | You never saved it                                                            | Save it with PlayerPrefs **before** `LoadScene`                                         |

---

## Part 7: Timer and Leaderboard Tips

We will build these live. Here are the tips to remember.

### Timer tips

- The game has four states: **Waiting**, **Playing**, **Paused**, **Finished**.
- The timer only counts while the state is **Playing**.
- Count with `elapsed += Time.deltaTime;` inside `Update()`. When `timeScale` is 0, this stops by itself.
- Stop the timer when the player touches the **finish trigger**. Use a trigger, not a normal collision, because the player should pass through the finish line, not bump into it.
- Remember: the timer also counts **thinking time** at puzzles. That is fair. Fast thinkers get better times.
- Show the time as minutes, seconds, and hundredths so it looks clean.
- Use MVC: the Model holds `ElapsedTime`, the View only shows it.

### Leaderboard tips

Each entry has two things: a **name** and a **time**.

1. Load the old list.
2. Add the new result.
3. Sort by the **shortest** time.
4. Keep only the top 5.
5. Save it again.

PlayerPrefs cannot save a list directly. So we turn the list into text with **JSON**. Unity has a tool for this: `JsonUtility`. There is one catch. `JsonUtility` cannot save a list on its own. The list must sit **inside a class**.

```csharp
using System;
using System.Collections.Generic;
using UnityEngine;

[Serializable]
public class ScoreEntry
{
    public string name;
    public float time;
}

[Serializable]
public class ScoreBoard
{
    public List<ScoreEntry> entries = new List<ScoreEntry>();
}

public static class LeaderboardStore
{
    private const string Key = "leaderboard";
    private const int MaxEntries = 5;

    public static ScoreBoard Load()
    {
        string json = PlayerPrefs.GetString(Key, "");
        if (string.IsNullOrEmpty(json)) return new ScoreBoard();
        return JsonUtility.FromJson<ScoreBoard>(json);
    }

    public static void Add(string playerName, float time)
    {
        ScoreBoard board = Load();
        board.entries.Add(new ScoreEntry { name = playerName, time = time });

        // Shortest time first
        board.entries.Sort((a, b) => a.time.CompareTo(b.time));

        // Keep only the best 5
        if (board.entries.Count > MaxEntries)
            board.entries.RemoveRange(MaxEntries, board.entries.Count - MaxEntries);

        PlayerPrefs.SetString(Key, JsonUtility.ToJson(board));
        PlayerPrefs.Save();
    }
}
```

> **Tip:** To show the list on screen, create **one row prefab** (a name text and a time text). Then spawn one row for each entry inside a **Vertical Layout Group**. This uses Lesson 11 and prefabs together.

---

## When Something Does Not Work

| What you see                                | Check this first                                                                                                                                                                  |
| ------------------------------------------- | --------------------------------------------------------------------------------------------------------------------------------------------------------------------------------- |
| Buttons do not respond                      | Is there **one** EventSystem? With the **new Input System** only, select the EventSystem and use the button that replaces the old input module with **InputSystemUIInputModule**. |
| `OnTriggerEnter` never fires                | Is **Is Trigger** checked? Does the player have a **Rigidbody** or **CharacterController**? (Lesson 5)                                                                            |
| The plate does nothing                      | Is the **Tag** right (`Player` or `Crate`)? Is the plate collider a trigger? Is the **On Pressed ()** list connected?                                                             |
| The gate does not open                      | Did you pick **Gate → Open** in the list, and drag the **scene** Gate object, not a prefab file?                                                                                  |
| The player jumps over the gate              | The gate must go wall to wall and be taller than any jump.                                                                                                                        |
| Combination gate opens too early            | Check **Required Count**. Does any puzzle call `ReportSolved` twice?                                                                                                              |
| Puzzles reset after the player dies         | You reloaded the scene. Only move the player to the checkpoint.                                                                                                                   |
| Player does not move back to the checkpoint | CharacterController? Turn it off, move, turn it on. Rigidbody? Reset the velocity.                                                                                                |
| Pause does not freeze the camera            | Disable the player's input while paused.                                                                                                                                          |
| The timer keeps running during pause        | Use `Time.deltaTime`, and only count in the **Playing** state.                                                                                                                    |
| Name is lost between scenes                 | Save with PlayerPrefs **before** `LoadScene`.                                                                                                                                     |
| Leaderboard is empty after restart          | Did you call `PlayerPrefs.Save()`? Is the list inside a class for `JsonUtility`?                                                                                                  |
| UI looks different on another screen        | Canvas Scaler: **Scale With Screen Size**, 1920 × 1080 (Lesson 12)                                                                                                                |

---

## Self-Check Questions

1. Why do we write a theme sentence before we build anything?
2. Why must you design differently when the player can move freely?
3. What are the four hazard building blocks?
4. What question helps you turn a theme into a hazard?
5. A puzzle gate needs three things from the MVC worksheet. What are they?
6. What does a `UnityEvent` do in `PressurePlate`? Where have you seen something like it before?
7. How does a **combination puzzle** know when to open the gate?
8. Why does setting `Time.timeScale = 0` stop the timer and the gate at the same time?
9. What is the difference between PlayerPrefs and Prefabs?
10. Why do we save the player name before calling `LoadScene`?
11. Why should the player respawn at a checkpoint without reloading the scene?
12. Why can `JsonUtility` not save a plain list, and how do we fix it?

---

## Practice Tasks

### Easy

- Write your theme sentence and choose your palette (3 main colors and 1 accent). Apply it to your scene with materials, light, and fog.
- Build your menu with a name input, a Start button, and a Quit button. Make it match your theme.
- Use `PlayerPrefs` so the last name appears in the box when you open the game again.
- Build **one plate and gate**. Step on the plate and watch the gate open.

### Medium

- Build **three** challenges that fit your theme. Mix hazards and puzzle gates, and use at least **two different** types.
- Place them in the four zones so the challenge grows from 0 to 100.
- Connect Menu → Game → Game Over with scene loading. Test Restart, Back to Menu, and Quit.
- Add a **pause menu** with Resume, Restart, and Back to Menu.

### Hard

- Build a **combination puzzle gate** that needs at least two different puzzle types (for example: a plate **and** 3 collected items).
- Add **checkpoints**: when a gate opens, save that spot. When the player dies, play the **death animation** and respawn there, without reloading the scene.
- Build the timer and the leaderboard. Split every UI script into **Model, View, and Controller**.

---

## Your Assignment Checklist

Before you submit, check every item.

- [ ] My game has a clear **theme**, and the world, challenges, and UI all match it.
- [ ] The player moves with the **third-person controller**, and the level works for a player who can move freely.
- [ ] There are **at least 3 obstacles** (hazards or puzzle gates), and they get harder along the path.
- [ ] The path is **100 units** long, with a start and a finish.
- [ ] My gates and walls cannot be skipped by jumping or dashing.
- [ ] The **timer** starts when the game starts, stops at the finish, and does not run while paused.
- [ ] The flow works: **Menu (name + start) → Game → Game Over (leaderboard, restart, back to menu, quit)**.
- [ ] The leaderboard shows the **player name** and the **shortest time**.
- [ ] I use **Rigidbody**, **colliders**, and **OnTriggerEnter / OnTriggerExit** properly.
- [ ] I use **animations**, including a death animation.
- [ ] My UI scripts follow **MVC**.
- [ ] I use only the **new Input System**. Old input is not accepted.
- [ ] All my scripts are inside one **Scripts** folder.
- [ ] I will submit on **Moodle**: one **gameplay video** and the **Scripts folder**.

**Bonus ideas that make a game stronger:** a combination puzzle, a pause menu, checkpoints at gates, and sound for every important moment.

## Key Takeaways

- **Pick a theme first.** One sentence, ten minutes. It makes every other decision easier.
- A **free player** can jump, dash, wait, and go back. Design walls, gates, and kill zones for that.
- A good challenge is **easy to read**, **fair**, and **quick to retry**.
- A **puzzle gate** is simple: solve the puzzle, the gate opens. The puzzle only **announces** that it is solved. It does not know about the gate.
- A **combination puzzle** counts solved puzzles. When the count is enough, the gate opens.
- Make the difficulty grow in **zones**, and turn **one knob at a time**.
- `Time.timeScale = 0` pauses the game. Always set it back to `1` when a scene starts.
- **PlayerPrefs** is a small storage box. Use it for names, settings, and scores. Save the name **before** you change the scene.
- Scenes must be in the **build list**, and the name must match exactly.
- Plan with the **MVC worksheet** first: data, reasoning, UI.

Now pick your theme, open Unity, and start building. See you in class.

---

**Contracted by NextSkill**
*a Gamestorms company, delivering this course for Isra University*

![NextSkill Isra Me sponsor banner](/images/nexskill-isra-me.png)