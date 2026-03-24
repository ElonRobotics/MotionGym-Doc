---
layout: default
title: 状态管理
nav_order: 6
has_children: true
---

# 状态管理文档

本节详细介绍应用的 Pinia 状态管理模块。

## Store 列表

| Store | 路径 | 用途 |
|-------|------|------|
| motionViewer | `stores/motionViewer.ts` | 运动查看器状态 |
| modelViewer | `stores/modelViewer.ts` | 模型查看器状态 |
| motionMapping | `stores/motionMapping.ts` | 运动映射状态 |
| actionEditor | `stores/actionEditor.ts` | 动作编辑器状态 |

## 状态架构

```
stores/
├── motionViewer.ts      # 运动查看器
│   ├── selectedResource
│   ├── isLoading
│   ├── error
│   ├── isPlaying
│   ├── currentFrame
│   ├── playbackSpeed
│   ├── totalFrames
│   └── droppedBundles
├── modelViewer.ts       # 模型查看器
│   ├── selectedResource
│   ├── sidebarWidth
│   ├── droppedBundles
│   └── error
├── motionMapping.ts     # 运动映射
│   ├── sourceType
│   ├── sourceHuman
│   ├── targetRobot
│   └── retargetPlayback
└── actionEditor.ts      # 动作编辑器
    ├── selectedJoint
    ├── keyframes
    └── curves
```
