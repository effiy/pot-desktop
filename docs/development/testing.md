# 测试指南

本文档介绍了 Pot 项目的测试策略、测试工具和最佳实践。

## 🎯 测试策略

### 测试金字塔

```
    /\
   /  \    E2E Tests (端到端测试)
  /____\   Integration Tests (集成测试)
 /______\  Unit Tests (单元测试)
```

#### 测试分层

1. **单元测试 (70%)**

    - 测试单个函数、组件或模块
    - 快速执行，易于维护
    - 提供即时反馈

2. **集成测试 (20%)**

    - 测试模块间的交互
    - 验证组件协作
    - 测试 API 集成

3. **端到端测试 (10%)**
    - 测试完整用户流程
    - 模拟真实使用场景
    - 验证整体功能

## 🛠️ 测试工具

### 前端测试工具

#### Jest + React Testing Library

**配置文件**: `jest.config.js`

```javascript
module.exports = {
    testEnvironment: 'jsdom',
    setupFilesAfterEnv: ['<rootDir>/src/setupTests.js'],
    moduleNameMapping: {
        '\\.(css|less|scss|sass)$': 'identity-obj-proxy',
        '\\.(jpg|jpeg|png|gif|svg)$': '<rootDir>/__mocks__/fileMock.js',
    },
    collectCoverageFrom: ['src/**/*.{js,jsx,ts,tsx}', '!src/**/*.d.ts', '!src/index.js', '!src/main.jsx'],
    coverageThreshold: {
        global: {
            branches: 70,
            functions: 70,
            lines: 70,
            statements: 70,
        },
    },
    testMatch: ['<rootDir>/src/**/__tests__/**/*.{js,jsx,ts,tsx}', '<rootDir>/src/**/*.{test,spec}.{js,jsx,ts,tsx}'],
};
```

#### 设置文件

**`src/setupTests.js`**:

```javascript
import '@testing-library/jest-dom';
import { configure } from '@testing-library/react';

// 配置 Testing Library
configure({ testIdAttribute: 'data-testid' });

// Mock Tauri API
window.__TAURI__ = {
    invoke: jest.fn(),
    event: {
        listen: jest.fn(),
        emit: jest.fn(),
    },
};

// Mock IntersectionObserver
global.IntersectionObserver = class IntersectionObserver {
    constructor() {}
    observe() {
        return null;
    }
    disconnect() {
        return null;
    }
    unobserve() {
        return null;
    }
};
```

### 后端测试工具

#### Rust 测试框架

**`Cargo.toml`** 测试依赖:

```toml
[dev-dependencies]
tokio-test = "0.4"
mockall = "0.11"
serial_test = "2.0"
tempfile = "3.0"
```

## 📝 单元测试

### React 组件测试

#### 基础组件测试

```javascript
// src/components/Button/__tests__/Button.test.jsx
import { render, screen, fireEvent } from '@testing-library/react';
import Button from '../Button';

describe('Button Component', () => {
    it('should render button with text', () => {
        render(<Button>Click me</Button>);

        expect(screen.getByRole('button')).toBeInTheDocument();
        expect(screen.getByText('Click me')).toBeInTheDocument();
    });

    it('should handle click events', () => {
        const handleClick = jest.fn();
        render(<Button onClick={handleClick}>Click me</Button>);

        fireEvent.click(screen.getByRole('button'));

        expect(handleClick).toHaveBeenCalledTimes(1);
    });

    it('should be disabled when disabled prop is true', () => {
        render(<Button disabled>Click me</Button>);

        expect(screen.getByRole('button')).toBeDisabled();
    });

    it('should apply custom className', () => {
        render(<Button className='custom-class'>Click me</Button>);

        expect(screen.getByRole('button')).toHaveClass('custom-class');
    });
});
```

#### Hook 测试

