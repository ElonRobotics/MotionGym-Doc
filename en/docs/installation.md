---
layout: default
title: Installation
nav_order: 2
description: Install Video2bvh on Ubuntu 22.04 / 24.04
lang: en
ref: installation
permalink: /en/docs/installation.html
---

# Installation

Video2bvh consists of two components:

| Component | Description |
|-----------|-------------|
| `video2bvh-api` | Rust HTTP API backend (pre-built binary) |
| `motion_viewer_rust` | Tauri frontend (Vue 3 + Tauri 2.x) |

## Step 1: Install System Dependencies

### Ubuntu 22.04 / 24.04

Tauri 2.x requires the following system packages:

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

| Package | Description |
|---------|-------------|
| `libwebkit2gtk-4.1-dev` | Tauri window rendering engine |
| `libgtk-3-dev` | UI component library |
| `libayatana-appindicator3-dev` | System tray support |
| `librsvg2-dev` | SVG image support |
| `build-essential` | GCC compiler toolchain |
| `cmake` | CMake build tool |
| `pkg-config` | pkg-config utility |

## Step 2: Install Rust

Recommended: install Rust toolchain via `rustup`:

```bash
curl --proto '=https' --tlsv1.2 -sSf https://sh.rustup.rs | sh
source ~/.cargo/env
```

Verify:

```bash
rustc --version
cargo --version
```

## Step 3: Install Node.js and pnpm

Frontend build requires Node.js (recommended 20.x LTS) and pnpm:

```bash
# Install nvm
curl -o- https://raw.githubusercontent.com/nvm-sh/nvm/v0.39.7/install.sh | bash
source ~/.bashrc

# Install Node.js 20.x
nvm install 20
nvm use 20

# Install pnpm
npm install -g pnpm

# Verify
node --version
pnpm --version
```

## Step 4: Clone the Project

```bash
git clone https://github.com/Shanghai-MotionLab/Video2bvh.git
cd Video2bvh
```

## Step 5: Configure Environment Variables

```bash
cp .env.example .env
```

Default `.env` configuration:

```env
ASSET_ROOT=./assets
ROBOT_URDF=./assets/unitree_g1/urdf/g1_mocap_29dof.urdf
PORT=8080
RUST_LOG=info
```

## Step 6: Start the Backend API

```bash
./run.sh
```

Health check:

```bash
curl http://127.0.0.1:8080/health
# Expected: {"ok":true,"service":"video2bvh-api"}
```

## Step 7: Start the Frontend Viewer

In a second terminal:

```bash
./run_viewer.sh
```

Or manually:

```bash
cd motion_viewer_rust
pnpm install
pnpm run tauri:dev
```

The frontend connects to `http://127.0.0.1:8080` by default. To override:

```bash
cd motion_viewer_rust
VIDEO2BVH_API_URL="http://127.0.0.1:8080" pnpm run tauri:dev
```

## One-Command Startup

To start both API and Viewer together:

```bash
./run_all.sh
```

This script:
1. Starts `video2bvh-api` in the background
2. Waits for `/health` to be ready
3. Launches the Viewer
4. Stops the API on exit

## Troubleshooting

{: .warning }
> **Tauri startup error "WebKitWebProcess not found"**
>
> Run Step 1 to ensure all system dependencies are installed.

{: .warning }
> **API startup error "missing executable"**
>
> Make sure you are running from the project root directory and the `video2bvh-api` binary is executable.

{: .warning }
> **Viewer fails to connect to API**
>
> Confirm the API is running via `./run.sh` and the health check at Step 6 succeeds.

## Next Steps

- [Rust API](./reference/rust.html) - Rust SDK reference
- [HTTP API](./reference/http-api.html) - HTTP API reference
