---
layout: default
title: HumanPose
parent: Concepts
nav_order: 1
has_toc: true
description: Canonical in-memory pose type used by the pipeline
lang: en
ref: concepts-human-pose
permalink: /en/concepts/human-pose.html
---

# HumanPose

`HumanPose` is the canonical in-memory representation for one joint pose after BVH loading and normalization.

## Definition

```rust
pub struct HumanPose {
    pub pos: [f64; 3],
    pub quat_wxyz: [f64; 4],
}
```

## Fields

| Field | Type | Meaning |
| :--- | :--- | :--- |
| `pos` | `[f64; 3]` | World-space joint position in meters |
| `quat_wxyz` | `[f64; 4]` | World-space quaternion in `wxyz` order |

## Why it matters

- BVH frames are loaded as `HashMap<String, HumanPose>`
- `RetargetSession::retarget_frame()` consumes the same structure
- CSV export changes only the robot root quaternion order to `xyzw`
