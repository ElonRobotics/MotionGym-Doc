---
layout: default
title: 核心概念
nav_order: 3
has_toc: true
description: MotionGym 中与重定向相关的核心数据结构与处理流程
lang: zh
ref: concepts
---

# 核心概念

本节解释 MotionGym 在 Rust 实现里真正参与 BVH 解析、模型加载、IK 求解和重定向输出的核心对象。

## 阅读顺序

1. [HumanPose](./concepts/human-pose.html)
2. [IK 求解器](./concepts/ik-solver.html)
3. [机器人模型](./concepts/robot-model.html)
4. [BVH 与模型加载](./concepts/loader.html)

## 处理链路

```text
BVH 文件
  -> BVH 规整化 / 标准骨架转换
  -> 逐帧 HumanPose
  -> MotionRetargetEngine / RetargetSession
  -> Unitree G1 root + dof 输出
  -> CSV
```

## 核心对象

| 页面 | 作用 |
| :--- | :--- |
| [HumanPose](./concepts/human-pose.html) | 表示单个关节在世界坐标系下的位置和旋转 |
| [IK 求解器](./concepts/ik-solver.html) | 将笛卡尔目标转换成机器人关节位姿 |
| [机器人模型](./concepts/robot-model.html) | 从 URDF/MJCF 构建运动学树和关节限位 |
| [BVH 与模型加载](./concepts/loader.html) | 负责 BVH 标准化、URDF/MJCF 解析和标准骨架导出 |

## 与 Unitree G1 的关系

文档中的高层示例默认以 `Robot::UnitreeG1` 为目标机器人。源码里的默认 URDF 路径为：

```text
assets/unitree_g1/urdf/g1_mocap_29dof.urdf
```

如果你只是想尽快接入，请直接阅读 [Rust API](./docs/reference/rust.html)。
