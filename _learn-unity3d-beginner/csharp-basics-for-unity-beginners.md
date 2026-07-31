---
title: "Lesson 6: C# Basics for Unity — Build a 2-Ball Coin Game While Learning to Code"
order: 6
description: "Learn C# programming fundamentals — data types, variables, conditions, loops, arrays, Unity classes, object references, Rigidbody physics, and trigger collisions — by building a simple 2-ball coin collection scene in Unity."
tags: [Unity, CSharp, Game Development, Beginner Guide, Variables, Loops, Arrays, Conditional Statements, Unity Scripts, Object References, Rigidbody, Physics, Colliders, Tags, OnTriggerEnter]
---

# C# Basics for Unity — Build a 2-Ball Coin Game While Learning to Code

---

## Is Learning to Code Still Worth It When AI Writes the Code?

This is a fair question. If AI tools can generate entire scripts in seconds, why spend time learning C# basics at all?

Think about it this way. Why do we learn to read if someone else can read to us? Why do we solve math problems by hand before picking up a calculator?

The answer is the same in both cases: you need to understand the subject before a tool can be useful to you. If you hand a calculator to someone who doesn't understand numbers, it won't help them — they won't know what to type in, or whether the answer makes sense.

Programming works the same way. AI can write code for you. But if you cannot read that code, you won't know:

- Whether it actually does what you wanted
- Where the problem is when the game breaks
- How to change one small thing without breaking everything else

You don't need to memorize every line of syntax. But you do need to be able to **read the language** — to look at a script and understand what is happening. That skill starts with the fundamentals.

In this lesson, we will cover those fundamentals: **data types, variables, conditional statements, loops, arrays, Unity classes, object references, Rigidbody physics, and trigger collisions**. And we will learn all of it by building one small, complete Unity scene.

---

## The Scene We Are Building

Before writing a single line of code, let's decide what we are making. Having a clear goal makes everything easier to understand.

**The scene:**

- A flat plane as the game world
- A **blue ball** — controlled by the player (arrow keys), moved with a `Rigidbody`
- A **red ball** — controlled by simple AI, also moved with a `Rigidbody`
- Both balls carry a `Sphere Collider` so they can physically touch the ground and each other
- **5 yellow coins** placed around the plane, each with a trigger collider and a `"Coin"` tag
- Each ball moves around and tries to collect coins
- When a ball's collider touches a coin's trigger, the coin disappears and the ball's score goes up

That's it. Small enough to understand immediately, but it contains every concept we need to cover.

---

## 1. Data Types

**Data** is information. In your game, the ball's speed is data. The player's score is data. Whether the game is still running is data.

Every piece of data in C# has a **type**. The type tells the computer what kind of information it is and how much memory to use for it.

The four types you will use most often:

| Type     | Stores                | Example                   |
| -------- | --------------------- | ------------------------- |
| `int`    | Whole numbers         | Coin score: `0`, `1`, `5` |
| `float`  | Numbers with decimals | Ball speed: `5.5f`        |
| `bool`   | True or False only    | Is the key held down?     |
| `string` | Text                  | Ball name: `"Player"`     |

Notice the `f` after float values — `5.5f` — this tells C# the number is a float and not a double. Unity uses floats for almost everything movement-related.

---

## 2. Variables

A **variable** is a named storage location for data. You give it a type and a name, and the value inside can change at any time.

```csharp
float speed = 5.5f;   // how fast the ball moves
int score = 0;        // coins collected so far
bool isGameOver = false;
string ballName = "Player";
```

In our scene, the ball's speed, score, and name are all variables. When the ball collects a coin, the `score` variable increases by 1. When the game ends, `isGameOver` changes from `false` to `true`.

Variables are the memory of your game. Without them, nothing would be remembered between frames.

---

## 3. Unity Classes and Scripts

Now we need to understand how Unity organizes code — because this affects everything else.

