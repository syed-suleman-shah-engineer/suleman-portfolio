---
title: "AI Navigation and Finite State Machines: Smart Enemies"
order: 13
tags: ["unity", "ai", "navmesh", "navigation", "fsm", "state-machine", "enemies", "agents"]
description: "Make characters walk around a level by themselves with NavMesh, then give them a brain with a Finite State Machine."
---

# AI Navigation and Finite State Machines

## What you will build

In this lesson you will build two things:

1. A **hero** that walks to any spot you tap.
2. A **zombie** that waits, sees you, runs after you, and attacks.

The zombie is not magic. It is two simple ideas working together:

- **AI Navigation** gives the zombie *legs*. It knows how to walk around walls.
- **A Finite State Machine (FSM)** gives the zombie a *brain*. It knows what to do next.

By the end of this lesson you can:

- Explain what a NavMesh and a NavMeshAgent are.
- Bake a NavMesh and make a character walk on it.
- Make a hero move to the spot you tap.
- Explain what a Finite State Machine is and why we use one.
- Write a zombie that uses four states: Idle, Chase, Attack and Dead.

---

## Part 1: AI Navigation

### The taxi idea

Think about a taxi app.

- The **map** shows where the taxi can drive.
- The **taxi** drives by itself.
- You only give one thing: the **address**.

You do not turn the steering wheel. You say where you want to go, and the taxi finds the road.

Unity works the same way:

| Taxi app | Unity            | What it does                          |
| -------- | ---------------- | ------------------------------------- |
| Map      | **NavMesh**      | Marks the places a character can walk |
| Taxi     | **NavMeshAgent** | The character that walks by itself    |
| Address  | **Destination**  | The point the agent must reach        |

> **Why not just move the enemy with code?** If you push an enemy straight toward the player, it walks into walls and gets stuck. A NavMeshAgent finds a path *around* the walls for you.

### Step 1: Build a small arena

1. Create a **Plane**. Name it `Ground`. Set its scale to `5, 1, 5`.
2. Create a new **Layer** called `Ground` and set the plane to this layer.
3. Add 3 or 4 **Cubes** on the plane. Make them tall (Scale Y = 2). These are your walls.

### Step 2: Bake the NavMesh

1. If you do not see **NavMesh Surface** in the next step, install the **AI Navigation** package. Open **Window → Package Manager**, choose **Unity Registry**, search for *AI Navigation*, and click **Install**.
2. Select `Ground`. Click **Add Component** and add **NavMesh Surface**.
3. Click the **Bake** button.

You will see a **blue area** over the ground. That blue area is the NavMesh. It is the only place an agent can walk.

Look at your cubes. They made holes in the blue area, because agents cannot walk through walls.

> **Try it:** Move one cube. Click **Bake** again. Watch the blue area change. If you forget to bake again, the agents still use the old map.

### Step 3: Make the hero

1. Create a **Capsule**. Name it `Hero`. Set its **Tag** to `Player`.
2. Place it on the blue area.
3. Click **Add Component** and add **NavMeshAgent**.

The NavMeshAgent has settings you will use all the time:

| Setting               | What it does                                       |
| --------------------- | -------------------------------------------------- |
| **Speed**             | How fast the agent walks                           |
| **Angular Speed**     | How fast it turns                                  |
| **Acceleration**      | How quickly it speeds up and slows down            |
| **Stopping Distance** | How far from the destination it stops              |
| **Radius and Height** | The size of the agent's body (used to avoid walls) |

> **Important:** Do not add a normal Rigidbody to an agent. The agent moves itself. A Rigidbody will fight with it.

### Step 4: Tap to move

Now we write the script. We build it in small pieces.

**First, the fields.** The script needs the camera, the ground layer, and the agent.

```csharp
using UnityEngine;
using UnityEngine.AI;
using UnityEngine.InputSystem;

public class TapToMove : MonoBehaviour
{
    [SerializeField] private Camera cam;
    [SerializeField] private LayerMask groundLayer;

    private NavMeshAgent agent;
}
```

