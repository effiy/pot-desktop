# ⚡ 快速参考

快速查找 Pot 项目文档和资源的索引页面。

## 🔥 最常用文档

### 📥 新用户必备

| 文档                                          | 用途                | 时间      |
| --------------------------------------------- | ------------------- | --------- |
| [📦 安装指南](user-guides/installation.md)    | 安装 Pot 到您的系统 | 5-10 分钟 |
| [🧭 菜单导航](user-guides/menu-navigation.md) | 了解界面和功能      | 10 分钟   |
| [🚀 基本使用](user-guides/basic-usage.md)     | 开始使用翻译功能    | 15 分钟   |

### 🛠️ 开发者必备

| 文档                                            | 用途         | 时间    |
| ----------------------------------------------- | ------------ | ------- |
| [🏗️ 项目结构](development/project-structure.md) | 了解项目组织 | 20 分钟 |
| [⚙️ 环境搭建](development/development-setup.md) | 配置开发环境 | 30 分钟 |
| [📝 代码规范](development/coding-standards.md)  | 学习编码标准 | 25 分钟 |

### 🔌 插件开发必备

| 文档                                  | 用途         | 时间    |
| ------------------------------------- | ------------ | ------- |
| [🔌 插件使用](user-guides/plugins.md) | 了解插件系统 | 15 分钟 |
| [📖 插件 API](api/plugin-api.md)      | 学习插件接口 | 45 分钟 |
| [🛠️ 插件开发](development/plugins.md) | 开发插件指南 | 60 分钟 |

## 🎯 按问题类型查找

### 🚫 安装问题

