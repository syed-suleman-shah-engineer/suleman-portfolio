---
title: "Lesson 1: Install Unity 3D and Set Up Your Development Environment"
order: 1
description: "A step-by-step beginner's guide to installing Unity Hub, setting up a free license, and creating your first Unity 3D project — no experience required."
tags: [Unity, Unity3D, Game Development, Beginner Guide, Unity Hub, Installation]
---

Welcome to Lesson 1 of the Unity 3D Development Course. In this lesson, we'll introduce the Unity engine, walk through installing Unity Hub and the Unity Editor step by step, and set up your very first project — no prior game development experience required.

## Goal

By the end of this lesson, you will be able to:

- Explain what Unity is and why it's a popular choice for beginners and professional studios alike
- Download and install Unity Hub, the control center for everything Unity
- Activate your free Unity Personal license
- Install the correct version of the Unity Editor, and understand why choosing LTS matters
- Create your first Unity project with the right settings for your game
- Recognize and navigate the core panels of the Unity Editor: Scene, Game, Hierarchy, Inspector, Console, and Project window

## About Unity 3D and Why Choose It for Game Development?

Every popular development tool tends to build a large community around it, and Unity is no exception. A large community means something very practical for you as a learner: thousands of free and paid assets, tutorials, and forum threads that have already solved most of the problems you'll run into. If you get stuck on a bug at 2 a.m., chances are someone else has already written about it.

### Is Unity Free to Use?

Yes. **Unity Personal** is completely free for individuals and small studios whose total annual revenue and funding stays under $200,000 USD. Cross that threshold, and you're required to move to **Unity Pro** (up to $25 million in revenue) or **Unity Enterprise** beyond that.

So how does Unity make money if most beginners pay nothing? Mainly through:

- Paid subscriptions (Pro and Enterprise) for established studios and companies
- Optional cloud services — hosting, multiplayer backends, analytics, and "economy" tools — billed on a pay-as-you-grow basis as your game scales

You may also hear other developers mention Unity's controversial "Runtime Fee" announcement from 2023, which understandably alarmed a lot of the community. Unity has since fully reversed course: games built with Unity 6 (and every version before it) can be published without paying any runtime fee, royalty, or revenue share. As a working developer, I personally never felt much impact even during that controversy — mainly because I build my own backend services (in ASP.NET) rather than relying on Unity's paid cloud tools — but it's worth knowing this history, since you'll likely hear about it from other developers along the way.

### Why Cross-Platform Development Matters

For me, the single best thing about Unity is that it's genuinely cross-platform. You build your game once, and — with some platform-specific adjustments — the same project can be exported to:

- **Desktop** — Windows, macOS, and Linux
- **Mobile** — Android and iOS
- **Web** — WebGL, playable directly in a browser
- **Consoles** — PlayStation and Xbox (with the appropriate developer registration)
- **VR** — Meta Quest and other headsets

It's a bit like writing a document once in a word processor and then exporting it as a PDF, an eBook, or a printed page — the content stays the same, but Unity handles most of the adjustment work for each format.

This matters more than it might sound. Earlier in my career, deployment was often the most painful part of any project — I've seen builds stall for months because of a single missing dependency, even when the actual app only took a few weeks to build. Unity doesn't eliminate every deployment challenge, but it removes a huge amount of that friction, which is a big part of why it remains so popular across teams of every size.

## Installing Unity: Hub, License, and Editor Setup

### What Is Unity Hub?

Before installing Unity itself, you'll install **Unity Hub** — a companion app that manages everything around the Editor. Think of it as a control panel: it installs whichever Unity Editor version(s) you need, manages your license, and keeps track of all your projects in one place, much like an app store manages installs and updates for your other software.

To get started, search "Unity Hub" on Google, or use the official link below. You'll need to sign in with a Google account or a Unity ID (free to create) before you're able to download it.

![unity-hub-download-page](/images/install-unity/download-unity-hub-page.png)

