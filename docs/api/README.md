# API 文档

这个目录包含了 Pot 项目的各种 API 文档。

## 📚 API 分类

### 插件系统

-   [插件 API](plugin-api.md) - 插件开发完整 API 参考
-   [插件开发指南](plugin-development.md) - 如何开发 Pot 插件

### 外部集成

-   [外部调用 API](external-api.md) - 外部程序如何调用 Pot 功能
-   [HTTP API](http-api.md) - HTTP 接口文档
-   [命令行接口](cli-api.md) - 命令行调用接口

### 内部 API

-   [配置 API](config-api.md) - 配置系统 API
-   [存储 API](storage-api.md) - 数据存储相关 API
-   [窗口管理 API](window-api.md) - 窗口控制 API

## 🔧 API 使用场景

### 插件开发者

如果您想开发 Pot 插件，请查看：

1. [插件 API](plugin-api.md) - 了解可用的 API
2. [插件开发指南](plugin-development.md) - 学习开发流程
3. 参考现有插件的实现

### 应用集成

如果您想在其他应用中集成 Pot 功能：

1. [外部调用 API](external-api.md) - 了解调用方式
2. [HTTP API](http-api.md) - 使用 HTTP 接口
3. [命令行接口](cli-api.md) - 使用命令行调用

### 内部开发

如果您参与 Pot 核心开发：

1. [配置 API](config-api.md) - 配置系统使用
2. [存储 API](storage-api.md) - 数据存储操作
3. [窗口管理 API](window-api.md) - 窗口控制功能

## 📖 API 设计原则

-   **一致性**: 所有 API 遵循统一的命名和调用规范
-   **类型安全**: 使用 TypeScript 和 Rust 的类型系统确保安全性
-   **异步优先**: 大部分 API 都是异步的，避免阻塞用户界面
-   **错误处理**: 提供详细的错误信息和处理机制
-   **向后兼容**: 保持 API 的向后兼容性

## 🚀 快速开始

### 插件开发

```javascript
// 基本插件结构
export default {
    name: 'my-plugin',
    version: '1.0.0',
    translate: async (text, from, to) => {
        // 翻译逻辑
        return result;
    },
};
```

### 外部调用

```bash
# 命令行调用
pot translate "Hello World" --from=en --to=zh

# HTTP 调用
curl -X POST http://localhost:60828/translate \
  -H "Content-Type: application/json" \
  -d '{"text":"Hello World","from":"en","to":"zh"}'
```

## 📝 版本兼容性

| API 版本 | Pot 版本 | 状态 | 说明           |
| -------- | -------- | ---- | -------------- |
| v3.x     | 3.0.0+   | 当前 | 最新稳定版本   |
| v2.x     | 2.0.0+   | 维护 | 仅修复严重 Bug |
| v1.x     | 1.0.0+   | 废弃 | 不再支持       |

## 🤝 贡献 API 文档

如果您发现 API 文档有问题或需要补充：

1. 在 GitHub 提交 Issue
2. 直接提交 Pull Request
3. 联系维护团队

---

_API 文档会随着项目发展持续更新，请关注版本变化。_
