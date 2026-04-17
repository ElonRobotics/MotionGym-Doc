---
layout: default
title: 重定向接口
parent: HTTP API
nav_order: 3
lang: zh
ref: http-api-retarget
---

# 重映射

## POST /retarget/bvh

将 BVH 文件重定向到机器人姿态。

### 请求

```json
{
  "bvhPath": "/path/to/motion.bvh",
  "fps": 60,
  "offsetToGround": false,
  "axisOrder": "XYZ",
  "startFrame": 0,
  "endFrame": 100
}
```

| 字段 | 类型 | 说明 |
| :--- | :--- | :--- |
| `bvhPath` | `string` | BVH 文件路径 |
| `fps` | `number` | 输出帧率 |
| `offsetToGround` | `boolean` | 是否将根位移平移到贴地状态 |
| `axisOrder` | `string` | 旋转轴顺序覆盖 |
| `startFrame` | `number` | 起始帧，0-based，含起点 |
| `endFrame` | `number` | 结束帧，0-based，含终点 |

### 响应

```json
{
  "fps": 60,
  "frameCount": 100,
  "rootPositionStride": 3,
  "rootRotationStride": 4,
  "dofCount": 29,
  "rotationOrder": "xyzw",
  "rootPositions": [x,y,z, x,y,z, ...],
  "rootRotations": [x,y,z,w, x,y,z,w, ...],
  "dofPositions": [pos1, pos2, ..., posN, ...]
}
```

| 字段 | 类型 | 说明 |
| :--- | :--- | :--- |
| `fps` | `u32` | 输出帧率 |
| `frameCount` | `usize` | 输出帧数 |
| `rootPositionStride` | `usize` | 固定为 `3` |
| `rootRotationStride` | `usize` | 固定为 `4` |
| `dofCount` | `usize` | 自由度数量 |
| `rotationOrder` | `string` | 根四元数顺序，当前为 `xyzw` |
| `rootPositions` | `number[]` | `frameCount × 3` |
| `rootRotations` | `number[]` | `frameCount × 4`，顺序为 `xyzw` |
| `dofPositions` | `number[]` | `frameCount × dofCount` |

### 错误响应

`POST /retarget/bvh` 的错误返回仍然保留 `error` 字段，同时会为 BVH 相关错误返回结构化信息。

示例：

```json
{
  "error": "BVH syntax error: unsupported joint naming such as 'mixamorig:' was detected",
  "code": "BVH_SYNTAX_ERROR",
  "category": "bvh_syntax_error",
  "details": {
    "path": "/path/to/motion.bvh",
    "detectedToken": "mixamorig:"
  },
  "retryable": false
}
```

说明：

- BVH 文件会先按实际内容加载，再判断是否属于受支持格式
- `code` 用于程序分支处理
- `code` 当前只使用 `BVH_SYNTAX_ERROR`、`BVH_FORMAT_ERROR`、`BVH_PARSE_FAILED`
- `category` 当前只使用 `bvh_syntax_error`、`bvh_format_error`、`bvh_parse_error`
- `details.path` 指向触发错误的输入文件
