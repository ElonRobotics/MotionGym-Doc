---
layout: default
title: 服务概览
nav_order: 5
has_children: true
---

# 服务文档

本节详细介绍应用的业务服务模块。

## 前端服务

| 服务 | 路径 | 功能描述 |
|------|------|----------|
| viewerAssetLoader | `services/viewerAssetLoader.ts` | 3D 资源加载服务 |
| BvhMotionService | `utils/motion/BvhMotionService.ts` | BVH 运动数据解析 |
| groundAlignment | `utils/motion/groundAlignment.ts` | 地面对齐计算 |
| ActionTrajectoryService | `utils/motion/ActionTrajectoryService.ts` | 动作轨迹服务 |
| urdfLoader | `utils/urdfLoader.ts` | URDF 文件加载 |
| fileUtils | `utils/fileUtils.ts` | 文件操作工具 |
| meshCache | `utils/meshCache.ts` | 网格缓存 |

## 后端服务 (Rust)

| 服务 | 路径 | 功能描述 |
|------|------|----------|
| motion_service | `src-tauri/src/services/motion_service.rs` | 运动数据处理 |
| retarget_native | `src-tauri/src/services/retarget_native.rs` | 运动重定向 |
