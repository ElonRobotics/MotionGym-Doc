---
layout: default
title: BVH 与模型加载
parent: 核心概念
nav_order: 4
has_toc: true
description: BVH 标准化、标准骨架和模型文件加载
lang: zh
ref: concepts-loader
---

# BVH 与模型加载

加载层负责两类输入：

1. 动作输入：BVH
2. 模型输入：URDF / MJCF

## BVH 的两类标准化

底层 `load_bvh()` 支持显式覆盖旋转轴顺序：

```rust
load_bvh(path, max_frames, axis_order)
```

当 `axis_order` 为 `Some("XYZ")`、`Some("ZXY")` 等时，会在 hierarchy 解析完成后、motion 处理前覆盖所有关节的 rotation channel 名称，position channel 保持不变；传 `None` 则沿用 BVH 文件声明。

### 1. Retarget-ready BVH

接口：`ee_sdk::convert_bvh_to_retarget_bvh`

作用：

- 保留原骨架层级
- 统一旋转通道顺序
- 让后续解析和重定向更稳定

```rust
let ok = ee_sdk::convert_bvh_to_retarget_bvh(
    "data/jobs/source/bvh/good_time.bvh",
    "good_time_normalized.bvh",
    Some("ZXY"),
    None,
);

assert!(ok);
```

输出片段：

```text
CHANNELS 3 Zrotation Xrotation Yrotation
```

### 2. Standard BVH

接口：`ee_sdk::standard_bvh::convert_bvh_to_standard_bvh`

作用：

- 把不同来源 BVH 统一成项目标准 24 关节骨架
- 统一骨架命名
- 便于数据交换、调试和回归测试

```rust
use ee_sdk::standard_bvh::convert_bvh_to_standard_bvh;

let summary = convert_bvh_to_standard_bvh(
    "data/jobs/source/bvh/good_time.bvh",
    "good_time_standard.bvh",
)?;

println!("fps={}, frames={}", summary.fps, summary.frame_count);
```

输出片段：

```text
ROOT Hips
JOINT Spine3
JOINT LeftToe
JOINT RightToe
```

## 标准骨架关节名

文档与源码当前围绕以下标准关节名工作：

`Hips`, `Spine`, `Spine1`, `Spine2`, `Spine3`, `Neck`, `Head`, `LeftShoulder`, `LeftArm`, `LeftForeArm`, `LeftHand`, `RightShoulder`, `RightArm`, `RightForeArm`, `RightHand`, `LeftUpLeg`, `LeftLeg`, `LeftFoot`, `LeftToe`, `RightUpLeg`, `RightLeg`, `RightFoot`, `RightToe`。

## 模型文件加载

重定向除了读 BVH，还需要目标机器人模型。

- URDF: 主要用于构建目标机器人运动学模型
- MJCF: 主要用于导出顺序和模拟侧配置对齐

对于 Unitree G1，高层示例通常直接指定 URDF：

```rust
.with_robot_file("assets/unitree_g1/urdf/g1_mocap_29dof.urdf")
```