`LayerMask` lets us say "only hit the Ground layer". Taps on other things are ignored.

**Next, Awake.** We get the agent and find the camera.

```csharp
void Awake()
{
    agent = GetComponent<NavMeshAgent>();
    if (cam == null) cam = Camera.main;
}
```

**Next, Update.** We wait for a tap. We turn the tap position into a ray. If the ray hits the ground, we give that point to the agent.

```csharp
void Update()
{
    if (Pointer.current == null) return;
    if (!Pointer.current.press.wasPressedThisFrame) return;

    Vector2 screenPosition = Pointer.current.position.ReadValue();
    Ray ray = cam.ScreenPointToRay(screenPosition);

    if (Physics.Raycast(ray, out RaycastHit hit, 100f, groundLayer))
    {
        agent.SetDestination(hit.point);
    }
}
```

`Pointer.current` works for **both the mouse and a finger**. So the same code works in the editor and on an Android phone.

> **Using the old Input Manager?** If your project uses the old input system, replace the two input lines with `Input.GetMouseButtonDown(0)` and `Input.mousePosition`.

**The complete script:**

```csharp
using UnityEngine;
using UnityEngine.AI;
using UnityEngine.InputSystem;

public class TapToMove : MonoBehaviour
{
    [SerializeField] private Camera cam;
    [SerializeField] private LayerMask groundLayer;

    private NavMeshAgent agent;

    void Awake()
    {
        agent = GetComponent<NavMeshAgent>();
        if (cam == null) cam = Camera.main;
    }

    void Update()
    {
        if (Pointer.current == null) return;
        if (!Pointer.current.press.wasPressedThisFrame) return;

        Vector2 screenPosition = Pointer.current.position.ReadValue();
        Ray ray = cam.ScreenPointToRay(screenPosition);

        if (Physics.Raycast(ray, out RaycastHit hit, 100f, groundLayer))
        {
            agent.SetDestination(hit.point);
        }
    }
}
```

Add this script to `Hero`. Set **Ground Layer** to `Ground`. Press Play and tap around.

> **Try it:** Tap on the far side of a wall. Watch the hero walk *around* it. You wrote no path code at all.

---

## Part 2: Finite State Machines

### The problem

Your zombie has to do many things:

- Stand still until it sees the player.
- Run after the player.
- Stop and hit the player when close.
- Fall down when it is killed.

If you write all of this with many `if` statements in one place, the code becomes a mess. Soon the zombie is hitting the player *while* it is dead.

### The shop guard idea

Think about a guard at a shop door.

- **Standing at the door.** Waiting.
- **Running after a thief.** The guard saw someone steal.
- **Holding the thief.** The guard caught them.

The guard is only in **one** of these at a time. Something *happens* and the guard changes to another one.

That is a **Finite State Machine**:

- **Finite** means there are a small, fixed number of options.
- **State** means what the character is doing right now.
- **Machine** means the rules for changing from one state to another are clear.

### Three rules of an FSM

1. The character is in **exactly one state** at a time.
2. Each state has **one job**.
3. A change of state (a **transition**) only happens when a clear **condition** is true.

### The zombie's states

| State      | What the zombie does      | Goes to   | When                                   |
| ---------- | ------------------------- | --------- | -------------------------------------- |
| **Idle**   | Stands still              | Chase     | Player is closer than the detect range |
| **Chase**  | Runs toward the player    | Attack    | Player is closer than the attack range |
| **Attack** | Stops and hits the player | Chase     | Player moves out of attack range       |
| **Dead**   | Stops for good            | (nothing) | Zombie is killed                       |

You can read it like a story: *wait, see the player, run, hit, and if killed, stop.*

> **Think about it:** Why does Dead have no way out? Because a dead zombie must never wake up again.

### Build the zombie, step by step

**Step 1: The states and the settings.**

We use an `enum`. An enum is a list of names. It stops us from making spelling mistakes with text.

