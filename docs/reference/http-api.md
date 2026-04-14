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

`ee-api` 提供面向 WebUI 和服务集成的 HTTP 接口。

## 接口总览

| 端点 | 说明 |
|------|------|
| `POST /skeleton/bvh` | 从 BVH 提取骨架数据 |
| `POST /convert/bvh` | 归一化并导出 BVH |
| `POST /export/standard-bvh` | 导出标准骨架 BVH |
| `POST /retarget/bvh` | BVH 重定向到机器人姿态 |
| `POST /robot-model` | 查询机器人模型信息 |
| `POST /robot-info` | 查询机器人基本信息 |
| `POST /batch/retarget/bvh` | 批量 BVH 重定向 |
| `POST /batch/convert/bvh` | 批量 BVH 归一化 |
| `POST /batch/export/standard-bvh` | 批量导出标准骨架 BVH |
