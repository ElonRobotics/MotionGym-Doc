---
layout: default
title: Resample
parent: Rust API
nav_order: 7
lang: en
ref: rust-api-resample
permalink: /en/docs/reference/rust/resample.html
---

# Resample

`ee_sdk::resample` provides frame-rate resampling for human pose sequences.

## Constants

| Constant | Value | Description |
| :--- | :--- | :--- |
| `STANDARD_FPS` | 30 | Standard output frame rate |
| `MIN_INPUT_FPS` | 15 | Minimum accepted input frame rate |
| `MAX_INPUT_FPS` | 120 | Maximum accepted input frame rate |

## resample_frames

Resample a pose sequence from `src_fps` to `dst_fps`.

```rust
pub fn resample_frames(
    frames: &[Frame],
    src_fps: u32,
    dst_fps: u32,
) -> Result<Vec<Frame>>
```

### Parameters

| Param | Type | Description |
| :--- | :--- | :--- |
| `frames` | `&[Frame]` | Input frame list |
| `src_fps` | `u32` | Source frame rate |
| `dst_fps` | `u32` | Target frame rate |

### Returns

`Result<Vec<Frame>>` — Resampled frame list

### Algorithm

- Downsample: nearest-neighbor
- Upsample: linear interpolation (lerp) + spherical interpolation (slerp)

### Example

```rust
use ee_sdk::prelude::*;
use ee_sdk::resample::{resample_frames, STANDARD_FPS};

let frames: Vec<ee_sdk::resample::Frame> = /* 60fps frames */;
let resampled = resample_frames(&frames, 60, STANDARD_FPS)?;
```