```javascript
// src/hooks/__tests__/useTranslation.test.js
import { renderHook, act } from '@testing-library/react';
import useTranslation from '../useTranslation';

// Mock API
jest.mock('../api/translationApi', () => ({
    translate: jest.fn(),
}));

describe('useTranslation Hook', () => {
    it('should initialize with default state', () => {
        const { result } = renderHook(() => useTranslation());

        expect(result.current.isLoading).toBe(false);
        expect(result.current.result).toBe(null);
        expect(result.current.error).toBe(null);
    });

    it('should handle translation request', async () => {
        const mockTranslate = require('../api/translationApi').translate;
        mockTranslate.mockResolvedValue('你好');

        const { result } = renderHook(() => useTranslation());

        await act(async () => {
            await result.current.translate('Hello', 'en', 'zh');
        });

        expect(result.current.result).toBe('你好');
        expect(result.current.isLoading).toBe(false);
    });

    it('should handle translation error', async () => {
        const mockTranslate = require('../api/translationApi').translate;
        mockTranslate.mockRejectedValue(new Error('Translation failed'));

        const { result } = renderHook(() => useTranslation());

        await act(async () => {
            await result.current.translate('Hello', 'en', 'zh');
        });

        expect(result.current.error).toBe('Translation failed');
        expect(result.current.isLoading).toBe(false);
    });
});
```

#### 工具函数测试

```javascript
// src/utils/__tests__/stringUtils.test.js
import { formatText, truncateText, detectLanguage, sanitizeInput } from '../stringUtils';

describe('String Utils', () => {
    describe('formatText', () => {
        it('should trim whitespace', () => {
            expect(formatText('  hello world  ')).toBe('hello world');
        });

        it('should normalize line breaks', () => {
            expect(formatText('hello\r\nworld')).toBe('hello\nworld');
        });

        it('should handle empty string', () => {
            expect(formatText('')).toBe('');
        });
    });

    describe('truncateText', () => {
        it('should truncate long text', () => {
            const longText = 'This is a very long text that should be truncated';
            expect(truncateText(longText, 20)).toBe('This is a very long...');
        });

        it('should not truncate short text', () => {
            expect(truncateText('Short text', 20)).toBe('Short text');
        });
    });

    describe('detectLanguage', () => {
        it('should detect English', () => {
            expect(detectLanguage('Hello World')).toBe('en');
        });

        it('should detect Chinese', () => {
            expect(detectLanguage('你好世界')).toBe('zh');
        });

        it('should handle mixed text', () => {
            expect(detectLanguage('Hello 世界')).toBe('mixed');
        });
    });
});
```

### Rust 单元测试

#### 基础函数测试

```rust
// src-tauri/src/utils/text_processor.rs
#[cfg(test)]
mod tests {
    use super::*;

    #[test]
    fn test_normalize_text() {
        assert_eq!(normalize_text("  hello world  "), "hello world");
        assert_eq!(normalize_text("hello\r\nworld"), "hello\nworld");
        assert_eq!(normalize_text(""), "");
    }

    #[test]
    fn test_detect_language() {
        assert_eq!(detect_language("Hello World"), Some("en"));
        assert_eq!(detect_language("你好世界"), Some("zh"));
        assert_eq!(detect_language(""), None);
    }

    #[test]
    fn test_split_sentences() {
        let text = "Hello world. How are you? I'm fine.";
        let sentences = split_sentences(text);
        assert_eq!(sentences.len(), 3);
        assert_eq!(sentences[0], "Hello world.");
        assert_eq!(sentences[1], "How are you?");
        assert_eq!(sentences[2], "I'm fine.");
    }

    #[test]
    #[should_panic(expected = "Empty text")]
    fn test_process_empty_text() {
        process_text("").unwrap();
    }
}
```

#### 异步函数测试

```rust
// src-tauri/src/services/translation_service.rs
#[cfg(test)]
mod tests {
    use super::*;
    use tokio_test;

    #[tokio::test]
    async fn test_translate_success() {
        let service = TranslationService::new("test_api_key");
        let result = service.translate("Hello", "en", "zh").await;

        assert!(result.is_ok());
        assert_eq!(result.unwrap(), "你好");
    }

    #[tokio::test]
    async fn test_translate_empty_text() {
        let service = TranslationService::new("test_api_key");
        let result = service.translate("", "en", "zh").await;

        assert!(result.is_err());
        assert_eq!(
            result.unwrap_err().to_string(),
            "Text cannot be empty"
        );
    }

    #[tokio::test]
    async fn test_translate_network_error() {
        let mut service = TranslationService::new("invalid_key");
        service.set_timeout(Duration::from_millis(1)); // 强制超时

        let result = service.translate("Hello", "en", "zh").await;

        assert!(result.is_err());
        assert!(result.unwrap_err().to_string().contains("timeout"));
    }
}
```

#### Mock 测试

