# 项目目录结构说明

## 项目概述

Pot 是一个跨平台的划词翻译软件，基于 Tauri 框架开发，使用 React + Rust 技术栈。本文档详细说明了项目的目录结构和各个组件的作用。

## 技术栈

-   **前端**: React 18 + Vite + TailwindCSS + NextUI
-   **后端**: Rust + Tauri
-   **构建工具**: Vite + Tauri CLI
-   **包管理**: pnpm

## 根目录结构

```
pot-desktop/
├── asset/                          # 项目资源文件
├── docs/                          # 项目文档
├── public/                        # 静态资源文件
├── src/                          # 前端源码
├── src-tauri/                    # Rust 后端源码
├── patches/                      # 补丁文件
├── updater/                      # 更新程序
├── node_modules/                 # Node.js 依赖
├── package.json                  # Node.js 项目配置
├── pnpm-lock.yaml               # pnpm 锁定文件
├── vite.config.js               # Vite 构建配置
├── tailwind.config.cjs          # TailwindCSS 配置
├── postcss.config.js            # PostCSS 配置
├── daemon.html                  # 守护进程页面
└── index.html                   # 主页面模板
```

## 详细目录说明

### 📁 asset/

项目展示和文档用的资源文件

```
asset/
├── *.png                        # 项目截图
└── *.gif                        # 功能演示动图
```

### 📁 docs/

项目文档集合

```
docs/
├── README.md                    # 中文说明文档
├── README_EN.md                 # 英文说明文档
├── README_KR.md                 # 韩文说明文档
├── CHANGELOG                    # 更新日志
├── LICENSE                      # 开源许可证
├── com.pot_app.pot.metainfo.xml # Linux 应用元信息
└── PROJECT_STRUCTURE.md         # 项目结构说明（本文档）
```

### 📁 public/

静态资源文件，会被直接复制到构建输出目录

```
public/
├── icon.png                     # 应用图标 (PNG)
├── icon.svg                     # 应用图标 (SVG)
├── logo/                        # 各种服务商和平台的图标
│   ├── *.svg                    # 矢量图标
│   ├── *.png                    # 位图图标
│   └── *.webp                   # WebP 格式图标
├── tesseract-core-simd-lstm.wasm.js  # Tesseract OCR 核心文件
└── worker.min.js                # Web Worker 文件
```

### 📁 src/

前端 React 应用源码

```
src/
├── App.jsx                      # 根组件
├── main.jsx                     # 应用入口文件
├── style.css                    # 全局样式
├── components/                  # 通用组件
│   └── WindowControl/           # 窗口控制组件
├── hooks/                       # React Hooks
│   ├── index.jsx                # Hooks 导出文件
│   ├── useConfig.jsx            # 配置管理 Hook
│   ├── useGetState.jsx          # 状态获取 Hook
│   ├── useSyncAtom.jsx          # 原子状态同步 Hook
│   ├── useToastStyle.jsx        # Toast 样式 Hook
│   └── useVoice.jsx             # 语音功能 Hook
├── i18n/                        # 国际化
│   ├── index.jsx                # i18n 配置
│   └── locales/                 # 各语言翻译文件
├── services/                    # 服务模块
│   ├── collection/              # 收藏功能服务
│   ├── recognize/               # 文字识别服务
│   ├── translate/               # 翻译服务
│   └── tts/                     # 文字转语音服务
├── utils/                       # 工具函数
│   ├── env.js                   # 环境变量工具
│   ├── index.js                 # 通用工具函数
│   ├── invoke_plugin.js         # 插件调用工具
│   ├── lang_detect.js           # 语言检测工具
│   ├── language.ts              # 语言定义
│   ├── service_instance.ts      # 服务实例管理
│   └── store.js                 # 状态存储工具
└── window/                      # 各个窗口组件
    ├── Config/                  # 配置窗口
    ├── Recognize/               # 识别窗口
    ├── Screenshot/              # 截图窗口
    ├── Translate/               # 翻译窗口
    └── Updater/                 # 更新窗口
```

### 📁 src-tauri/

Tauri Rust 后端源码

