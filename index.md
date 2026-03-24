---
title: Home
layout: home
---

# Motion Viewer

基于 Tauri + Vue 3 + Rust 构建的机器人运动可视化桌面应用，支持 URDF、BVH、CSV、GLTF 等多种格式的模型和运动数据加载与 3D 可视化。

## 核心功能

### 运动查看器

加载和播放 BVH、CSV、JSON 格式的运动数据，提供帧播放控制、时间轴拖拽、播放速度调节等功能。

### 模型查看器

支持加载 URDF、GLTF/GLB、OBJ、DAE 等格式的机器人模型和场景文件，提供模型预览和诊断信息显示。

### 运动映射

将源运动数据映射到目标机器人模型，支持骨骼对应关系配置和实时预览。

### 动作编辑器

提供关键帧编辑、曲线调整、骨骼控制等功能，支持运动轨迹的可视化和编辑。

## 技术栈

### 前端

- **Vue 3**: 用户界面框架
- **TypeScript**: 类型安全的 JavaScript
- **Three.js**: 3D 图形库
- **Pinia**: 状态管理
- **Vite**: 构建工具

### 后端

- **Rust**: 系统级编程语言
- **Tauri 2**: 桌面应用框架
- **ndarray**: 多维数组处理

## 快速开始

### 安装依赖

```bash
pnpm install
```

### 开发模式

```bash
pnpm run tauri dev
```

### 构建应用

```bash
pnpm run tauri build
```

## 文档目录

- [架构设计](architecture/design.md) - 整体架构和技术选型
- [页面文档](pages/overview.md) - 各功能页面说明
- [组件文档](components/overview.md) - 可复用组件介绍
- [服务文档](services/overview.md) - 业务服务详解
- [状态管理](stores/overview.md) - Pinia 状态管理
- [安装指南](setup/installation.md) - 环境配置和运行说明