```rust
// src-tauri/src/services/api_client.rs
use mockall::automock;

#[automock]
pub trait ApiClient {
    async fn post(&self, url: &str, body: &str) -> Result<String, ApiError>;
    async fn get(&self, url: &str) -> Result<String, ApiError>;
}

#[cfg(test)]
mod tests {
    use super::*;

    #[tokio::test]
    async fn test_translation_with_mock() {
        let mut mock_client = MockApiClient::new();
        mock_client
            .expect_post()
            .with(
                mockall::predicate::eq("https://api.example.com/translate"),
                mockall::predicate::str::contains("Hello")
            )
            .times(1)
            .returning(|_, _| Ok(r#"{"result": "你好"}"#.to_string()));

        let service = TranslationService::new(Box::new(mock_client));
        let result = service.translate("Hello", "en", "zh").await;

        assert_eq!(result.unwrap(), "你好");
    }
}
```

## 🔗 集成测试

### API 集成测试

```javascript
// src/services/__tests__/translationService.integration.test.js
import TranslationService from '../translationService';

// 这些测试需要真实的 API 密钥，通常在 CI 环境中跳过
const isCI = process.env.CI === 'true';
const skipIntegration = !process.env.API_KEY || isCI;

describe.skipIf(skipIntegration)('Translation Service Integration', () => {
    let service;

    beforeAll(() => {
        service = new TranslationService({
            apiKey: process.env.API_KEY,
            timeout: 10000,
        });
    });

    it('should translate text using real API', async () => {
        const result = await service.translate('Hello World', 'en', 'zh');

        expect(result).toBeTruthy();
        expect(typeof result).toBe('string');
        expect(result.length).toBeGreaterThan(0);
    }, 15000); // 增加超时时间

    it('should handle rate limiting gracefully', async () => {
        const promises = Array(10)
            .fill()
            .map(() => service.translate('Test', 'en', 'zh'));

        const results = await Promise.allSettled(promises);
        const successful = results.filter((r) => r.status === 'fulfilled');

        expect(successful.length).toBeGreaterThan(0);
    });
});
```

### 组件集成测试

```javascript
// src/components/TranslationPanel/__tests__/TranslationPanel.integration.test.jsx
import { render, screen, fireEvent, waitFor } from '@testing-library/react';
import { rest } from 'msw';
import { setupServer } from 'msw/node';
import TranslationPanel from '../TranslationPanel';

// 设置 Mock 服务器
const server = setupServer(
    rest.post('/api/translate', (req, res, ctx) => {
        return res(
            ctx.json({
                result: '你好世界',
                from: 'en',
                to: 'zh',
            })
        );
    })
);

beforeAll(() => server.listen());
afterEach(() => server.resetHandlers());
afterAll(() => server.close());

describe('TranslationPanel Integration', () => {
    it('should translate text end-to-end', async () => {
        render(<TranslationPanel />);

        // 输入文本
        const input = screen.getByPlaceholderText('Enter text to translate');
        fireEvent.change(input, { target: { value: 'Hello World' } });

        // 点击翻译按钮
        const translateButton = screen.getByRole('button', { name: /translate/i });
        fireEvent.click(translateButton);

        // 等待翻译结果
        await waitFor(() => {
            expect(screen.getByText('你好世界')).toBeInTheDocument();
        });

        // 验证加载状态已消失
        expect(screen.queryByText('Translating...')).not.toBeInTheDocument();
    });

    it('should handle translation errors', async () => {
        // 模拟 API 错误
        server.use(
            rest.post('/api/translate', (req, res, ctx) => {
                return res(ctx.status(500), ctx.json({ error: 'Translation failed' }));
            })
        );

        render(<TranslationPanel />);

        const input = screen.getByPlaceholderText('Enter text to translate');
        fireEvent.change(input, { target: { value: 'Hello World' } });

        const translateButton = screen.getByRole('button', { name: /translate/i });
        fireEvent.click(translateButton);

        await waitFor(() => {
            expect(screen.getByText(/translation failed/i)).toBeInTheDocument();
        });
    });
});
```

## 🎭 端到端测试

### Playwright 配置

**`playwright.config.js`**:

```javascript
import { defineConfig, devices } from '@playwright/test';

export default defineConfig({
    testDir: './e2e',
    fullyParallel: true,
    forbidOnly: !!process.env.CI,
    retries: process.env.CI ? 2 : 0,
    workers: process.env.CI ? 1 : undefined,
    reporter: 'html',
    use: {
        baseURL: 'http://localhost:1420',
        trace: 'on-first-retry',
        screenshot: 'only-on-failure',
    },
    projects: [
        {
            name: 'chromium',
            use: { ...devices['Desktop Chrome'] },
        },
        {
            name: 'webkit',
            use: { ...devices['Desktop Safari'] },
        },
    ],
    webServer: {
        command: 'pnpm tauri dev',
        port: 1420,
        reuseExistingServer: !process.env.CI,
    },
});
```

