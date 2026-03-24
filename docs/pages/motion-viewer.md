---
layout: default
title: 运动查看器
parent: 页面文档
nav_order: 1
---

# 运动查看器 (Motion Viewer)

## 概述

运动查看器页面用于加载和播放 BVH、CSV、JSON 等格式的运动数据，是应用的核心功能之一。

## 路由

```
/motion
```

## 源文件

`src/pages/MotionViewerPage.vue`

## 支持的文件格式

| 格式 | 扩展名 | 说明 |
|------|--------|------|
| BVH | `.bvh` | BioVision Hierarchy 格式，动捕数据标准格式 |
| CSV | `.csv` | 逗号分隔值格式的自定义运动数据 |
| JSON | `.json` | JSON 格式的运动数据 |
| NPZ | `.npz` | NumPy 压缩格式 |
| PKL | `.pkl` | Python pickle 格式 |

## 核心功能

### 1. 文件加载

- **资源浏览器**: 左侧文件树，支持浏览项目目录
- **拖放支持**: 支持将 BVH 文件直接拖入视口加载
- **Tauri 后端**: 通过 Rust 后端加载文件，提高大文件处理性能

### 2. 播放控制

- **播放/暂停**: 切换运动播放状态
- **帧跳转**: 通过时间轴或输入框跳转到指定帧
- **播放速度**: 支持 0.25x、0.5x、1x、2x 等速度调节
- **循环模式**: 默认循环播放

### 3. 状态显示

- **当前帧/总帧数**: 实时显示播放位置
- **FPS**: 运动数据帧率
- **关节数**: 骨骼数量
- **数据大小**: 文件大小

## 状态管理

使用 Pinia store (`stores/motionViewer.ts`) 管理状态：

- `selectedResource`: 当前选中的运动资源
- `isLoading`: 加载状态
- `error`: 错误信息
- `isPlaying`: 播放状态
- `currentFrame`: 当前帧
- `playbackSpeed`: 播放速度
- `totalFrames`: 总帧数
- `droppedBundles`: 拖放的文件束

## 关键函数

### loadMotionResource

加载运动资源的主要函数，支持多种格式：

```typescript
async function loadMotionResource(resource: SelectedResource): Promise<void>
```

处理流程：
1. 设置加载状态
2. 根据文件扩展名选择加载方式
3. 调用 Tauri 后端或前端服务加载数据
4. 更新状态管理中的运动统计信息

### useFramePlayback

帧播放控制 Hook：

```typescript
useFramePlayback({
  isPlaying,
  getFrame,
  setFrame,
  totalFrames,
  fps,
  speed,
  mode: 'loop',
})
```

## 依赖服务

- `BvhMotionService`: BVH 文件解析
- `safeInvoke`: Tauri 后端调用
- `processDropEvent`: 拖放事件处理
