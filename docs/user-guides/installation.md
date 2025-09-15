# 安装指南

本指南将帮助您在不同操作系统上安装 Pot 翻译软件。

## 📋 系统要求

### Windows

-   Windows 10 或更高版本
-   x64 架构

### macOS

-   macOS 10.15 (Catalina) 或更高版本
-   支持 Intel 和 Apple Silicon (M1/M2) 芯片

### Linux

-   主流 Linux 发行版 (Ubuntu, Fedora, Arch Linux 等)
-   x64 架构
-   支持 X11 和 Wayland 显示协议

## 💾 下载安装

### 方式一：GitHub Releases (推荐)

1. 访问 [GitHub Releases 页面](https://github.com/pot-app/pot-desktop/releases)
2. 选择最新版本
3. 根据您的系统下载对应的安装包：

| 操作系统              | 文件名                 | 说明               |
| --------------------- | ---------------------- | ------------------ |
| Windows               | `pot_*_x64_en-US.msi`  | Windows 安装包     |
| macOS (Intel)         | `pot_*_x64.dmg`        | Intel 芯片 Mac     |
| macOS (Apple Silicon) | `pot_*_aarch64.dmg`    | M1/M2 芯片 Mac     |
| Linux (deb)           | `pot_*_amd64.deb`      | Debian/Ubuntu 系列 |
| Linux (rpm)           | `pot-*.x86_64.rpm`     | RedHat/Fedora 系列 |
| Linux (AppImage)      | `pot_*_amd64.AppImage` | 通用 Linux 格式    |

### 方式二：包管理器安装

#### Windows (Chocolatey)

```powershell
# 即将支持
```

#### macOS (Homebrew)

```bash
# 即将支持
```

#### Linux (各发行版)

**Ubuntu/Debian:**

```bash
# 下载 deb 包后安装
sudo dpkg -i pot_*_amd64.deb
sudo apt-get install -f  # 解决依赖问题
```

**Fedora/RHEL:**

```bash
# 下载 rpm 包后安装
sudo rpm -i pot-*.x86_64.rpm
```

**Arch Linux:**

```bash
# 使用 AUR
yay -S pot-desktop
# 或
paru -S pot-desktop
```

## 🔧 详细安装步骤

### Windows 安装

1. 下载 `pot_*_x64_en-US.msi` 文件
2. 双击运行安装程序
3. 按照安装向导完成安装
4. 安装完成后，从开始菜单启动 Pot

**注意**: 首次运行时 Windows Defender 可能会提示安全警告，这是正常现象。

### macOS 安装

1. 下载对应架构的 `.dmg` 文件
2. 双击打开 dmg 文件
3. 将 Pot 拖拽到 Applications 文件夹
4. 从启动台或 Applications 文件夹启动 Pot

**重要**: 由于 macOS 的安全机制，首次运行时需要：

1. 右键点击 Pot 应用
2. 选择"打开"
3. 在弹出的对话框中点击"打开"

### Linux 安装

#### 使用 deb 包 (Ubuntu/Debian)

```bash
# 下载后安装
wget https://github.com/pot-app/pot-desktop/releases/download/v3.0.7/pot_3.0.7_amd64.deb
sudo dpkg -i pot_3.0.7_amd64.deb

# 如果有依赖问题
sudo apt-get install -f
```

#### 使用 AppImage (通用)

```bash
# 下载 AppImage
wget https://github.com/pot-app/pot-desktop/releases/download/v3.0.7/pot_3.0.7_amd64.AppImage

# 添加执行权限
chmod +x pot_3.0.7_amd64.AppImage

# 运行
./pot_3.0.7_amd64.AppImage
```

## ⚙️ 首次运行设置

安装完成后，首次运行 Pot 时需要进行基本配置：

1. **选择界面语言**: 支持中文、英文、日文、韩文等多种语言
2. **设置快捷键**: 配置划词翻译、截图OCR等功能的快捷键
3. **选择翻译引擎**: 配置您要使用的翻译服务
4. **权限设置**:
    - macOS: 需要授予辅助功能权限
    - Linux: 可能需要授予截图权限

## 🔍 验证安装

安装成功后，您可以通过以下方式验证：

1. **基本功能**: 选中一段文本，按下划词翻译快捷键
2. **截图OCR**: 使用截图OCR快捷键测试文字识别
3. **设置页面**: 打开设置页面检查各项配置

## 🛠️ 故障排除

### 常见问题

**Windows:**

-   如果提示"缺少 Visual C++ 运行库"，请安装最新的 [Microsoft Visual C++ Redistributable](https://docs.microsoft.com/en-us/cpp/windows/latest-supported-vc-redist)

**macOS:**

-   如果提示"无法打开，因为它来自身份不明的开发者"，请参考上面的安装步骤
-   如果辅助功能权限设置有问题，请到"系统偏好设置 > 安全性与隐私 > 隐私 > 辅助功能"中添加 Pot

**Linux:**

-   如果 AppImage 无法运行，请确保安装了 FUSE: `sudo apt install fuse`
-   Wayland 用户如果遇到截图问题，请参考 [Wayland 支持文档](../README.md#wayland-支持)

### 获取帮助

如果安装过程中遇到问题：

1. 查看 [故障排除文档](troubleshooting.md)
2. 在 [GitHub Issues](https://github.com/pot-app/pot-desktop/issues) 搜索相关问题
3. 加入 [QQ 频道](https://pd.qq.com/s/akns94e1r) 获取社区帮助

## 🔄 更新升级

Pot 支持自动检查更新，您也可以：

1. 在设置中启用自动更新
2. 手动检查更新 (帮助 > 检查更新)
3. 从 GitHub Releases 下载新版本手动安装

---

_安装完成后，建议阅读 [基本使用指南](basic-usage.md) 了解如何使用 Pot 的各项功能。_
