# 插件 API 文档

本文档详细说明了 Pot 插件系统的 API 接口、开发规范和使用方法。

## 🔌 插件系统概述

### API 版本

当前 API 版本: **v3.0**

兼容性：

-   Pot 3.0.0+ 支持 API v3.0
-   Pot 2.x 支持 API v2.0（已废弃）

### 插件类型

| 插件类型 | 接口名称            | 描述             | 示例                  |
| -------- | ------------------- | ---------------- | --------------------- |
| 翻译插件 | `TranslationPlugin` | 提供文本翻译服务 | Google 翻译、百度翻译 |
| OCR 插件 | `OcrPlugin`         | 提供图像文字识别 | 百度 OCR、腾讯 OCR    |
| TTS 插件 | `TtsPlugin`         | 提供文字转语音   | Edge TTS、Azure TTS   |
| 存储插件 | `StoragePlugin`     | 提供数据存储服务 | Anki 集成、欧路词典   |

## 🔤 翻译插件 API

### 接口定义

```typescript
interface TranslationPlugin {
    /** 插件元信息 */
    manifest: PluginManifest;

    /** 翻译文本 */
    translate(params: TranslationParams): Promise<TranslationResult>;

    /** 获取支持的语言 */
    getSupportedLanguages(): Language[];

    /** 验证配置 */
    validateConfig(config: Record<string, any>): ValidationResult;

    /** 测试连接 */
    testConnection(config: Record<string, any>): Promise<TestResult>;
}
```

### 数据类型

#### TranslationParams

```typescript
interface TranslationParams {
    /** 要翻译的文本 */
    text: string;

    /** 源语言代码 (ISO 639-1) */
    from: string;

    /** 目标语言代码 (ISO 639-1) */
    to: string;

    /** 可选参数 */
    options?: {
        /** 翻译模式 */
        mode?: 'fast' | 'accurate' | 'creative';

        /** 上下文信息 */
        context?: string;

        /** 专业领域 */
        domain?: 'general' | 'technical' | 'medical' | 'legal';

        /** 自定义参数 */
        [key: string]: any;
    };
}
```

#### TranslationResult

```typescript
interface TranslationResult {
    /** 翻译结果文本 */
    text: string;

    /** 检测到的源语言 */
    from: string;

    /** 目标语言 */
    to: string;

    /** 服务标识 */
    service: string;

    /** 备选翻译 */
    alternatives?: string[];

    /** 置信度 (0-1) */
    confidence?: number;

    /** 详细信息 */
    details?: {
        /** 音标 */
        phonetic?: string;

        /** 词性 */
        partOfSpeech?: string;

        /** 例句 */
        examples?: string[];

        /** 同义词 */
        synonyms?: string[];
    };

    /** 元数据 */
    metadata?: Record<string, any>;
}
```

### 实现示例

