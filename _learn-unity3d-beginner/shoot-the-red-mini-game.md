---
title: "Save the Humans: Tap to Shoot, Interfaces and the Vertical Slice"
order: 15
tags: ["unity", "beginner", "vertical-slice", "input-system", "raycast", "interfaces", "mobile-game", "android", "game-design", "zombie"]
description: "Build a complete tap-to-shoot zombie game from a fresh project. Learn tap input that works on touch and mouse, raycasts, interfaces and a simple chase. Then build it for Android."
---

# Save the Humans: Tap to Shoot, Interfaces and the Vertical Slice

## What you will build

The zombies are coming. A group of humans is running toward the safe line, right next to you. Behind each human, a hungry zombie is running **faster**. You have one job: **tap the zombies and shoot them before they catch the humans.**

- **White** cylinders are humans.
- **Red** cylinders are zombies.
- Humans run toward you and the **safe line**. Every human who crosses it is saved.
- Zombies chase the human in front of them. If a zombie catches a human, the human is eaten and you **lose a life**.
- If you shoot a human by mistake, you also **lose a life**.
- You have **3 lives**.
- Lose all 3 lives and the game is over.
- If every human has either crossed the line or been lost, and you still have a life, **you win**.

The camera does not move. The game is simple on purpose. A simple game is a game you can finish today, and a finished game is a game you can put on a phone.

By the end of this lesson you can:

- Explain what a vertical slice is and write a slice plan for a game.
- Build a new URP project with a working game loop.
- Write tap input that works with a mouse **and** a touch screen.
- Use one **interface** for two kinds of characters.
- Make a simple **chase** with one line of code.
- Build the game for Android (or for PC, if Android is not installed on your lab computer).

> **Fresh PC today?** Good. This lesson starts from an empty project. You do not need anything from earlier lessons.

---

## Before we code: the vertical slice

Game studios do not build a whole game and hope it works. They first build a **vertical slice**.

A vertical slice is a **small piece of the game that works from start to finish**. Think of a cake. If you cut it from top to bottom, you see every layer, but you only have a thin piece. A vertical slice shows every layer of the game (input, rules, screen, win and lose, restart, a real device) but only a little content.

|                         | Prototype        | Vertical slice                       | Full game              |
| ----------------------- | ---------------- | ------------------------------------ | ---------------------- |
| **Size**                | One idea         | A small, complete path               | Everything             |
| **Looks**               | Ugly is fine     | Looks like the real thing            | Final                  |
| **Question it answers** | "Can this work?" | "Is this fun, and can we finish it?" | "Is it ready to ship?" |

**Today, our whole game is a vertical slice.** It is small, but a player can start it, play it, win or lose, and play again.

### The slice plan for Save the Humans

A studio writes the plan *before* building. Here is ours. You can use this shape for your own game ideas.

**Goal of the slice:** prove that tapping to shoot zombies is fun for a short round, and that the whole game works from start to restart on a real device.

**The slice flow.** This is the exact path a player takes. We build toward this path, and we test by walking along it.

| Step             | What the player sees or does                                                                                                                  | What it proves                                | Built in          |
| ---------------- | --------------------------------------------------------------------------------------------------------------------------------------------- | --------------------------------------------- | ----------------- |
| 1. Start         | An empty road with a green safe line. The screen shows 3 lives and "Saved: 0 / 8". The first human appears far away, with a zombie behind it. | The scene, spawner and HUD work               | Stages 1, 2 and 4 |
| 2. First shot    | The zombie gets close to the human. The player taps the zombie. It disappears.                                                                | Tap input, raycast and interface work         | Stage 3           |
| 3. First mistake | A zombie catches a human, or the player taps a human by mistake. Lives go from 3 to 2.                                                        | The lose-a-life rule works, and there is risk | Stage 4           |
| 4. Pressure      | A new human and zombie appear every few seconds. The player must choose who to shoot first.                                                   | The game stays tense                          | Stage 2           |
| 5a. Win          | All humans have crossed the line or been lost, and the player still has a life. "You saved 6 of 8 humans!" appears.                           | The win rule works                            | Stage 4           |
| 5b. Lose         | Lives reach 0. "The zombies won" appears.                                                                                                     | The lose rule works                           | Stage 4           |
| 6. Restart       | The player taps Restart and a fresh round begins.                                                                                             | The loop repeats                              | Stage 4           |
| 7. On a device   | The same game runs on a phone (or a PC build).                                                                                                | It is real, not just in the editor            | Stage 5           |

**In the slice and out of the slice**

