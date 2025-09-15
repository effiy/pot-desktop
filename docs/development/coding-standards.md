# 代码规范

本文档定义了 Pot 项目的代码规范和最佳实践，确保代码质量和团队协作的一致性。

## 📋 总体原则

### 核心理念

-   **可读性优先**: 代码应该易于理解和维护
-   **一致性**: 遵循统一的代码风格和命名约定
-   **简洁性**: 避免不必要的复杂性
-   **可测试性**: 编写易于测试的代码
-   **性能意识**: 考虑性能影响，但不过度优化

## 🎨 代码格式化

### 自动化工具

项目使用以下工具确保代码格式一致：

#### Prettier (JavaScript/TypeScript)

**配置文件**: `.prettierrc`

```json
{
    "semi": true,
    "trailingComma": "es5",
    "singleQuote": true,
    "printWidth": 100,
    "tabWidth": 4,
    "useTabs": false,
    "bracketSpacing": true,
    "arrowParens": "avoid",
    "endOfLine": "lf"
}
```

#### Rustfmt (Rust)

**配置文件**: `rustfmt.toml`

```toml
edition = "2021"
max_width = 100
hard_tabs = false
tab_spaces = 4
newline_style = "Unix"
use_small_heuristics = "Default"
reorder_imports = true
reorder_modules = true
remove_nested_parens = true
```

#### EditorConfig

**配置文件**: `.editorconfig`

```ini
root = true

[*]
charset = utf-8
end_of_line = lf
insert_final_newline = true
trim_trailing_whitespace = true
indent_style = space
indent_size = 4

[*.{json,yml,yaml}]
indent_size = 2

[*.md]
trim_trailing_whitespace = false
```

### 格式化命令

```bash
# 格式化所有代码
pnpm format

# 检查格式
pnpm format:check

# 格式化 Rust 代码
cargo fmt

# 检查 Rust 格式
cargo fmt --check
```

## 🔤 命名约定

### JavaScript/TypeScript

#### 变量和函数

```javascript
// ✅ 使用 camelCase
const userName = 'john_doe';
const apiEndpoint = 'https://api.example.com';

function getUserData(userId) {
    return fetchUserById(userId);
}

// 布尔值使用 is/has/can 前缀
const isLoggedIn = true;
const hasPermission = false;
const canEdit = checkEditPermission();
```

#### 常量

```javascript
// ✅ 使用 SCREAMING_SNAKE_CASE
const MAX_RETRY_ATTEMPTS = 3;
const API_BASE_URL = 'https://api.pot-app.com';
const DEFAULT_TIMEOUT = 5000;
```

#### 类和组件

```javascript
// ✅ 使用 PascalCase
class UserManager {
    constructor(apiClient) {
        this.apiClient = apiClient;
    }
}

// React 组件
function TranslationPanel({ text, onTranslate }) {
    return <div>{text}</div>;
}

// 或者
const TranslationPanel = ({ text, onTranslate }) => {
    return <div>{text}</div>;
};
```

#### 文件命名

```bash
# ✅ 组件文件使用 PascalCase
TranslationPanel.jsx
UserSettings.jsx

# ✅ 工具函数使用 camelCase
apiClient.js
stringUtils.js

# ✅ Hook 文件使用 camelCase，以 use 开头
useTranslation.jsx
useApiClient.jsx

# ✅ 类型定义文件
types.ts
apiTypes.ts
```

### Rust

#### 变量和函数

```rust
// ✅ 使用 snake_case
let user_name = "john_doe";
let api_endpoint = "https://api.example.com";

fn get_user_data(user_id: u32) -> Result<User, Error> {
    fetch_user_by_id(user_id)
}

// 布尔值使用描述性名称
let is_logged_in = true;
let has_permission = false;
let can_edit = check_edit_permission();
```

#### 常量