```javascript
// 基础翻译插件实现
export default class ExampleTranslationPlugin {
    constructor() {
        this.manifest = {
            name: 'example-translate',
            version: '1.0.0',
            displayName: 'Example Translation Service',
            description: 'An example translation plugin',
            author: 'Plugin Developer',
            homepage: 'https://github.com/example/plugin',
            supportedLanguages: ['en', 'zh', 'ja'],
            configSchema: {
                apiKey: { type: 'string', required: true },
                endpoint: { type: 'string', required: true },
                timeout: { type: 'number', default: 30 },
            },
        };
    }

    async translate({ text, from, to, options = {} }) {
        // 1. 验证输入
        this.validateInput(text, from, to);

        // 2. 获取配置
        const config = await this.getConfig();

        // 3. 调用翻译 API
        const response = await this.callAPI(
            {
                text,
                from,
                to,
                ...options,
            },
            config
        );

        // 4. 处理响应
        return this.processResponse(response, from, to);
    }

    validateInput(text, from, to) {
        if (!text || !text.trim()) {
            throw new PluginError('EMPTY_TEXT', 'Text cannot be empty');
        }

        if (!this.isLanguageSupported(from) || !this.isLanguageSupported(to)) {
            throw new PluginError('UNSUPPORTED_LANGUAGE', 'Language not supported');
        }
    }

    async callAPI(params, config) {
        const response = await fetch(config.endpoint, {
            method: 'POST',
            headers: {
                'Content-Type': 'application/json',
                Authorization: `Bearer ${config.apiKey}`,
            },
            body: JSON.stringify(params),
            signal: AbortSignal.timeout(config.timeout * 1000),
        });

        if (!response.ok) {
            throw new PluginError('API_ERROR', `HTTP ${response.status}: ${response.statusText}`);
        }

        return await response.json();
    }

    processResponse(response, from, to) {
        return {
            text: response.translatedText,
            from: response.detectedLanguage || from,
            to,
            service: this.manifest.name,
            confidence: response.confidence,
            alternatives: response.alternatives,
        };
    }

    getSupportedLanguages() {
        return [
            { code: 'en', name: 'English', flag: '🇺🇸' },
            { code: 'zh', name: '中文', flag: '🇨🇳' },
            { code: 'ja', name: '日本語', flag: '🇯🇵' },
        ];
    }

    validateConfig(config) {
        const errors = [];

        if (!config.apiKey) {
            errors.push('API Key is required');
        }

        if (!config.endpoint) {
            errors.push('Endpoint is required');
        } else if (!this.isValidUrl(config.endpoint)) {
            errors.push('Invalid endpoint URL');
        }

        return {
            isValid: errors.length === 0,
            errors,
        };
    }

    async testConnection(config) {
        try {
            await this.callAPI(
                {
                    text: 'test',
                    from: 'en',
                    to: 'zh',
                },
                config
            );

            return { success: true, message: 'Connection successful' };
        } catch (error) {
            return { success: false, message: error.message };
        }
    }
}
```

## 📷 OCR 插件 API

### 接口定义

```typescript
interface OcrPlugin {
    manifest: PluginManifest;
    recognize(params: OcrParams): Promise<OcrResult>;
    getSupportedLanguages(): Language[];
    validateConfig(config: Record<string, any>): ValidationResult;
    testConnection(config: Record<string, any>): Promise<TestResult>;
}
```

### 数据类型

#### OcrParams

```typescript
interface OcrParams {
    /** 图像数据 (base64 编码) */
    image: string;

    /** 识别语言 */
    language?: string;

    /** 识别选项 */
    options?: {
        /** 检测方向 */
        detectOrientation?: boolean;

        /** 识别表格 */
        recognizeTable?: boolean;

        /** 输出格式 */
        outputFormat?: 'text' | 'json' | 'markdown';

        /** 置信度阈值 */
        confidenceThreshold?: number;
    };
}
```

#### OcrResult

```typescript
interface OcrResult {
    /** 识别的文本 */
    text: string;

    /** 整体置信度 */
    confidence: number;

    /** 检测到的语言 */
    language: string;

    /** 服务标识 */
    service: string;

    /** 文本区域 */
    regions?: TextRegion[];

    /** 表格数据 */
    tables?: TableData[];

    /** 元数据 */
    metadata?: {
        /** 处理时间 (ms) */
        processingTime?: number;

        /** 图像尺寸 */
        imageSize?: { width: number; height: number };

        /** 检测到的方向 */
        orientation?: number;
    };
}

interface TextRegion {
    /** 区域文本 */
    text: string;

    /** 区域置信度 */
    confidence: number;

    /** 边界框 */
    boundingBox: {
        x: number;
        y: number;
        width: number;
        height: number;
    };

    /** 文本行 */
    lines?: TextLine[];
}

interface TextLine {
    text: string;
    confidence: number;
    boundingBox: BoundingBox;
    words?: TextWord[];
}
```

### OCR 插件实现示例