```csharp
using UnityEngine;
using UnityEngine.AI;

public class ZombieAI : MonoBehaviour
{
    private enum State { Idle, Chase, Attack, Dead }

    [SerializeField] private Transform target;
    [SerializeField] private float detectRange = 10f;
    [SerializeField] private float attackRange = 1.5f;
    [SerializeField] private float attackCooldown = 1f;

    private NavMeshAgent agent;
    private State state = State.Idle;
    private float nextAttackTime;
}
```

- `target` is the hero.
- `detectRange` is how far the zombie can see.
- `attackRange` is how close it must be to hit.
- `attackCooldown` is the time between two hits.

**Step 2: Awake.**

A zombie spawned later cannot have the hero dragged into its slot. So the zombie finds the hero by the `Player` tag.

```csharp
void Awake()
{
    agent = GetComponent<NavMeshAgent>();
    agent.stoppingDistance = attackRange * 0.8f;

    if (target == null)
    {
        GameObject player = GameObject.FindWithTag("Player");
        if (player != null) target = player.transform;
    }
}
```

We set `stoppingDistance` a little smaller than `attackRange`. This makes the zombie stop *inside* its hitting range.

**Step 3: Update.**

Every frame, we measure the distance to the hero. Then we run the code for the current state. This is the heart of the FSM.

```csharp
void Update()
{
    if (state == State.Dead || target == null) return;

    float distance = Vector3.Distance(transform.position, target.position);

    switch (state)
    {
        case State.Idle:   UpdateIdle(distance);   break;
        case State.Chase:  UpdateChase(distance);  break;
        case State.Attack: UpdateAttack(distance); break;
    }
}
```

The `switch` picks one block, so only **one state runs at a time**. That is rule 1.

**Step 4: ChangeState.**

All transitions go through **one method**. This is the most important habit in this lesson.

```csharp
private void ChangeState(State newState)
{
    if (state == newState) return;

    state = newState;
    Debug.Log(name + " is now " + state);

    switch (state)
    {
        case State.Idle:
            agent.isStopped = true;
            break;
        case State.Chase:
            agent.isStopped = false;
            break;
        case State.Attack:
            agent.isStopped = true;
            break;
        case State.Dead:
            agent.isStopped = true;
            break;
    }
}
```

The second `switch` runs **once**, at the moment we enter a state. It is the place for "do this one time when we arrive" code. The `Debug.Log` line helps you see every change in the Console.

**Step 5: The three state methods.**

Each method has one job and one or two transitions.

```csharp
private void UpdateIdle(float distance)
{
    if (distance <= detectRange)
        ChangeState(State.Chase);
}

private void UpdateChase(float distance)
{
    agent.SetDestination(target.position);

    if (distance <= attackRange)
        ChangeState(State.Attack);
}

private void UpdateAttack(float distance)
{
    FaceTarget();

    if (distance > attackRange + 0.5f)
    {
        ChangeState(State.Chase);
        return;
    }

    if (Time.time >= nextAttackTime)
    {
        nextAttackTime = Time.time + attackCooldown;
        Debug.Log(name + " hits the player!");
    }
}

private void FaceTarget()
{
    Vector3 direction = target.position - transform.position;
    direction.y = 0f;

    if (direction != Vector3.zero)
        transform.rotation = Quaternion.LookRotation(direction);
}
```

Notice the `+ 0.5f` in the Attack method. It is a small **buffer**. Without it, the zombie could flip between Attack and Chase many times a second when the hero stands right at the edge. A buffer fixes that.

For now the hit only prints a message. In the next lesson we will connect it to real health and damage.

**Step 6: Dying.**