### E2E 测试示例

```javascript
// e2e/translation.spec.js
import { test, expect } from '@playwright/test';

test.describe('Translation Feature', () => {
    test.beforeEach(async ({ page }) => {
        await page.goto('/');
        await page.waitForLoadState('networkidle');
    });

    test('should translate text successfully', async ({ page }) => {
        // 输入要翻译的文本
        await page.fill('[data-testid="translation-input"]', 'Hello World');

        // 选择语言
        await page.selectOption('[data-testid="from-language"]', 'en');
        await page.selectOption('[data-testid="to-language"]', 'zh');

        // 点击翻译按钮
        await page.click('[data-testid="translate-button"]');

        // 等待翻译结果
        await expect(page.locator('[data-testid="translation-result"]')).toBeVisible();
        await expect(page.locator('[data-testid="translation-result"]')).toContainText('你好');
    });

    test('should handle screenshot OCR', async ({ page }) => {
        // 点击截图 OCR 按钮
        await page.click('[data-testid="screenshot-ocr-button"]');

        // 等待截图工具出现
        await expect(page.locator('[data-testid="screenshot-overlay"]')).toBeVisible();

        // 模拟截图选择（这里需要根据具体实现调整）
        await page.mouse.move(100, 100);
        await page.mouse.down();
        await page.mouse.move(300, 200);
        await page.mouse.up();

        // 等待 OCR 结果
        await expect(page.locator('[data-testid="ocr-result"]')).toBeVisible();
    });

    test('should save translation history', async ({ page }) => {
        // 执行翻译
        await page.fill('[data-testid="translation-input"]', 'Test text');
        await page.click('[data-testid="translate-button"]');
        await page.waitForSelector('[data-testid="translation-result"]');

        // 打开历史记录
        await page.click('[data-testid="history-button"]');

        // 验证历史记录中包含刚才的翻译
        await expect(page.locator('[data-testid="history-item"]').first()).toContainText('Test text');
    });
});
```

## 📊 测试覆盖率

### 生成覆盖率报告

```bash
# 运行测试并生成覆盖率报告
pnpm test:coverage

# 查看 HTML 报告
pnpm coverage:open
```

### 覆盖率配置

```javascript
// jest.config.js
module.exports = {
    collectCoverageFrom: [
        'src/**/*.{js,jsx,ts,tsx}',
        '!src/**/*.d.ts',
        '!src/index.js',
        '!src/**/*.stories.{js,jsx,ts,tsx}',
        '!src/**/__tests__/**',
    ],
    coverageThreshold: {
        global: {
            branches: 70,
            functions: 70,
            lines: 70,
            statements: 70,
        },
        './src/services/': {
            branches: 80,
            functions: 80,
            lines: 80,
            statements: 80,
        },
    },
    coverageReporters: ['text', 'lcov', 'html'],
};
```

### Rust 覆盖率

```bash
# 安装 tarpaulin
cargo install cargo-tarpaulin

# 生成覆盖率报告
cargo tarpaulin --out Html --output-dir coverage

# 查看报告
open coverage/tarpaulin-report.html
```

## 🚀 性能测试

### 前端性能测试

```javascript
// src/utils/__tests__/performance.test.js
import { performance } from 'perf_hooks';
import { translateLargeText } from '../textProcessor';

describe('Performance Tests', () => {
    it('should translate large text within time limit', async () => {
        const largeText = 'Lorem ipsum '.repeat(1000);

        const start = performance.now();
        await translateLargeText(largeText);
        const end = performance.now();

        const duration = end - start;
        expect(duration).toBeLessThan(5000); // 应在 5 秒内完成
    });

    it('should handle concurrent translations efficiently', async () => {
        const texts = Array(10).fill('Test text for translation');

        const start = performance.now();
        await Promise.all(texts.map((text) => translateText(text, 'en', 'zh')));
        const end = performance.now();

        const duration = end - start;
        expect(duration).toBeLessThan(10000); // 并发处理应在 10 秒内完成
    });
});
```

### Rust 性能测试

