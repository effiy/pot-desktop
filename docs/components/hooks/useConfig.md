# useConfig Hook

useConfig 是用于管理应用配置的自定义 Hook，提供配置的读取、更新和同步功能。

## 📋 概述

useConfig Hook 封装了配置管理的复杂逻辑，提供简单的接口来读取和更新配置项，并自动处理配置同步和持久化。

### 主要功能

-   配置项的读取和写入
-   配置变化的实时同步
-   默认值处理
-   类型安全的配置访问
-   错误处理和重试机制

## 🔧 API 参考

### 函数签名

```typescript
function useConfig<T>(key: string, defaultValue: T): [T, (value: T) => Promise<void>, boolean];
```

### 参数

| 参数           | 类型     | 描述         |
| -------------- | -------- | ------------ |
| `key`          | `string` | 配置项的键名 |
| `defaultValue` | `T`      | 默认值       |

### 返回值

返回一个包含三个元素的数组：

| 索引 | 类型                          | 描述           |
| ---- | ----------------------------- | -------------- |
| `0`  | `T`                           | 当前配置值     |
| `1`  | `(value: T) => Promise<void>` | 更新配置的函数 |
| `2`  | `boolean`                     | 是否正在加载   |

## 💡 使用示例

### 基础用法

```jsx
import { useConfig } from '@/hooks/useConfig';

const GeneralSettings = () => {
    const [language, setLanguage] = useConfig('app_language', 'en');
    const [theme, setTheme] = useConfig('app_theme', 'system');
    const [fontSize, setFontSize] = useConfig('app_font_size', 16);

    return (
        <div className='space-y-4'>
            <Select
                label='界面语言'
                selectedKeys={[language]}
                onSelectionChange={(keys) => setLanguage(Array.from(keys)[0])}
            >
                <SelectItem key='en'>English</SelectItem>
                <SelectItem key='zh'>中文</SelectItem>
                <SelectItem key='ja'>日本語</SelectItem>
            </Select>

            <Select
                label='主题'
                selectedKeys={[theme]}
                onSelectionChange={(keys) => setTheme(Array.from(keys)[0])}
            >
                <SelectItem key='light'>浅色</SelectItem>
                <SelectItem key='dark'>深色</SelectItem>
                <SelectItem key='system'>跟随系统</SelectItem>
            </Select>

            <Slider
                label='字体大小'
                value={fontSize}
                onChange={setFontSize}
                min={12}
                max={24}
                step={1}
            />
        </div>
    );
};
```

### 复杂配置对象

```jsx
// 管理复杂配置对象
const TranslationSettings = () => {
    const [translationConfig, setTranslationConfig] = useConfig('translation_config', {
        autoTranslate: false,
        showAlternatives: true,
        saveHistory: true,
        defaultSourceLang: 'auto',
        defaultTargetLang: 'zh',
        services: {
            google: { enabled: true, priority: 1 },
            baidu: { enabled: false, priority: 2 },
        },
    });

    const updateServiceConfig = (serviceName, config) => {
        setTranslationConfig((prev) => ({
            ...prev,
            services: {
                ...prev.services,
                [serviceName]: {
                    ...prev.services[serviceName],
                    ...config,
                },
            },
        }));
    };

    return (
        <div className='space-y-4'>
            <Switch
                isSelected={translationConfig.autoTranslate}
                onValueChange={(value) => setTranslationConfig((prev) => ({ ...prev, autoTranslate: value }))}
            >
                自动翻译
            </Switch>

            <Switch
                isSelected={translationConfig.services.google.enabled}
                onValueChange={(enabled) => updateServiceConfig('google', { enabled })}
            >
                启用 Google 翻译
            </Switch>
        </div>
    );
};
```

### 配置验证

```jsx
// 带验证的配置 Hook
const useValidatedConfig = (key, defaultValue, validator) => {
    const [config, setConfig, isLoading] = useConfig(key, defaultValue);
    const [validationError, setValidationError] = useState(null);

    const updateConfig = useCallback(
        async (newValue) => {
            if (validator) {
                const validation = validator(newValue);
                if (!validation.isValid) {
                    setValidationError(validation.error);
                    return;
                }
            }

            setValidationError(null);
            await setConfig(newValue);
        },
        [setConfig, validator]
    );

    return [config, updateConfig, isLoading, validationError];
};

// 使用示例
const ApiKeySettings = () => {
    const [apiKey, setApiKey, isLoading, error] = useValidatedConfig('openai_api_key', '', (value) => ({
        isValid: value.startsWith('sk-'),
        error: value ? '无效的 OpenAI API 密钥格式' : null,
    }));

    return (
        <Input
            label='OpenAI API Key'
            value={apiKey}
            onValueChange={setApiKey}
            isInvalid={!!error}
            errorMessage={error}
            isLoading={isLoading}
        />
    );
};
```

### 配置同步