We add one public method. Other scripts (like the hero's attack) will call it.

```csharp
public void Die()
{
    ChangeState(State.Dead);
    Destroy(gameObject, 2f);
}
```

The zombie stays on the ground for 2 seconds, then disappears.

**Step 7: See the ranges (optional but very helpful).**

```csharp
void OnDrawGizmosSelected()
{
    Gizmos.color = Color.yellow;
    Gizmos.DrawWireSphere(transform.position, detectRange);

    Gizmos.color = Color.red;
    Gizmos.DrawWireSphere(transform.position, attackRange);
}
```

Select the zombie in the Scene view. You will see a yellow ring (sees you) and a red ring (hits you).

### The complete ZombieAI script

```csharp
using UnityEngine;
using UnityEngine.AI;

public class ZombieAI : MonoBehaviour
{
    private enum State { Idle, Chase, Attack, Dead }

    [SerializeField] private Transform target;
    [SerializeField] private float detectRange = 10f;
    [SerializeField] private float attackRange = 1.5f;
    [SerializeField] private float attackCooldown = 1f;

    private NavMeshAgent agent;
    private State state = State.Idle;
    private float nextAttackTime;

    void Awake()
    {
        agent = GetComponent<NavMeshAgent>();
        agent.stoppingDistance = attackRange * 0.8f;

        if (target == null)
        {
            GameObject player = GameObject.FindWithTag("Player");
            if (player != null) target = player.transform;
        }
    }

    void Update()
    {
        if (state == State.Dead || target == null) return;

        float distance = Vector3.Distance(transform.position, target.position);

        switch (state)
        {
            case State.Idle:   UpdateIdle(distance);   break;
            case State.Chase:  UpdateChase(distance);  break;
            case State.Attack: UpdateAttack(distance); break;
        }
    }

    private void ChangeState(State newState)
    {
        if (state == newState) return;

        state = newState;
        Debug.Log(name + " is now " + state);

        switch (state)
        {
            case State.Idle:
                agent.isStopped = true;
                break;
            case State.Chase:
                agent.isStopped = false;
                break;
            case State.Attack:
                agent.isStopped = true;
                break;
            case State.Dead:
                agent.isStopped = true;
                break;
        }
    }

    private void UpdateIdle(float distance)
    {
        if (distance <= detectRange)
            ChangeState(State.Chase);
    }

    private void UpdateChase(float distance)
    {
        agent.SetDestination(target.position);

        if (distance <= attackRange)
            ChangeState(State.Attack);
    }

    private void UpdateAttack(float distance)
    {
        FaceTarget();

        if (distance > attackRange + 0.5f)
        {
            ChangeState(State.Chase);
            return;
        }

        if (Time.time >= nextAttackTime)
        {
            nextAttackTime = Time.time + attackCooldown;
            Debug.Log(name + " hits the player!");
        }
    }

    private void FaceTarget()
    {
        Vector3 direction = target.position - transform.position;
        direction.y = 0f;

        if (direction != Vector3.zero)
            transform.rotation = Quaternion.LookRotation(direction);
    }

    public void Die()
    {
        ChangeState(State.Dead);
        Destroy(gameObject, 2f);
    }

    void OnDrawGizmosSelected()
    {
        Gizmos.color = Color.yellow;
        Gizmos.DrawWireSphere(transform.position, detectRange);

        Gizmos.color = Color.red;
        Gizmos.DrawWireSphere(transform.position, attackRange);
    }
}
```

### Set up the zombie

1. Create a **Capsule**. Name it `Zombie`. Give it a green material so you can tell it apart.
2. Add a **NavMeshAgent**. Set **Speed** to `3`.
3. Add the **ZombieAI** script.
4. Place it on the blue area, far from the hero.
5. Drag it into your Project window to make a **Prefab**. We will spawn many copies later.

Press Play. Walk the hero close to the zombie and watch the Console:

```
Zombie is now Chase
Zombie is now Attack
Zombie hits the player!
```

> **Try it:** Walk away from the zombie while it attacks. Does it go back to Chase? Now stand still. Does it hit you again after one second?

> **Try it:** While the game runs, right-click the `ZombieAI` component and look at it in the Inspector. Change `detectRange` live and see how the zombie's behavior changes.

---

## Tuning the feel

Numbers are not only code. They decide how the game *feels*.

| Setting             | Small value                                     | Large value                                      |
| ------------------- | ----------------------------------------------- | ------------------------------------------------ |
| **Detect Range**    | Zombie notices you late, so the game feels easy | Zombie notices you early, so the game feels hard |
| **Agent Speed**     | Easy to run away                                | Hard to escape. Feels scary                      |
| **Attack Cooldown** | Lots of damage, feels stressful                 | Gentle, gives the player time                    |
| **Attack Range**    | Player can dodge easily                         | Player gets hit from far away                    |

> **Design rule:** A good enemy is not the fastest or the strongest. It is the one that gives the player a *fair chance* to decide something.

---

## Common mistakes

| What you see                                                                      | Why it happens                                                  | The fix                                        |
| --------------------------------------------------------------------------------- | --------------------------------------------------------------- | ---------------------------------------------- |
| Error: `SetDestination` can only be called on an active agent placed on a NavMesh | The agent is not standing on the blue area                      | Move it onto the blue area and bake again      |
| The agent does not move at all                                                    | NavMesh not baked, or `isStopped` is still true                 | Click **Bake**. Check the state in the Console |
| The agent walks through a wall                                                    | The wall was added after baking                                 | Bake again                                     |
| The zombie does nothing                                                           | `target` is empty                                               | Tag the hero as `Player`                       |
| The zombie shakes between Chase and Attack                                        | No buffer between the two ranges                                | Add the `+ 0.5f` buffer                        |
| The agent slides or jumps strangely                                               | A Rigidbody is on the agent                                     | Remove the Rigidbody                           |
| Tapping does nothing                                                              | Ground is not on the `Ground` layer, or Ground Layer is not set | Check the layer on the plane and on the script |

---

## Recap

| Idea                     | In one sentence                                                         |
| ------------------------ | ----------------------------------------------------------------------- |
| **NavMesh**              | The map that shows where agents can walk                                |
| **Bake**                 | Build the NavMesh from your level                                       |
| **NavMeshAgent**         | A character that walks to a destination by itself                       |
| **SetDestination**       | Tell the agent where to go                                              |
| **Finite State Machine** | A character is in one state at a time and changes states by clear rules |
| **State**                | What the character is doing right now                                   |
| **Transition**           | A change from one state to another, caused by a condition               |
| **enum**                 | A list of names, used here to list the states                           |
| **Buffer**               | A small extra distance that stops a state from flickering               |

---

## Self-check questions

1. What is the difference between a NavMesh and a NavMeshAgent?
2. What does the blue area in the Scene view mean?
3. Why must you bake again after moving a wall?
4. What are the three rules of a Finite State Machine?
5. Why do we put every state change inside one `ChangeState` method?
6. What problem does the `+ 0.5f` buffer solve?
7. Why does the Dead state have no transition out?

---

## Practice tasks

### Easy

- Change `detectRange`, `attackRange` and the agent speed. Write down how each change affects the game.
- Add a second zombie with a different color and speed. Does it use the same script?

### Medium

- Add a **Patrol** state. In Idle, instead of standing still, the zombie walks between two points. If the player gets close, it switches to Chase.
- Add a **Lose Interest** transition. If the player is farther than `loseRange` (for example 15 units), the zombie goes back to Idle.

### Hard

- Add a **Rage** state. When the zombie has chased for more than 5 seconds, its speed goes up by 50 percent. Draw a new transition table before you write any code.
- Draw the full state diagram of your zombie on paper (circles for states, arrows for transitions with the condition written on each arrow). Then check that your code matches your drawing.

---

## What comes next

Right now the zombie only prints a message when it hits. In the next lesson we connect it to the game:

- The hero gets **health** and takes real damage.
- The hero **attacks** a zombie when you tap it, and the zombie calls `Die()`.
- A **spawner** makes many zombies, and a **timer** makes the game tense.

---

*Contracted by NextSkill. A Gamestorms company. Created for Isra University.*