```javascript
export default class ExampleOcrPlugin {
    constructor() {
        this.manifest = {
            name: 'example-ocr',
            version: '1.0.0',
            displayName: 'Example OCR Service',
            description: 'An example OCR plugin',
            author: 'Plugin Developer',
            supportedLanguages: ['en', 'zh', 'ja'],
        };
    }

    async recognize({ image, language = 'auto', options = {} }) {
        try {
            // 1. 预处理图像
            const processedImage = await this.preprocessImage(image, options);

            // 2. 调用 OCR API
            const response = await this.callOcrAPI({
                image: processedImage,
                language,
                ...options,
            });

            // 3. 后处理结果
            return this.postprocessResult(response, options);
        } catch (error) {
            throw new PluginError('OCR_FAILED', `OCR recognition failed: ${error.message}`);
        }
    }

    async preprocessImage(imageBase64, options) {
        // 图像预处理
        if (options.enhanceContrast) {
            imageBase64 = await this.enhanceContrast(imageBase64);
        }

        if (options.denoiseImage) {
            imageBase64 = await this.denoiseImage(imageBase64);
        }

        return imageBase64;
    }

    async callOcrAPI(params) {
        const config = await this.getConfig();

        const response = await fetch(`${config.endpoint}/ocr`, {
            method: 'POST',
            headers: {
                'Content-Type': 'application/json',
                Authorization: `Bearer ${config.apiKey}`,
            },
            body: JSON.stringify(params),
        });

        if (!response.ok) {
            throw new Error(`OCR API error: ${response.status}`);
        }

        return await response.json();
    }

    postprocessResult(response, options) {
        let text = response.text;

        // 文本清理
        if (options.cleanText !== false) {
            text = this.cleanText(text);
        }

        // 格式化输出
        if (options.outputFormat === 'markdown') {
            text = this.formatAsMarkdown(response.regions);
        }

        return {
            text,
            confidence: response.confidence,
            language: response.detectedLanguage,
            service: this.manifest.name,
            regions: response.regions,
            metadata: {
                processingTime: response.processingTime,
                imageSize: response.imageSize,
            },
        };
    }
}
```

## 🔊 TTS 插件 API

### 接口定义

```typescript
interface TtsPlugin {
    manifest: PluginManifest;
    speak(params: TtsParams): Promise<TtsResult>;
    getVoices(): Voice[];
    validateConfig(config: Record<string, any>): ValidationResult;
    testConnection(config: Record<string, any>): Promise<TestResult>;
}
```

### 数据类型

#### TtsParams

```typescript
interface TtsParams {
    /** 要合成的文本 */
    text: string;

    /** 语言代码 */
    language: string;

    /** 语音 ID */
    voice?: string;

    /** 语音选项 */
    options?: {
        /** 语速 (0.5-2.0) */
        speed?: number;

        /** 音调 (0.5-2.0) */
        pitch?: number;

        /** 音量 (0.0-1.0) */
        volume?: number;

        /** 输出格式 */
        format?: 'mp3' | 'wav' | 'ogg';

        /** 采样率 */
        sampleRate?: number;
    };
}
```

#### TtsResult

```typescript
interface TtsResult {
    /** 音频 URL (如果是流式) */
    audioUrl?: string;

    /** 音频数据 (如果是二进制) */
    audioData?: ArrayBuffer;

    /** 音频时长 (秒) */
    duration?: number;

    /** 服务标识 */
    service: string;

    /** 使用的语音 */
    voice?: string;

    /** 元数据 */
    metadata?: {
        /** 文件大小 (字节) */
        fileSize?: number;

        /** 音频格式 */
        format?: string;

        /** 采样率 */
        sampleRate?: number;
    };
}
```

## 💾 存储插件 API

### 接口定义

```typescript
interface StoragePlugin {
    manifest: PluginManifest;
    save(data: StorageData): Promise<StorageResult>;
    load(query: StorageQuery): Promise<StorageData[]>;
    delete(id: string): Promise<boolean>;
    search(query: SearchQuery): Promise<SearchResult>;
    validateConfig(config: Record<string, any>): ValidationResult;
    testConnection(config: Record<string, any>): Promise<TestResult>;
}
```