```rust
// ✅ 使用 SCREAMING_SNAKE_CASE
const MAX_RETRY_ATTEMPTS: u32 = 3;
const API_BASE_URL: &str = "https://api.pot-app.com";
const DEFAULT_TIMEOUT: u64 = 5000;
```

#### 结构体和枚举

```rust
// ✅ 使用 PascalCase
struct UserData {
    id: u32,
    name: String,
    email: String,
}

enum TranslationService {
    Google,
    Baidu,
    DeepL,
    OpenAI,
}
```

#### 模块和文件

```bash
# ✅ 模块文件使用 snake_case
user_manager.rs
api_client.rs
string_utils.rs

# ✅ 模块声明
mod user_manager;
mod api_client;
```

## 📂 项目结构规范

### 前端结构

```
src/
├── components/          # 可复用组件
│   ├── common/         # 通用组件
│   ├── forms/          # 表单组件
│   └── ui/            # UI 基础组件
├── hooks/              # 自定义 Hook
├── services/           # 服务模块
│   ├── api/           # API 相关
│   ├── storage/       # 存储相关
│   └── utils/         # 工具函数
├── types/              # TypeScript 类型定义
├── constants/          # 常量定义
├── styles/            # 样式文件
└── utils/             # 通用工具函数
```

### 后端结构

```
src-tauri/src/
├── commands/           # Tauri 命令
├── services/          # 业务服务
├── models/            # 数据模型
├── utils/             # 工具函数
├── config/            # 配置管理
├── error/             # 错误处理
└── lib.rs            # 库入口
```

## 🔧 代码质量

### ESLint 规则

**配置文件**: `.eslintrc.js`

```javascript
module.exports = {
    root: true,
    env: {
        browser: true,
        es2021: true,
        node: true,
    },
    extends: [
        'eslint:recommended',
        '@typescript-eslint/recommended',
        'plugin:react/recommended',
        'plugin:react-hooks/recommended',
        'prettier',
    ],
    parser: '@typescript-eslint/parser',
    parserOptions: {
        ecmaFeatures: {
            jsx: true,
        },
        ecmaVersion: 12,
        sourceType: 'module',
    },
    plugins: ['react', '@typescript-eslint', 'react-hooks'],
    rules: {
        // 错误级别
        'no-console': 'warn',
        'no-debugger': 'error',
        'no-unused-vars': 'error',

        // React 相关
        'react/prop-types': 'off',
        'react/react-in-jsx-scope': 'off',
        'react-hooks/rules-of-hooks': 'error',
        'react-hooks/exhaustive-deps': 'warn',

        // TypeScript 相关
        '@typescript-eslint/no-unused-vars': 'error',
        '@typescript-eslint/explicit-function-return-type': 'off',
        '@typescript-eslint/no-explicit-any': 'warn',

        // 代码风格
        'prefer-const': 'error',
        'no-var': 'error',
        eqeqeq: 'error',
        curly: 'error',
    },
    settings: {
        react: {
            version: 'detect',
        },
    },
};
```

### Clippy 规则

**配置文件**: `clippy.toml`

```toml
# Clippy 配置
avoid-breaking-exported-api = false
msrv = "1.70.0"

# 允许的 lint
allow = [
    "clippy::module_name_repetitions",
    "clippy::struct_excessive_bools"
]

# 拒绝的 lint
deny = [
    "clippy::unwrap_used",
    "clippy::expect_used",
    "clippy::panic",
    "clippy::unimplemented"
]
```

### 代码检查命令

```bash
# JavaScript/TypeScript 检查
pnpm lint
pnpm lint:fix

# Rust 代码检查
cargo clippy
cargo clippy -- -D warnings

# 类型检查
pnpm type-check
```

## 📝 注释规范

### JavaScript/TypeScript

