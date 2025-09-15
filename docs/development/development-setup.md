# 开发环境搭建

本指南将帮助您搭建 Pot 项目的开发环境。

## 📋 环境要求

### 基础要求

-   **Node.js**: 18.0.0 或更高版本
-   **Rust**: 1.70.0 或更高版本
-   **pnpm**: 8.0.0 或更高版本

### 系统特定要求

#### Windows

-   Visual Studio 2019 或更高版本 (或 Build Tools)
-   Windows 10 SDK

#### macOS

-   Xcode Command Line Tools
-   macOS 10.15 或更高版本

#### Linux

```bash
# Ubuntu/Debian
sudo apt update
sudo apt install -y git curl build-essential libssl-dev pkg-config libgtk-3-dev libwebkit2gtk-4.0-dev libappindicator3-dev librsvg2-dev

# Fedora
sudo dnf install -y git curl gcc-c++ openssl-devel pkg-config gtk3-devel webkit2gtk3-devel libappindicator-gtk3-devel librsvg2-devel

# Arch Linux
sudo pacman -S --needed git curl base-devel openssl pkg-config gtk3 webkit2gtk libappindicator-gtk3 librsvg
```

## 🛠️ 安装开发工具

### 1. 安装 Rust

```bash
# 使用官方安装脚本
curl --proto '=https' --tlsv1.2 -sSf https://sh.rustup.rs | sh

# 重新加载环境变量
source ~/.cargo/env

# 验证安装
rustc --version
cargo --version
```

### 2. 安装 Node.js

推荐使用 Node.js 版本管理器：

```bash
# 使用 nvm (Linux/macOS)
curl -o- https://raw.githubusercontent.com/nvm-sh/nvm/v0.39.0/install.sh | bash
nvm install 18
nvm use 18

# 或直接从官网下载安装
# https://nodejs.org/
```

### 3. 安装 pnpm

```bash
# 使用 npm 安装
npm install -g pnpm

# 或使用官方安装脚本
curl -fsSL https://get.pnpm.io/install.sh | sh -

# 验证安装
pnpm --version
```

### 4. 安装 Tauri CLI

```bash
# 使用 cargo 安装
cargo install tauri-cli

# 验证安装
cargo tauri --version
```

## 📥 克隆项目

```bash
# 克隆仓库
git clone https://github.com/pot-app/pot-desktop.git
cd pot-desktop

# 安装依赖
pnpm install
```

## ⚙️ 配置开发环境

### 1. 环境变量设置

创建 `.env` 文件（可选）：

```bash
# 开发模式配置
TAURI_DEBUG=true
RUST_LOG=debug
```

### 2. IDE 配置

#### VS Code (推荐)

安装推荐的扩展：

```json
{
    "recommendations": [
        "rust-lang.rust-analyzer",
        "tauri-apps.tauri-vscode",
        "bradlc.vscode-tailwindcss",
        "esbenp.prettier-vscode"
    ]
}
```

#### 其他 IDE

-   **WebStorm**: 安装 Rust 插件
-   **Vim/Neovim**: 配置 rust-analyzer LSP

## 🚀 启动开发环境

### 开发模式启动

```bash
# 启动开发服务器
pnpm tauri dev
```

这个命令会：

1. 启动 Vite 开发服务器 (前端)
2. 编译 Rust 代码 (后端)
3. 启动 Tauri 应用程序
4. 启用热重载功能

### 分别启动前后端

```bash
# 终端 1: 启动前端开发服务器
pnpm dev

# 终端 2: 启动 Tauri 开发环境
pnpm tauri dev --no-watch
```

## 🔨 构建项目

### 开发构建

```bash
# 构建前端
pnpm build

# 构建 Tauri 应用 (debug)
pnpm tauri build --debug
```

### 生产构建

```bash
# 完整构建
pnpm tauri build
```

构建产物位置：

-   **Windows**: `src-tauri/target/release/bundle/msi/`
-   **macOS**: `src-tauri/target/release/bundle/dmg/`
-   **Linux**: `src-tauri/target/release/bundle/deb/` 和 `src-tauri/target/release/bundle/appimage/`

## 🧪 运行测试

```bash
# 前端测试
pnpm test

# Rust 测试
cd src-tauri
cargo test
```

## 🔧 常用开发命令

```bash
# 安装依赖
pnpm install

# 启动开发环境
pnpm tauri dev

# 构建前端
pnpm build

# 构建应用
pnpm tauri build

# 代码格式化
pnpm format

# 代码检查
pnpm lint

# 更新依赖
pnpm update

# 清理构建缓存
pnpm clean
```

## 🐛 调试技巧

### 前端调试

1. 在开发模式下打开开发者工具 (Ctrl+Shift+I)
2. 使用 React DevTools 浏览器扩展
3. 查看控制台日志和网络请求

### 后端调试

```bash
# 启用 Rust 日志
RUST_LOG=debug pnpm tauri dev

# 使用 println! 宏输出调试信息
println!("Debug: {:?}", variable);
```

### Tauri 调试

```bash
# 启用 Tauri 调试日志
TAURI_DEBUG=true pnpm tauri dev
```

## 📁 项目结构概览

```
pot-desktop/
├── src/                    # React 前端代码
├── src-tauri/             # Rust 后端代码
├── public/                # 静态资源
├── docs/                  # 文档
├── package.json           # Node.js 配置
├── vite.config.js         # Vite 配置
├── tailwind.config.cjs    # TailwindCSS 配置
└── pnpm-lock.yaml        # 依赖锁定文件
```

详细的项目结构说明请参考 [项目结构文档](project-structure.md)。

## 🛠️ 故障排除

### 常见问题

**依赖安装失败**

```bash
# 清理缓存重新安装
pnpm store prune
rm -rf node_modules pnpm-lock.yaml
pnpm install
```

**Rust 编译错误**

```bash
# 更新 Rust 工具链
rustup update

# 清理 Rust 构建缓存
cd src-tauri
cargo clean
```

**Tauri 启动失败**

```bash
# 检查 Tauri CLI 版本
cargo tauri --version

# 重新安装 Tauri CLI
cargo install tauri-cli --force
```

### 平台特定问题

**Windows:**

-   确保安装了 Visual Studio Build Tools
-   检查 Windows SDK 版本

**macOS:**

-   确保安装了 Xcode Command Line Tools: `xcode-select --install`
-   检查 macOS 版本兼容性

**Linux:**

-   确保安装了所有必需的系统依赖
-   Wayland 用户可能需要额外配置

## 🤝 贡献代码

开发环境搭建完成后，您可以：

1. 阅读 [贡献指南](contributing.md)
2. 查看 [项目结构](project-structure.md)
3. 了解 [代码规范](coding-standards.md)
4. 开始您的第一个 PR！

## 📚 相关资源

-   [Tauri 官方文档](https://tauri.app/v1/guides/)
-   [Rust 官方文档](https://doc.rust-lang.org/)
-   [React 官方文档](https://react.dev/)
-   [Vite 官方文档](https://vitejs.dev/)
-   [TailwindCSS 文档](https://tailwindcss.com/)

---

_如果在搭建过程中遇到问题，请查看 [故障排除文档](../user-guides/troubleshooting.md) 或在 GitHub 提交 Issue。_
