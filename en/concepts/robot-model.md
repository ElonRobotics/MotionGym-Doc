---
layout: default
title: Robot Model
parent: Concepts
nav_order: 3
has_toc: true
description: Kinematic model built from URDF or MJCF input
lang: en
ref: concepts-robot-model
permalink: /en/concepts/robot-model.html
---

# Robot Model

`RobotModel` stores joint metadata, the kinematic tree, limits, and forward-kinematics cache.

## Typical source

```text
URDF / MJCF
  -> parsed asset model
  -> RobotModel::from_parsed(...)
```

For Unitree G1, the documentation examples use:

```text
assets/unitree_g1/urdf/g1_mocap_29dof.urdf
```
