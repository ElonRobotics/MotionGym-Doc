---
layout: default
title: 基本概念
parent: Rust API
nav_order: 1
lang: zh
ref: rust-api-core-types
---

# 基本概念

---

## BVH 基础

BVH（Biovision Hierarchy）是一种通用的人体动画文件格式，几乎所有动捕软件和 3D 软件都支持导出。

BVH 文件包含两部分：

| 部分 | 内容 |
|------|------|
| **HIERARCHY** | 骨骼骨架结构：关节名称、父子关系、骨骼长度（OFFSET） |
| **MOTION** | 运动数据：每帧各关节的旋转和位置值 |

### 骨骼树

BVH 以树状结构描述骨骼。根节点是 **Hips（髋部）**，记录人体在世界中的位置；子节点记录相对于父关节的旋转。

```
ROOT Hips
├── Spine
│   └── Spine1 → Spine2 → Spine3
│       ├── LeftShoulder → LeftArm → LeftForeArm → LeftHand
│       └── RightShoulder → RightArm → RightForeArm → RightHand
├── LeftUpLeg → LeftLeg → LeftFoot → LeftToe
└── RightUpLeg → RightLeg → RightFoot → RightToe
```

### 旋转表示

BVH 用 **Euler 角**存储旋转，旋转顺序为 **Z → X → Y**。

- **Z 轴**：横滚轴（Roll），控制身体左右倾斜
- **X 轴**：俯仰轴（Pitch），控制身体前后倾斜
- **Y 轴**：偏航轴（Yaw），控制身体左右转动

### 坐标系

- **Y 轴向上**，**Z 轴朝前**
- **单位**：长度 cm，时间 s

---

## 标准化

### 为什么要标准化

不同来源的 BVH 文件存在巨大差异：

| 差异 | 来源 A | 来源 B |
|------|--------|--------|
| 关节命名 | `Hips`, `Spine`, `L_UpLeg` | `pelvis`, `back`, `left_thigh` |
| 骨架层级 | 完整 24 关节 | 缺失肩胛，仅 18 关节 |
| 骨骼长度 | 成人平均 170cm | 儿童 120cm |
| 旋转顺序 | Z-X-Y | X-Z-Y |

直接混用会导致重映射结果扭曲或关节错位。**标准化**统一关节命名、骨架结构（24 关节）、骨骼长度参考值、旋转顺序。

### 标准骨架（24 关节）

所有输入数据最终映射到项目统一的 24 关节骨架：

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

### T-Pose

T-Pose 是所有动捕数据的零位参考：双臂侧平举绕 X 轴旋转约 ±90°，双腿伸直并拢，脚朝向 +Z，脊柱直立。

### 标准骨架 → Unitree G1 关节映射

| 标准骨架关节 | Unitree G1 关节 | 说明 |
|------------|----------------|------|
| Hips | `pelvis` | 骨盆，根节点 |
| Spine | — | G1 无独立脊柱关节，通过 torso 约束 |
| Spine2 | `torso_link` | 躯干链，G1 脊柱主体 |
| LeftArm | `left_shoulder_yaw_link` | 左肩 |
| LeftForeArm | `left_elbow_link` | 左肘 |
| LeftHand | `left_wrist_yaw_link` | 左腕 |
| RightArm | `right_shoulder_yaw_link` | 右肩 |
| RightForeArm | `right_elbow_link` | 右肘 |
| RightHand | `right_wrist_yaw_link` | 右腕 |
| LeftUpLeg | `left_hip_yaw_link` | 左髋 |
| LeftLeg | `left_knee_link` | 左膝 |
| LeftFootMod | `left_ankle_roll_link` | 左踝 |
| RightUpLeg | `right_hip_yaw_link` | 右髋 |
| RightLeg | `right_knee_link` | 右膝 |
| RightFootMod | `right_ankle_roll_link` | 右踝 |

{: .note }
> Spine1、Spine3、Neck、Head、LeftShoulder、RightShoulder、LeftFoot、RightFoot、LeftToe、RightToe 不参与 G1 重定向，但在 Standard BVH 中保留。