```javascript
/**
 * 翻译文本的函数
 * @param {string} text - 要翻译的文本
 * @param {string} from - 源语言代码
 * @param {string} to - 目标语言代码
 * @returns {Promise<string>} 翻译结果
 * @throws {TranslationError} 当翻译失败时抛出错误
 */
async function translateText(text, from, to) {
    // 验证输入参数
    if (!text || !text.trim()) {
        throw new Error('Text cannot be empty');
    }

    // 调用翻译服务
    const result = await translationService.translate({
        text: text.trim(),
        from,
        to,
    });

    return result;
}

// 单行注释用于解释复杂逻辑
const processedText = text
    .replace(/\s+/g, ' ') // 合并多个空格
    .trim(); // 移除首尾空格
```

### Rust

````rust
/// 翻译文本的函数
///
/// # Arguments
///
/// * `text` - 要翻译的文本
/// * `from` - 源语言代码
/// * `to` - 目标语言代码
///
/// # Returns
///
/// 返回翻译结果的 Result
///
/// # Errors
///
/// 当翻译失败时返回 TranslationError
///
/// # Examples
///
/// ```
/// let result = translate_text("Hello", "en", "zh").await?;
/// assert_eq!(result, "你好");
/// ```
pub async fn translate_text(
    text: &str,
    from: &str,
    to: &str
) -> Result<String, TranslationError> {
    // 验证输入参数
    if text.trim().is_empty() {
        return Err(TranslationError::EmptyText);
    }

    // 调用翻译服务
    let result = translation_service
        .translate(text.trim(), from, to)
        .await?;

    Ok(result)
}
````

## 🧪 测试规范

### 测试文件结构

```
src/
├── components/
│   ├── Button.jsx
│   └── __tests__/
│       └── Button.test.jsx
├── utils/
│   ├── stringUtils.js
│   └── __tests__/
│       └── stringUtils.test.js
└── services/
    ├── apiClient.js
    └── __tests__/
        └── apiClient.test.js
```

### 测试命名

```javascript
// ✅ 描述性的测试名称
describe('TranslationService', () => {
    describe('translateText', () => {
        it('should translate text successfully', () => {
            // 测试实现
        });

        it('should throw error when text is empty', () => {
            // 测试实现
        });

        it('should handle network timeout gracefully', () => {
            // 测试实现
        });
    });
});
```

### 测试结构 (AAA 模式)

```javascript
it('should calculate total price correctly', () => {
    // Arrange - 准备测试数据
    const items = [
        { name: 'Item 1', price: 10.99 },
        { name: 'Item 2', price: 5.5 },
    ];
    const taxRate = 0.1;

    // Act - 执行被测试的功能
    const result = calculateTotal(items, taxRate);

    // Assert - 验证结果
    expect(result).toBe(18.14);
});
```

### Rust 测试

```rust
#[cfg(test)]
mod tests {
    use super::*;

    #[test]
    fn test_translate_text_success() {
        // Arrange
        let text = "Hello";
        let from = "en";
        let to = "zh";

        // Act
        let result = translate_text(text, from, to);

        // Assert
        assert!(result.is_ok());
        assert_eq!(result.unwrap(), "你好");
    }

    #[test]
    #[should_panic(expected = "EmptyText")]
    fn test_translate_text_empty_input() {
        translate_text("", "en", "zh").unwrap();
    }
}
```

## 🔒 错误处理

### JavaScript/TypeScript

```javascript
// ✅ 使用自定义错误类
class TranslationError extends Error {
    constructor(message, code, originalError) {
        super(message);
        this.name = 'TranslationError';
        this.code = code;
        this.originalError = originalError;
    }
}

// ✅ 统一的错误处理
async function translateText(text, from, to) {
    try {
        const result = await apiClient.translate(text, from, to);
        return result;
    } catch (error) {
        // 记录详细错误信息
        logger.error('Translation failed', {
            text: text.substring(0, 100), // 只记录前100个字符
            from,
            to,
            error: error.message,
        });

        // 抛出标准化错误
        throw new TranslationError('Failed to translate text', 'TRANSLATION_FAILED', error);
    }
}
```

### Rust

