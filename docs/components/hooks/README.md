# Hooks 文档

自定义 React Hooks 提供可复用的状态逻辑和副作用管理。

## 📋 Hooks 列表

### 配置管理

-   [useConfig](useConfig.md) - 应用配置管理
-   [useSyncAtom](useSyncAtom.md) - 原子状态同步
-   [useGetState](useGetState.md) - 状态获取工具

### 服务集成

-   [useTranslation](useTranslation.md) - 翻译服务集成
-   [useOCR](useOCR.md) - OCR 服务集成
-   [useTTS](useTTS.md) - 语音合成服务
-   [useVoice](useVoice.md) - 语音录制和播放

### 系统集成

-   [useClipboard](useClipboard.md) - 剪贴板操作
-   [useHotkey](useHotkey.md) - 全局快捷键
-   [useTheme](useTheme.md) - 主题管理
-   [useNotification](useNotification.md) - 系统通知

### UI 状态

-   [useToastStyle](useToastStyle.md) - Toast 样式管理
-   [useModal](useModal.md) - 模态框控制
-   [useLoading](useLoading.md) - 加载状态管理

## 🎯 设计原则

### 单一职责

每个 Hook 应该只负责一个特定的功能：

```jsx
// ✅ 好的设计 - 单一职责
const useClipboard = () => {
    const copyToClipboard = useCallback(async (text) => {
        await navigator.clipboard.writeText(text);
    }, []);

    const readFromClipboard = useCallback(async () => {
        return await navigator.clipboard.readText();
    }, []);

    return { copyToClipboard, readFromClipboard };
};

// ❌ 不好的设计 - 职责过多
const useEverything = () => {
    // 包含翻译、配置、剪贴板等多种功能
};
```

### 可组合性

Hooks 应该能够与其他 Hooks 组合使用：

```jsx
// ✅ 可组合的 Hooks
const useTranslationWithHistory = () => {
    const { translate } = useTranslation();
    const { addToHistory } = useHistory();

    const translateAndSave = useCallback(
        async (params) => {
            const result = await translate(params);
            await addToHistory(result);
            return result;
        },
        [translate, addToHistory]
    );

    return { translateAndSave };
};
```

### 错误处理

```jsx
// 统一的错误处理模式
const useTranslation = () => {
    const [isLoading, setIsLoading] = useState(false);
    const [error, setError] = useState(null);

    const translate = useCallback(async (params) => {
        setIsLoading(true);
        setError(null);

        try {
            const result = await translateAPI(params);
            return result;
        } catch (err) {
            setError(err.message);
            throw err;
        } finally {
            setIsLoading(false);
        }
    }, []);

    const clearError = useCallback(() => {
        setError(null);
    }, []);

    return { translate, isLoading, error, clearError };
};
```

## 🔧 开发规范

### 命名约定

-   Hook 名称以 `use` 开头
-   使用 camelCase 命名
-   名称应该清晰描述功能

```jsx
// ✅ 好的命名
const useTranslationService = () => {};
const useClipboardOperations = () => {};
const useThemeManager = () => {};

// ❌ 不好的命名
const useUtils = () => {};
const useStuff = () => {};
const useData = () => {};
```

### 返回值设计

#### 对象形式 vs 数组形式

```jsx
// ✅ 简单状态使用数组形式（类似 useState）
const useToggle = (initialValue = false) => {
    const [value, setValue] = useState(initialValue);
    const toggle = useCallback(() => setValue((v) => !v), []);

    return [value, toggle];
};

// ✅ 复杂状态使用对象形式
const useTranslation = () => {
    return {
        translate,
        isLoading,
        error,
        history,
        clearError,
        retryLast,
    };
};
```

#### 一致的命名模式

```jsx
// 统一的命名模式
const useAsyncOperation = () => {
    return {
        // 数据
        data,

        // 状态
        isLoading,
        isError,
        isSuccess,

        // 错误
        error,

        // 操作
        execute,
        reset,
        retry,
    };
};
```

### 依赖管理

```jsx
// 正确的依赖数组
const useTranslation = () => {
    const [config] = useConfig();

    const translate = useCallback(
        async (text) => {
            // 使用 config.apiKey
            return await translateAPI(text, config.apiKey);
        },
        [config.apiKey]
    ); // 只依赖实际使用的属性

    return { translate };
};
```

## 🧪 测试策略

### Hook 测试