| In the slice (we build today)            | Out of the slice (the full game, later) |
| ---------------------------------------- | --------------------------------------- |
| 1 road, fixed camera                     | More places and levels                  |
| 2 kinds of characters (human and zombie) | Fast zombies, big zombies, soldiers     |
| Tap to shoot, 3 lives                    | Ammo, reload, power-ups                 |
| Win, lose and restart                    | Stars, missions, a story                |
| HUD with lives and saved humans          | Settings, shop, daily rewards           |
| Android or PC build                      | Play Store release                      |

The rule: **if it is not in the left column, we do not build it today.** This is how studios finish games. They say no to extra things until the slice works.

**Success criteria.** How do we know the slice is done?

- A new player understands the game without anyone explaining it.
- You can win and lose, and Restart always works.
- No errors appear in the Console.
- The game runs on a device, not only in the editor.
- A friend plays it for 2 minutes and wants one more round.

### Where would ads go?

A good plan also decides where ads go **before** the game is built. We will not build ads today. We only plan them. Here is a plan for Save the Humans:

| Where                           | Ad type                    | When                                                         | Rule                                    |
| ------------------------------- | -------------------------- | ------------------------------------------------------------ | --------------------------------------- |
| After Game Over                 | Rewarded ad                | The player taps "Watch an ad to try again with 1 extra life" | The player chooses. It is never forced. |
| Between rounds                  | Interstitial (full screen) | When the player taps Restart, but only every 3rd round       | Never in the first 2 minutes of play    |
| Main menu (if we add one later) | Banner                     | Menu only                                                    | Never during play                       |

Three rules that studios keep:

1. **Never show an ad while the player is in the middle of a round.** It feels unfair.
2. **A rewarded ad always gives something the player wants.** Here, one more life.
3. **If an ad fails to load, the game just continues.** The player never sees an error.

> **Try it:** Look at the slice flow table again. Which step is the best moment for an ad, and which is the worst? Why?

Keep this table open while you work. After each stage, ask yourself: *"Which step of the slice did I just finish?"*

---

## Part 1: The four ideas

### Idea 1: Tap input

A mouse click and a finger tap are almost the same thing. Both say: "the user pressed **here** on the screen."

Unity's Input System has a thing called a **Pointer**. A Pointer can be a mouse, a finger, or a pen. If we ask for `Pointer.current`, Unity gives us whichever one the user used last. So one script works on the PC and on the phone. You already used `Pointer.current` in the tap-to-move script in Lesson 14.

### Idea 2: Raycast

A tap gives us a point on a flat screen. But our game is 3D. How do we find which character is under the finger?

Think of a **laser pointer**. You hold it at the screen where your finger touches. The laser goes straight into the 3D world and stops at the first thing it touches. That laser is a **ray**. Casting the ray is a **raycast**.

```
finger on screen  ──►  ray goes into the world  ──►  first object it hits
```

### Idea 3: Interface

We have two kinds of characters: humans and zombies. The shooter does not want to ask "is this a human or a zombie?" It only wants to ask: **"Can you be hit?"**

An **interface** is a promise. Think of a **work badge**. The guard at the door does not care who you are. He only checks that you have a badge.

Our badge is called `IDamageable`. Any script that wears the badge promises to have a `Hit()` method. The shooter calls `Hit()` and does not care who it hit. The human and the zombie each decide what happens when they are hit.

There is a bonus. The zombie will also use `Hit()` to **eat** a human. `Hit()` just means "something hurt you." It does not matter if the player or a zombie did it.

### Idea 4: A simple chase

How does a zombie follow a human? Each frame, it takes a small step **toward** the human. Unity has a method for this:

```csharp
transform.position = Vector3.MoveTowards(transform.position, target.position, step);
```

`MoveTowards` says: "Start here, aim at that point, move this far." Do it every frame and the zombie follows its target wherever it goes. It is a chase in one line.

In Lesson 14 the zombie used a NavMeshAgent. Here the road is flat and open, so `MoveTowards` is enough.

| Word            | Meaning                                                                                 |
| --------------- | --------------------------------------------------------------------------------------- |
| **Pointer**     | A mouse, a finger, or a pen. One script can read all three.                             |
| **Ray**         | An invisible line that starts at the camera and goes through the tap point.             |
| **Raycast**     | Sending the ray out and asking what it touched first.                                   |
| **Interface**   | A promise: "I will have these methods."                                                 |
| **Collider**    | The invisible shape that a raycast can touch. No collider, no hit.                      |
| **MoveTowards** | Moves a point a small step toward another point.                                        |
| **Coroutine**   | A method that can wait in the middle, for example "spawn, wait 2 seconds, spawn again". |

