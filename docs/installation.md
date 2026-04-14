---
layout: default
title: 安装
nav_order: 2
description: 在 Ubuntu 22.04 / 24.04 上安装 Video2bvh
lang: zh
ref: installation
---

# 安装

Video2bvh 由两部分组成：

| 组件 | 说明 |
|------|------|
| `video2bvh-api` | Rust HTTP API 后端（二进制） |
| `motion_viewer_rust` | Tauri 前端（Vue 3 + Tauri 2.x） |

## 第一步：安装系统依赖

### Ubuntu 22.04 / 24.04

Tauri 2.x 需要以下系统依赖：

```bash
sudo apt update
sudo apt install -y \
  libwebkit2gtk-4.1-dev \
  libgtk-3-dev \
  libayatana-appindicator3-dev \
  librsvg2-dev \
  build-essential \
  cmake \
  pkg-config
```

| 依赖包 | 说明 |
|--------|------|
| `libwebkit2gtk-4.1-dev` | Tauri 窗口渲染引擎 |
| `libgtk-3-dev` | UI 组件库 |
| `libayatana-appindicator3-dev` | 系统托盘支持 |
| `librsvg2-dev` | SVG 图像支持 |
| `build-essential` | GCC 编译器工具链 |
| `cmake` | CMake 构建工具 |
| `pkg-config` | pkg-config 工具 |

## 第二步：安装 Rust

推荐使用 `rustup` 安装 Rust 工具链：

```bash
curl --proto '=https' --tlsv1.2 -sSf https://sh.rustup.rs | sh
source ~/.cargo/env
```

验证：

```bash
rustc --version
cargo --version
```

## 第三步：安装 Node.js 和 pnpm

前端构建需要 Node.js（推荐 20.x LTS）和 pnpm：

```bash
# 安装 nvm
curl -o- https://raw.githubusercontent.com/nvm-sh/nvm/v0.39.7/install.sh | bash
source ~/.bashrc

# 安装 Node.js 20.x
nvm install 20
nvm use 20

# 安装 pnpm
npm install -g pnpm

# 验证
node --version
pnpm --version
```

## 第四步：获取项目

从 GitHub 克隆项目：

```bash
git clone https://github.com/Shanghai-MotionLab/Video2bvh.git
cd Video2bvh
```

## 第五步：配置环境变量

```bash
cp .env.example .env
```

`.env` 默认配置：

```env
ASSET_ROOT=./assets
ROBOT_URDF=./assets/unitree_g1/urdf/g1_mocap_29dof.urdf
PORT=8080
RUST_LOG=info
```

## 第六步：启动后端 API

```bash
./run.sh
```

健康检查：

```bash
curl http://127.0.0.1:8080/health
# 预期响应：{"ok":true,"service":"video2bvh-api"}
```

## 第七步：启动前端 Viewer

在第二个终端中：

```bash
./run_viewer.sh
```

或手动启动：

```bash
cd motion_viewer_rust
pnpm install
pnpm run tauri:dev
```

前端默认连接 `http://127.0.0.1:8080`。如需修改：

```bash
cd motion_viewer_rust
VIDEO2BVH_API_URL="http://127.0.0.1:8080" pnpm run tauri:dev
```

## 一键联调

如需 API 和 Viewer 一同启动：

```bash
./run_all.sh
```

该脚本会：
1. 在后台启动 `video2bvh-api`
2. 等待 `/health` 就绪
3. 启动 Viewer
4. 退出时自动停止 API

## 常见问题

{: .warning }
> **Tauri 启动报错 "WebKitWebProcess not found"**
>
> 执行第一步，确保所有系统依赖安装完整。

{: .warning }
> **API 启动报错 "missing executable"**
>
> 确认在项目根目录运行，且 `video2bvh-api` 二进制有执行权限。

{: .warning }
> **Viewer 启动失败 "cannot connect to API"**
>
> 确认 API 已通过 `./run.sh` 启动，并在第二步健康检查确认服务正常。

## 下一步

- [Rust API](./reference/rust.html) - 查看 Rust SDK 接口文档
- [HTTP API](./reference/http-api.html) - 查看 HTTP API 接口文档
