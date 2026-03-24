---
layout: default
title: viewerAssetLoader
parent: 服务概览
nav_order: 1
---

# viewerAssetLoader 服务

## 概述

viewerAssetLoader 是 3D 资源加载服务，负责加载机器人模型和场景文件。

## 源文件

`src/services/viewerAssetLoader.ts`

## 核心函数

### loadViewerRobot

加载机器人模型：

```typescript
async function loadViewerRobot(
  runtime: ViewerRuntime,
  resource: SelectedResource,
  droppedBundles?: DroppedBundle[]
): Promise<UrdfRobotLike>
```

### createInitialViewerDiagnostics

创建初始诊断信息：

```typescript
function createInitialViewerDiagnostics(): ViewerDiagnostics
```

### resetViewerDiagnostics

重置诊断信息：

```typescript
function resetViewerDiagnostics(diagnostics: ViewerDiagnostics): void
```

## 类型定义

### ViewerDiagnostics

```typescript
interface ViewerDiagnostics {
  toggles: SceneDiagnosticsToggles;
  performance: ScenePerformanceStats;
  camera: SceneCameraState;
}
```
