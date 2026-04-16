---
layout: default
title: 输入骨架接口
parent: HTTP API
nav_order: 2
lang: zh
ref: http-api-input
---

# 数据导入

## POST /skeleton/bvh

从 BVH 文件提取骨架数据。

该接口返回的是 BVH 骨架快照数据，用于查看源动作的骨架结构与逐帧位姿，不包含机器人自由度信息，因此响应中没有 `dofJoints` 或类似字段。

### 请求

```json
{
  "bvhPath": "/path/to/motion.bvh",
  "axisOrder": "XYZ"
}
```

| 字段 | 类型 | 说明 |
| :--- | :--- | :--- |
| `bvhPath` | `string` | BVH 文件路径 |
| `axisOrder` | `string` | 旋转轴顺序覆盖 |

### 响应

```json
{
  "fps": 60,
  "frameCount": 100,
  "joints": ["Hips", "Spine", "LeftUpLeg", ...],
  "positions": [x,y,z, x,y,z, ...],
  "rotations": [x,y,z,w, x,y,z,w, ...]
}
```

| 字段 | 类型 | 说明 |
| :--- | :--- | :--- |
| `fps` | `u32` | 帧率 |
| `frameCount` | `usize` | 帧数 |
| `joints` | `string[]` | BVH 骨架关节名称列表 |
| `positions` | `number[]` | `frameCount × joints × 3`，位置数据 |
| `rotations` | `number[]` | `frameCount × joints × 4`，顺序为 `xyzw` |

---

## POST /convert/bvh

将 BVH 文件归一化并导出。

### 请求

```json
{
  "inputPath": "/path/to/source.bvh",
  "outputPath": "/path/to/output.bvh",
  "axisOrder": "ZXY",
  "fps": 60
}
```

| 字段 | 类型 | 说明 |
| :--- | :--- | :--- |
| `inputPath` | `string` | 输入 BVH 路径 |
| `outputPath` | `string` | 输出 BVH 路径 |
| `axisOrder` | `string` | 旋转轴顺序覆盖 |
| `fps` | `number` | 目标帧率 |

### 响应

```json
{
  "fps": 60,
  "frameCount": 100
}
```

---

## POST /export/standard-bvh

导出标准骨架 BVH。

### 请求

```json
{
  "inputPath": "/path/to/retarget.bvh",
  "outputPath": "/path/to/standard.bvh"
}
```

| 字段 | 类型 | 说明 |
| :--- | :--- | :--- |
| `inputPath` | `string` | 输入路径 |
| `outputPath` | `string` | 输出路径 |

### 响应

```json
{
  "fps": 60,
  "frameCount": 100
}
```