In Unity, every script is a **class**. A class is a blueprint that describes what an object **is** and what it can **do**. You write the blueprint once, and Unity can use it on any number of GameObjects.

Every script that attaches to a GameObject must extend `MonoBehaviour`. This gives it access to Unity-specific lifecycle methods like `Start()`, `Update()`, and `FixedUpdate()`.

Here is the basic structure every Unity script follows:

```csharp
using UnityEngine;

public class BallController : MonoBehaviour
{
    // --- Data (variables) ---
    public float speed = 5.0f;
    public int score = 0;
    public string ballName = "Ball";

    void Start()
    {
        // Runs once when the game starts
    }

    void Update()
    {
        // Runs every rendered frame
    }

    void FixedUpdate()
    {
        // Runs on a fixed timestep, in sync with the physics engine
    }
}
```

This `BallController` script is the blueprint for our balls. In the scene, we attach this same script to **both** the player ball and the AI ball. Each ball gets its own copy — with its own `speed`, its own `score`, its own `ballName`. The blueprint is the same; the data inside each copy is independent.

This is an important idea: **one script, many GameObjects**.

---

## 4. Object References — How Scripts Talk to Each Other

This is one of the most important concepts in Unity, and one that confuses many beginners. Let's take it step by step.

In our scene we have three scripts:

- `BallController` — attached to the player ball and the AI ball; also owns physics movement and coin collection
- `PlayerInput` — reads keyboard input and tells the player ball which way to move
- `AIController` — tells the AI ball which way to move, toward the nearest coin

`PlayerInput` and `AIController` are **not** attached to the balls themselves. They live on separate GameObjects. So how does `PlayerInput` know which ball to move?

The answer is a **reference** — a variable that holds a direct connection to another object or script.

```csharp
public BallController ball;
```

This line declares a reference variable. In the Unity Inspector, it appears as an empty slot. You drag the player ball's GameObject into that slot, and now `PlayerInput` has a permanent, direct link to the player ball's `BallController`. Whenever `PlayerInput` says `ball.Move(...)`, it talks to that specific ball — not the AI ball, not any other object.

Here is how the connections look in the scene:

```
[PlayerBall]             [AIBall]
  BallController           BallController
       ↑                        ↑
  referenced by            referenced by
       |                        |
  [PlayerInput]           [AIController]
```

Both `PlayerInput` and `AIController` reference a `BallController`. But they reference **different** ones. This is how Unity lets you use the same script on many objects without the scripts interfering with each other.

---

## 5. Physics, Colliders, and Tags

Before writing the full `BallController`, we need three physics building blocks. You've seen these before — here's how they apply to our scene.

**Rigidbody** — a component that hands an object over to Unity's physics engine. Once a GameObject has a `Rigidbody`, gravity affects it, and it can be moved in physics-safe ways instead of just teleporting its `transform.position` directly. Both balls get a `Rigidbody`.

**Sphere Collider** — defines the physical shape Unity uses to detect contact. Both balls get a `Sphere Collider` so they can rest on the plane and bump into things. Each coin also gets a collider — but with one difference:

**Is Trigger** — a checkbox on a collider. When it's **off**, the collider is solid — objects physically stop or push each other. When it's **on**, the collider becomes a *sensor*: other objects can pass straight through it, but Unity still tells your script the moment they overlap. We want the ball to pass through a coin (not bounce off it), so every coin's collider has **Is Trigger = true**.

**Tags** — a short label you assign to a GameObject in the Inspector, like `"Coin"`, `"Enemy"`, or `"Player"`. Tags let a script ask *"what kind of object is this?"* without needing to know anything else about it. We give every coin the tag `"Coin"`.

**Update() vs. FixedUpdate()** — `Update()` runs once per rendered frame, and frame rate can vary. `FixedUpdate()` runs on a fixed timestep controlled by the physics engine. Because our balls move with a `Rigidbody`, the movement itself belongs in `FixedUpdate()` — this keeps movement smooth and consistent no matter how fast or slow the game is rendering.