[Official Unity Hub installation guide](https://docs.unity.com/en-us/hub/install-hub)

Once installed, here's what Unity Hub's layout looks like:

![unity-hub-window](/images/install-unity/unity-hub-window.png)

On the left-hand navigation, you'll see: **Projects, Templates, Install, Learn, Resources,** and **Licenses**.

### Activating Your Free Unity Personal License

Go to the **Licenses** tab first. You should see a free **Personal** license available automatically — if it doesn't appear, refresh the page or add a license manually.

Click **"Get a free personal license,"** agree to the terms, and continue. That's it — you now have everything you need to start building.

![add-licenses-window](/images/install-unity/add-licenses-window.png)

### Installing the Unity Editor: Why LTS Matters

Next, go to the **Install** tab. For this course, we'll only install the **LTS** version.

**LTS stands for Long Term Support**, and it should always be your default choice unless you have a specific reason not to use it. Think of it the way you'd think about choosing a reliable family car over an experimental prototype: LTS releases are tested, stable, and supported for a long window of time (Unity 6.3 LTS, for example, is supported until December 2027), while newer "Update" releases ship features faster but with a shorter support lifespan and a higher chance that something changes mid-project.

Many developers are understandably cautious about upgrading an existing project to a newer Unity version — and that caution is reasonable once you're deep into production. But if you're starting fresh, always lean toward the current LTS. You get the most stability and the longest support window, with the least risk of running into deprecated features later.

![install-unity-editor-window](/images/install-unity/install-unity-editor-window.png)

I'm using **Unity 6.3 LTS** for this course, and I'd recommend you do the same unless you have a specific reason to pick a different version. Click install, and Unity Hub takes care of the rest — just let it run.

With the Editor installed, we're ready to create your first project.

## Creating Your First Unity Project

![new-project-window](/images/install-unity/new-project-window.png)

The **New Project** window has a few settings worth understanding properly, since they're easy to misconfigure without realizing it.

### Editor Version and Template

At the top, you'll choose which installed **Editor version** to use — on a first install, you'll likely only see the one version you just installed.

Below that is a list of **templates**, where you choose the type of project you're building:

- **Universal 2D** — for 2D games
- **Universal 3D** — for 3D games

Both are built on Unity's **Universal Render Pipeline (URP)** — a rendering system optimized to run well across almost every platform, from low-end mobile devices to high-end PCs. If you ever need extremely high-end, cinematic-quality visuals for PC- or console-only projects, Unity also offers a **High Definition Render Pipeline (HDRP)** template — at the cost of much heavier hardware requirements and reduced platform support.

A simple way to think about it: URP is like a good smartphone camera — versatile, efficient, and usable almost anywhere. HDRP is like a full professional camera rig — capable of stunning results, but heavier, more demanding, and only practical in the right setting. For this course, and for the vast majority of learning projects, **Universal 3D** is exactly what you want.

### Naming and Saving Your Project

On the right, give your project any name you like, and make sure you're creating it as a **Local** project rather than a Cloud one. Cloud projects sync automatically to Unity's servers — not harmful, but unnecessary while you're still learning, so keep things simple and local for now.

Choose a location on your computer, leave the remaining settings at their defaults, and click **Create project**.

Once the project is created, Unity opens the Editor for you — and it should look something like this:


## Understanding the Unity Editor Layout

![unity-3d-editor](/images/install-unity/unity-3d-editor.png)

The Unity Editor can look overwhelming the first time you see it — don't worry, you don't need to memorize any of it today. You'll get comfortable with the layout naturally, simply by using it every day. For now, here's a short introduction to what you're looking at.

Along the top sits the main menu bar: **File, Edit, Assets, GameObject, Component, Services, Window,** and **Help** — plus **Play, Pause,** and **Step** buttons for testing your game directly inside the Editor.

The rest of the Editor is made up of six main panels: **Hierarchy, Inspector, Console, Project, Scene,** and **Game**. Let's walk through each, starting with the two most important concepts in Unity: the Scene and the GameObject.

### Scene and GameObjects: The Building Blocks of Every Game

Think of a **Scene** as a stage — imagine a classroom, complete with a whiteboard, a projector, and rows of student desks. That entire room, exactly as it's arranged, is essentially what a Unity Scene represents: a self-contained space that holds everything placed inside it, whether that's a menu screen, a single level, or an entire game world.

A **GameObject** is any individual item placed inside that scene. In our classroom example, each desk, the whiteboard, and the projector would each be their own GameObject.

Every new Scene comes with one GameObject by default: the **Camera**. The Camera determines what the player actually sees — it's effectively the audience's eyes, deciding the angle and position from which your game world is viewed.

Every GameObject, without exception, also comes with a **Transform** component by default, describing:

- **Position** — where the object is located
- **Rotation** — which direction it's facing
- **Scale** — how big or small it is

A useful comparison: if a GameObject were a person, their Transform would be their GPS coordinates (position), the direction they're facing (rotation), and their height (scale).

### Hierarchy and Inspector

- **Hierarchy** — a list of every GameObject currently in your Scene, similar to a table of contents. Objects can also be nested inside one another (a "child" inside a "parent"), much like a family tree.
- **Inspector** — whenever you select a GameObject, its properties appear here. Think of it as that object's ID card: it shows you exactly what makes the object up, and lets you edit those details directly.

### Scene View vs. Game View

These two windows are often confused by beginners, but the distinction is simple:

- The **Scene** window is your workspace — think of it as a film set, where you can see all the props, lighting, and equipment as you build and arrange everything.
- The **Game** window shows exactly what your Camera sees — the same way a movie audience only sees what the camera actually filmed, not the crew and equipment standing just out of frame. Whatever appears in the Game window is what your players will experience.

### Console

The **Console** is where Unity logs everything worth knowing — errors, warnings, and any custom messages you write yourself. Treat it the way you'd treat a car's dashboard warning lights: it's the first place to check whenever something isn't working as expected.

### The Project Window and Your Game's Assets

The **Project window** holds every resource available to your game — models, images, audio, scripts, and scenes. Think of it as a warehouse: everything you might need lives here, ready to be pulled onto the stage (your Scene) when needed.

You create a new Scene directly inside this window, then double-click it to open and start editing.

If you need a 3D model, you can download one from anywhere online and import it into your project — but Unity doesn't support every file format equally:

- **3D models** — Unity directly reads `.fbx`, `.obj`, `.dae` (Collada), `.3ds`, `.dxf`, and `.skp` (SketchUp) files. It can also import proprietary files like `.blend` (Blender) or `.max` (3ds Max) directly, but only if that software is installed on the same computer, since Unity quietly converts them to FBX behind the scenes during import. Engineering/CAD formats such as `.step`, `.iges`, or `.stl` aren't supported directly and need to be converted first.
- **Images** — `.psd`, `.png`, `.jpg`, `.tga`, `.tiff`, `.gif`, `.bmp`, `.exr`, and `.hdr` are all supported. Even layered Photoshop `.psd` files import without issue — Unity flattens them automatically for use in-game, without ever touching your original file.
- **Audio** — `.wav`, `.mp3`, `.ogg`, and `.aiff` cover the vast majority of use cases.

One more detail worth knowing early: every file you import creates a matching **.meta** file alongside it. You won't see it in the Project window, but if you look at your project folder through Windows Explorer or macOS Finder, it's there. Think of it as a barcode sticker Unity attaches to every asset — it's how Unity keeps track of that exact file, even if you rename it later. Never delete `.meta` files manually.

If you browse your project folder outside Unity, you'll also notice other auto-generated folders alongside `Assets`, such as `Library`, `Packages`, and `ProjectSettings`. These are managed entirely by Unity — the `Assets` folder is the only one you'll be working in day to day.

---

With that, your development environment is fully set up. In the next lesson, we'll start writing our first lines of C# and get a GameObject moving on screen.