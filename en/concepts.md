---
layout: default
title: Concepts
nav_order: 3
has_toc: true
description: Concrete concepts behind BVH loading and retargeting
lang: en
ref: concepts
permalink: /en/concepts.html
---

# Concepts

This section explains the concrete data structures and processing stages used by MotionGym.

## Reading Order

1. [HumanPose](./concepts/human-pose.html)
2. [IK Solver](./concepts/ik-solver.html)
3. [BVH And Asset Loading](./concepts/loader.html)

## Pipeline

```text
BVH file
  -> normalization / standard skeleton conversion
  -> per-frame HumanPose
  -> MotionRetargetEngine / RetargetSession
  -> Unitree G1 root pose + DOF output
  -> CSV
```