---

## 目标机器人

### Robot

目标机器人类型，当前仅支持 Unitree G1。

```rust
Robot::UnitreeG1
```

### load_robot_description

加载并解析机器人模型描述文件，同时支持 URDF 和 MJCF 两种格式：

| 文件扩展名 | 格式 |
|-----------|------|
| `.xml` | MJCF |
| `.urdf` | URDF |

```rust
pub fn load_robot_description(
    robot: Robot,
    robot_file: Option<&str>,
) -> Result<RobotDescription>
```

| 参数 | 类型 | 说明 |
|------|------|------|
| `robot` | `Robot` | 目标机器人 |
| `robot_file` | `Option<&str>` | 自定义模型文件路径，`None` 使用内置默认路径 |

```rust
use ee_sdk::prelude::*;

let desc = load_robot_description(Robot::UnitreeG1, None)?;
// 指定自定义 MJCF 文件
let desc = load_robot_description(Robot::UnitreeG1, Some("path/to/robot.xml"))?;
```

### RobotDescription

| 字段 | 类型 | 说明 |
|------|------|------|
| `robot_name` | `String` | 机器人名称 |
| `root_link` | `String` | 根 link 名称 |
| `dof_count` | `usize` | 自由度数量 |
| `dof_names` | `Vec<String>` | 自由度名称列表 |
| `joints` | `Vec<JointDescription>` | 关节描述列表 |

### JointDescription

| 字段 | 类型 | 说明 |
|------|------|------|
| `name` | `String` | 关节名称 |
| `joint_type` | `String` | 关节类型（revolute / prismatic 等） |
| `parent_link` | `String` | 父 link 名称 |
| `child_link` | `String` | 子 link 名称 |
| `axis` | `[f64; 3]` | 旋转轴方向向量 |
| `lower_limit` | `f64` | 位置下限 |
| `upper_limit` | `f64` | 位置上限 |
| `velocity_limit` | `f64` | 最大转速 |

---

## API 类型

### Prelude

Rust 的 `prelude` 模式：将高频类型集中在 `prelude` 模块中，通过一行导入所有常用类型。

```rust
use ee_sdk::prelude::*;
```

包含：`Robot`、`RetargetOutput`、`RetargetSession`、`BvhRetargetOptions`、`RetargetConfig`、`resample_frames`、`STANDARD_FPS`、`MIN_INPUT_FPS`、`MAX_INPUT_FPS`、`StandardBvh`、`StandardBvhSummary`、`load_robot_description`、`RobotDescription`、`JointDescription`、`SkeletonData`、`supported_robots`、`supported_source_humans`、`load_bvh_skeleton_snapshot`

### HumanPose

单个关节在某一帧的姿态：

```rust
pub struct HumanPose {
    pub pos: [f64; 3],        // 位置 (m)。根节点为世界坐标，子节点为相对于父关节的偏移
    pub quat_wxyz: [f64; 4], // 四元数 (w, x, y, z)。表示相对于父关节的旋转
}
```

### Frame

一帧内所有关节的姿态，键为关节名称：

```rust
type Frame = HashMap<String, HumanPose>;
```

`load_bvh` 返回的 `BvhLoadResult.frames` 即 `Vec<Frame>`。

### FrameOutput

`RetargetSession::retarget_frame()` 对单帧的输出结果：

| 字段 | 类型 | 说明 |
|------|------|------|
| `root_pos` | `[f64; 3]` | 根位移（m） |
| `root_rot_xyzw` | `[f64; 4]` | 根旋转（四元数 xyzw） |
| `dof_pos` | `Vec<f64>` | 各关节自由度值，按机器人 DOF 顺序排列 |

### RetargetOutput

`retarget_from_bvh` 的完整输出：

| 字段 | 类型 | 说明 |
|------|------|------|
| `frames` | `Vec<FrameOutput>` | 每帧的机器人关节值 |
| `robot` | `Robot` | 目标机器人类型 |
