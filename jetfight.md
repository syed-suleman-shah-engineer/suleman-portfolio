---
layout: default
title: "Unity Course"
permalink: /jetfight/
---

<section style="max-width: 1100px; margin: 0 auto; padding: 60px 24px 120px">
  <div style="margin-bottom: 40px">
    <p class="section-label">Learning Track</p>
    <h1 class="section-title" style="font-size: clamp(30px, 5vw, 44px)">
      Unity Development Course
    </h1>
    <p
      style="
        margin-top: 10px;
        max-width: 640px;
        font-size: 14px;
        color: var(--t2);
        line-height: 1.8;
      "
    >
      A structured path from Unity fundamentals to production-ready gameplay
      systems. Start from lesson 1 and move forward in order.
    </p>
  </div>

  {% assign lessons = site.unity_course | sort: "order" %}

  <div
    style="
      display: grid;
      grid-template-columns: repeat(auto-fit, minmax(280px, 1fr));
      gap: 16px;
    "
  >
    {% for lesson in lessons %}
    <a href="{{ lesson.url }}" style="text-decoration: none">
      <article class="card" style="padding: 22px; min-height: 190px">
        <div style="display: flex; align-items: center; gap: 8px; margin-bottom: 12px">
          <span class="tag tag-featured"
            >Lesson {{ lesson.order | default: forloop.index }}</span
          >
          <span class="pill">Unity Course</span>
        </div>

        <h2
          style="
            font-size: 18px;
            font-weight: 700;
            line-height: 1.35;
            letter-spacing: -0.01em;
            color: var(--t1);
            margin-bottom: 10px;
          "
        >
          {{ lesson.title }}
        </h2>

        {% if lesson.description %}
        <p style="font-size: 14px; color: var(--t2); line-height: 1.75; flex: 1">
          {{ lesson.description }}
        </p>
        {% endif %}

        <p style="margin-top: 18px; font-size: 13px; color: var(--gold); font-weight: 600">
          Open Lesson →
        </p>
      </article>
    </a>
    {% endfor %}
  </div>
</section>