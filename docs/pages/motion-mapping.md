---
layout: default
title: 运动映射
parent: 页面文档
nav_order: 3
---

# 运动映射 (Motion Mapping)

## 概述

运动映射页面用于将源运动数据映射到目标机器人模型，支持骨骼对应关系配置和实时预览。

## 路由

```
/mapping
```

## 源文件

`src/pages/MotionMappingPage.vue`

## 核心功能

### 1. 运动数据映射

支持以下源类型：
- **视频数据 (mp4)**: 视频驱动的运动捕捉
- **动捕数据 (bvh)**: BVH 格式的动作数据
- **其他类型**: 自定义格式

### 2. 目标机器人

支持的机器人类型：
- **Unitree G1**: 宇树 G1 人形机器人
- **Fourier N1**: 傅利叶 N1 机器人
- **Unitree H1**: 宇树 H1 人形机器人

### 3. 映射配置

- **骨骼对应关系**: 配置源与目标骨骼的映射
- **DOF 映射**: 关节自由度对应
- **根部对齐**: 计算地面对齐参数

### 4. 重定向播放

- **FPS**: 输出运动帧率
- **帧数**: 输出运动总帧数
- **DOF 数量**: 输出的自由度数量
- **根部位置/旋转**: 实时显示根部变换

### 5. UI 面板

- **左侧面板**: 源运动和目标模型选择（可折叠）
- **右侧面板**: 映射配置和日志（可折叠）
- **日志面板**: 显示处理状态和错误信息

## 状态管理

使用 Pinia store (`stores/motionMapping.ts`) 管理状态：

- `sourceType`: 源数据类型
- `sourceHuman`: 源人体类型
- `targetRobot`: 目标机器人
- `retargetPlayback`: 重定向播放数据

## 关键函数

### computeGroundAlignment

计算地面对齐参数：

```typescript
function computeGroundAlignment(
  rootPositions: Float32Array,
  heightOffset: number
): GroundAlignment
```

### getAlignedRootY

获取对齐后的根部 Y 坐标：

```typescript
function getAlignedRootY(
  rootPositions: Float32Array,
  frame: number,
  alignment: GroundAlignment
): number
```

## 依赖服务

- `safeInvoke`: Tauri 后端调用
- `groundAlignment`: 地面对齐工具
- `ThreeViewer`: 3D 渲染组件
