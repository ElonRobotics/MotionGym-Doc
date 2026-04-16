---
layout: default
title: IK Solver
parent: Concepts
nav_order: 2
has_toc: true
description: Inverse kinematics tasks and solver options
lang: en
ref: concepts-ik-solver
permalink: /en/concepts/ik-solver.html
---

# IK Solver

The low-level solver lives in `ee-core/src/solver/ik.rs`. It converts Cartesian task targets into robot joint updates.

## Important fields in `IKSolverOptions`

| Field | Type | Default | Meaning |
| :--- | :--- | :--- | :--- |
| `solver_type` | `String` | `"daqp"` | Backend solver name |
| `damping` | `f64` | `0.5` | Damped least-squares coefficient |
| `max_iterations` | `usize` | `100` | Iteration cap per frame |
| `dt` | `f64` | `0.001` | Time step for velocity-space computations |

## `FrameTask`

`FrameTask` describes one tracked body or joint target with position/orientation weights.
