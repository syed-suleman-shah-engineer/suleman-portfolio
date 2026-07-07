---
title: "Lesson 2: Dummy Test Lesson"
order: 2
description: "A sample lesson used to verify course layout, sidebar outline, and navigation."
tags: [Unity, Course, Test]
---

## Goal

This is a dummy lesson to test the Unity course structure.

## What You Should See

- The lesson appears on the Unity Course index page.
- The sidebar on lesson pages shows the full course outline.
- This lesson is highlighted as active when opened.
- Previous and next lesson links appear at the bottom.

## Sample Snippet

```csharp
using UnityEngine;

public class DummySpin : MonoBehaviour
{
    [SerializeField] private float speed = 90f;

    private void Update()
    {
        transform.Rotate(0f, speed * Time.deltaTime, 0f);
    }
}
```

## Notes

Replace this file with your real Lesson 2 content when you are ready.
