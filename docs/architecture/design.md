# Motion Viewer Rust + WebUI 架构设计

## 项目概述

将原有的 TypeScript + Three.js 的 motion_viewer 项目重构为 Rust + WebUI 架构，使用 Tauri + Vue 3，保持所有原有功能，不牺牲性能。

## 技术栈

- **前端**: Vue 3 + TypeScript + Three.js
- **后端**: Rust (Tauri)
- **状态管理**: Pinia
- **构建工具**: Vite + Tauri CLI
- **测试**: Vitest + Rust tests

## 架构设计

### 1. 前端架构 (Vue 3 + TypeScript)

```
src/
├── pages/                    # 页面组件
│   ├── MotionViewerPage.vue  # 运动查看器页面
│   ├── ModelViewerPage.vue   # 模型查看器页面
│   ├── MotionMappingPage.vue # 运动映射页面
│   └── ActionEditorPage.vue  # 动作编辑器页面
├── components/               # 可复用组件
│   ├── ThreeViewer/          # Three.js 3D 查看器
│   ├── Explorer/              # 文件资源浏览器
│   ├── MenuBar/               # 菜单栏
│   └── AIAssistantPanel/      # AI 辅助面板
├── stores/                   # Pinia 状态管理
│   ├── motionViewer.ts        # 运动查看器状态
│   ├── modelViewer.ts         # 模型查看器状态
│   ├── motionMapping.ts       # 运动映射状态
│   └── actionEditor.ts        # 动作编辑器状态
├── services/                 # 业务服务
│   └── viewerAssetLoader.ts   # 资源加载服务
├── utils/                    # 工具函数
│   ├── motion/               # 运动数据处理
│   │   ├── BvhMotionService.ts
│   │   ├── groundAlignment.ts
│   │   └── SplineUtils.ts
│   ├── urdfLoader.ts         # URDF 加载器
│   └── fileUtils.ts          # 文件工具
├── viewer/                   # 3D 可视化核心
│   ├── SceneController.ts    # 场景控制器
│   ├── ViewerRuntime.ts      # 运行时管理
│   └── ViewerFrameScheduler.ts # 帧调度器
└── types/                    # TypeScript 类型定义
    ├── viewer.ts
    ├── resources.ts
    └── actionEditor.ts
```

### 2. 后端架构 (Rust)

```
src-tauri/
├── src/
│   ├── lib.rs              # 主库文件/Tauri 命令定义
│   ├── main.rs             # 入口文件
│   ├── models.rs           # 数据模型定义
│   └── services/           # 后端服务
│       ├── mod.rs          # 模块导出
│       ├── motion_service.rs  # 运动数据处理
│       └── retarget_native.rs # 运动重定向原生实现
├── native/                 # 原生代码
│   ├── retarget_bridge.cpp
│   └── retarget_bridge.hpp
└── Cargo.toml
```

### 3. 数据流设计

```
前端 (Vue 3) <--[Tauri Commands]--> 后端 (Rust)
       |                                  |
       v                                  v
Three.js 渲染 <--[文件数据]--> 文件系统操作
       |                                  |
       v                                  v
    GPU 渲染                          CPU 密集型计算
       |                                  |
       v                                  v
   可视化显示                         数据处理/解析
```

## 功能模块

### 1. 运动查看器 (Motion Viewer)

支持加载和播放 BVH、CSV、JSON 格式的运动数据，提供帧播放控制、时间轴拖拽、播放速度调节等功能。

### 2. 模型查看器 (Model Viewer)

支持加载 URDF、GLTF/GLB、OBJ、DAE 等格式的机器人模型和场景文件，提供模型预览和诊断信息显示。

### 3. 运动映射 (Motion Mapping)

将源运动数据映射到目标机器人模型，支持骨骼对应关系配置和实时预览。

### 4. 动作编辑器 (Action Editor)

提供关键帧编辑、曲线调整、骨骼控制等功能，支持运动轨迹的可视化和编辑。

## 性能优化

### 1. 渲染性能

- 使用 Three.js 的 WebGL 渲染
- 实现模型实例化
- 添加细节层次 (LOD)
- 使用 GPU 加速计算

### 2. 数据加载性能

- 实现文件缓存机制
- 使用流式加载大文件
- 添加后台线程处理
- 支持增量加载

### 3. 内存管理

- 及时释放不再使用的资源
- 实现对象池
- 监控内存使用
- 添加内存警告机制

## Rust 后端优势

### 1. 性能提升

- **文件解析**: Rust 的零成本抽象和内存安全
- **数据处理**: 多线程并行处理 (Rayon)
- **内存效率**: 无垃圾回收，精确内存控制

### 2. 安全性

- 编译时内存安全保证
- 线程安全的数据结构
- 防止常见的安全漏洞

### 3. 跨平台支持

- 原生支持 Windows、macOS、Linux
- 一致的性能表现
- 无需浏览器兼容性处理

## 与原项目的兼容性

### 保持兼容的功能

- 所有文件格式支持 (URDF, BVH, CSV, SMPL, OBJ, GLTF)
- 相同的用户界面和工作流程
- 相同的键盘快捷键
- 相同的预设系统

### 改进的功能

- 更快的文件加载速度
- 更好的内存管理
- 更稳定的长时间运行
- 原生文件系统访问

## 开发计划

### 第1周：基础架构

- 项目设置和依赖安装
- 基础 Three.js 场景搭建
- 文件拖放基础功能

### 第2周：核心功能

- URDF 模型加载
- BVH 运动播放
- 基本 UI 控件

### 第3周：高级功能

- SMPL 模型支持
- CSV 运动数据
- 预设系统

### 第4周：优化和测试

- 性能优化
- 内存管理
- 测试和文档

## 风险评估和缓解

### 风险1：Three.js 与 Tauri 集成问题

- **缓解**: 使用现有的成功案例作为参考，逐步集成

### 风险2：Rust 后端性能未达预期

- **缓解**: 使用性能分析工具，优化热点代码

### 风险3：文件格式兼容性问题

- **缓解**: 保持与原项目相同的解析逻辑，逐步迁移

### 风险4：内存泄漏

- **缓解**: 使用 Rust 的所有权系统，添加内存监控

## 成功标准

### 功能完整性

- [ ] 支持所有原项目的文件格式
- [ ] 实现所有原项目的功能
- [ ] 保持相同的用户体验

### 性能指标

- [ ] 渲染性能 ≥ 60 FPS
- [ ] 文件加载速度 ≥ 原项目
- [ ] 内存使用 ≤ 原项目的 120%

### 质量指标

- [ ] 无崩溃运行时间 ≥ 24小时
- [ ] 测试覆盖率 ≥ 80%
- [ ] 用户界面响应时间 ≤ 100ms

## 结论

通过将 motion_viewer 重构为 Rust + WebUI 架构，我们可以在保持所有原有功能的同时，获得更好的性能、安全性和跨平台支持。Tauri + Vue 3 的组合提供了现代 Web 开发的便利性和 Rust 的系统级性能优势。
