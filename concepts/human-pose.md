---
layout: default
title: HumanPose
parent: 核心概念
nav_order: 1
has_toc: true
description: 重定向流程里使用的人体姿态表示
lang: zh
ref: concepts-human-pose
---

# HumanPose

`HumanPose` 是 MotionGym 在内存中表示单个关节姿态的基础结构。BVH 文件被加载并标准化后，最终会被转换成按关节组织的 `HumanPose`。

## 定义

源码定义位于 `ee-common/src/pose.rs`：

```rust
pub struct HumanPose {
    pub pos: [f64; 3],
    pub quat_wxyz: [f64; 4],
}
```

## 字段说明

| 字段 | 类型 | 说明 |
| :--- | :--- | :--- |
| `pos` | `[f64; 3]` | 关节在世界坐标系中的位置，单位为米 |
| `quat_wxyz` | `[f64; 4]` | 关节旋转四元数，顺序为 `wxyz` |

## 为什么它重要

- `load_bvh()` 返回的每一帧数据，本质上是 `HashMap<String, HumanPose>`
- `RetargetSession::retarget_frame()` 接收的也是这类数据
- `convert_bvh_to_standard_bvh()` 写出的标准 BVH，重新读回后仍会得到 `HumanPose` 帧序列

## 与 CSV 输出的区别

`HumanPose` 内部使用 `quat_wxyz`。但机器人根姿态导出到 CSV 时，`RetargetOutput` / `FrameOutput` 中的根四元数使用 `xyzw` 顺序。

```text
HumanPose: quat_wxyz
CSV root quaternion: root_rot_xyzw
```

## 示例

```rust
use ee_sdk::ee_core::HumanPose;

let hips = HumanPose {
    pos: [0.0, 0.95, 0.0],
    quat_wxyz: [1.0, 0.0, 0.0, 0.0],
};

println!("hips pos = {:?}", hips.pos);
println!("hips quat = {:?}", hips.quat_wxyz);
```

示例输出：

```text
hips pos = [0.0, 0.95, 0.0]
hips quat = [1.0, 0.0, 0.0, 0.0]
```
