---
layout: default
title: Rust API
nav_order: 4
has_toc: true
description: ee-sdk Rust API 参考文档
lang: zh
ref: rust-api
---

# Rust API

`ee-sdk` 是 MotionGym 对外的主接口层，提供从 BVH 文件到机器人关节值的一站式重映射能力。所有类型通过 `use ee_sdk::prelude::*;` 一并导入。

## 最小示例

```rust
use ee_sdk::prelude::*;

let output = retarget_from_bvh(
    "data/jobs/source/bvh/good_time.bvh",
    BvhRetargetOptions::new(Robot::UnitreeG1),
)?;

std::fs::write("output.csv", output.to_csv())?;
```