```
src-tauri/
├── Cargo.toml                   # Rust 项目配置
├── Cargo.lock                   # Rust 依赖锁定文件
├── build.rs                     # 构建脚本
├── tauri.conf.json              # Tauri 主配置文件
├── tauri.*.conf.json            # 各平台特定配置
├── webview.*.json               # WebView 配置文件
├── src/                         # Rust 源码
│   ├── main.rs                  # 程序入口
│   ├── backup.rs                # 备份功能
│   ├── clipboard.rs             # 剪贴板操作
│   ├── cmd.rs                   # 命令处理
│   ├── config.rs                # 配置管理
│   ├── error.rs                 # 错误处理
│   ├── hotkey.rs                # 全局快捷键
│   ├── lang_detect.rs           # 语言检测
│   ├── screenshot.rs            # 截图功能
│   ├── server.rs                # HTTP 服务器
│   ├── system_ocr.rs            # 系统 OCR
│   ├── tray.rs                  # 系统托盘
│   ├── updater.rs               # 自动更新
│   └── window.rs                # 窗口管理
├── icons/                       # 应用图标文件
├── icons_mac/                   # macOS 专用图标
├── resources/                   # 资源文件
│   ├── ocr-aarch64-apple-darwin # ARM64 macOS OCR 引擎
│   └── ocr-x86_64-apple-darwin  # x64 macOS OCR 引擎
└── target/                      # Rust 构建输出目录
```

### 📁 services/ 详细说明

#### collection/ - 收藏功能

-   `anki/` - Anki 单词卡片集成
-   `eudic/` - 欧路词典集成

#### recognize/ - 文字识别

-   `baidu/` - 百度通用文字识别
-   `baidu_accurate/` - 百度高精度文字识别
-   `baidu_img/` - 百度图片文字识别
-   `iflytek/` - 讯飞文字识别
-   `iflytek_intsig/` - 讯飞手写识别
-   `iflytek_latex/` - 讯飞公式识别
-   `qrcode/` - 二维码识别
-   `simple_latex/` - 简单公式识别
-   `system/` - 系统 OCR
-   `tencent/` - 腾讯文字识别
-   `tencent_accurate/` - 腾讯高精度识别
-   `tencent_img/` - 腾讯图片识别
-   `tesseract/` - Tesseract 开源 OCR
-   `volcengine/` - 火山引擎识别
-   `volcengine_multi_lang/` - 火山引擎多语言识别

#### translate/ - 翻译服务

支持多种翻译引擎，包括：

-   Google 翻译
-   百度翻译
-   腾讯翻译
-   阿里翻译
-   有道翻译
-   DeepL 翻译
-   OpenAI 翻译
-   等等...

#### tts/ - 文字转语音

-   `lingva/` - Lingva TTS 服务

### 📁 window/ 详细说明

#### Config/ - 配置窗口

```
Config/
├── index.jsx                    # 配置窗口主组件
├── style.css                    # 配置窗口样式
├── components/                  # 配置组件
├── pages/                       # 各个配置页面
└── routes/                      # 路由配置
```

#### Recognize/ - 识别窗口

```
Recognize/
├── index.jsx                    # 识别窗口主组件
├── ControlArea/                 # 控制区域组件
├── ImageArea/                   # 图片显示区域
└── TextArea/                    # 文本显示区域
```

### 📁 其他重要文件

-   **package.json**: Node.js 项目配置，定义了依赖、脚本和项目元信息
-   **vite.config.js**: Vite 构建工具配置
-   **tailwind.config.cjs**: TailwindCSS 样式框架配置
-   **pnpm-lock.yaml**: pnpm 包管理器的锁定文件
-   **daemon.html**: 后台守护进程的 HTML 页面
-   **patches/hyprland.patch**: Hyprland 窗口管理器的补丁文件

## 构建和开发

### 开发环境启动

```bash
pnpm dev          # 启动开发服务器
pnpm tauri dev    # 启动 Tauri 开发环境
```

### 构建项目

```bash
pnpm build        # 构建前端
pnpm tauri build  # 构建完整应用
```

### 更新程序

```bash
pnpm updater      # 运行更新程序
```

## 插件系统

项目支持插件系统，通过 `src/utils/invoke_plugin.js` 实现插件调用机制，允许扩展各种翻译、识别和收藏功能。

## 国际化支持

项目支持多种语言，翻译文件位于 `src/i18n/locales/` 目录下，包括：

-   中文（简体/繁体）
-   英语
-   日语
-   韩语
-   法语
-   德语
-   俄语
-   等多种语言

## 跨平台支持

-   **Windows**: 完整功能支持
-   **macOS**: 完整功能支持，包含 ARM64 和 x64 架构
-   **Linux**: 支持主流发行版，包含 Wayland 支持

---

_本文档会随着项目发展持续更新，如有疑问请参考项目 README 或提交 Issue。_