### 数据类型

#### StorageData

```typescript
interface StorageData {
    /** 数据 ID */
    id?: string;

    /** 数据类型 */
    type: 'translation' | 'word' | 'phrase' | 'sentence';

    /** 数据内容 */
    content: {
        /** 源文本 */
        source: string;

        /** 目标文本 */
        target: string;

        /** 源语言 */
        sourceLanguage: string;

        /** 目标语言 */
        targetLanguage: string;

        /** 上下文 */
        context?: string;

        /** 标签 */
        tags?: string[];

        /** 备注 */
        notes?: string;
    };

    /** 元数据 */
    metadata?: {
        /** 创建时间 */
        createdAt?: string;

        /** 更新时间 */
        updatedAt?: string;

        /** 使用次数 */
        usageCount?: number;

        /** 难度等级 */
        difficulty?: number;
    };
}
```

## 🔧 插件工具 API

### 配置管理 API

```javascript
// 获取插件配置
const config = await invoke('plugin_get_config', {
    pluginName: 'my-plugin',
});

// 保存插件配置
await invoke('plugin_set_config', {
    pluginName: 'my-plugin',
    config: {
        apiKey: 'your-api-key',
        endpoint: 'https://api.example.com',
    },
});

// 监听配置变更
import { listen } from '@tauri-apps/api/event';

const unlisten = await listen('plugin-config-changed', (event) => {
    if (event.payload.pluginName === 'my-plugin') {
        // 处理配置变更
        console.log('Config changed:', event.payload.config);
    }
});
```

### 日志 API

```javascript
// 插件日志记录
await invoke('plugin_log', {
    pluginName: 'my-plugin',
    level: 'info',
    message: 'Translation completed successfully',
    metadata: {
        text: 'Hello World',
        result: '你好世界',
        duration: 1234,
    },
});

// 日志级别
const LogLevel = {
    ERROR: 'error',
    WARN: 'warn',
    INFO: 'info',
    DEBUG: 'debug',
};
```

### 缓存 API

```javascript
// 设置缓存
await invoke('plugin_cache_set', {
    pluginName: 'my-plugin',
    key: 'translation:hello-world:en-zh',
    value: '你好世界',
    ttl: 3600, // 1 小时过期
});

// 获取缓存
const cached = await invoke('plugin_cache_get', {
    pluginName: 'my-plugin',
    key: 'translation:hello-world:en-zh',
});

// 删除缓存
await invoke('plugin_cache_delete', {
    pluginName: 'my-plugin',
    key: 'translation:hello-world:en-zh',
});

// 清空所有缓存
await invoke('plugin_cache_clear', {
    pluginName: 'my-plugin',
});
```

### 网络请求 API

```javascript
// HTTP 请求工具
const response = await invoke('plugin_http_request', {
    pluginName: 'my-plugin',
    request: {
        method: 'POST',
        url: 'https://api.example.com/translate',
        headers: {
            'Content-Type': 'application/json',
            Authorization: 'Bearer your-token',
        },
        body: JSON.stringify({
            text: 'Hello World',
            from: 'en',
            to: 'zh',
        }),
        timeout: 30000,
    },
});

// 文件下载
await invoke('plugin_download_file', {
    pluginName: 'my-plugin',
    url: 'https://example.com/audio.mp3',
    path: '/tmp/audio.mp3',
});
```

## 🎯 错误处理

### 标准错误类型

