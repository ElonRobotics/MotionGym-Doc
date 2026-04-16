---
layout: default
title: Retargeting
parent: Rust API
nav_order: 4
lang: en
ref: rust-api-entry-points
permalink: /en/docs/reference/rust/entry-points.html
---

# Retargeting

## retarget_from_bvh

High-level entry point for converting BVH file to robot poses.

```rust
pub fn retarget_from_bvh(
    bvh_path: &str,
    options: BvhRetargetOptions,
) -> Result<RetargetOutput>
```

### Parameters

| Param | Type | Description |
| :--- | :--- | :--- |
| `bvh_path` | `&str` | BVH file path |
| `options` | `BvhRetargetOptions` | Retargeting configuration |

### Returns

`Result<RetargetOutput>`

### Example

```rust
use ee_sdk::prelude::*;

let output = retarget_from_bvh(
    "data/jobs/source/bvh/good_time.bvh",
    BvhRetargetOptions::new(Robot::UnitreeG1)
        .with_frame_range(Some(0), Some(299))
        .with_fps(Some(60))
        .with_offset_to_ground(true),
)?;

std::fs::write("output.csv", output.to_csv())?;
```
