---
layout: default
title: 导出接口
parent: HTTP API
nav_order: 6
lang: zh
ref: http-api-export
---

# 导出

## POST /post-process/csv

对重映射生成的 CSV 文件做统一导出处理，支持：

- 保持 CSV 输出
- 导出为 JSON
- 导出为 NPZ
- 导出为 PKL
- 可选重采样
- 可选附加速度信号

该接口的输入是 **RetargetOutput 导出的 CSV 文件**，不是原始 BVH 文件。

### 请求

```json
{
  "inputPath": "/path/to/output.csv",
  "outputPath": "/path/to/output.json",
  "format": "json",
  "fps": 30,
  "resampleFps": 60,
  "includeVelocities": true
}
```

| 字段 | 类型 | 必填 | 说明 |
|------|------|------|------|
| `inputPath` | `string` | 是 | 输入 CSV 文件路径 |
| `outputPath` | `string` | 是 | 输出文件路径 |
| `format` | `string` | 否 | 输出格式：`csv` / `json` / `npz` / `pkl`，默认 `csv` |
| `fps` | `number` | 是 | 输入 CSV 的帧率。CSV 本身不存储帧率，因此该字段必填 |
| `resampleFps` | `number` | 否 | 若设置，则先重采样到目标帧率再导出 |
| `includeVelocities` | `boolean` | 否 | 是否附加根线速度、根角速度、关节速度 |

### 响应

```json
{
  "fps": 60,
  "frameCount": 200,
  "format": "json",
  "outputPath": "/path/to/output.json"
}
```

| 字段 | 类型 | 说明 |
|------|------|------|
| `fps` | `u32` | 导出结果帧率 |
| `frameCount` | `usize` | 导出结果帧数 |
| `format` | `string` | 实际输出格式 |
| `outputPath` | `string` | 实际输出文件路径 |

### format 可选值

| 值 | 说明 |
|----|------|
| `csv` | 保持 CSV 输出 |
| `json` | 导出统一结构 JSON |
| `npz` | 导出 NumPy 压缩文件 |
| `pkl` | 导出 Python Pickle 文件 |

### JSON 输出结构

当 `format = "json"` 时，输出文件结构与 Rust SDK 的统一导出层一致：

```json
{
  "meta": {
    "fps": 30,
    "frameCount": 100,
    "rotationFormat": "xyzw",
    "positionUnit": "meter",
    "schemaVersion": 1
  },
  "signals": {
    "rootPos": [[0, 0, 0.8]],
    "rootRotXyzw": [[0, 0, 0, 1]],
    "dofPos": [[0.1, 0.2]]
  }
}
```

如果 `includeVelocities = true`，还会额外包含：

- `rootLinVel`
- `rootAngVel`
- `dofVel`

{: .note }
> `npz` 和 `pkl` 导出依赖 Python 导出桥接，因此运行环境需要可用的 `python3`。
