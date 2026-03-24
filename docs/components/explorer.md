---
layout: default
title: Explorer
parent: 组件概览
nav_order: 2
---

# Explorer 组件

## 概述

Explorer 是文件资源浏览器组件，提供项目文件的树形展示、搜索和选择功能。

## 源文件

`src/components/Explorer/Explorer.vue`

## 子组件

- **FileTree**: 文件树组件 (`Explorer/FileTree.vue`)

## 属性 (Props)

| 属性 | 类型 | 默认值 | 说明 |
|------|------|--------|------|
| selectedPath | `string` | - | 当前选中的文件路径 |
| showSearch | `boolean` | `false` | 是否显示搜索框 |
| showCategories | `boolean` | `false` | 是否显示分类 |
| visibleCategoryIds | `string[]` | - | 可见的分类 ID |
| allowedFileTypes | `string[]` | - | 允许的文件类型 |
| initialExpandedCategories | `string[]` | - | 初始展开的分类 |
| allowedDropKinds | `string[]` | - | 允许的拖放类型 |
| droppedBundles | `DroppedBundle[]` | `[]` | 拖放的文件束 |

## 事件 (Emits)

| 事件 | 参数 | 说明 |
|------|------|------|
| selectResource | `resource: SelectedResource` | 选中资源 |
| doubleClickResource | `resource: SelectedResource` | 双击资源 |
| droppedBundlesChange | `bundles: DroppedBundle[]` | 拖放文件变化 |

## 核心功能

### 1. 文件浏览

树形结构展示项目文件，支持分类显示。

### 2. 资源选择

支持单击和双击选择资源，触发不同事件。

### 3. 拖放支持

接收外部拖放的文件束并更新显示。

### 4. 搜索功能

可选的搜索框，支持过滤文件。

## 工具模块

`src/components/Explorer/explorerTree.ts` - 文件树构建工具
