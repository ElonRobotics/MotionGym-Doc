---
layout: default
title: 重映射
parent: Rust API
nav_order: 4
lang: zh
ref: rust-api-entry-points
---

# 重映射

## retarget_from_bvh

高层主入口，将 BVH 文件路径转换为机器人姿态。

```rust
pub fn retarget_from_bvh(
    bvh_path: &str,
    options: BvhRetargetOptions,
) -> Result<RetargetOutput>
```

### 参数

| 参数 | 类型 | 说明 |
| :--- | :--- | :--- |
| `bvh_path` | `&str` | BVH 文件路径 |
| `options` | `BvhRetargetOptions` | 重定向配置 |

### 返回值

`Result<RetargetOutput>`

### 示例

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