---

## Part 2: The stages

Work stage by stage. If you fall behind, ask for the scripts of the stage you missed and keep going.

| Stage | Goal                         |
| ----- | ---------------------------- |
| 1     | Project, platform and scene  |
| 2     | Humans run, zombies chase    |
| 3     | Tap to shoot                 |
| 4     | Lives, win, lose and restart |
| 5     | Build and play on a device   |

---

## Stage 1: Project, platform and scene

### Create the project

1. Open **Unity Hub**.
2. Click **New project**.
3. Pick the **Universal 3D** template (this is URP).
4. Name it `SaveTheHumans` and click **Create project**.

### Choose the platform

1. Go to **File > Build Profiles**.
2. Look for **Android** in the list.
3. If you can see it, select it and click **Switch Profile**.
4. If Android is **not installed**, do **not** wait for it. Stay on **PC**. You can do the Android build again at home.

> **Why switch early?** Switching platform makes Unity reimport assets, and that can take time. It is better to do it while the project is empty.

### Check the input setting

1. Go to **Edit > Project Settings > Player**.
2. Open **Other Settings**.
3. Find **Active Input Handling**. It should be **Input System Package (New)** or **Both**.

If it says **Input Manager (Old)**, change it. Unity will ask to restart. Say yes.

### Build the scene

Our road runs **away** from the camera. Humans start far away and run toward you.

1. In the Hierarchy, keep the **Directional Light** and delete anything else you do not need.
2. Right-click in the Hierarchy and choose **3D Object > Plane**. Name it `Ground`.
3. Set the Ground to **Position** `X: 0, Y: 0, Z: 18` and **Scale** `X: 2, Y: 1, Z: 5`.
4. Right-click in the Hierarchy and choose **3D Object > Cube**. Name it `SafeLine`. Set **Position** `X: 0, Y: 0.05, Z: 0` and **Scale** `X: 20, Y: 0.1, Z: 0.6`.
5. Select the **Main Camera** and set:
   - **Position:** `X: 0, Y: 7, Z: -8`
   - **Rotation:** `X: 25, Y: 0, Z: 0`
6. Open the **Game** view. Set the aspect ratio to **16:9** (or **Full HD Landscape**). Our game is wide, like a phone held sideways.

> **Try it:** Move the camera a little and watch the Game view. Can you see the safe line at the bottom and the far end of the road at the top?

### Make the three materials

1. In the Project window, right-click and choose **Create > Material**. Name it `Human_Mat`.
2. Click the color box next to **Base Map** and pick **white**.
3. Make another material called `Zombie_Mat`. Pick **red**.
4. Make a third material called `Safe_Mat`. Pick **green**.
5. Drag `Safe_Mat` onto the `SafeLine` object.

### Make the two characters

1. Right-click in the Hierarchy and choose **3D Object > Cylinder**. Name it `Human`.
2. Drag `Human_Mat` onto it.
3. Make another cylinder. Name it `Zombie`. Drag `Zombie_Mat` onto it.

A cylinder already has a **Capsule Collider**. That is good. It is what the ray will touch.

---

## Stage 2: Humans run, zombies chase

### The interface

Create a folder called `Scripts`. Inside it, create a C# script called `IDamageable`. Replace everything in it with this:

```csharp
public interface IDamageable
{
    void Hit();
}
```

That is the whole badge. It is one promise: "I have a `Hit()` method."

### The human

Create a script called `Human`.

First, make the human run toward the camera. In our world the camera looks along the **Z axis**, so "toward the camera" means **smaller Z**. `Vector3.back` points that way.

```csharp
using UnityEngine;

public class Human : MonoBehaviour, IDamageable
{
    [SerializeField] private float speed = 2.5f;

    public void SetSpeed(float newSpeed)
    {
        speed = newSpeed;
    }

    void Update()
    {
        transform.position += Vector3.back * speed * Time.deltaTime;
    }

    public void Hit()
    {
        Debug.Log("A human was hit!");
        Destroy(gameObject);
    }
}
```

Look at the first line of the class: `MonoBehaviour, IDamageable`. This is how a script wears the badge. After the comma we list the interface. Unity will now **force** us to write `Hit()`. If we forget, we get an error. That is the promise being checked.

`Time.deltaTime` makes the speed the same on every computer.

Now add the **safe line**. When the human reaches it, the human is saved. Add one field and change `Update`:

```csharp
[SerializeField] private float safeLineZ = 0f;

void Update()
{
    transform.position += Vector3.back * speed * Time.deltaTime;

    if (transform.position.z <= safeLineZ)
    {
        Debug.Log("A human is safe!");
        Destroy(gameObject);
    }
}
```

`safeLineZ` is `0` because our `SafeLine` object sits at Z = 0.

### The zombie

Create a script called `Zombie`. A zombie has a **target**: the human it is chasing.

Start with the chase:

```csharp
using UnityEngine;

public class Zombie : MonoBehaviour, IDamageable
{
    [SerializeField] private float speed = 4f;

    private Human target;

    public void SetTarget(Human human)
    {
        target = human;
    }

    public void SetSpeed(float newSpeed)
    {
        speed = newSpeed;
    }

    void Update()
    {
        if (target == null) return;

        transform.position = Vector3.MoveTowards(
            transform.position,
            target.transform.position,
            speed * Time.deltaTime);
    }

    public void Hit()
    {
        Destroy(gameObject);
    }
}
```

`if (target == null) return;` is important. If the human is gone (saved, eaten or shot), the zombie has nothing to chase, so it stops. Unity treats a destroyed object as `null`.

Now add the **eating**. Add a field, and add the last lines to `Update`:

```csharp
[SerializeField] private float eatDistance = 1.2f;

void Update()
{
    if (target == null) return;

    transform.position = Vector3.MoveTowards(
        transform.position,
        target.transform.position,
        speed * Time.deltaTime);

    float distance = Vector3.Distance(transform.position, target.transform.position);
    if (distance <= eatDistance)
    {
        target.Hit();
        target = null;
    }
}
```

When the zombie gets close, it calls `target.Hit()`. This is the same `Hit()` the shooter uses. The human does not care who hit it.

`target = null` makes the zombie stop after it eats. It is busy chewing.

### Add the scripts to the characters

1. Select `Human` in the Hierarchy. Add the **Human** script.
2. Select `Zombie`. Add the **Zombie** script.

### Make them prefabs

1. Create a folder called `Prefabs`.
2. Drag `Human` into it. Drag `Zombie` into it.
3. **Delete** both from the Hierarchy. The spawner will make them for us.

### The spawner

We do not want everyone to appear at once. We want a new group every few seconds. For that we use a **coroutine**. A coroutine can say "do this, wait 2 seconds, do it again."

Create an empty object (**right-click > Create Empty**), name it `Spawner`, and add a new script called `Spawner`:

```csharp
using System.Collections;
using UnityEngine;

public class Spawner : MonoBehaviour
{
    [SerializeField] private Human humanPrefab;
    [SerializeField] private Zombie zombiePrefab;
    [SerializeField] private int humanCount = 8;
    [SerializeField] private float timeBetweenGroups = 2.5f;
    [SerializeField] private float humanStartZ = 25f;
    [SerializeField] private float zombieGap = 8f;
    [SerializeField] private float roadHalfWidth = 6f;
    [SerializeField] private Vector2 humanSpeedRange = new Vector2(2f, 3f);
    [SerializeField] private Vector2 zombieSpeedRange = new Vector2(4f, 5f);

    void Start()
    {
        StartCoroutine(SpawnRoutine());
    }

    private IEnumerator SpawnRoutine()
    {
        for (int i = 0; i < humanCount; i++)
        {
            SpawnGroup();
            yield return new WaitForSeconds(timeBetweenGroups);
        }
    }

    private void SpawnGroup()
    {
        float x = Random.Range(-roadHalfWidth, roadHalfWidth);

        Vector3 humanPosition = new Vector3(x, 1f, humanStartZ);
        Human human = Instantiate(humanPrefab, humanPosition, Quaternion.identity);
        human.SetSpeed(Random.Range(humanSpeedRange.x, humanSpeedRange.y));

        Vector3 zombiePosition = new Vector3(x, 1f, humanStartZ + zombieGap);
        Zombie zombie = Instantiate(zombiePrefab, zombiePosition, Quaternion.identity);
        zombie.SetSpeed(Random.Range(zombieSpeedRange.x, zombieSpeedRange.y));
        zombie.SetTarget(human);
    }
}
```

Things to notice:

- `yield return new WaitForSeconds(...)` is the "wait" in the coroutine.
- Each group is one human and one zombie **behind** it (`zombieGap` units further away).
- The zombie is **faster** than the human, so it will catch up. The numbers are a fair fight. Humans take about 10 seconds to cross the road. A zombie needs about 4 to 8 seconds to catch its human. You have time to react, but not much.
- The Y position is `1`. A cylinder is 2 units tall and its center is in the middle. A Y of `1` puts the **bottom** of the cylinder exactly on the ground.
- We changed the `Human` and `Zombie` fields to the **script types**. When you drag a prefab into the box, Unity checks it has that script.

