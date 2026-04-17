---
layout: default
title: HTTP API
nav_order: 5
has_toc: true
description: ee-api HTTP 接口参考文档
lang: zh
ref: http-api
---

# HTTP API

`ee-api` 提供面向 WebUI 和服务集成的本地 HTTP 接口。

当前稳定公共接口很窄，主要面向固定内置配置的 `unitree_g1` BVH 重定向。其余端点为兼容保留的内部接口。

## 接口总览

| 端点 | 说明 | 状态 |
|------|------|------|
| `GET /health` | 健康检查 | 稳定公共接口 |
| `GET /supported/sources` | 查询当前支持的 source 列表 | 稳定公共接口 |
| `GET /supported/robots` | 查询当前支持的机器人列表 | 稳定公共接口 |
| `GET /robot-info` | 查询机器人模型路径和根四元数顺序 | 稳定公共接口 |
| `POST /retarget/bvh` | BVH 重定向到机器人姿态 | 稳定公共接口 |
| `GET /supported` | 查询默认 source / target | 兼容保留 |
| `GET /robot-model` | 查询机器人模型拓扑信息 | 兼容保留 |
| `POST /skeleton/bvh` | 从 BVH 提取骨架数据 | 兼容保留 |
| `POST /convert/bvh` | 归一化并导出 BVH | 兼容保留 |
| `POST /export/standard-bvh` | 导出标准骨架 BVH | 兼容保留 |
| `POST /batch/retarget/bvh` | 批量 BVH 重定向 | 兼容保留 |
| `POST /batch/convert/bvh` | 批量 BVH 归一化 | 兼容保留 |
| `POST /batch/export/standard-bvh` | 批量导出标准骨架 BVH | 兼容保留 |
| `POST /post-process/csv` | 对重映射 CSV 做统一导出处理 | 兼容保留 |

## 错误响应

当前业务错误统一使用 `400 Bad Request`，并至少返回：

```json
{
  "error": "..."
}
```

对于 BVH 相关接口，服务会在实际加载 BVH 后再判断格式是否受支持，并在兼容原有字段的同时返回结构化错误：

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

当前 BVH 错误码只收敛为 3 类：

- `BVH_SYNTAX_ERROR`
- `BVH_FORMAT_ERROR`
- `BVH_PARSE_FAILED`
