---
layout: default
title: 运动数据服务 (Rust)
parent: 服务概览
nav_order: 4
---

# 运动数据服务 (Rust)

## 概述

Rust 后端的运动数据处理服务，提供高性能的运动数据解析和处理功能。

## 源文件

`src-tauri/src/services/motion_service.rs`

## 核心功能

### 1. BVH 加载

```rust
#[tauri::command]
pub fn load_bvh_motion(path: String) -> Result<BvhMotionData, String>
```

### 2. CSV 加载

```rust
#[tauri::command]
pub fn load_csv_motion(path: String) -> Result<CsvMotionLoadResult, String>
```

### 3. 文件读取

```rust
#[tauri::command]
pub fn read_text_file(path: String) -> Result<String, String>
```

## 数据模型

### BvhMotionData

```rust
pub struct BvhMotionData {
    pub frame_count: usize,
    pub fps: f64,
    pub joint_count: usize,
    pub data: Vec<f32>,
}
```

### CsvMotionLoadResult

```rust
pub struct CsvMotionLoadResult {
    pub clip: MotionClip,
}
```
