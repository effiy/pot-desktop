# 开发常见问题

本文档收集了开发 Pot 项目时遇到的常见问题及解决方案。

## 🚀 快速诊断

### 问题分类

| 问题类型        | 常见症状                 | 快速跳转                        |
| --------------- | ------------------------ | ------------------------------- |
| 🛠️ **环境搭建** | 依赖安装失败、工具链问题 | [环境搭建问题](#️-环境搭建问题) |
| 📦 **依赖管理** | 包安装失败、版本冲突     | [依赖管理问题](#-依赖管理问题)  |
| 🔧 **构建问题** | 编译失败、构建错误       | [构建问题](#-构建问题)          |
| 🐛 **调试问题** | 调试工具、日志查看       | [调试问题](#-调试问题)          |
| 🔄 **开发流程** | Git、代码规范、测试      | [开发流程问题](#-开发流程问题)  |
| 🚀 **部署问题** | 打包、发布、CI/CD        | [部署问题](#-部署问题)          |

## 🛠️ 环境搭建问题

### ❌ Node.js 版本问题

**症状**: 提示 Node.js 版本不兼容

**解决方案**:

1. **检查版本要求**:

    ```bash
    node --version  # 需要 >= 18.0.0
    npm --version
    ```

2. **使用版本管理器**:

    ```bash
    # 使用 nvm
    nvm install 18
    nvm use 18

    # 使用 fnm
    fnm install 18
    fnm use 18
    ```

3. **更新到最新 LTS**:
    ```bash
    # 安装最新 LTS 版本
    nvm install --lts
    nvm use --lts
    ```

### ❌ Rust 工具链问题

**症状**: Rust 编译失败或工具链缺失

**解决方案**:

1. **安装/更新 Rust**:

    ```bash
    # 安装 Rust
    curl --proto '=https' --tlsv1.2 -sSf https://sh.rustup.rs | sh

    # 更新工具链
    rustup update

    # 检查版本
    rustc --version  # 需要 >= 1.70.0
    ```

2. **添加必要组件**:

    ```bash
    # 添加目标平台
    rustup target add x86_64-pc-windows-msvc  # Windows
    rustup target add x86_64-apple-darwin     # macOS Intel
    rustup target add aarch64-apple-darwin    # macOS Apple Silicon

    # 添加工具
    rustup component add clippy rustfmt
    ```

3. **配置环境变量**:
    ```bash
    # 添加到 .bashrc 或 .zshrc
    export PATH="$HOME/.cargo/bin:$PATH"
    source ~/.bashrc
    ```

### ❌ Tauri CLI 安装失败

**症状**: 无法安装或运行 Tauri CLI

**解决方案**:

1. **重新安装 Tauri CLI**:

    ```bash
    # 卸载旧版本
    cargo uninstall tauri-cli

    # 安装最新版本
    cargo install tauri-cli

    # 验证安装
    cargo tauri --version
    ```

2. **解决权限问题**:

    ```bash
    # Linux/macOS: 确保 cargo bin 目录在 PATH 中
    echo 'export PATH="$HOME/.cargo/bin:$PATH"' >> ~/.bashrc
    source ~/.bashrc
    ```

3. **使用预编译二进制**:
    ```bash
    # 下载预编译版本
    wget https://github.com/tauri-apps/tauri/releases/latest/download/tauri-cli-x86_64-unknown-linux-gnu.tar.gz
    tar -xzf tauri-cli-x86_64-unknown-linux-gnu.tar.gz
    sudo mv tauri /usr/local/bin/
    ```

### ❌ 系统依赖缺失

**症状**: 编译时提示缺少系统库

**解决方案**:

**Ubuntu/Debian**:

```bash
sudo apt update
sudo apt install -y \
    build-essential \
    curl \
    wget \
    libssl-dev \
    libgtk-3-dev \
    libwebkit2gtk-4.0-dev \
    libappindicator3-dev \
    librsvg2-dev \
    pkg-config
```

**Fedora/RHEL**:

```bash
sudo dnf install -y \
    gcc-c++ \
    openssl-devel \
    gtk3-devel \
    webkit2gtk3-devel \
    libappindicator-gtk3-devel \
    librsvg2-devel \
    pkg-config
```

**Arch Linux**:

```bash
sudo pacman -S --needed \
    base-devel \
    openssl \
    gtk3 \
    webkit2gtk \
    libappindicator-gtk3 \
    librsvg
```

**macOS**:

```bash
# 安装 Xcode Command Line Tools
xcode-select --install

# 或安装完整 Xcode
# 从 App Store 安装 Xcode
```

**Windows**:

-   安装 Visual Studio 2019 或更高版本
-   或安装 Visual Studio Build Tools
-   安装 Windows 10 SDK

## 📦 依赖管理问题

### ❌ pnpm 安装失败

**症状**: `pnpm install` 命令失败

**解决方案**:

1. **清理缓存**:

    ```bash
    # 清理 pnpm 缓存
    pnpm store prune

    # 删除 node_modules 和锁定文件
    rm -rf node_modules pnpm-lock.yaml

    # 重新安装
    pnpm install
    ```

2. **网络问题**:

    ```bash
    # 使用国内镜像
    pnpm config set registry https://registry.npmmirror.com/

    # 或使用代理
    pnpm config set proxy http://proxy.company.com:8080
    pnpm config set https-proxy http://proxy.company.com:8080
    ```

3. **权限问题**:

    ```bash
    # Linux/macOS: 修复权限
    sudo chown -R $(whoami) ~/.pnpm-store

    # Windows: 以管理员身份运行终端
    ```

### ❌ Cargo 依赖编译失败

**症状**: Rust 依赖编译出错

**解决方案**:

1. **清理构建缓存**:

    ```bash
    cd src-tauri
    cargo clean
    cargo build
    ```

2. **更新依赖**:

    ```bash
    # 更新 Cargo.lock
    cargo update

    # 检查依赖冲突
    cargo tree
    ```

3. **网络问题**:

    ```bash
    # 使用国内镜像
    # 在 ~/.cargo/config.toml 中添加：
    [source.crates-io]
    replace-with = 'ustc'

    [source.ustc]
    registry = "https://mirrors.ustc.edu.cn/crates.io-index"
    ```

### ❌ 版本冲突

**症状**: 依赖版本不兼容

**解决方案**:

1. **检查版本约束**:

    ```bash
    # 查看依赖树
    pnpm list
    cargo tree

    # 检查冲突
    pnpm audit
    ```

2. **手动解决冲突**:

    ```json
    // package.json 中添加 resolutions
    {
        "pnpm": {
            "overrides": {
                "problematic-package": "^1.0.0"
            }
        }
    }
    ```

3. **降级或升级**:

    ```bash
    # 降级到兼容版本
    pnpm add package-name@1.0.0

    # 升级到最新版本
    pnpm update package-name
    ```

## 🔧 构建问题

### ❌ Vite 构建失败

**症状**: 前端构建过程中出错

**解决方案**:

1. **内存不足**:

    ```bash
    # 增加 Node.js 内存限制
    export NODE_OPTIONS="--max-old-space-size=4096"
    pnpm build
    ```

2. **路径问题**:

    ```javascript
    // vite.config.js 检查路径配置
    export default defineConfig({
        base: './', // 确保使用相对路径
        build: {
            outDir: 'dist',
        },
    });
    ```

3. **依赖问题**:

    ```bash
    # 检查是否有缺失的依赖
    pnpm list --depth=0

    # 重新安装依赖
    rm -rf node_modules pnpm-lock.yaml
    pnpm install
    ```

### ❌ Tauri 构建失败

**症状**: `pnpm tauri build` 命令失败

**解决方案**:

1. **检查 Tauri 配置**:

    ```json
    // src-tauri/tauri.conf.json
    {
        "build": {
            "beforeBuildCommand": "pnpm build",
            "beforeDevCommand": "pnpm dev",
            "devPath": "http://localhost:1420",
            "distDir": "../dist"
        }
    }
    ```

2. **平台特定问题**:

    ```bash
    # Windows: 确保有签名证书（可选）
    # macOS: 检查开发者证书
    # Linux: 确保有必要的系统库
    ```

3. **调试构建过程**:

    ```bash
    # 启用详细日志
    RUST_LOG=debug pnpm tauri build

    # 分步构建
    pnpm build                    # 构建前端
    cd src-tauri && cargo build   # 构建后端
    pnpm tauri build             # 最终构建
    ```

### ❌ 跨平台构建问题

**症状**: 在不同平台上构建失败

**解决方案**:

1. **路径分隔符**:

    ```javascript
    // 使用 path 模块处理路径
    import path from 'path';
    const filePath = path.join(__dirname, 'file.txt');
    ```

2. **平台特定依赖**:

    ```toml
    # Cargo.toml 中使用条件编译
    [target.'cfg(target_os = "windows")'.dependencies]
    windows = { version = "0.48", features = ["Win32_UI_WindowsAndMessaging"] }

    [target.'cfg(target_os = "macos")'.dependencies]
    cocoa = "0.24"
    ```

3. **环境变量**:
    ```bash
    # 使用跨平台的环境变量设置
    # .env 文件
    VITE_API_URL=http://localhost:3000
    ```

## 🐛 调试问题

### ❌ 前端调试

**症状**: 无法调试 React 组件

**解决方案**:

1. **启用开发者工具**:

    ```javascript
    // src-tauri/tauri.conf.json
    {
      "build": {
        "devPath": "http://localhost:1420",
        "beforeDevCommand": "pnpm dev"
      },
      "tauri": {
        "allowlist": {
          "all": false,
          "shell": {
            "all": false,
            "open": true
          }
        },
        "windows": [{
          "fullscreen": false,
          "height": 600,
          "resizable": true,
          "title": "pot",
          "width": 800,
          "center": true,
          "decorations": true
        }]
      }
    }
    ```

2. **使用 React DevTools**:

    ```bash
    # 安装浏览器扩展
    # Chrome: React Developer Tools
    # Firefox: React Developer Tools
    ```

3. **添加调试日志**:

    ```javascript
    // 使用 console.log 进行调试
    console.log('Debug info:', data);

    // 使用 React 错误边界
    class ErrorBoundary extends React.Component {
        constructor(props) {
            super(props);
            this.state = { hasError: false };
        }

        static getDerivedStateFromError(error) {
            return { hasError: true };
        }

        componentDidCatch(error, errorInfo) {
            console.error('Error caught:', error, errorInfo);
        }

        render() {
            if (this.state.hasError) {
                return <h1>Something went wrong.</h1>;
            }
            return this.props.children;
        }
    }
    ```

### ❌ Rust 后端调试

**症状**: 无法调试 Rust 代码

**解决方案**:

1. **启用调试日志**:

    ```rust
    use log::{debug, info, warn, error};

    fn main() {
        env_logger::init();

        info!("Application starting");
        debug!("Debug information: {:?}", data);
    }
    ```

2. **使用调试器**:

    ```bash
    # 使用 GDB (Linux)
    gdb target/debug/pot

    # 使用 LLDB (macOS)
    lldb target/debug/pot

    # VS Code 调试配置
    # .vscode/launch.json
    {
      "version": "0.2.0",
      "configurations": [
        {
          "type": "lldb",
          "request": "launch",
          "name": "Debug Rust",
          "program": "${workspaceFolder}/src-tauri/target/debug/pot",
          "args": [],
          "cwd": "${workspaceFolder}"
        }
      ]
    }
    ```

3. **单元测试调试**:

    ```rust
    #[cfg(test)]
    mod tests {
        use super::*;

        #[test]
        fn test_function() {
            let result = my_function();
            assert_eq!(result, expected_value);
        }
    }
    ```

### ❌ Tauri IPC 调试

**症状**: 前后端通信问题

**解决方案**:

1. **启用 IPC 日志**:

    ```bash
    # 设置环境变量
    export RUST_LOG=tauri=debug
    pnpm tauri dev
    ```

2. **检查命令定义**:

    ```rust
    // src-tauri/src/main.rs
    #[tauri::command]
    fn my_command(param: String) -> Result<String, String> {
        println!("Received parameter: {}", param);
        Ok(format!("Processed: {}", param))
    }

    fn main() {
        tauri::Builder::default()
            .invoke_handler(tauri::generate_handler![my_command])
            .run(tauri::generate_context!())
            .expect("error while running tauri application");
    }
    ```

3. **前端调用测试**:

    ```javascript
    import { invoke } from '@tauri-apps/api/tauri';

    async function testCommand() {
        try {
            const result = await invoke('my_command', { param: 'test' });
            console.log('Result:', result);
        } catch (error) {
            console.error('Error:', error);
        }
    }
    ```

## 🔄 开发流程问题

### ❌ Git 工作流问题

**症状**: Git 操作失败或冲突

**解决方案**:

1. **配置 Git**:

    ```bash
    # 设置用户信息
    git config --global user.name "Your Name"
    git config --global user.email "your.email@example.com"

    # 设置默认编辑器
    git config --global core.editor "code --wait"

    # 设置换行符处理
    git config --global core.autocrlf input  # Linux/macOS
    git config --global core.autocrlf true   # Windows
    ```

2. **解决合并冲突**:

    ```bash
    # 查看冲突文件
    git status

    # 手动解决冲突后
    git add .
    git commit -m "Resolve merge conflicts"

    # 或使用合并工具
    git mergetool
    ```

3. **分支管理**:

    ```bash
    # 创建功能分支
    git checkout -b feature/new-feature

    # 推送分支
    git push -u origin feature/new-feature

    # 合并到主分支
    git checkout main
    git merge feature/new-feature
    ```

### ❌ 代码格式化问题

**症状**: 代码格式不一致

**解决方案**:

1. **配置 Prettier**:

    ```json
    // .prettierrc
    {
        "semi": true,
        "trailingComma": "es5",
        "singleQuote": true,
        "printWidth": 80,
        "tabWidth": 2
    }
    ```

2. **配置 ESLint**:

    ```json
    // .eslintrc.json
    {
        "extends": ["eslint:recommended", "@typescript-eslint/recommended", "prettier"],
        "rules": {
            "no-unused-vars": "warn",
            "no-console": "warn"
        }
    }
    ```

3. **自动格式化**:

    ```bash
    # 格式化所有文件
    pnpm format

    # 检查代码质量
    pnpm lint

    # 自动修复
    pnpm lint:fix
    ```

### ❌ 测试问题

**症状**: 测试运行失败

**解决方案**:

1. **配置测试环境**:

    ```javascript
    // jest.config.js
    module.exports = {
        testEnvironment: 'jsdom',
        setupFilesAfterEnv: ['<rootDir>/src/setupTests.js'],
        moduleNameMapping: {
            '\\.(css|less|scss|sass)$': 'identity-obj-proxy',
        },
    };
    ```

2. **编写单元测试**:

    ```javascript
    // src/utils/__tests__/helper.test.js
    import { formatText } from '../helper';

    describe('formatText', () => {
        test('should format text correctly', () => {
            const result = formatText('hello world');
            expect(result).toBe('Hello World');
        });
    });
    ```

3. **运行测试**:

    ```bash
    # 运行所有测试
    pnpm test

    # 运行特定测试
    pnpm test helper.test.js

    # 监听模式
    pnpm test --watch
    ```

## 🚀 部署问题

### ❌ 构建产物问题

**症状**: 构建的应用无法正常运行

**解决方案**:

1. **检查构建配置**:

    ```json
    // package.json
    {
        "scripts": {
            "build": "vite build",
            "tauri:build": "tauri build"
        }
    }
    ```

2. **验证构建产物**:

    ```bash
    # 检查构建输出
    ls -la dist/
    ls -la src-tauri/target/release/bundle/

    # 测试构建版本
    pnpm preview
    ```

3. **处理路径问题**:
    ```javascript
    // vite.config.js
    export default defineConfig({
        base: './', // 使用相对路径
        build: {
            assetsDir: 'assets',
            rollupOptions: {
                output: {
                    manualChunks: {
                        vendor: ['react', 'react-dom'],
                    },
                },
            },
        },
    });
    ```

### ❌ 签名和公证问题

**症状**: macOS 应用无法通过安全检查

**解决方案**:

1. **配置开发者证书**:

    ```bash
    # 查看可用证书
    security find-identity -v -p codesigning

    # 配置 Tauri
    # src-tauri/tauri.conf.json
    {
      "tauri": {
        "bundle": {
          "macOS": {
            "signingIdentity": "Developer ID Application: Your Name"
          }
        }
      }
    }
    ```

2. **公证配置**:

    ```bash
    # 设置公证凭据
    export APPLE_ID="your-apple-id@example.com"
    export APPLE_PASSWORD="app-specific-password"
    export APPLE_TEAM_ID="YOUR_TEAM_ID"
    ```

3. **自动化签名**:
    ```bash
    # 在 CI/CD 中配置
    - name: Setup signing
      run: |
        echo "$MACOS_CERTIFICATE" | base64 --decode > certificate.p12
        security create-keychain -p "$KEYCHAIN_PASSWORD" build.keychain
        security import certificate.p12 -k build.keychain -P "$P12_PASSWORD"
    ```

### ❌ CI/CD 问题

**症状**: 自动化构建失败

**解决方案**:

1. **GitHub Actions 配置**:

    ```yaml
    # .github/workflows/build.yml
    name: Build
    on: [push, pull_request]

    jobs:
        build:
            strategy:
                matrix:
                    os: [ubuntu-latest, windows-latest, macos-latest]
            runs-on: ${{ matrix.os }}

            steps:
                - uses: actions/checkout@v3
                - uses: actions/setup-node@v3
                  with:
                      node-version: 18
                      cache: 'pnpm'

                - name: Install dependencies
                  run: pnpm install

                - name: Build
                  run: pnpm tauri build
    ```

2. **缓存优化**:

    ```yaml
    - name: Cache Rust dependencies
      uses: actions/cache@v3
      with:
          path: |
              ~/.cargo/registry
              ~/.cargo/git
              src-tauri/target
          key: ${{ runner.os }}-cargo-${{ hashFiles('**/Cargo.lock') }}
    ```

3. **环境变量管理**:
    ```yaml
    env:
        RUST_LOG: debug
        NODE_OPTIONS: --max-old-space-size=4096
    ```

## 🔍 性能优化

### ❌ 应用启动慢

**症状**: 应用启动时间过长

**解决方案**:

1. **优化打包体积**:

    ```javascript
    // vite.config.js
    export default defineConfig({
        build: {
            rollupOptions: {
                output: {
                    manualChunks: {
                        vendor: ['react', 'react-dom'],
                        utils: ['lodash', 'moment'],
                    },
                },
            },
        },
    });
    ```

2. **懒加载组件**:

    ```javascript
    import { lazy, Suspense } from 'react';

    const LazyComponent = lazy(() => import('./LazyComponent'));

    function App() {
        return (
            <Suspense fallback={<div>Loading...</div>}>
                <LazyComponent />
            </Suspense>
        );
    }
    ```

3. **优化 Rust 编译**:
    ```toml
    # Cargo.toml
    [profile.release]
    opt-level = 3
    lto = true
    codegen-units = 1
    panic = "abort"
    ```

### ❌ 内存使用过高

**症状**: 应用占用内存过多

**解决方案**:

1. **检查内存泄漏**:

    ```javascript
    // 使用 React DevTools Profiler
    // 监控组件渲染和内存使用

    useEffect(() => {
        const timer = setInterval(() => {
            // 清理逻辑
        }, 1000);

        return () => clearInterval(timer); // 清理定时器
    }, []);
    ```

2. **优化图片资源**:

    ```javascript
    // 使用适当的图片格式和尺寸
    // 实现图片懒加载
    const LazyImage = ({ src, alt }) => {
        const [loaded, setLoaded] = useState(false);

        return (
            <img
                src={loaded ? src : placeholder}
                alt={alt}
                onLoad={() => setLoaded(true)}
            />
        );
    };
    ```

3. **Rust 内存管理**:

    ```rust
    // 避免不必要的内存分配
    use std::sync::Arc;

    // 使用 Arc 共享数据
    let shared_data = Arc::new(expensive_data);

    // 及时释放资源
    drop(large_object);
    ```

## 📚 参考资源

### 官方文档

-   [Tauri 文档](https://tauri.app/v1/guides/)
-   [React 文档](https://react.dev/)
-   [Vite 文档](https://vitejs.dev/)
-   [Rust 文档](https://doc.rust-lang.org/)

### 社区资源

-   [Tauri Discord](https://discord.com/invite/SpmNs4S)
-   [GitHub Discussions](https://github.com/pot-app/pot-desktop/discussions)
-   [Stack Overflow](https://stackoverflow.com/questions/tagged/tauri)

### 调试工具

-   [React DevTools](https://chrome.google.com/webstore/detail/react-developer-tools/)
-   [Rust Analyzer](https://rust-analyzer.github.io/)
-   [Tauri DevTools](https://github.com/tauri-apps/tauri-docs/blob/dev/docs/guides/debugging/vs-code.md)

---

_本文档持续更新中，如果您遇到其他开发问题，欢迎通过 GitHub Issues 反馈。_
