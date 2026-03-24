---
layout: default
title: 地面对齐服务
parent: 服务概览
nav_order: 3
---

# 地面对齐服务

## 概述

地面对齐服务用于计算运动数据的地面接触参数，确保机器人模型在地面上的正确放置。

## 源文件

`src/utils/motion/groundAlignment.ts`

## 核心函数

### computeGroundAlignment

计算地面对齐参数：

```typescript
function computeGroundAlignment(
  rootPositions: Float32Array,
  heightOffset: number
): GroundAlignment
```

**参数**:
- `rootPositions`: 根部位置数组
- `heightOffset`: 高度偏移

**返回值**: 地面对齐参数

### getAlignedRootY

获取对齐后的根部 Y 坐标：

```typescript
function getAlignedRootY(
  rootPositions: Float32Array,
  frame: number,
  alignment: GroundAlignment
): number
```

### syncGroundToRobot

同步地面到机器人：

```typescript
function syncGroundToRobot(
  robot: UrdfRobotLike,
  alignment: GroundAlignment
): void
```

## 类型定义

### GroundAlignment

```typescript
interface GroundAlignment {
  footHeight: number;
  groundY: number;
  offset: number;
}
```
