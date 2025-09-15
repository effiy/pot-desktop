# 🛠️ 开发文档

> 💻 为 Pot 项目开发者和贡献者提供完整的技术文档

## 🎯 开发者导航

### 🆕 新贡献者快速入门

| 阶段         | 文档                             | 预计时间 | 技能要求 |
| ------------ | -------------------------------- | -------- | -------- |
| **了解项目** | [项目结构](project-structure.md) | 30 分钟  | 基础     |
| **搭建环境** | [环境搭建](development-setup.md) | 45 分钟  | 中等     |
| **学习规范** | [代码规范](coding-standards.md)  | 30 分钟  | 中等     |
| **开始贡献** | [贡献指南](contributing.md)      | 20 分钟  | 基础     |

### 👨‍💻 开发角色导航

<details>
<summary><strong>🎨 前端开发者</strong></summary>

**核心文档**:

-   [组件开发](components.md) - React 组件开发规范
-   [路由菜单](routes-and-menus.md) - 前端路由系统
-   [测试指南](testing.md) - 前端测试策略

**相关资源**:

-   [组件文档](../components/) - UI 组件库
-   [代码规范](coding-standards.md) - JavaScript/TypeScript 规范

</details>

<details>
<summary><strong>🦀 后端开发者</strong></summary>

**核心文档**:

-   [架构设计](architecture.md) - Rust + Tauri 架构
-   [插件开发](plugins.md) - 插件系统开发
-   [构建部署](build-and-deploy.md) - 构建和发布

**相关资源**:

-   [API 文档](../api/) - 接口规范
-   [代码规范](coding-standards.md) - Rust 编码规范

</details>

<details>
<summary><strong>🔌 插件开发者</strong></summary>

**核心文档**:

-   [插件开发](plugins.md) - 完整插件开发指南
-   [插件 API](../api/plugin-api.md) - API 接口文档

**参考资源**:

-   [插件使用](../user-guides/plugins.md) - 用户视角的插件使用
-   [架构设计](architecture.md) - 插件系统架构

</details>

<details>
<summary><strong>🚀 DevOps 工程师</strong></summary>

**核心文档**:

-   [构建部署](build-and-deploy.md) - CI/CD 和部署流程
-   [开发流程](workflow.md) - 分支管理和发布流程

**相关资源**:

-   [环境搭建](development-setup.md) - 开发环境配置
-   [开发问题](troubleshooting.md) - 构建问题解决

</details>

## 📚 技术文档分类

### 🏗️ 架构和设计

-   [🏛️ 架构设计](architecture.md) - 系统整体架构和设计理念
-   [📁 项目结构](project-structure.md) - 详细的目录结构说明
-   [🧭 路由菜单](routes-and-menus.md) - 前端路由和菜单系统

### 🚀 环境和流程

-   [⚙️ 环境搭建](development-setup.md) - 开发环境配置和工具安装
-   [🔄 开发流程](workflow.md) - Git 工作流和分支管理策略
-   [📦 构建部署](build-and-deploy.md) - 项目构建、打包和发布流程

### 📖 开发规范

-   [📝 代码规范](coding-standards.md) - 代码风格和编写规范
-   [🎨 组件开发](components.md) - UI 组件开发指南和最佳实践
-   [🔌 插件开发](plugins.md) - 插件系统开发完整指南
-   [🧪 测试指南](testing.md) - 测试策略、工具和最佳实践

### 🤝 协作和支持

-   [🤲 贡献指南](contributing.md) - 参与项目开发和贡献流程
-   [🛠️ 开发问题](troubleshooting.md) - 开发过程中的问题解决方案

## 🛠️ 技术栈

**前端**:

-   React 18 + Hooks
-   Vite (构建工具)
-   TailwindCSS + NextUI (UI框架)
-   Jotai (状态管理)
-   React Router (路由)
-   i18next (国际化)

**后端**:

-   Rust
-   Tauri (跨平台框架)
-   Tokio (异步运行时)
-   Serde (序列化)

**工具链**:

-   pnpm (包管理)
-   TypeScript (类型检查)
-   Prettier (代码格式化)
-   Tauri CLI (构建工具)

## 🚀 快速开始

1. **了解项目**: 先阅读[项目结构](project-structure.md)
2. **搭建环境**: 按照[开发环境搭建](development-setup.md)配置环境
3. **开始开发**: 参考[贡献指南](contributing.md)
4. **构建部署**: 查看[构建和部署](build-and-deploy.md)

## 📖 相关资源

-   [Tauri 官方文档](https://tauri.app/)
-   [React 官方文档](https://react.dev/)
-   [Rust 官方文档](https://doc.rust-lang.org/)
-   [Vite 官方文档](https://vitejs.dev/)

## 🤝 参与贡献

我们欢迎各种形式的贡献：

-   代码贡献
-   文档改进
-   Bug 报告
-   功能建议
-   翻译工作

请先阅读[贡献指南](contributing.md)了解详细信息。
