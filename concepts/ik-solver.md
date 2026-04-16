---
layout: default
title: IK 求解器
parent: 核心概念
nav_order: 2
has_toc: true
description: 逆运动学求解器和任务对象
lang: zh
ref: concepts-ik-solver
---

# IK 求解器

MotionGym 的底层 IK 位于 `ee-core/src/solver/ik.rs`。它的职责是根据目标位姿和机器人模型，求出每一帧的关节更新量。

## IKSolverOptions

`IKSolverOptions` 是底层求解器配置。它不是 `ee-sdk` 顶层主入口，但会影响低层求解行为。

源码中的几个关键字段：

| 字段 | 类型 | 默认值 | 说明 |
| :--- | :--- | :--- | :--- |
| `solver_type` | `String` | `"daqp"` | 底层求解器实现名 |
| `damping` | `f64` | `0.5` | 阻尼最小二乘的阻尼系数 |
| `max_iterations` | `usize` | `100` | 每帧最大迭代次数 |
| `dt` | `f64` | `0.001` | 速度级计算使用的时间步长 |
| `variable_is_velocity` | `bool` | `false` | 是否直接在速度空间求解 |

## FrameTask

`FrameTask` 表示一个需要跟踪的目标帧。它至少包含：

| 字段 | 类型 | 说明 |
| :--- | :--- | :--- |
| `frame_name` | `String` | 目标 link / frame 名称 |
| `frame_type` | `String` | 通常是 `body` 或 `joint` |
| `position_cost` | `f64` | 位置误差权重 |
| `orientation_cost` | `f64` | 旋转误差权重 |
| `lm_damping` | `f64` | 每个任务自身的 LM 阻尼 |

## 典型用法

```rust
use ee_sdk::ee_core::solver::ik::{FrameTask, IKSolverOptions};

let options = IKSolverOptions {
    damping: 0.1,
    max_iterations: 50,
    ..Default::default()
};

let mut hand_task = FrameTask::new("right_palm_link", "body", 1.0, 1.0, 1.0);
hand_task.set_target([0.45, -0.10, 0.95], [1.0, 0.0, 0.0, 0.0]);

println!("solver={}, max_iterations={}", options.solver_type, options.max_iterations);
```

示例输出：

```text
solver=daqp, max_iterations=50
```

## 和高层 API 的关系

如果你使用 `retarget_from_bvh()` 或 `RetargetSession`，通常不会直接操作 `IKSolverOptions`，而是通过 `GMROptions` / `GMROptionsBuilder` 间接控制求解流程。