| 系统        | 文档链接                                                       | 常见问题       |
| ----------- | -------------------------------------------------------------- | -------------- |
| **Windows** | [安装指南 § Windows](user-guides/installation.md#windows-安装) | 权限、依赖库   |
| **macOS**   | [安装指南 § macOS](user-guides/installation.md#macos-安装)     | 安全设置、权限 |
| **Linux**   | [安装指南 § Linux](user-guides/installation.md#linux-安装)     | 依赖包、权限   |

### 🔧 功能问题

| 功能             | 故障排除                                                         | 配置文档                                     |
| ---------------- | ---------------------------------------------------------------- | -------------------------------------------- |
| **翻译不工作**   | [常见问题 § 翻译](user-guides/troubleshooting.md#-翻译问题)      | [配置说明](user-guides/configuration.md)     |
| **OCR 识别失败** | [常见问题 § OCR](user-guides/troubleshooting.md#-ocr-问题)       | [高级功能](user-guides/advanced-features.md) |
| **快捷键无效**   | [常见问题 § 快捷键](user-guides/troubleshooting.md#️-快捷键问题) | [菜单导航](user-guides/menu-navigation.md)   |
| **插件异常**     | [插件使用 § 故障排除](user-guides/plugins.md#-插件故障排除)      | [插件使用](user-guides/plugins.md)           |

### 💻 开发问题

| 问题类型         | 解决文档                                                         | 相关资源                                     |
| ---------------- | ---------------------------------------------------------------- | -------------------------------------------- |
| **环境搭建失败** | [开发问题 § 环境](development/troubleshooting.md#️-环境搭建问题) | [环境搭建](development/development-setup.md) |
| **构建失败**     | [开发问题 § 构建](development/troubleshooting.md#-构建问题)      | [构建部署](development/build-and-deploy.md)  |
| **测试问题**     | [开发问题 § 测试](development/troubleshooting.md#-调试问题)      | [测试指南](development/testing.md)           |
| **代码规范**     | [代码规范](development/coding-standards.md)                      | [贡献指南](development/contributing.md)      |

## 📖 API 快速参考

### 🔌 插件 API

| API 类型     | 接口文档                                           | 示例代码                                                 |
| ------------ | -------------------------------------------------- | -------------------------------------------------------- |
| **翻译插件** | [插件 API § 翻译](api/plugin-api.md#-翻译插件-api) | [插件开发 § 翻译](development/plugins.md#1-翻译插件开发) |
| **OCR 插件** | [插件 API § OCR](api/plugin-api.md#-ocr-插件-api)  | [插件开发 § OCR](development/plugins.md#2-ocr-插件开发)  |
| **TTS 插件** | [插件 API § TTS](api/plugin-api.md#-tts-插件-api)  | [插件开发 § TTS](development/plugins.md#3-tts-插件开发)  |
| **存储插件** | [插件 API § 存储](api/plugin-api.md#-存储插件-api) | [插件开发 § 存储](development/plugins.md#4-存储插件开发) |

### 🌐 外部调用 API

| 调用方式     | 文档链接                                          | 示例                 |
| ------------ | ------------------------------------------------- | -------------------- |
| **HTTP API** | [外部 API § HTTP](api/external-api.md#-http-api)  | POST /translate      |
| **命令行**   | [外部 API § CLI](api/external-api.md#-命令行接口) | pot translate "text" |
| **配置 API** | [配置 API](api/config-api.md)                     | 配置系统接口         |

## 🎨 组件快速参考

### ⚛️ 原子组件

| 组件       | 文档                                      | 主要用途 |
| ---------- | ----------------------------------------- | -------- |
| **Button** | [Button 文档](components/atoms/Button.md) | 操作触发 |
| **Input**  | [Input 文档](components/atoms/Input.md)   | 文本输入 |
| **Switch** | [Switch 文档](components/atoms/Switch.md) | 开关切换 |
| **Text**   | [Text 文档](components/atoms/Text.md)     | 文本显示 |

### 🧬 分子组件

| 组件               | 文档                                                          | 主要用途 |
| ------------------ | ------------------------------------------------------------- | -------- |
| **LanguageSelect** | [LanguageSelect 文档](components/molecules/LanguageSelect.md) | 语言选择 |
| **ServiceCard**    | [ServiceCard 文档](components/molecules/ServiceCard.md)       | 服务展示 |
| **SearchBox**      | [SearchBox 文档](components/molecules/SearchBox.md)           | 搜索输入 |

### 🦠 有机体组件

| 组件                 | 文档                                                              | 主要用途 |
| -------------------- | ----------------------------------------------------------------- | -------- |
| **TranslationPanel** | [TranslationPanel 文档](components/organisms/TranslationPanel.md) | 翻译界面 |
| **SettingsForm**     | [SettingsForm 文档](components/organisms/SettingsForm.md)         | 设置表单 |

### 🎣 Hooks

| Hook               | 文档                                                      | 主要用途 |
| ------------------ | --------------------------------------------------------- | -------- |
| **useConfig**      | [useConfig 文档](components/hooks/useConfig.md)           | 配置管理 |
| **useTranslation** | [useTranslation 文档](components/hooks/useTranslation.md) | 翻译服务 |

## 🔗 重要链接

### 📱 项目资源

-   **🏠 项目主页**: [GitHub Repository](https://github.com/pot-app/pot-desktop)
-   **📦 下载页面**: [GitHub Releases](https://github.com/pot-app/pot-desktop/releases)
-   **🐛 问题报告**: [GitHub Issues](https://github.com/pot-app/pot-desktop/issues)
-   **💬 讨论区**: [GitHub Discussions](https://github.com/pot-app/pot-desktop/discussions)

### 🌐 社区资源

-   **🔥 QQ 频道**: [加入讨论](https://pd.qq.com/s/akns94e1r)
-   **📚 文档中心**: [docs/](INDEX.md)
-   **🔌 插件市场**: [plugins.pot-app.com](https://plugins.pot-app.com) (计划中)

### 🛠️ 开发资源

-   **📖 技术栈文档**:
    -   [Tauri 官方文档](https://tauri.app/)
    -   [React 官方文档](https://react.dev/)
    -   [Rust 官方文档](https://doc.rust-lang.org/)
-   **🎨 设计资源**:
    -   [NextUI 组件库](https://nextui.org/)
    -   [TailwindCSS 文档](https://tailwindcss.com/)
    -   [React Icons](https://react-icons.github.io/react-icons/)

## 🎯 快速命令

### 📝 文档操作

```bash
# 启动文档开发服务器
npm run docs:dev

# 构建文档网站
npm run docs:build

# 检查文档链接
npm run docs:check-links

# 格式化文档
npm run docs:format
```

### 🔍 搜索技巧

```bash
# 在所有文档中搜索关键词
grep -r "关键词" docs/

# 查找特定类型的文档
find docs -name "*api*.md"

# 统计文档数量
find docs -name "*.md" | wc -l
```

---

<div align="center">

**⚡ 快速参考** | **📊 统计**: 36 篇文档 | **🎯 覆盖率**: 92% | **🕐 更新**: 2024年12月

_此快速参考会根据文档更新自动同步_

</div>