```rust
// ✅ 使用 thiserror 定义错误类型
use thiserror::Error;

#[derive(Error, Debug)]
pub enum TranslationError {
    #[error("Text cannot be empty")]
    EmptyText,

    #[error("Network request failed: {0}")]
    NetworkError(#[from] reqwest::Error),

    #[error("API returned error: {code} - {message}")]
    ApiError { code: u16, message: String },

    #[error("Unsupported language: {0}")]
    UnsupportedLanguage(String),
}

// ✅ 使用 Result 类型
pub async fn translate_text(
    text: &str,
    from: &str,
    to: &str
) -> Result<String, TranslationError> {
    if text.trim().is_empty() {
        return Err(TranslationError::EmptyText);
    }

    let result = api_client
        .translate(text, from, to)
        .await
        .map_err(TranslationError::NetworkError)?;

    Ok(result)
}
```

## 🎯 性能最佳实践

### React 性能优化

```javascript
// ✅ 使用 React.memo 优化组件
const TranslationResult = React.memo(({ result, loading }) => {
    if (loading) {
        return <LoadingSpinner />;
    }
    return <div>{result}</div>;
});

// ✅ 使用 useMemo 缓存计算结果
const ExpensiveComponent = ({ data }) => {
    const processedData = useMemo(() => {
        return data.map((item) => processItem(item));
    }, [data]);

    return <div>{processedData}</div>;
};

// ✅ 使用 useCallback 缓存函数
const SearchComponent = ({ onSearch }) => {
    const handleInputChange = useCallback(
        debounce((value) => {
            onSearch(value);
        }, 300),
        [onSearch]
    );

    return <input onChange={(e) => handleInputChange(e.target.value)} />;
};
```

### Rust 性能优化

```rust
// ✅ 使用引用避免不必要的克隆
pub fn process_text(text: &str) -> String {
    text.chars()
        .filter(|c| !c.is_whitespace())
        .collect()
}

// ✅ 使用 Cow 类型优化字符串处理
use std::borrow::Cow;

pub fn normalize_text(text: &str) -> Cow<str> {
    if text.chars().all(|c| !c.is_whitespace()) {
        Cow::Borrowed(text)
    } else {
        Cow::Owned(text.chars().filter(|c| !c.is_whitespace()).collect())
    }
}

// ✅ 使用 Arc 共享数据
use std::sync::Arc;

#[derive(Clone)]
pub struct SharedConfig {
    inner: Arc<ConfigInner>,
}
```

## 📚 文档规范

### README 文件

每个模块都应该有清晰的 README 文件：

```markdown
# 模块名称

简短描述模块的功能和用途。

## 安装

\`\`\`bash
pnpm install
\`\`\`

## 使用方法

\`\`\`javascript
import { functionName } from './module';

const result = functionName(params);
\`\`\`

## API 文档

### functionName(params)

描述函数的功能和用法。

**参数**:

-   `param1` (string): 参数描述
-   `param2` (number): 参数描述

**返回值**: 返回值描述

**示例**:
\`\`\`javascript
const result = functionName('example', 42);
\`\`\`
```

### 代码内文档

```javascript
/**
 * @fileoverview 翻译服务模块
 * 提供多种翻译引擎的统一接口
 * @author Pot Team
 * @version 1.0.0
 */

/**
 * 翻译服务配置接口
 * @interface TranslationConfig
 * @property {string} apiKey - API 密钥
 * @property {string} endpoint - API 端点
 * @property {number} timeout - 请求超时时间（毫秒）
 */

/**
 * 翻译服务类
 * @class TranslationService
 */
class TranslationService {
    /**
     * 构造函数
     * @param {TranslationConfig} config - 服务配置
     */
    constructor(config) {
        this.config = config;
    }
}
```

## 🔄 版本控制

### Git 提交规范