With those three pieces in place, here is the full `BallController`:

```csharp
using UnityEngine;

public class BallController : MonoBehaviour
{
    public float speed = 5.0f;
    public int score = 0;
    public string ballName = "Ball";

    private Rigidbody rb;
    private Vector3 moveDirection = Vector3.zero;

    void Start()
    {
        rb = GetComponent<Rigidbody>();
    }

    void FixedUpdate()
    {
        rb.MovePosition(rb.position + moveDirection * speed * Time.fixedDeltaTime);
    }

    public void Move(Vector3 direction)
    {
        moveDirection = direction;
    }

    private void OnTriggerEnter(Collider other)
    {
        bool isCoin = other.CompareTag("Coin");

        if (isCoin)
        {
            CollectCoin();
            other.gameObject.SetActive(false);
        }
    }

    private void CollectCoin()
    {
        score = score + 1;
        Debug.Log(ballName + " collected a coin! Score: " + score);
    }
}
```

A few things to notice:

- `Start()` grabs the `Rigidbody` component once, when the game begins, and stores it in `rb`.
- `Move()` no longer moves anything directly — it just records *which direction* the ball wants to go, in `moveDirection`. This is a small but important change: input and movement are now separate.
- `FixedUpdate()` is where the actual movement happens, using `rb.MovePosition()` — a physics-safe way to move a Rigidbody, instead of editing `transform.position` by hand.
- `OnTriggerEnter(Collider other)` is called automatically by Unity the instant this ball's collider overlaps another trigger collider. `other` is whatever it touched.
- `other.CompareTag("Coin")` returns a `bool` — `true` if the object it touched is tagged `"Coin"`, `false` otherwise. We store that answer in `isCoin` and branch on it with an `if` statement, exactly like the conditional statements from Section 6 below.

You might expect an `OnTriggerExit()` here too, since we learned that method earlier. We don't need it in this case: the coin is disabled the *instant* the trigger fires, so there's nothing left to detect an "exit" from. `OnTriggerExit()` becomes useful for things that last longer — a slow zone, a damage field, a checkpoint — where you care about the moment an object *leaves*, not just enters.

The player ball in our scene will have `speed = 6.0f` and `ballName = "Player"`. The AI ball will have `speed = 4.5f` and `ballName = "AI"`. Same script, different values.

---

## 6. Conditional Statements

A **conditional statement** lets your code make a decision. It evaluates whether something is true or false, then runs a block of code accordingly.

```csharp
if (condition is true)
{
    // do this
}
else
{
    // do this instead
}
```

You already saw one conditional statement above — `if (isCoin)` inside `OnTriggerEnter()`. Here's another place the same pattern shows up: player input.

### Player input is a conditional statement

When the player presses an arrow key, the ball should move. When no key is pressed, the ball should stay still. That is a decision — and it maps directly to an `if` statement.

In Unity, `Input.GetKey()` returns a `bool` — `true` if the key is being held, `false` if it is not. That `bool` goes straight into your condition:

```csharp
bool isKeyHeld = Input.GetKey(KeyCode.RightArrow);

if (isKeyHeld)
{
    ball.Move(Vector3.right);
}
```

Think of it as asking a yes/no question every frame: *"Is the right arrow key being held right now?"* If yes, move right. If no, do nothing. That question is a `bool`. Your `if` statement acts on the answer.

Here is the full `PlayerInput` script:

```csharp
using UnityEngine;

public class PlayerInput : MonoBehaviour
{
    public BallController ball; // reference to the player ball

    void Update()
    {
        bool pressedUp    = Input.GetKey(KeyCode.UpArrow);
        bool pressedDown  = Input.GetKey(KeyCode.DownArrow);
        bool pressedLeft  = Input.GetKey(KeyCode.LeftArrow);
        bool pressedRight = Input.GetKey(KeyCode.RightArrow);

        if (pressedUp)
        {
            ball.Move(Vector3.forward);
        }
        else if (pressedDown)
        {
            ball.Move(Vector3.back);
        }
        else if (pressedLeft)
        {
            ball.Move(Vector3.left);
        }
        else if (pressedRight)
        {
            ball.Move(Vector3.right);
        }
        else
        {
            ball.Move(Vector3.zero);
        }
    }
}
```

