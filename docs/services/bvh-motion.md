---
layout: default
title: BvhMotionService
parent: 服务概览
nav_order: 2
---

# BvhMotionService 服务

## 概述

BvhMotionService 是 BVH 运动数据解析服务，用于加载和解析 BioVision Hierarchy 格式的运动数据。

## 源文件

`src/utils/motion/BvhMotionService.ts`

## 核心功能

### 1. BVH 文件加载

支持从文件和拖放束加载 BVH 数据：

```typescript
async loadFromFile(path: string): Promise<BvhMotionData>
async loadFromDroppedFiles(
  fileMap: Map<string, File>,
  resourcePath: string
): Promise<BvhMotionLoadResult>
```

### 2. 数据解析

解析 BVH 文件结构，包括：
- 层级结构 (Hierarchy)
- 关节定义 (Joint)
- 运动数据 (Motion)

### 3. 格式转换

将 BVH 数据转换为内部格式：

```typescript
toInternalFormat(bvhData: any): BvhMotionData
```

## 类型定义

### BvhMotionData

```typescript
interface BvhMotionData {
  frameCount: number;
  fps: number;
  jointCount: number;
  data: Float32Array;
  jointNames: string[];
}
```

### BvhMotionLoadResult

```typescript
interface BvhMotionLoadResult {
  selectedBvhPath: string;
  frameCount: number;
  fps: number;
  jointCount: number;
}
```
