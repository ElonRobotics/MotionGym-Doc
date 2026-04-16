---
layout: default
title: 首页
nav_order: 1
description: 高性能机器人运动重定向引擎文档
lang: zh
ref: home
---

# MotionGym

<div class="hero-block">
  <p class="hero-kicker">Motion Retargeting Engine</p>
  <h2>面向机器人动作映射的文档中心</h2>
  <p class="hero-lead">文档重点覆盖 <code>ee-sdk</code>、本地 HTTP API、BVH 标准化、Unitree G1 重定向、姿态表示和 IK 求解。当前稳定公开接口包括 Rust SDK 和本地 HTTP API。</p>
  <div class="hero-actions">
    <a class="btn btn-primary" href="./docs/installation.html">开始安装</a>
    <a class="btn btn-secondary" href="./docs/reference/rust.html">查看 Rust API</a>
  </div>
</div>

## 文档结构

<div class="card-grid">
  <div class="feature-card">
    <h3>安装</h3>
    <p>从零开始，依次完成环境配置、项目创建、依赖安装、运行第一个示例。</p>
    <p><a href="./docs/installation.html">进入安装</a></p>
  </div>
  <div class="feature-card">
    <h3>核心概念</h3>
    <p>理解 HumanPose、机器人模型、IK 求解器以及 BVH / URDF / MJCF 加载链路。</p>
    <p><a href="./concepts.html">进入概念页</a></p>
  </div>
  <div class="feature-card">
    <h3>Rust API</h3>
    <p>基本概念、数据导入、重映射配置、机器人查询。面向接入方的完整接口文档。</p>
    <p><a href="./docs/reference/rust.html">进入 Rust API</a></p>
  </div>
  <div class="feature-card">
    <h3>HTTP API</h3>
    <p>面向 WebUI / 服务集成的 HTTP 接口。骨架提取、机器人模型查询、BVH 重定向端点。</p>
    <p><a href="./docs/reference/http-api.html">进入 HTTP API</a></p>
  </div>
</div>
  <div class="feature-card">
    <h3>核心概念</h3>
    <p>理解 HumanPose、机器人模型、IK 求解器以及 BVH / URDF 加载链路。</p>
    <p><a href="./concepts.html">进入概念页</a></p>
  </div>
  <div class="feature-card">
    <h3>Rust API</h3>
    <p>面向接入方的公开接口文档，重点覆盖 Unitree G1 与 BVH 标准化。</p>
    <p><a href="./docs/reference/rust.html">进入 API 页</a></p>
  </div>
  <div class="feature-card">
    <h3>HTTP API</h3>
    <p>面向 WebUI / 服务集成的接口说明，覆盖骨架提取、机器人模型和 BVH 重定向端点。</p>
    <p><a href="./docs/reference/http-api.html">进入 HTTP API</a></p>
  </div>
</div>

{: .note }
> 如果你的目标是尽快接入 MotionGym，请优先阅读 [安装](./docs/installation.html)、[Rust API](./docs/reference/rust.html) 和 [HTTP API](./docs/reference/http-api.html)。

## 快速示例

```rust
use ee_sdk::prelude::*;

let output = retarget_from_bvh(
    "data/jobs/source/bvh/good_time.bvh",
    BvhRetargetOptions::new(Robot::UnitreeG1)
        .with_offset_to_ground(true),
)?;

std::fs::write("output.csv", output.to_csv())?;
```

## 核心模块

| 模块 | 作用 |
| :--- | :--- |
| `ee-common` | 提供通用类型、数学工具和 BVH / 模型加载基础设施。 |
| `ee-core` | 提供机器人模型、IK 求解器与底层重定向引擎。 |
| `ee-sdk` | 提供面向集成方的高层公开接口。 |
| `ee-api` | 提供面向服务集成的 HTTP 接口。 |
| `ee-cli` | 提供批处理与命令行入口。 |
