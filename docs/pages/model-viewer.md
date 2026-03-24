---
layout: default
title: 模型查看器
parent: 页面文档
nav_order: 2
---

# 模型查看器 (Model Viewer)

## 概述

模型查看器页面用于加载和预览 URDF、GLTF/GLB、OBJ、DAE 等格式的机器人模型和场景文件。

## 路由

```
/model
```

## 源文件

`src/pages/ModelViewerPage.vue`

## 支持的文件格式

| 格式 | 扩展名 | 说明 |
|------|--------|------|
| URDF | `.urdf` | 机器人描述格式，ROS 标准 |
| GLTF | `.gltf` | GL 传输格式 |
| GLB | `.glb` | GL 二进制格式 |
| OBJ | `.obj` | Wavefront 对象格式 |
| DAE | `.dae` | Collada 格式 |

## 核心功能

### 1. 文件加载

- **资源浏览器**: 左侧文件树，支持 `robot` 和 `scene` 分类
- **文件类型过滤**: 仅显示允许的文件类型
- **拖放支持**: 支持拖放 robot 类型文件

### 2. 3D 查看器

- **网格显示**: 可选的地面网格
- **地面显示**: 可选的地面平面
- **背景颜色**: 深色主题 (#07121a)
- **诊断信息**: 开发模式显示诊断覆盖层

### 3. 面板调整

- **可调整侧边栏宽度**: 拖拽分隔线调整左侧面板宽度
- **响应式布局**: 自适应窗口大小

## 状态管理

使用 Pinia store (`stores/modelViewer.ts`) 管理状态：

- `selectedResource`: 当前选中的模型资源
- `sidebarWidth`: 侧边栏宽度
- `droppedBundles`: 拖放的文件束
- `error`: 错误信息

## 组件集成

### ThreeViewer

核心 3D 渲染组件，属性配置：

```vue
<ThreeViewer
  :show-grid="true"
  :show-ground="true"
  background-color="#07121a"
  :show-diagnostics-overlay="showViewerDiagnostics"
  viewer-label="Model"
  :resource-to-load="store.selectedResource"
  :dropped-bundles="store.droppedBundles"
/>
```

## 事件处理

- `@load`: 资源加载成功
- `@error`: 加载出错
- `@dropped-bundles-change`: 拖放文件变化