```jsx
// 多组件配置同步
const useSharedConfig = (key, defaultValue) => {
    const [config, setConfig, isLoading] = useConfig(key, defaultValue);

    // 监听配置变化事件
    useEffect(() => {
        const handleConfigChange = (event) => {
            if (event.detail.key === key) {
                // 配置已在其他地方更新，同步本地状态
                setConfig(event.detail.value);
            }
        };

        window.addEventListener('config-changed', handleConfigChange);

        return () => {
            window.removeEventListener('config-changed', handleConfigChange);
        };
    }, [key]);

    const updateConfig = useCallback(
        async (newValue) => {
            await setConfig(newValue);

            // 广播配置变化
            window.dispatchEvent(
                new CustomEvent('config-changed', {
                    detail: { key, value: newValue },
                })
            );
        },
        [key, setConfig]
    );

    return [config, updateConfig, isLoading];
};
```

## 🎯 高级用法

### 配置迁移

```jsx
// 配置版本迁移
const useMigratedConfig = (key, defaultValue, migrations = {}) => {
    const [rawConfig, setRawConfig, isLoading] = useConfig(key, null);

    const migratedConfig = useMemo(() => {
        if (!rawConfig) return defaultValue;

        let config = rawConfig;
        const currentVersion = config._version || 1;

        // 应用迁移
        Object.keys(migrations)
            .map(Number)
            .filter((version) => version > currentVersion)
            .sort()
            .forEach((version) => {
                config = migrations[version](config);
                config._version = version;
            });

        return config;
    }, [rawConfig, defaultValue, migrations]);

    const setConfig = useCallback(
        async (newValue) => {
            const valueWithVersion = {
                ...newValue,
                _version: Math.max(...Object.keys(migrations).map(Number), 1),
            };
            await setRawConfig(valueWithVersion);
        },
        [setRawConfig, migrations]
    );

    return [migratedConfig, setConfig, isLoading];
};
```

### 配置备份和恢复

```jsx
// 配置备份 Hook
const useConfigBackup = () => {
    const createBackup = useCallback(async () => {
        const allConfig = await invoke('export_all_config');
        const backup = {
            version: '3.0.7',
            timestamp: new Date().toISOString(),
            config: allConfig,
        };

        const blob = new Blob([JSON.stringify(backup, null, 2)], {
            type: 'application/json',
        });

        const url = URL.createObjectURL(blob);
        const a = document.createElement('a');
        a.href = url;
        a.download = `pot-config-backup-${Date.now()}.json`;
        a.click();

        URL.revokeObjectURL(url);
    }, []);

    const restoreBackup = useCallback(async (file) => {
        const text = await file.text();
        const backup = JSON.parse(text);

        if (backup.version !== '3.0.7') {
            throw new Error('不兼容的备份版本');
        }

        await invoke('import_all_config', { config: backup.config });

        // 刷新页面以应用新配置
        window.location.reload();
    }, []);

    return { createBackup, restoreBackup };
};
```

## 🧪 测试

### Hook 测试

```javascript
import { renderHook, act } from '@testing-library/react';
import { useConfig } from '../useConfig';

// Mock Tauri API
jest.mock('@tauri-apps/api/tauri', () => ({
    invoke: jest.fn(),
}));

describe('useConfig Hook', () => {
    beforeEach(() => {
        jest.clearAllMocks();
    });

    it('should return default value initially', async () => {
        const mockInvoke = require('@tauri-apps/api/tauri').invoke;
        mockInvoke.mockResolvedValue(null);

        const { result } = renderHook(() => useConfig('test_key', 'default'));

        await act(async () => {
            // 等待初始加载完成
        });

        expect(result.current[0]).toBe('default');
        expect(result.current[2]).toBe(false); // isLoading
    });

    it('should update config value', async () => {
        const mockInvoke = require('@tauri-apps/api/tauri').invoke;
        mockInvoke.mockResolvedValue('initial_value');

        const { result } = renderHook(() => useConfig('test_key', 'default'));

        await act(async () => {
            await result.current[1]('new_value');
        });

        expect(mockInvoke).toHaveBeenCalledWith('set_config', {
            key: 'test_key',
            value: 'new_value',
        });
    });

    it('should handle config loading error', async () => {
        const mockInvoke = require('@tauri-apps/api/tauri').invoke;
        mockInvoke.mockRejectedValue(new Error('Config load failed'));

        const { result } = renderHook(() => useConfig('test_key', 'default'));

        await act(async () => {
            // 等待错误处理
        });

        expect(result.current[0]).toBe('default');
    });
});
```

## 📚 相关 Hooks

-   [useSyncAtom](useSyncAtom.md) - 状态同步 Hook
-   [useGetState](useGetState.md) - 状态获取 Hook
-   [useTranslation](useTranslation.md) - 翻译服务 Hook

---

_useConfig Hook 是配置管理的核心，确保了配置的一致性和可靠性。_