Select the Spawner. Drag the `Human` prefab into **Human Prefab**. Drag the `Zombie` prefab into **Zombie Prefab**.

Press **Play**. You should see humans running toward you with zombies catching up behind them. When a zombie reaches a human, the Console shows "A human was hit!"

> **Try it:** Change `zombieSpeedRange` to `(2, 3)`, the same as the humans. What happens? Now change it to `(8, 9)`. How does the game feel? Find the number where the game is fun.

---

## Stage 3: Tap to shoot

### The input reader

Create an empty object called `Input`. Add a new script called `InputReader`:

```csharp
using System;
using UnityEngine;
using UnityEngine.InputSystem;

public class InputReader : MonoBehaviour
{
    public event Action<Vector2> Tapped;

    void Update()
    {
        Pointer pointer = Pointer.current;
        if (pointer == null)
        {
            return;
        }

        if (pointer.press.wasPressedThisFrame)
        {
            Vector2 screenPosition = pointer.position.ReadValue();
            Tapped?.Invoke(screenPosition);
        }
    }
}
```

Read it line by line:

| Line                        | What it does                                                                                   |
| --------------------------- | ---------------------------------------------------------------------------------------------- |
| `Pointer.current`           | The mouse, finger, or pen the user used last                                                   |
| `if (pointer == null)`      | No pointer yet (for example, no touch yet), so do nothing                                      |
| `press.wasPressedThisFrame` | True only on the frame the press **starts**. This stops one long press from shooting 60 times. |
| `position.ReadValue()`      | The screen position of the press, in pixels                                                    |
| `Tapped?.Invoke(...)`       | Shouts "a tap happened here!" to anyone listening                                              |

`event Action<Vector2>` is a **shout**. The `InputReader` does not know who is listening. It only reads input and shouts. This keeps it small and reusable.

### The shooter

Create an empty object called `Shooter`. Add a new script called `Shooter`:

```csharp
using UnityEngine;

public class Shooter : MonoBehaviour
{
    [SerializeField] private InputReader input;
    [SerializeField] private Camera cam;
    [SerializeField] private float maxDistance = 100f;

    void OnEnable()
    {
        input.Tapped += OnTapped;
    }

    void OnDisable()
    {
        input.Tapped -= OnTapped;
    }

    private void OnTapped(Vector2 screenPosition)
    {
        Ray ray = cam.ScreenPointToRay(screenPosition);

        if (Physics.Raycast(ray, out RaycastHit hit, maxDistance))
        {
            if (hit.collider.TryGetComponent<IDamageable>(out IDamageable target))
            {
                target.Hit();
            }
        }
    }
}
```

This is the important part of the lesson, so read it slowly:

1. `OnEnable` and `OnDisable`: we start listening when this object turns on, and **stop** when it turns off. Always remove what you add.
2. `ScreenPointToRay`: turns the tap point into a ray that goes into the 3D world.
3. `Physics.Raycast`: sends the ray. If it hits something, `hit` tells us what.
4. `TryGetComponent<IDamageable>`: asks the object we hit, "Do you wear the badge?" If yes, we get it as `target`.
5. `target.Hit()`: the shooter does not know if it is a human or a zombie. It does not need to.

In the Inspector of the Shooter:

- Drag the `Input` object into **Input**.
- Drag the **Main Camera** into **Cam**.

Press **Play**. Tap a zombie. It disappears. Tap a human. The Console says "A human was hit!"

> **Careful:** A zombie right behind a human can be hard to tap. If the human is in front, your ray hits the human first. Tap the part of the zombie you can see, or tap earlier, when the zombie is still far from its human.

> **Try it:** Click on the empty ground. Nothing happens. Why? (Hint: the ray hits the ground, but the ground has no `IDamageable`.)

---

## Stage 4: Lives, win, lose and restart

Now we make the rules. Two scripts do the work:

- `GameManager` knows the rules: lives, how many humans are saved, and win or lose.
- `GameHud` only shows text on the screen.

Keeping the rules and the screen in separate scripts is the same idea you learned in the MVC lesson.

### The UI

