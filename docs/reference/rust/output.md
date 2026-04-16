---
layout: default
title: 导出
parent: Rust API
nav_order: 6
lang: zh
ref: rust-api-output
---

# 导出

## RetargetOutput

| 属性 | 类型 | 说明 |
| :--- | :--- | :--- |
| `fps` | `u32` | 输出帧率 |
| `root_pos_frames` | `Vec<[f64; 3]>` | 根位移序列 |
| `root_rot_frames` | `Vec<[f64; 4]>` | 根旋转序列（xyzw） |
| `dof_pos_frames` | `Vec<Vec<f64>>` | 关节自由度序列 |

### 方法

| 方法 | 说明 |
| :--- | :--- |
| `to_csv()` | 导出为 CSV 字符串 |
| `to_json(options)` | 导出为 JSON 字符串 |
| `to_export_sequence(options)` | 构建统一导出结构 `ExportSequence` |
| `export_to_file(path, options)` | 导出到文件，支持 `csv` / `json` / `npz` / `pkl` |

`to_csv()` 生成的 CSV 由三部分数据组成：根平移、根旋转、关节自由度。

---

## 统一导出层

`RetargetOutput` 现在通过统一导出层输出多种格式，核心配置由 `ExportFormat` 和 `ExportOptions` 控制。

### ExportFormat

```rust
pub enum ExportFormat {
    Csv,
    Json,
    Npz,
    Pkl,
}
```

| 格式 | 说明 |
|------|------|
| `Csv` | 默认格式，兼容原有重映射输出 |
| `Json` | 结构化导出，便于调试与服务集成 |
| `Npz` | NumPy 压缩格式，适合 Python / 机器学习流程 |
| `Pkl` | Python Pickle 格式，适合下游 Python 工具 |

### ExportOptions

```rust
pub struct ExportOptions {
    pub format: ExportFormat,
    pub include_velocities: bool,
    pub include_meta: bool,
    pub pretty_json: bool,
}
```

| 字段 | 类型 | 说明 |
|------|------|------|
| `format` | `ExportFormat` | 导出格式 |
| `include_velocities` | `bool` | 是否附带线速度 / 角速度 / 关节速度 |
| `include_meta` | `bool` | 是否在导出结果中附带元信息 |
| `pretty_json` | `bool` | JSON 是否格式化输出 |

默认值：

```rust
ExportOptions::default()
```

- `format = Csv`
- `include_velocities = false`
- `include_meta = true`
- `pretty_json = true`

### ExportSequence

统一导出结构：

```rust
pub struct ExportSequence {
    pub meta: Option<ExportMeta>,
    pub signals: ExportSignals,
}
```

#### ExportMeta

| 字段 | 类型 | 说明 |
|------|------|------|
| `fps` | `u32` | 帧率 |
| `frame_count` | `usize` | 帧数 |
| `robot` | `Option<String>` | 机器人名称（如有） |
| `source` | `Option<String>` | 数据来源（如有） |
| `dof_names` | `Option<Vec<String>>` | 自由度名称（如有） |
| `rotation_format` | `String` | 四元数顺序，当前为 `xyzw` |
| `position_unit` | `String` | 位置单位，当前为 `meter` |
| `schema_version` | `u32` | 导出结构版本 |

#### ExportSignals

| 字段 | 类型 | 说明 |
|------|------|------|
| `root_pos` | `Vec<[f64; 3]>` | 根位移序列 |
| `root_rot_xyzw` | `Vec<[f64; 4]>` | 根旋转序列 |
| `dof_pos` | `Vec<Vec<f64>>` | 关节自由度序列 |
| `root_lin_vel` | `Option<Vec<[f64; 3]>>` | 根线速度 |
| `root_ang_vel` | `Option<Vec<[f64; 3]>>` | 根角速度 |
| `dof_vel` | `Option<Vec<Vec<f64>>>` | 关节速度 |

---

## 导出示例

### 导出 CSV

```rust
use ee_sdk::prelude::*;

let output = retarget_from_bvh(
    "data/jobs/source/bvh/good_time.bvh",
    BvhRetargetOptions::new(Robot::UnitreeG1),
)?;

std::fs::write("output.csv", output.to_csv())?;
```

### 导出 JSON

```rust
use ee_sdk::{ExportFormat, ExportOptions};

let json = output.to_json(&ExportOptions {
    format: ExportFormat::Json,
    include_velocities: true,
    ..Default::default()
})?;

std::fs::write("output.json", json)?;
```

### 导出 NPZ / PKL

`npz` 和 `pkl` 通过 `export_to_file()` 导出，底层会调用 Python 导出桥接脚本。

```rust
use ee_sdk::{ExportFormat, ExportOptions};

output.export_to_file(
    "output.npz",
    &ExportOptions {
        format: ExportFormat::Npz,
        include_velocities: true,
        ..Default::default()
    },
)?;

output.export_to_file(
    "output.pkl",
    &ExportOptions {
        format: ExportFormat::Pkl,
        include_velocities: true,
        ..Default::default()
    },
)?;
```

{: .note }
> `npz` 和 `pkl` 导出依赖仓库中的 Python 导出桥接脚本 `scripts/export_retarget.py`，因此运行环境需要可用的 `python3`。

---

## FrameOutput

`RetargetSession::retarget_frame()` 的返回值。

| 属性 | 类型 | 说明 |
| :--- | :--- | :--- |
| `root_pos` | `[f64; 3]` | 单帧根位移 |
| `root_rot_xyzw` | `[f64; 4]` | 单帧根旋转（四元数 xyzw） |
| `dof_pos` | `Vec<f64>` | 单帧关节自由度值 |
