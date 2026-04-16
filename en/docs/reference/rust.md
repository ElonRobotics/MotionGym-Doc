---
layout: default
title: Rust API
nav_order: 4
has_toc: true
description: Public Rust API for MotionGym
lang: en
ref: rust-api
permalink: /en/docs/reference/rust.html
---

# Rust API

`ee-sdk` is the public integration layer for MotionGym, providing end-to-end retargeting from BVH files to robot joint values. All types are conveniently available via `use ee_sdk::prelude::*;`.

## Quick Start

```rust
use ee_sdk::prelude::*;

let output = retarget_from_bvh(
    "data/jobs/source/bvh/good_time.bvh",
    BvhRetargetOptions::new(Robot::UnitreeG1),
)?;

std::fs::write("output.csv", output.to_csv())?;
```