```rust
// src-tauri/src/services/mod.rs
#[cfg(test)]
mod benchmarks {
    use super::*;
    use std::time::Instant;

    #[test]
    fn benchmark_text_processing() {
        let large_text = "Lorem ipsum ".repeat(10000);

        let start = Instant::now();
        let _result = process_large_text(&large_text);
        let duration = start.elapsed();

        assert!(duration.as_millis() < 1000); // 应在 1 秒内完成
    }

    #[tokio::test]
    async fn benchmark_concurrent_translations() {
        let texts = vec!["Test text"; 100];

        let start = Instant::now();
        let futures = texts.iter().map(|&text| translate_text(text, "en", "zh"));
        let _results = futures::future::join_all(futures).await;
        let duration = start.elapsed();

        assert!(duration.as_secs() < 30); // 并发处理应在 30 秒内完成
    }
}
```

## 🎯 测试最佳实践

### 测试命名

```javascript
// ✅ 好的测试名称
describe('UserService', () => {
    describe('when user is authenticated', () => {
        it('should return user profile data', () => {});
        it('should allow profile updates', () => {});
    });

    describe('when user is not authenticated', () => {
        it('should throw authentication error', () => {});
        it('should redirect to login page', () => {});
    });
});

// ❌ 不好的测试名称
describe('UserService', () => {
    it('test1', () => {});
    it('should work', () => {});
    it('user stuff', () => {});
});
```

### 测试数据管理

```javascript
// 使用工厂函数创建测试数据
const createUser = (overrides = {}) => ({
    id: 1,
    name: 'John Doe',
    email: 'john@example.com',
    role: 'user',
    ...overrides,
});

const createTranslationRequest = (overrides = {}) => ({
    text: 'Hello World',
    from: 'en',
    to: 'zh',
    service: 'google',
    ...overrides,
});

// 在测试中使用
it('should handle admin users differently', () => {
    const adminUser = createUser({ role: 'admin' });
    // 测试逻辑
});
```

### 异步测试

```javascript
// ✅ 正确的异步测试
it('should fetch user data', async () => {
    const userData = await fetchUserData(1);
    expect(userData.name).toBe('John Doe');
});

// ✅ 使用 resolves/rejects
it('should fetch user data', () => {
    return expect(fetchUserData(1)).resolves.toMatchObject({
        name: 'John Doe',
    });
});

// ❌ 忘记等待异步操作
it('should fetch user data', () => {
    fetchUserData(1); // 缺少 await 或 return
    // 测试会在异步操作完成前结束
});
```

### 测试隔离

```javascript
describe('TranslationService', () => {
    let service;

    beforeEach(() => {
        // 每个测试前重新创建服务实例
        service = new TranslationService();
    });

    afterEach(() => {
        // 清理副作用
        jest.clearAllMocks();
        localStorage.clear();
    });
});
```

## 🔧 测试工具和脚本

### 测试命令

```bash
# 运行所有测试
pnpm test

# 运行特定测试文件
pnpm test Button.test.jsx

# 运行测试并监听变化
pnpm test:watch

# 运行测试并生成覆盖率
pnpm test:coverage

# 运行 E2E 测试
pnpm test:e2e

# 运行性能测试
pnpm test:performance
```

### 调试测试

```javascript
// 使用 debug 模式
it.only('should debug this test', () => {
    debugger; // 在浏览器中断点
    console.log('Debug info:', data);
    // 测试逻辑
});

// 使用 screen.debug() 查看 DOM
it('should render correctly', () => {
    render(<Component />);
    screen.debug(); // 打印当前 DOM 结构
});
```

### 测试环境变量

```bash
# .env.test
NODE_ENV=test
API_BASE_URL=http://localhost:3001
SKIP_INTEGRATION_TESTS=true
```

## 📋 测试检查清单

### 提交前检查

-   [ ] 所有测试通过
-   [ ] 测试覆盖率达标
-   [ ] 新功能有对应测试
-   [ ] 修复的 Bug 有回归测试
-   [ ] 测试名称清晰描述意图
-   [ ] 没有跳过的测试（除非有充分理由）
-   [ ] 测试运行时间合理
-   [ ] 清理了测试副作用

### 代码审查检查

-   [ ] 测试覆盖了主要功能路径
-   [ ] 测试覆盖了边界条件
-   [ ] 测试覆盖了错误情况
-   [ ] 测试数据合理且多样
-   [ ] 测试独立且可重复
-   [ ] 测试易于理解和维护

---

_测试是保证代码质量的重要手段，请确保为您的代码编写充分的测试。_