遵循 [Conventional Commits](https://www.conventionalcommits.org/) 规范：

```bash
# 格式
<type>[optional scope]: <description>

[optional body]

[optional footer(s)]
```

**类型 (type)**:

-   `feat`: 新功能
-   `fix`: 修复 bug
-   `docs`: 文档变更
-   `style`: 代码格式变更（不影响代码逻辑）
-   `refactor`: 重构（既不是新功能也不是修复 bug）
-   `perf`: 性能优化
-   `test`: 添加或修改测试
-   `chore`: 构建过程或辅助工具的变动

**示例**:

```bash
feat(translation): add Google Translate support

Add Google Translate API integration with error handling
and rate limiting.

Closes #123
```

### 分支命名

```bash
# 功能分支
feature/translation-service
feature/user-interface-redesign

# 修复分支
fix/memory-leak-issue
fix/translation-error-handling

# 发布分支
release/v1.2.0

# 热修复分支
hotfix/critical-security-patch
```

## 🛠️ 开发工具配置

### VS Code 设置

**`.vscode/settings.json`**:

```json
{
    "editor.formatOnSave": true,
    "editor.codeActionsOnSave": {
        "source.fixAll.eslint": true,
        "source.organizeImports": true
    },
    "files.associations": {
        "*.jsx": "javascriptreact",
        "*.tsx": "typescriptreact"
    },
    "emmet.includeLanguages": {
        "javascript": "javascriptreact"
    },
    "rust-analyzer.checkOnSave.command": "clippy",
    "rust-analyzer.cargo.features": "all"
}
```

### 推荐扩展

**`.vscode/extensions.json`**:

```json
{
    "recommendations": [
        "esbenp.prettier-vscode",
        "dbaeumer.vscode-eslint",
        "rust-lang.rust-analyzer",
        "tauri-apps.tauri-vscode",
        "bradlc.vscode-tailwindcss",
        "ms-vscode.vscode-typescript-next"
    ]
}
```

## ✅ 代码审查清单

### 提交前检查

-   [ ] 代码已格式化 (`pnpm format`)
-   [ ] 通过所有 lint 检查 (`pnpm lint`)
-   [ ] 通过所有测试 (`pnpm test`)
-   [ ] 类型检查通过 (`pnpm type-check`)
-   [ ] 构建成功 (`pnpm build`)
-   [ ] 添加了必要的测试
-   [ ] 更新了相关文档
-   [ ] 提交信息遵循规范

### 代码审查要点

-   [ ] 代码逻辑正确且高效
-   [ ] 错误处理完善
-   [ ] 命名清晰且一致
-   [ ] 注释充分且准确
-   [ ] 没有安全漏洞
-   [ ] 性能影响可接受
-   [ ] 向后兼容性
-   [ ] 测试覆盖率足够

## 🔧 自动化检查

### Pre-commit Hook

**`.husky/pre-commit`**:

```bash
#!/bin/sh
. "$(dirname "$0")/_/husky.sh"

# 运行格式化检查
pnpm format:check

# 运行 lint 检查
pnpm lint

# 运行类型检查
pnpm type-check

# 运行测试
pnpm test

# 运行 Rust 检查
cd src-tauri
cargo fmt -- --check
cargo clippy -- -D warnings
```

### CI/CD 检查

```yaml
# .github/workflows/code-quality.yml
name: Code Quality
on: [push, pull_request]

jobs:
    quality:
        runs-on: ubuntu-latest
        steps:
            - uses: actions/checkout@v3
            - uses: actions/setup-node@v3
              with:
                  node-version: 18
                  cache: 'pnpm'

            - name: Install dependencies
              run: pnpm install

            - name: Format check
              run: pnpm format:check

            - name: Lint check
              run: pnpm lint

            - name: Type check
              run: pnpm type-check

            - name: Run tests
              run: pnpm test

            - name: Rust format check
              run: cargo fmt -- --check
              working-directory: src-tauri

            - name: Rust clippy check
              run: cargo clippy -- -D warnings
              working-directory: src-tauri
```

---

_本文档会随着项目发展持续更新，请确保遵循最新版本的规范。_
