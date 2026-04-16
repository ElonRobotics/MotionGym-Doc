---
layout: default
title: 重采样
parent: Rust API
nav_order: 7
lang: zh
ref: rust-api-resample
---

# 重采样

`ee_sdk::resample` 模块提供帧率重采样功能。

## 常量

| 常量 | 值 | 说明 |
| :--- | :--- | :--- |
| `STANDARD_FPS` | 30 | 标准输出帧率 |
| `MIN_INPUT_FPS` | 15 | 最小接受输入帧率 |
| `MAX_INPUT_FPS` | 120 | 最大接受输入帧率 |

## resample_frames

将姿态序列从 `src_fps` 重采样到 `dst_fps`。

```rust
pub fn resample_frames(
    frames: &[Frame],
    src_fps: u32,
    dst_fps: u32,
) -> Result<Vec<Frame>>
```

### 参数

| 参数 | 类型 | 说明 |
| :--- | :--- | :--- |
| `frames` | `&[Frame]` | 输入帧列表 |
| `src_fps` | `u32` | 源帧率 |
| `dst_fps` | `u32` | 目标帧率 |

### 返回值

`Result<Vec<Frame>>` — 重采样后的帧列表

### 算法

- 降采样：最近邻抽取
- 升采样：线性插值位置（lerp）+ 四元数球面插值（slerp）

### 示例

```rust
use ee_sdk::prelude::*;
use ee_sdk::resample::{resample_frames, STANDARD_FPS};

let frames: Vec<ee_sdk::resample::Frame> = /* 60fps 帧 */;
let resampled = resample_frames(&frames, 60, STANDARD_FPS)?;
```