```typescript
enum PluginErrorCode {
    // 通用错误
    UNKNOWN_ERROR = 'UNKNOWN_ERROR',
    INVALID_CONFIG = 'INVALID_CONFIG',
    NETWORK_ERROR = 'NETWORK_ERROR',

    // 翻译错误
    EMPTY_TEXT = 'EMPTY_TEXT',
    UNSUPPORTED_LANGUAGE = 'UNSUPPORTED_LANGUAGE',
    TRANSLATION_FAILED = 'TRANSLATION_FAILED',
    API_QUOTA_EXCEEDED = 'API_QUOTA_EXCEEDED',

    // OCR 错误
    INVALID_IMAGE = 'INVALID_IMAGE',
    OCR_FAILED = 'OCR_FAILED',
    LOW_CONFIDENCE = 'LOW_CONFIDENCE',

    // TTS 错误
    TTS_FAILED = 'TTS_FAILED',
    VOICE_NOT_FOUND = 'VOICE_NOT_FOUND',

    // 存储错误
    STORAGE_FAILED = 'STORAGE_FAILED',
    ITEM_NOT_FOUND = 'ITEM_NOT_FOUND',
}

class PluginError extends Error {
    constructor(code, message, details = {}) {
        super(message);
        this.name = 'PluginError';
        this.code = code;
        this.details = details;
    }
}
```

### 错误处理最佳实践

```javascript
// ✅ 良好的错误处理
async function translate(params) {
    try {
        return await this.callAPI(params);
    } catch (error) {
        // 记录详细错误信息
        this.logError('Translation failed', {
            params,
            error: error.message,
            stack: error.stack,
        });

        // 根据错误类型抛出标准化错误
        if (error.code === 'NETWORK_ERROR') {
            throw new PluginError(
                'NETWORK_ERROR',
                'Network connection failed. Please check your internet connection.',
                { originalError: error }
            );
        } else if (error.code === 'API_QUOTA_EXCEEDED') {
            throw new PluginError('API_QUOTA_EXCEEDED', 'API quota exceeded. Please check your account limits.', {
                quotaInfo: error.quotaInfo,
            });
        } else {
            throw new PluginError('TRANSLATION_FAILED', 'Translation service temporarily unavailable.', {
                originalError: error,
            });
        }
    }
}
```

## 🔒 插件安全规范

### 权限系统

```typescript
interface PluginPermissions {
    /** 网络访问权限 */
    network?: {
        /** 允许的域名 */
        allowedDomains: string[];

        /** 允许的协议 */
        allowedProtocols: ['http', 'https'];

        /** 请求频率限制 */
        rateLimit?: {
            requests: number;
            window: number; // 时间窗口（秒）
        };
    };

    /** 文件系统权限 */
    filesystem?: {
        /** 允许读取的路径 */
        readPaths: string[];

        /** 允许写入的路径 */
        writePaths: string[];
    };

    /** 系统 API 权限 */
    system?: {
        /** 允许的系统调用 */
        allowedApis: string[];
    };
}
```

### 安全检查

```javascript
// 插件安全验证
export function validatePlugin(plugin) {
    const checks = [
        validateManifest(plugin.manifest),
        validatePermissions(plugin.permissions),
        validateCode(plugin.code),
        validateDependencies(plugin.dependencies),
    ];

    const errors = checks.filter((check) => !check.passed);

    return {
        isValid: errors.length === 0,
        errors: errors.map((error) => error.message),
    };
}

function validateCode(code) {
    // 代码安全检查
    const dangerousPatterns = [/eval\s*\(/, /Function\s*\(/, /document\.write/, /innerHTML\s*=/];

    for (const pattern of dangerousPatterns) {
        if (pattern.test(code)) {
            return {
                passed: false,
                message: `Dangerous code pattern detected: ${pattern}`,
            };
        }
    }

    return { passed: true };
}
```

## 📊 插件性能优化

### 性能监控

```javascript
export class PluginPerformanceMonitor {
    constructor(pluginName) {
        this.pluginName = pluginName;
        this.metrics = new Map();
    }

    async measureOperation(operationName, operation) {
        const startTime = performance.now();

        try {
            const result = await operation();
            const duration = performance.now() - startTime;

            this.recordMetric(operationName, {
                duration,
                success: true,
                timestamp: Date.now(),
            });

            return result;
        } catch (error) {
            const duration = performance.now() - startTime;

            this.recordMetric(operationName, {
                duration,
                success: false,
                error: error.message,
                timestamp: Date.now(),
            });

            throw error;
        }
    }

    recordMetric(operation, data) {
        if (!this.metrics.has(operation)) {
            this.metrics.set(operation, []);
        }

        const operationMetrics = this.metrics.get(operation);
        operationMetrics.push(data);

        // 只保留最近 100 条记录
        if (operationMetrics.length > 100) {
            operationMetrics.shift();
        }

        // 发送性能数据到主应用
        this.reportMetrics(operation, data);
    }

    async reportMetrics(operation, data) {
        await invoke('plugin_report_metrics', {
            pluginName: this.pluginName,
            operation,
            metrics: data,
        });
    }
}
```

