---
layout: default
title: ThreeViewer
parent: 组件概览
nav_order: 1
---

# ThreeViewer 组件

## 概述

ThreeViewer 是应用的核心 3D 渲染组件，基于 Three.js 封装，提供机器人模型和运动数据的 3D 可视化功能。

## 源文件

`src/components/ThreeViewer/ThreeViewer.vue`

## 属性 (Props)

| 属性 | 类型 | 默认值 | 说明 |
|------|------|--------|------|
| className | `string` | `''` | 额外的 CSS 类名 |
| showGrid | `boolean` | `true` | 是否显示网格 |
| showGround | `boolean` | `true` | 是否显示地面 |
| backgroundColor | `string` | `'#07121a'` | 背景颜色 |
| resourceToLoad | `SelectedResource \| null` | `null` | 要加载的资源 |
| droppedBundles | `DroppedBundle[]` | `[]` | 拖放的文件束 |
| enableDrop | `boolean` | `true` | 是否启用拖放 |
| followRobot | `boolean` | `false` | 是否跟随机器人 |
| showCom | `boolean` | `false` | 是否显示质心 |
| comPosition | `{x, y, z} \| null` | `null` | 质心位置 |
| showControls | `boolean` | `true` | 是否显示控制器 |
| interactive | `boolean` | `true` | 是否启用交互 |
| showDiagnosticsOverlay | `boolean` | `false` | 显示诊断覆盖层 |
| viewerLabel | `string` | `''` | 查看器标签 |

## 事件 (Emits)

| 事件 | 参数 | 说明 |
|------|------|------|
| load | - | 资源加载成功 |
| error | `message: string` | 加载出错 |
| robotLoaded | `robot: UrdfRobotLike` | 机器人加载完成 |
| droppedBundlesChange | `bundles: DroppedBundle[]` | 拖放文件变化 |
| followChange | `enabled: boolean` | 跟随状态变化 |
| comChange | `enabled: boolean` | 质心显示状态变化 |
| cameraChange | `state: SceneCameraState` | 相机状态变化 |
| interactionStart | - | 交互开始 |
| interactionEnd | - | 交互结束 |

## 核心功能

### 1. 模型加载

支持 URDF、GLTF、GLB、OBJ、DAE 等格式的机器人模型加载。

### 2. 场景控制

- **旋转模式**: 旋转相机视角
- **平移模式**: 平移相机视角
- **跟随模式**: 跟随机器人移动
- **质心显示**: 显示机器人质心位置

### 3. 拖放支持

支持拖放文件到视口，自动识别并加载模型。

### 4. 诊断信息

开发模式下显示性能统计和诊断信息。

## 依赖模块

- `ViewerRuntime`: 运行时管理
- `SceneController`: 场景控制器
- `viewerAssetLoader`: 资源加载服务
- `processDropEvent`: 拖放事件处理
