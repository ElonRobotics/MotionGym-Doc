---
layout: default
title: 运动重定向服务
parent: 服务概览
nav_order: 5
---

# 运动重定向服务

## 概述

运动重定向服务用于将源运动数据映射到目标机器人模型，支持不同骨骼结构之间的运动转换。

## 源文件

### 前端

`src/utils/motion/ActionTrajectoryService.ts`

### 后端 (Rust)

`src-tauri/src/services/retarget_native.rs`

### 原生代码

`src-tauri/native/retarget_bridge.cpp`

## 核心功能

### 1. 骨骼映射

配置源与目标骨骼的对应关系：

```typescript
interface BoneMapping {
  source: string;
  target: string;
}
```

### 2. 轨迹计算

计算重定向后的运动轨迹：

```typescript
function computeTrajectory(
  sourceMotion: MotionData,
  mapping: BoneMapping[]
): RetargetPlaybackData
```

### 3. 原生加速

使用 C++ 原生代码进行高性能计算。

## RetargetPlaybackData

```typescript
interface RetargetPlaybackData {
  fps: number;
  frameCount: number;
  dofCount: number;
  rootPositions: Float32Array;
  rootRotations: Float32Array;
  dofPositions: Float32Array;
}
```
