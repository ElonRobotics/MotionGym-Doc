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

### 错误响应

`POST /skeleton/bvh` 与 `/retarget/bvh` 使用同一套 BVH 校验逻辑：先尝试加载 BVH，再判断是否属于当前支持的格式。

示例：

```json
{
  "error": "BVH syntax error: unsupported joint naming such as 'mixamorig:' was detected",
  "code": "BVH_SYNTAX_ERROR",
  "category": "bvh_syntax_error",
  "details": {
    "path": "/path/to/input.bvh",
    "detectedToken": "mixamorig:"
  },
  "retryable": false
}
```

当前只使用 3 类 BVH 错误码：

- `BVH_SYNTAX_ERROR`
- `BVH_FORMAT_ERROR`
- `BVH_PARSE_FAILED`

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

### 错误响应

`POST /convert/bvh` 对输入 BVH 也会先执行同一套结构校验。

返回格式与 `/retarget/bvh` 一致，仍保留原有 `error` 字段，并新增：

- `code`
- `category`
- `details`
- `retryable`

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

### 错误响应

`POST /export/standard-bvh` 同样会在导出前先加载并校验输入 BVH。

对于不受支持的 BVH 格式，会返回与 `/retarget/bvh` 相同结构的错误对象。