1. Right-click in the Hierarchy and choose **UI > Canvas**. Name it `HUD`.
2. On the Canvas, find **Canvas Scaler**. Set **UI Scale Mode** to **Scale With Screen Size** and **Reference Resolution** to `1920 x 1080`.
3. Right-click the Canvas and choose **UI > Text - TextMeshPro**. If Unity asks to import TMP Essentials, click **Import**.
4. Name this text `LivesText`. Use the **Anchor Presets** (the square in the Rect Transform) and hold **Alt + Shift** to snap it to the **top left**. Make the font size about `48`.
5. Make another text called `SavedText`. Snap it to the **top right**.
6. Right-click the Canvas and choose **UI > Panel**. Name it `ResultPanel`.
7. Right-click `ResultPanel` and add **UI > Text - TextMeshPro**. Name it `ResultText`. Put it in the center with a big font size.
8. Right-click `ResultPanel` and add **UI > Button - TextMeshPro**. Change its label to `Restart`. Put it below the result text.

### The HUD script

Create a script called `GameHud`. Add it to the **HUD** (Canvas) object.

```csharp
using TMPro;
using UnityEngine;

public class GameHud : MonoBehaviour
{
    [SerializeField] private TMP_Text livesText;
    [SerializeField] private TMP_Text savedText;
    [SerializeField] private GameObject resultPanel;
    [SerializeField] private TMP_Text resultText;

    void Awake()
    {
        resultPanel.SetActive(false);
    }

    public void ShowStats(int lives, int saved, int total)
    {
        livesText.text = "Lives: " + lives;
        savedText.text = "Saved: " + saved + " / " + total;
    }

    public void ShowResult(bool won, int saved, int total)
    {
        resultPanel.SetActive(true);

        if (won)
        {
            resultText.text = "You saved " + saved + " of " + total + " humans!";
        }
        else
        {
            resultText.text = "The zombies won";
        }
    }
}
```

In the Inspector, drag each object into its box: `LivesText`, `SavedText`, `ResultPanel`, `ResultText`.

> **Note:** `GameHud` stays on the Canvas, which is always on. Only the **panel** is switched off. If we put this script on the panel itself, it could not run while the panel is hidden.

### The game manager

Create an empty object called `GameManager`. Add a new script called `GameManager`:

```csharp
using UnityEngine;
using UnityEngine.SceneManagement;

public enum GameState
{
    Playing,
    GameOver
}

public class GameManager : MonoBehaviour
{
    public static GameManager Instance { get; private set; }

    [SerializeField] private int startingLives = 3;
    [SerializeField] private GameHud hud;

    private int lives;
    private int totalHumans;
    private int saved;
    private int lost;

    public GameState State { get; private set; } = GameState.Playing;
    public bool IsPlaying => State == GameState.Playing;

    void Awake()
    {
        Instance = this;
        lives = startingLives;
    }

    public void SetHumanCount(int count)
    {
        totalHumans = count;
        hud.ShowStats(lives, saved, totalHumans);
    }

    public void HumanArrived()
    {
        if (!IsPlaying) return;

        saved++;
        hud.ShowStats(lives, saved, totalHumans);
        CheckEnd();
    }

    public void HumanLost()
    {
        if (!IsPlaying) return;

        lives--;
        lost++;
        hud.ShowStats(lives, saved, totalHumans);
        CheckEnd();
    }

    private void CheckEnd()
    {
        if (lives <= 0)
        {
            EndGame(false);
        }
        else if (saved + lost >= totalHumans)
        {
            EndGame(true);
        }
    }

    private void EndGame(bool won)
    {
        State = GameState.GameOver;
        hud.ShowResult(won, saved, totalHumans);
    }

    public void Restart()
    {
        SceneManager.LoadScene(SceneManager.GetActiveScene().name);
    }
}
```

Let us break it into small pieces:

| Piece                     | Why we need it                                                            |
| ------------------------- | ------------------------------------------------------------------------- |
| `enum GameState`          | The game is in one state at a time: playing, or game over                 |
| `static Instance`         | Lets other scripts say `GameManager.Instance` without dragging references |
| `SetHumanCount`           | The spawner tells the manager how many humans will come                   |
| `HumanArrived()`          | A human crossed the line. Add one to saved.                               |
| `HumanLost()`             | A human was eaten or shot. Lose a life.                                   |
| `CheckEnd()`              | No lives left means a loss. Every human done and a life left means a win. |
| `if (!IsPlaying) return;` | After the game ends, nothing counts any more                              |

Drag the `HUD` object into the **Hud** box of the GameManager.

### Connect everything to the rules

Now we replace the `Debug.Log` lines with the real rules.

**In `Human`:** change the two places where we used `Debug.Log`, and stop moving when the game is over:

```csharp
void Update()
{
    if (!GameManager.Instance.IsPlaying) return;

    transform.position += Vector3.back * speed * Time.deltaTime;

    if (transform.position.z <= safeLineZ)
    {
        GameManager.Instance.HumanArrived();
        Destroy(gameObject);
    }
}

public void Hit()
{
    GameManager.Instance.HumanLost();
    Destroy(gameObject);
}
```

One method, two meanings. If the **player** taps a human, `Hit()` runs. If a **zombie** eats a human, `Hit()` runs too. Both cost a life. The human does not care who did it.

**In `Zombie`:** add one line at the top of `Update` so zombies freeze when the game is over:

```csharp
void Update()
{
    if (!GameManager.Instance.IsPlaying) return;
    if (target == null) return;

    // ...the rest stays the same
}
```

**In `Spawner`:** tell the manager how many humans will come. Change `Start`:

```csharp
void Start()
{
    GameManager.Instance.SetHumanCount(humanCount);
    StartCoroutine(SpawnRoutine());
}
```

**In `Shooter`:** add one line at the top of `OnTapped`, so tapping does nothing after the game ends:

```csharp
private void OnTapped(Vector2 screenPosition)
{
    if (!GameManager.Instance.IsPlaying) return;

    Ray ray = cam.ScreenPointToRay(screenPosition);
    // ...the rest stays the same
}
```

### The restart button

1. Select the **Restart** button.
2. In the **On Click ()** list, click the **+**.
3. Drag the `GameManager` object into the empty box.
4. In the function drop-down, choose **GameManager > Restart()**.

### Save the scene

1. Go to **File > Save As** and save the scene as `Game`.
2. Go to **File > Build Profiles** and click **Add Open Scenes**.

Restart needs the scene to be in the build list. Without this step the Restart button will show an error.

Press **Play** and test all of these:

- Tap a zombie before it reaches its human. The zombie disappears and the human is safe.
- Let a zombie catch a human. You lose a life.
- Tap a human by mistake. You lose a life.
- Lose 3 lives. "The zombies won" appears.
- Let the last human cross the line with a life left. "You saved X of 8 humans!" appears.
- Press **Restart**. Everything starts again.

> **Stuck?** Click the red message in the Console. The message tells you the script name and the line number. Most errors here are an empty box in the Inspector. Check that every box has something dragged into it.

---

## Stage 5: Build and play on a device

### Option A: Android phone

You need: Android installed in Unity Hub, a USB cable, and an Android phone.

**Turn on developer mode on the phone:**

1. Go to **Settings > About phone**.
2. Tap **Build number** 7 times.
3. Go back to **Settings > Developer options**.
4. Turn on **USB debugging**.
5. Plug in the phone. If it asks "Allow USB debugging?", tap **Allow**.

**Set up the game in Unity:**

1. Go to **Edit > Project Settings > Player**.
2. At the top, set **Company Name** and **Product Name**.
3. Open **Resolution and Presentation**. Set **Default Orientation** to **Landscape Left**.
4. Open **Other Settings**. Set a **Package Name** with this shape: `com.yourname.savethehumans`. Use only small letters and dots.
5. Go to **File > Build Profiles**. Choose the phone in **Run Device**.
6. Click **Build And Run**.

The first build is slow. This is normal. The next ones are faster.

### Option B: PC build

If Android is not installed:

1. Go to **File > Build Profiles**.
2. Choose **Windows**.
3. Click **Build** and pick a folder.
4. Open the `.exe` file and play.

You can send the folder (zipped) to a friend, and they can play it too.

### Do the Android build at home

If Android was missing today, install the **Android Build Support** module at home through Unity Hub (**Installs > the gear icon > Add modules**). Then repeat Option A. It is a big download, so use a good internet connection.

> **Try it:** On the phone, tap with one finger and then with two. Does the game still work? Your `InputReader` reads whatever pointer was used last, so it should.

---

## Complete scripts at a glance

| Script        | Sits on                       | Job                                                                       |
| ------------- | ----------------------------- | ------------------------------------------------------------------------- |
| `IDamageable` | (nothing, it is an interface) | The badge: "I have a `Hit()` method"                                      |
| `Human`       | Human prefab                  | Runs toward the safe line. Counts as saved if it arrives, or lost if hit. |
| `Zombie`      | Zombie prefab                 | Chases its human and eats it. Is destroyed if the player shoots it.       |
| `Spawner`     | `Spawner` object              | Sends a human and a zombie every few seconds                              |
| `InputReader` | `Input` object                | Reads mouse or touch and shouts `Tapped`                                  |
| `Shooter`     | `Shooter` object              | Turns a tap into a ray and calls `Hit()`                                  |
| `GameManager` | `GameManager` object          | Lives, saved humans, win and lose, restart                                |
| `GameHud`     | `HUD` canvas                  | Shows text on the screen                                                  |