```javascript
import { renderHook, act } from '@testing-library/react';
import { useConfig } from '../useConfig';

describe('useConfig Hook', () => {
    it('should return default value initially', () => {
        const { result } = renderHook(() => useConfig('test_key', 'default_value'));

        expect(result.current[0]).toBe('default_value');
    });

    it('should update config value', async () => {
        const { result } = renderHook(() => useConfig('test_key', 'default_value'));

        await act(async () => {
            await result.current[1]('new_value');
        });

        expect(result.current[0]).toBe('new_value');
    });
});
```

### 集成测试

```javascript
// 测试 Hook 与组件的集成
const TestComponent = () => {
    const [value, setValue] = useConfig('test_key', 'default');

    return (
        <div>
            <span data-testid='value'>{value}</span>
            <button onClick={() => setValue('updated')}>Update</button>
        </div>
    );
};

describe('useConfig Integration', () => {
    it('should work with components', async () => {
        render(<TestComponent />);

        expect(screen.getByTestId('value')).toHaveTextContent('default');

        await userEvent.click(screen.getByRole('button'));

        expect(screen.getByTestId('value')).toHaveTextContent('updated');
    });
});
```

## 📊 性能优化

### 避免不必要的重渲染

```jsx
// 使用 useMemo 缓存计算结果
const useFilteredData = (data, filter) => {
    const filteredData = useMemo(() => {
        return data.filter((item) => item.name.toLowerCase().includes(filter.toLowerCase()));
    }, [data, filter]);

    return filteredData;
};

// 使用 useCallback 缓存函数
const useAsyncAction = () => {
    const [isLoading, setIsLoading] = useState(false);

    const execute = useCallback(async (action) => {
        setIsLoading(true);
        try {
            await action();
        } finally {
            setIsLoading(false);
        }
    }, []);

    return { execute, isLoading };
};
```

### 内存泄漏防护

```jsx
// 正确清理副作用
const useWebSocket = (url) => {
    const [socket, setSocket] = useState(null);
    const [isConnected, setIsConnected] = useState(false);

    useEffect(() => {
        const ws = new WebSocket(url);

        ws.onopen = () => setIsConnected(true);
        ws.onclose = () => setIsConnected(false);

        setSocket(ws);

        // 清理函数
        return () => {
            ws.close();
            setSocket(null);
            setIsConnected(false);
        };
    }, [url]);

    return { socket, isConnected };
};
```

## 📚 最佳实践

### 自定义 Hook 模板

```jsx
// 标准 Hook 模板
const useCustomHook = (param1, param2, options = {}) => {
    // 1. 状态定义
    const [state, setState] = useState(initialState);
    const [isLoading, setIsLoading] = useState(false);
    const [error, setError] = useState(null);

    // 2. 副作用
    useEffect(() => {
        // 初始化逻辑
        return () => {
            // 清理逻辑
        };
    }, [param1, param2]);

    // 3. 操作函数
    const action = useCallback(
        async (...args) => {
            setIsLoading(true);
            setError(null);

            try {
                const result = await performAction(...args);
                setState(result);
                return result;
            } catch (err) {
                setError(err.message);
                throw err;
            } finally {
                setIsLoading(false);
            }
        },
        [
            /* 依赖 */
        ]
    );

    // 4. 返回值
    return {
        // 数据
        data: state,

        // 状态
        isLoading,
        error,

        // 操作
        action,
        reset: () => setState(initialState),
        clearError: () => setError(null),
    };
};
```

### Hook 组合模式

```jsx
// 高阶 Hook 模式
const withAsyncState = (asyncFunction) => {
    return (...args) => {
        const [data, setData] = useState(null);
        const [isLoading, setIsLoading] = useState(false);
        const [error, setError] = useState(null);

        const execute = useCallback(async (...executeArgs) => {
            setIsLoading(true);
            setError(null);

            try {
                const result = await asyncFunction(...executeArgs);
                setData(result);
                return result;
            } catch (err) {
                setError(err);
                throw err;
            } finally {
                setIsLoading(false);
            }
        }, []);

        return { data, isLoading, error, execute };
    };
};

// 使用高阶 Hook
const useTranslation = withAsyncState(translateAPI);
const useOCR = withAsyncState(ocrAPI);
```

---

_Hooks 是 React 应用状态逻辑复用的核心机制，遵循 Hooks 规则和最佳实践非常重要。_
