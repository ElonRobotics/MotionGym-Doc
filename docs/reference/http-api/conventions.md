---
layout: default
title: 基本概念
parent: HTTP API
nav_order: 1
lang: zh
ref: http-api-conventions
---

# 基本概念

## axisOrder

支持：`XYZ`、`XZY`、`YXZ`、`YZX`、`ZXY`、`ZYX`。

传入后：

- 在 hierarchy 解析完成后生效
- 在 motion 转换前覆盖 rotation channel 顺序
- position channel 保持不变

## 四元数顺序

- `/skeleton/bvh` 的 `rotations` 使用 `xyzw`
- `/retarget/bvh` 的 `rootRotations` 使用 `xyzw`

## 展平数组格式

| 字段 | 格式 |
| :--- | :--- |
| `positions` | `frameCount × joints × 3` |
| `rotations` | `frameCount × joints × 4` |
| `rootPositions` | `frameCount × 3` |
| `rootRotations` | `frameCount × 4` |
| `dofPositions` | `frameCount × dofCount` |

## 通用错误响应

```json
{
  "error": "error message string"
}
```