Every frame, Unity checks four conditions. The first one that is `true` runs its block and the rest are skipped. Notice the final `else`: if no arrow key is held, we tell the ball to move in `Vector3.zero` — no direction at all — so it stops instead of drifting on the last direction it remembers. `PlayerInput` still only decides *direction*; `BallController.FixedUpdate()` is what actually moves the Rigidbody.

---

## 7. Loops

A **loop** repeats a block of code multiple times without writing it out manually each time.

A `for` loop runs a set number of times. You give it three things:

1. A starting value
2. A condition that keeps it running
3. How to update the counter each pass

```csharp
for (int i = 0; i < 5; i++)
{
    Debug.Log("Coin number: " + i);
}
// Prints: Coin number: 0 ... Coin number: 4
```

`i` starts at `0`, runs while `i < 5`, and increases by `1` each pass. Five total iterations.

### Spawning coins with a loop

Instead of placing 5 individual coin GameObjects by hand, we can write a `CoinSpawner` that creates them at random positions when the scene starts. As long as our coin prefab already has its collider set to **Is Trigger = true** and its tag set to `"Coin"`, every coin the loop spawns is ready to be collected automatically:

```csharp
using UnityEngine;

public class CoinSpawner : MonoBehaviour
{
    public GameObject coinPrefab;
    public int coinCount = 5;

    void Start()
    {
        for (int i = 0; i < coinCount; i++)
        {
            Vector3 randomPosition = new Vector3(
                Random.Range(-8f, 8f),
                0.5f,
                Random.Range(-8f, 8f)
            );

            Instantiate(coinPrefab, randomPosition, Quaternion.identity);
        }
    }
}
```

Change `coinCount` to `10` and the loop spawns 10 coins with no other changes. The loop scales automatically — the code stays the same regardless of how many coins you want.

---

## 8. Arrays

An **array** holds multiple values of the same type under one variable name. Each value sits at a numbered position called an **index**, starting from `0`.

```csharp
int[] scores = { 0, 0, 0 };
scores[0] = 5;    // index 0 = first player
scores[1] = 3;    // index 1 = second player
```

Arrays are useful whenever you have a collection of similar things — a list of enemies, a list of waypoints, a list of coins.

### The AI uses an array to find the nearest coin

The `AIController` needs to know where all coins are so it can navigate toward the closest one. We store all coin positions in an array and loop through them to find the nearest:

```csharp
using UnityEngine;

public class AIController : MonoBehaviour
{
    public BallController ball;  // reference to the AI ball
    public Transform[] coins;    // all coins in the scene

    void Update()
    {
        Transform nearestCoin = FindNearestCoin();

        if (nearestCoin != null)
        {
            Vector3 direction = (nearestCoin.position - ball.transform.position).normalized;
            ball.Move(direction);
        }
        else
        {
            ball.Move(Vector3.zero);
        }
    }

    Transform FindNearestCoin()
    {
        Transform nearest = null;
        float shortestDistance = Mathf.Infinity;

        for (int i = 0; i < coins.Length; i++)
        {
            if (coins[i] == null) continue; // skip coins that were collected

            float distance = Vector3.Distance(ball.transform.position, coins[i].position);

            if (distance < shortestDistance)
            {
                shortestDistance = distance;
                nearest = coins[i];
            }
        }

        return nearest;
    }
}
```

