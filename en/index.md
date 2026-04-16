---
layout: default
title: Home
nav_order: 1
description: Motion retargeting documentation for MotionGym
lang: en
ref: home
permalink: /en/
---

# MotionGym

<div class="hero-block">
  <p class="hero-kicker">Motion Retargeting Engine</p>
  <h2>Documentation center for robot motion retargeting</h2>
  <p class="hero-lead">The stable public integration surface now includes both the Rust SDK and the local HTTP API. This site focuses on BVH preprocessing, Unitree G1 retargeting, data structures, and the kinematic pipeline.</p>
  <div class="hero-actions">
    <a class="btn btn-primary" href="{{ '/en/docs/installation.html' | relative_url }}">Installation</a>
    <a class="btn btn-secondary" href="{{ '/en/docs/reference/rust.html' | relative_url }}">Rust API</a>
  </div>
</div>

## Sections

| Section | Description |
| :--- | :--- |
| Installation | From scratch: environment setup, project creation, dependency install, and first run |
| Concepts | HumanPose, robot models, IK solver, BVH / URDF / MJCF loading pipeline |
| Rust API | Core concepts, data import, retargeting, robot model query — the complete SDK reference |
| HTTP API | Service endpoints for skeleton extraction, robot model query, and BVH retargeting |

## Quick Example

```rust
use ee_sdk::prelude::*;

let output = retarget_from_bvh(
    "data/jobs/source/bvh/good_time.bvh",
    BvhRetargetOptions::new(Robot::UnitreeG1),
)?;

std::fs::write("output.csv", output.to_csv())?;
```