### 缓存策略

```javascript
export class PluginCache {
    constructor(pluginName, options = {}) {
        this.pluginName = pluginName;
        this.ttl = options.ttl || 3600; // 默认 1 小时
        this.maxSize = options.maxSize || 100; // 最大缓存项数
    }

    async get(key) {
        try {
            const cached = await invoke('plugin_cache_get', {
                pluginName: this.pluginName,
                key,
            });

            if (cached && !this.isExpired(cached)) {
                return cached.value;
            }

            return null;
        } catch (error) {
            console.warn('Cache get failed:', error);
            return null;
        }
    }

    async set(key, value, customTtl = null) {
        try {
            await invoke('plugin_cache_set', {
                pluginName: this.pluginName,
                key,
                value,
                ttl: customTtl || this.ttl,
            });
        } catch (error) {
            console.warn('Cache set failed:', error);
        }
    }

    generateKey(params) {
        // 生成缓存键
        const sortedParams = Object.keys(params)
            .sort()
            .map((key) => `${key}:${params[key]}`)
            .join('|');

        return btoa(sortedParams).replace(/[/+=]/g, '');
    }

    isExpired(cached) {
        return Date.now() - cached.timestamp > cached.ttl * 1000;
    }
}
```

## 📋 插件开发最佳实践

### 1. 代码组织

```javascript
// ✅ 良好的插件结构
export default class WellOrganizedPlugin {
    constructor() {
        this.manifest = this.createManifest();
        this.cache = new PluginCache(this.manifest.name);
        this.monitor = new PluginPerformanceMonitor(this.manifest.name);
    }

    createManifest() {
        return {
            name: 'well-organized-plugin',
            version: '1.0.0',
            // ... 其他清单信息
        };
    }

    async translate(params) {
        return await this.monitor.measureOperation('translate', async () => {
            // 检查缓存
            const cacheKey = this.cache.generateKey(params);
            const cached = await this.cache.get(cacheKey);
            if (cached) {
                return cached;
            }

            // 执行翻译
            const result = await this.performTranslation(params);

            // 缓存结果
            await this.cache.set(cacheKey, result);

            return result;
        });
    }

    async performTranslation(params) {
        // 具体的翻译实现
    }
}
```

### 2. 配置管理

```javascript
export class ConfigManager {
    constructor(pluginName, schema) {
        this.pluginName = pluginName;
        this.schema = schema;
    }

    async getConfig() {
        const config = await invoke('plugin_get_config', {
            pluginName: this.pluginName,
        });

        return this.applyDefaults(config);
    }

    applyDefaults(config) {
        const result = { ...config };

        for (const [key, definition] of Object.entries(this.schema)) {
            if (result[key] === undefined && definition.default !== undefined) {
                result[key] = definition.default;
            }
        }

        return result;
    }

    validateConfig(config) {
        const errors = [];

        for (const [key, definition] of Object.entries(this.schema)) {
            if (definition.required && !config[key]) {
                errors.push(`${key} is required`);
            }

            if (config[key] && definition.type) {
                if (typeof config[key] !== definition.type) {
                    errors.push(`${key} must be of type ${definition.type}`);
                }
            }

            if (definition.validate) {
                const validation = definition.validate(config[key]);
                if (!validation.isValid) {
                    errors.push(...validation.errors);
                }
            }
        }

        return {
            isValid: errors.length === 0,
            errors,
        };
    }
}
```