---

## Common mistakes

| What you see                                      | Why it happens                                                                                                   | The fix                                                |
| ------------------------------------------------- | ---------------------------------------------------------------------------------------------------------------- | ------------------------------------------------------ |
| Tapping does nothing                              | **Active Input Handling** is set to the old Input Manager, or the `Input` object is not dragged into the Shooter | Check Player Settings and the Shooter in the Inspector |
| Error: NullReference on `GameManager.Instance`    | There is no `GameManager` object in the scene                                                                    | Create it and add the script                           |
| Error: `Human` or `Zombie` not found in `Spawner` | A script name does not match its class name                                                                      | The file name and the class name must be the same      |
| Humans walk past the line and nothing happens     | `SafeLine` is not at Z = 0, or `safeLineZ` is a different number                                                 | Make both Z = 0                                        |
| Zombies stand still                               | The `SetTarget` line is missing, or the human is already gone                                                    | Check `SpawnGroup` in the Spawner                      |
| A tap hits the human, not the zombie              | The zombie is hidden behind the human                                                                            | Tap earlier, or tap the part of the zombie you can see |
| HUD text does not show                            | TMP Essentials not imported, or a field in `GameHud` is empty                                                    | Import TMP Essentials and drag every field             |
| Restart gives a scene error                       | The scene is not in the build list                                                                               | **File > Build Profiles > Add Open Scenes**            |
| Android is missing in Build Profiles              | The Android module is not installed                                                                              | Stay on PC and do Android at home                      |

---

## Recap

| Idea               | In one sentence                                                  |
| ------------------ | ---------------------------------------------------------------- |
| **Vertical slice** | A small piece of the game that works from start to finish        |
| **Slice flow**     | The exact path a player takes through the slice                  |
| **Pointer**        | A mouse, a finger or a pen, read by one script                   |
| **Raycast**        | A line from the camera through the tap, to find what was touched |
| **Interface**      | A promise: "I have these methods"                                |
| **IDamageable**    | Our badge. Anything with `Hit()` can be shot or eaten            |
| **MoveTowards**    | One small step toward a target, every frame                      |
| **Coroutine**      | A method that can wait in the middle                             |
| **GameManager**    | The script that knows the rules                                  |
| **GameHud**        | The script that only shows text                                  |

---

## Self-check questions

1. Why do we use `Pointer.current` and not a mouse-only check?
2. What does `Physics.Raycast` need from an object before it can hit it?
3. In your own words, what is an interface? Use the work badge idea.
4. Why can `Shooter` call `Hit()` without knowing if the target is a human or a zombie?
5. The zombie also calls `Hit()`, on the human. Why is that a good use of the interface?
6. What does `Vector3.MoveTowards` do, and why does the zombie need to call it every frame?
7. What happens if you forget `if (!IsPlaying) return;` in `GameManager`?

---

## Practice tasks

### Easy

- Change the number of lives to 5.
- Change the number of humans to 12.
- Change the colors. Make the zombies green and the humans yellow.
- Change `zombieGap` and see how it changes the difficulty.

### Medium

- Play a sound when you shoot a zombie, and a different sound when a human is eaten.
- Add a **zombie counter**: show "Zombies shot: 5" in the HUD.
- Add a third kind of character, for example a **Soldier** who runs with the humans. It must wear the `IDamageable` badge. You should not need to change the `Shooter` script at all. That is the proof the interface works.

### Hard

- Make a zombie that has no target **keep running** to the safe line. If it reaches the line, the player loses a life.
- Add a **Ready** state with a Start button. `GameState` is a small state machine, like the zombie in Lesson 14. Nothing moves until the player presses Start.
- Add **ammo**: the player has 10 bullets and must press a Reload button.

---

## What comes next

You now have a vertical slice. To turn it into a full game, you would add:

- A **main menu** and a **settings** screen.
- More **levels** and more kinds of characters.
- **Sounds** and **effects** that make each shot feel good.
- The **ads** from your slice plan, placed exactly where you wrote them.

Try writing a slice plan for a game of your own. Use the same table: steps, what it proves, and where an ad would go.

---

*Contracted by NextSkill. A Gamestorms company. Created for Isra University.*