The `for` loop walks through every entry in `coins[]`. For each one, it checks the distance — that is a conditional statement. When it finds one closer than the previous best, it updates `nearest`. By the end of the loop, `nearest` holds the closest active coin. Notice that `AIController` never touches collision or scoring at all — it only decides *direction*, exactly like `PlayerInput`. Collecting the coin is entirely `BallController`'s job, triggered by physics, not by this script.

This one method uses **arrays**, **loops**, and **conditional statements** all working together.

---

## The Coin Script

Since collision detection now lives on the ball, the coin itself barely needs any code. A small spin animation is a nice touch and gives us a reason to use `Update()` one more time:

```csharp
using UnityEngine;

public class Coin : MonoBehaviour
{
    public float spinSpeed = 90f;

    void Update()
    {
        transform.Rotate(Vector3.up, spinSpeed * Time.deltaTime);
    }
}
```

Both the coin and the ball have colliders, but only the ball's script reacts when they touch. Unity doesn't require every object involved in a collision to have logic — only the one that needs to *do* something with the information.

---

## Putting the Scene Together

Here is the complete Hierarchy for this scene:

```
Main Camera
Directional Light
Plane

PlayerBall  (Sphere, blue material)
    ├─ Rigidbody        →  Use Gravity: true
    ├─ Sphere Collider  →  Is Trigger: false
    └─ BallController   →  speed: 6.0   ballName: "Player"

AIBall  (Sphere, red material)
    ├─ Rigidbody        →  Use Gravity: true
    ├─ Sphere Collider  →  Is Trigger: false
    └─ BallController   →  speed: 4.5   ballName: "AI"

PlayerManager  (Empty GameObject)
    └─ PlayerInput  →  ball: [PlayerBall's BallController]

AIManager  (Empty GameObject)
    └─ AIController  →  ball: [AIBall's BallController]
                        coins: [Coin_1, Coin_2, Coin_3, Coin_4, Coin_5]

CoinSpawner  (Empty GameObject)
    └─ CoinSpawner  →  coinPrefab: [Coin prefab]   coinCount: 5

Coin_1 through Coin_5  (Sphere, yellow material)
    ├─ Tag: "Coin"
    ├─ Sphere Collider  →  Is Trigger: true
    └─ Coin script      →  spinSpeed: 90
```

The most important thing to see here: `BallController` is attached to **both** `PlayerBall` and `AIBall`. Same script, two separate instances with their own data. `PlayerInput` holds a reference only to `PlayerBall`'s copy. `AIController` holds a reference only to `AIBall`'s copy. Neither script can accidentally control the wrong ball. And now, both balls detect coins the same way — through physics, using their own `Sphere Collider` and `Rigidbody`, not through code living on the coin.

---

## What You Learned

Every concept in this lesson appeared directly in the scene:

| Concept                    | Where it appeared                                        |
| -------------------------- | -------------------------------------------------------- |
| **Data Types**             | `float speed`, `int score`, `bool isCoin`                |
| **Variables**              | Storing speed, score, ball name, direction               |
| **Unity Classes**          | `BallController`, `PlayerInput`, `AIController`, `Coin`  |
| **Object References**      | `PlayerInput → PlayerBall`, `AIController → AIBall`      |
| **Rigidbody & Physics**    | `rb.MovePosition()` inside `FixedUpdate()`               |
| **Colliders & Triggers**   | `Sphere Collider` on balls; `Is Trigger = true` on coins |
| **Tags & OnTriggerEnter**  | `other.CompareTag("Coin")` inside `OnTriggerEnter()`     |
| **Conditional Statements** | Key input checks, `if (isCoin)`, distance comparisons    |
| **Loops**                  | Spawning coins, finding the nearest coin                 |
| **Arrays**                 | Storing all coin positions for the AI                    |

These are the foundations. Every Unity project — regardless of size or genre — is built on exactly these ideas at a larger scale. Once they feel natural in a small scene like this one, you will recognize them immediately in more complex projects.

And when you ask an AI tool to write a script for you, you will be able to read it, judge it, and adjust it — because now you speak the language.

Happy coding!