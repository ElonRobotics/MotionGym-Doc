---
layout: default
title: BVH And Asset Loading
parent: Concepts
nav_order: 4
has_toc: true
description: BVH normalization, standard skeleton conversion, and model loading
lang: en
ref: concepts-loader
permalink: /en/concepts/loader.html
---

# BVH And Asset Loading

The loading layer handles both motion input and robot model input.

## Two kinds of BVH normalization

The low-level `load_bvh()` function accepts an explicit axis-order override:

```rust
load_bvh(path, max_frames, axis_order)
```

When `axis_order` is `Some("XYZ")`, `Some("ZXY")`, etc., all rotation channel names are overridden after hierarchy parsing and before motion conversion. Position channels remain unchanged. `None` keeps the order declared in the BVH file.

| API | Result |
| :--- | :--- |
| `convert_bvh_to_retarget_bvh` | Keeps the original hierarchy but normalizes rotation channels |
| `convert_bvh_to_standard_bvh` | Exports the project standard 24-joint BVH skeleton |

## Unitree G1 model path

```text
assets/unitree_g1/urdf/g1_mocap_29dof.urdf
```
