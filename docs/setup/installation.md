---
layout: default
title: 安装指南
nav_order: 7
---

# 安装指南

## 环境要求

### 前端

- Node.js >= 18
- pnpm >= 10
- Vue 3
- TypeScript

### 后端

- Rust 1.70+
- Tauri CLI 2.x

## 安装步骤

### 1. 安装 pnpm

```bash
npm install -g pnpm
```

### 2. 安装依赖

```bash
pnpm install
```

### 3. 设置环境变量 (可选)

项目依赖第三方库，需要设置 LD_LIBRARY_PATH：

```bash
export LD_LIBRARY_PATH=/home/djw/Desktop/WebUI/third_party/linux_x86-64/lib:$LD_LIBRARY_PATH
```

可以在 shell 配置文件 (`~/.bashrc` 或 `~/.zshrc`) 中添加此配置。

## 运行命令

### 开发模式

```bash
# 前端 + Tauri 后端
pnpm run tauri dev

# 仅前端 (无 Tauri 支持)
pnpm run dev
```

### 构建

```bash
# 构建前端
pnpm run build

# 构建 Tauri 应用
pnpm run tauri build

# 发布版本
pnpm run tauri build -- --release
```

### 测试

```bash
pnpm run test
```

## 项目结构

```
motion_viewer_rust/
├── src/                    # 前端源代码 (Vue 3)
├── src-tauri/              # 后端源代码 (Rust)
├── public/                 # 静态资源
├── index.html              # HTML 入口
├── package.json            # Node.js 依赖
├── vite.config.ts          # Vite 配置
└── tsconfig.json           # TypeScript 配置
```

## 常见问题

### Tauri 后端不可用

错误信息: `Tauri backend unavailable`

解决方案: 使用 `pnpm run tauri dev` 而非 `pnpm run dev`

### 第三方库缺失

确保设置了正确的 LD_LIBRARY_PATH 环境变量。