### 3. 国际化支持

```javascript
// 插件国际化
export class PluginI18n {
    constructor(pluginName, locales) {
        this.pluginName = pluginName;
        this.locales = locales;
        this.currentLocale = 'en';
    }

    async init() {
        this.currentLocale = await invoke('get_app_language');
    }

    t(key, params = {}) {
        const locale = this.locales[this.currentLocale] || this.locales['en'];
        let text = this.getNestedValue(locale, key) || key;

        // 参数替换
        for (const [param, value] of Object.entries(params)) {
            text = text.replace(`{{${param}}}`, value);
        }

        return text;
    }

    getNestedValue(obj, path) {
        return path.split('.').reduce((current, key) => current?.[key], obj);
    }
}

// 使用示例
const i18n = new PluginI18n('my-plugin', {
    en: {
        error: {
            network: 'Network connection failed',
            quota: 'API quota exceeded',
        },
    },
    zh: {
        error: {
            network: '网络连接失败',
            quota: 'API 配额已用完',
        },
    },
});

// 在插件中使用
throw new PluginError('NETWORK_ERROR', i18n.t('error.network'));
```

## 📦 插件发布

### 发布流程

1. **准备发布**:

    ```bash
    # 构建插件
    npm run build

    # 运行测试
    npm test

    # 验证插件
    pot plugin validate ./dist
    ```

2. **版本管理**:

    ```bash
    # 更新版本号
    npm version patch  # 或 minor, major

    # 创建发布标签
    git tag v1.0.1
    git push origin v1.0.1
    ```

3. **发布到 npm**:

    ```bash
    # 发布到 npm
    npm publish

    # 或发布到私有仓库
    npm publish --registry https://your-registry.com
    ```

### 插件市场

```json
// 插件市场元信息
{
    "name": "pot-plugin-custom-translate",
    "displayName": "Custom Translation Service",
    "description": "A powerful custom translation service with advanced features",
    "version": "1.0.0",
    "author": "Plugin Developer",
    "category": "translation",
    "tags": ["translation", "custom", "api"],
    "screenshots": ["https://example.com/screenshot1.png", "https://example.com/screenshot2.png"],
    "documentation": "https://github.com/user/plugin/blob/main/README.md",
    "support": "https://github.com/user/plugin/issues",
    "downloads": 1234,
    "rating": 4.5,
    "lastUpdated": "2024-01-15T10:30:00Z"
}
```

## 🧪 插件测试

### 测试工具

```javascript
// 插件测试工具
export class PluginTester {
    constructor(plugin) {
        this.plugin = plugin;
    }

    async runTestSuite() {
        const results = {
            manifest: this.testManifest(),
            config: this.testConfig(),
            functionality: await this.testFunctionality(),
            performance: await this.testPerformance(),
            security: this.testSecurity(),
        };

        return this.generateReport(results);
    }

    testManifest() {
        const required = ['name', 'version', 'displayName', 'description'];
        const missing = required.filter((field) => !this.plugin.manifest[field]);

        return {
            passed: missing.length === 0,
            errors: missing.map((field) => `Missing manifest field: ${field}`),
        };
    }

    async testFunctionality() {
        const testCases = this.getTestCases();
        const results = [];

        for (const testCase of testCases) {
            try {
                const result = await this.plugin.translate(testCase.input);
                results.push({
                    testCase: testCase.name,
                    passed: this.validateResult(result, testCase.expected),
                    result,
                });
            } catch (error) {
                results.push({
                    testCase: testCase.name,
                    passed: false,
                    error: error.message,
                });
            }
        }

        return results;
    }
}
```

## 📚 相关文档

-   [组件开发](components.md) - UI 组件开发指南
-   [架构设计](architecture.md) - 插件系统架构
-   [外部调用 API](../api/external-api.md) - 外部 API 接口
-   [用户插件指南](../user-guides/plugins.md) - 用户插件使用指南

---

_插件 API 会随着系统发展持续更新，请关注版本变化和兼容性说明。_
