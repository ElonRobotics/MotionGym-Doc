---
layout: default
title: 数据导入
parent: Rust API
nav_order: 2
lang: zh
ref: rust-api-loader
---

# 数据导入

## load_bvh

从 BVH 文件加载并解析为标准化帧数据。

```rust
pub fn load_bvh(
    path: &str,
    max_frames: i64,
    axis_order: Option<&str>,
) -> Result<BvhLoadResult>
```

### 参数

| 参数 | 类型 | 说明 |
|------|------|------|
| `path` | `&str` | BVH 文件路径 |
| `max_frames` | `i64` | 最大加载帧数；`-1` 表示不限制 |
| `axis_order` | `Option<&str>` | 旋转轴顺序覆盖，如 `Some("ZXY")`；`None` 沿用文件声明 |

### axis_order 行为

- 仅覆盖 rotation channel 名称，不改变数值
- `Some("XYZ")`：强制使用 XYZ 顺序解释旋转数据
- `None`：使用文件头声明的顺序（推荐）

### 示例

```rust
use ee_sdk::ee_core::loader::bvh::load_bvh;

// 加载全部帧，使用文件声明的轴顺序
let result = load_bvh("motion.bvh", -1, None)?;

// 只加载前 100 帧，强制 ZXY 轴顺序
let result = load_bvh("motion.bvh", 100, Some("ZXY"))?;
```

### 返回值：BvhLoadResult

`load_bvh` 的返回值，包含解析后的完整骨架数据。

```rust
pub struct BvhLoadResult {
    pub frames: Vec<Frame>,  // Vec<HashMap<String, HumanPose>>
    pub fps: u32,
}
```

BVH HIERARCHY 中每个 `JOINT` 节点名称解析后即构成关节名列表，因此 `BvhLoadResult` 可查询所有关节名称。

#### 方法

| 方法 | 返回值 | 说明 |
|------|--------|------|
| `frame_count()` | `usize` | 帧数量 |
| `duration_secs()` | `f64` | 时长（秒） |
| `hierarchy()` | `Vec<(&str, Option<&str>)>` | 父子关节对列表，`None` = 根节点 |
| `joint_pose(frame_idx, joint)` | `Option<&HumanPose>` | 指定帧指定关节的姿态 |

#### 骨架结构示例

`hierarchy()` 从 BVH HIERARCHY 中的 `JOINT` 节点解析而来，直接反映源文件的骨架结构：

```rust
let result = load_bvh("motion.bvh", -1, Some("ZXY"))?;

println!("帧率: {} fps", result.fps);
println!("帧数: {}", result.frame_count());
println!("时长: {:.2}s", result.duration_secs());

// hierarchy() 返回 (关节名, 父关节名) 对，None = 根节点
for (joint, parent) in result.hierarchy() {
    println!("{} <- {:?}", joint, parent);
}
```

输出示例（标准骨架 BVH 的 hierarchy）：

```
ROOT Hips                # 骨盆，根节点
├── Spine                # 下脊柱
│   └── Spine1            # 中脊柱
│       └── Spine2        # 上脊柱
│           └── Spine3    # 胸部
│               ├── Neck
│               │   └── Head
│               ├── LeftShoulder
│               │   └── LeftArm          # 左上臂
│               │       └── LeftForeArm  # 左前臂
│               │           └── LeftHand  # 左手
│               └── RightShoulder
│                   └── RightArm
│                       └── RightForeArm
│                           └── RightHand
├── LeftUpLeg            # 左大腿
│   └── LeftLeg           # 左小腿
│       └── LeftFoot      # 左踝
│           └── LeftToe   # 左脚趾
└── RightUpLeg
    └── RightLeg
        └── RightFoot
            └── RightToe
```

不同的 BVH 文件关节名称不同，`hierarchy()` 如实返回源文件的节点声明。

#### 查询指定关节姿态

```rust
// 查询第 0 帧的 Hips 姿态
if let Some(pose) = result.joint_pose(0, "Hips") {
    println!("Hips 位置: {:?}", pose.pos);
    println!("Hips 旋转: {:?}", pose.quat_wxyz);
}
```

---

## export_bvh_file

将 BVH 文件归一化并重新导出，可指定轴顺序和目标帧率。

```rust
pub fn export_bvh_file(
    input_bvh_path: &str,
    output_bvh_path: &str,
    axis_order: &str,
    target_fps: Option<u32>,
) -> Result<()>
```

### 示例

```rust
use ee_sdk::ee_core::loader::bvh::export_bvh_file;

// 将输入 BVH 转换为 ZXY 轴顺序，输出 30fps
export_bvh_file("input.bvh", "output_normalized.bvh", "ZXY", Some(30))?;

// 保持原始帧率，只改变轴顺序
export_bvh_file("input.bvh", "output_zxy.bvh", "ZXY", None)?;
```

---

## convert_bvh_to_retarget_bvh

保留原始 hierarchy 结构，仅归一化旋转通道顺序。

```rust
pub fn convert_bvh_to_retarget_bvh(
    input_bvh_path: &str,
    output_bvh_path: &str,
    axis_order: Option<&str>,
    fps: Option<u32>,
) -> bool
```

返回 `true` 表示成功。

### 示例

```rust
let ok = ee_sdk::convert_bvh_to_retarget_bvh(
    "data/jobs/source/bvh/good_time.bvh",
    "good_time_normalized.bvh",
    Some("ZXY"),
    None,
);
assert!(ok);
```

---

## Standard Bvh

标准骨架封装，帧数据保证包含完整 24 个标准关节。若某关节缺失，自动从父关节复制。

### StandardBvh

```rust
pub struct StandardBvh {
    pub fps: u32,
    pub frames: Vec<HashMap<String, HumanPose>>,
}
```

### StandardBvhSummary

```rust
pub struct StandardBvhSummary {
    pub fps: u32,
    pub frame_count: usize,
}
```

### 函数

| 函数 | 说明 |
|------|------|
| `load_standard_bvh(path)` | 加载标准骨架 BVH |
| `save_standard_bvh(bvh, path)` | 导出标准 BVH 文件 |
| `convert_bvh_to_standard_bvh(in, out)` | 一站式转换：加载 → 标准化 → 导出 |

### 示例

```rust
use ee_sdk::standard_bvh::{load_standard_bvh, save_standard_bvh, convert_bvh_to_standard_bvh};

// 加载标准骨架（自动处理关节名映射和缺失填充）
let bvh = load_standard_bvh("motion.bvh")?;
save_standard_bvh(&bvh, "standard_motion.bvh")?;

// 一站式转换
let summary = convert_bvh_to_standard_bvh(
    "data/jobs/source/bvh/good_time.bvh",
    "good_time_standard.bvh",
)?;
println!("fps={}, frames={}", summary.fps, summary.frame_count);
```
