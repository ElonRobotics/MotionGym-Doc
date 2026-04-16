---
layout: default
title: 机器人模型
parent: 核心概念
nav_order: 3
has_toc: true
description: URDF/MJCF 解析后的运动学模型
lang: zh
ref: concepts-robot-model
---

# 机器人模型

`RobotModel` 位于 `ee-core/src/model/robot.rs`。它负责保存机器人关节信息、运动学树、关节限位以及前向运动学缓存。

## 它从哪里来

高层重定向流程不会让你手写 `RobotModel`。通常流程是：

```text
URDF / MJCF 文件
  -> loader 解析
  -> ParsedRobotModel
  -> RobotModel::from_parsed(...)
```

对 Unitree G1，文档示例默认使用：

```text
assets/unitree_g1/urdf/g1_mocap_29dof.urdf
```

## JointInfo

`RobotModel` 的公开字段里最重要的是 `joint_infos`，其元素类型为 `JointInfo`：

| 字段 | 类型 | 说明 |
| :--- | :--- | :--- |
| `name` | `String` | 关节名 |
| `joint_type` | `String` | 如 `floating`、`revolute`、`continuous` |
| `idx_q` | `usize` | 在广义位置向量 `q` 中的起始索引 |
| `nq` | `usize` | 该关节占用的 `q` 维度 |
| `idx_v` | `usize` | 在速度向量 `v` 中的起始索引 |
| `nv` | `usize` | 该关节占用的 `v` 维度 |
| `lower_limit` | `f64` | 下限 |
| `upper_limit` | `f64` | 上限 |
| `velocity_limit` | `f64` | 速度上限 |

## RobotModel 的几个关键公开字段

| 字段 | 说明 |
| :--- | :--- |
| `joint_infos` | 所有关节元数据 |
| `nq` | 广义位置维度 |
| `nv` | 广义速度维度 |
| `neutral_q` | 默认中性位姿 |
| `lower_limits` / `upper_limits` | 全局关节限位数组 |

## 示例

```rust
use ee_sdk::ee_core::model::RobotModel;

let robot = RobotModel::from_parsed(&parsed_data)?;

println!("nq={}, nv={}", robot.nq, robot.nv);
println!("joint_count={}", robot.joint_infos.len());
println!("first_joint={}", robot.joint_infos[0].name);
```

## 为什么文档里一直提到 URDF

因为高层 `RetargetConfig::with_robot_file(...)` 最终就是把你指定的 URDF 路径交给底层模型加载器，再生成 `RobotModel`。如果 URDF 路径不对，重定向就无法构造目标机器人。
