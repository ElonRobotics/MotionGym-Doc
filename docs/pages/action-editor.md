---
layout: default
title: 动作编辑器
parent: 页面文档
nav_order: 4
---

# 动作编辑器 (Action Editor)

## 概述

动作编辑器页面提供关键帧编辑、曲线调整、骨骼控制等功能，支持运动轨迹的可视化和编辑。

## 路由

```
/action
```

## 源文件

`src/pages/ActionEditorPage.vue`

## 核心功能

### 1. 曲线编辑

- **CurveEditorCanvas**: 曲线编辑器画布，支持贝塞尔曲线编辑
- **关键帧可视化**: 显示和编辑关键帧点

### 2. 骨骼控制

- **JointControlPanel**: 关节控制面板
- **BaseControlPanel**: 基础控制面板

### 3. 轨迹服务

- **ActionTrajectoryService**: 动作轨迹服务
- **SplineUtils**: 样条曲线工具

### 4. UI 布局

- **CSS 样式**: 自定义动作编辑器样式
- **响应式设计**: 适应不同屏幕尺寸

## 状态管理

使用 Pinia store (`stores/actionEditor.ts`) 管理状态：

- `selectedJoint`: 当前选中的关节
- `keyframes`: 关键帧数据
- `curves`: 曲线数据

## 独立运行

动作编辑器支持独立运行：

- `src/standalone/action-editor/App.vue`: 独立应用入口
- `src/standalone/action-editor/main.ts`: 独立应用入口文件
- `src/standalone/action-editor/router.ts`: 路由配置

## 类型定义

`src/types/actionEditor.ts` 定义了动作编辑器相关类型：

- 关节类型
- 关键帧类型
- 曲线类型
- 轨迹数据
