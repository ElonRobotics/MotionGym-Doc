---
layout: default
title: 批量处理
parent: HTTP API
nav_order: 5
lang: zh
ref: http-api-batch
---

# 批量处理

批量处理接口对整个文件夹中的 BVH 文件进行批量转换或重定向，单个文件失败不影响整体执行。

---

## POST /batch/retarget/bvh

将文件夹中的所有 BVH 文件批量重定向为机器人关节值 CSV 文件。

### 请求

```json
{
  "inputFolder": "/path/to/bvh/folder",
  "outputFolder": "/path/to/output/folder",
  "fps": 30,
  "offsetToGround": false,
  "axisOrder": "ZXY"
}
```

| 字段 | 类型 | 必填 | 说明 |
|------|------|------|------|
| `inputFolder` | `string` | 是 | BVH 文件所在目录 |
| `outputFolder` | `string` | 是 | 输出 CSV 目录 |
| `fps` | `number` | 否 | 输出帧率，默认 `30` |
| `offsetToGround` | `boolean` | 否 | 是否将根位移平移到贴地状态，默认 `false` |
| `axisOrder` | `string` | 否 | 旋转轴顺序覆盖，如 `"ZXY"` |

### 响应

```json
{
  "total": 2,
  "succeeded": 2,
  "failed": 0,
  "results": [
    {
      "fileName": "GVHMR_01.bvh",
      "status": "ok",
      "fps": 30,
      "frameCount": 841,
      "outputPath": "/path/to/output/GVHMR_01.csv"
    },
    {
      "fileName": "GVHMR_02.bvh",
      "status": "ok",
      "fps": 30,
      "frameCount": 413,
      "outputPath": "/path/to/output/GVHMR_02.csv"
    }
  ]
}
```

| 字段 | 类型 | 说明 |
|------|------|------|
| `total` | `number` | 处理文件总数 |
| `succeeded` | `number` | 成功数量 |
| `failed` | `number` | 失败数量 |
| `results[].fileName` | `string` | 原始文件名 |
| `results[].status` | `string` | `"ok"` 或 `"error"` |
| `results[].fps` | `number` | 输出帧率（仅成功时） |
| `results[].frameCount` | `number` | 输出帧数（仅成功时） |
| `results[].outputPath` | `string` | 输出文件路径（仅成功时） |
| `results[].error` | `string` | 错误原因（仅失败时） |

---

## POST /batch/convert/bvh

将文件夹中的所有 BVH 文件批量归一化并导出。

### 请求

```json
{
  "inputFolder": "/path/to/bvh/folder",
  "outputFolder": "/path/to/output/folder",
  "axisOrder": "ZXY",
  "fps": 30
}
```

| 字段 | 类型 | 必填 | 说明 |
|------|------|------|------|
| `inputFolder` | `string` | 是 | BVH 文件所在目录 |
| `outputFolder` | `string` | 是 | 归一化后 BVH 输出目录 |
| `axisOrder` | `string` | 否 | 旋转轴顺序覆盖，默认 `"ZXY"` |
| `fps` | `number` | 否 | 目标帧率，不设置则保持原帧率 |

### 响应

```json
{
  "total": 2,
  "succeeded": 2,
  "failed": 0,
  "results": [
    {
      "fileName": "motion_a.bvh",
      "status": "ok",
      "fps": 30,
      "frameCount": 600,
      "outputPath": "/path/to/output/motion_a.bvh"
    }
  ]
}
```

### 输出文件命名

输出文件名与输入文件相同（`{stem}.bvh`），保存在 `outputFolder` 中。

---

## POST /batch/export/standard-bvh

将文件夹中的所有 BVH 文件批量转换为标准骨架 BVH。

### 请求

```json
{
  "inputFolder": "/path/to/bvh/folder",
  "outputFolder": "/path/to/output/folder"
}
```

| 字段 | 类型 | 必填 | 说明 |
|------|------|------|------|
| `inputFolder` | `string` | 是 | BVH 文件所在目录 |
| `outputFolder` | `string` | 是 | 标准骨架 BVH 输出目录 |

### 响应

```json
{
  "total": 2,
  "succeeded": 2,
  "failed": 0,
  "results": [
    {
      "fileName": "motion_a.bvh",
      "status": "ok",
      "fps": 30,
      "frameCount": 600,
      "outputPath": "/path/to/output/motion_a.bvh"
    }
  ]
}
```

### 与 /batch/convert/bvh 的区别

| | `/batch/convert/bvh` | `/batch/export/standard-bvh` |
|---|---|---|
| 输出格式 | BVH（同输入） | 标准骨架 BVH |
| 骨架 | 保持原始骨架结构 | 归一化为 24 关节标准骨架 |
| 轴顺序 | 可指定 | 固定为 Z-X-Y |
| 用途 | 格式归一化 | 统一骨架格式，便于后续重定向 |
