# TranslationPanel 组件

TranslationPanel 是 Pot 应用的核心翻译界面组件，提供完整的文本翻译功能。

## 📋 概述

TranslationPanel 是一个复杂的有机体组件，集成了语言选择、文本输入、翻译操作、结果显示和辅助功能（如语音播放、复制等）。

### 主要功能

-   双语言文本输入和显示
-   语言选择和切换
-   翻译操作和状态管理
-   结果复制和语音播放
-   翻译历史记录
-   错误处理和用户反馈

## 🔧 API 参考

### 属性

| 属性                    | 类型                                  | 默认值   | 描述             |
| ----------------------- | ------------------------------------- | -------- | ---------------- |
| `initialText`           | `string`                              | `''`     | 初始文本内容     |
| `initialSourceLang`     | `string`                              | `'auto'` | 初始源语言       |
| `initialTargetLang`     | `string`                              | `'zh'`   | 初始目标语言     |
| `onTranslationComplete` | `(result: TranslationResult) => void` | -        | 翻译完成回调     |
| `onTextChange`          | `(text: string) => void`              | -        | 文本变化回调     |
| `enableHistory`         | `boolean`                             | `true`   | 是否启用历史记录 |
| `enableTTS`             | `boolean`                             | `true`   | 是否启用语音播放 |
| `enableCopy`            | `boolean`                             | `true`   | 是否启用复制功能 |
| `autoTranslate`         | `boolean`                             | `false`  | 是否自动翻译     |
| `className`             | `string`                              | -        | 自定义 CSS 类名  |

### 类型定义

```typescript
interface TranslationResult {
    sourceText: string;
    targetText: string;
    sourceLanguage: string;
    targetLanguage: string;
    service: string;
    confidence?: number;
    alternatives?: string[];
    timestamp: string;
}

interface TranslationPanelProps {
    initialText?: string;
    initialSourceLang?: string;
    initialTargetLang?: string;
    onTranslationComplete?: (result: TranslationResult) => void;
    onTextChange?: (text: string) => void;
    enableHistory?: boolean;
    enableTTS?: boolean;
    enableCopy?: boolean;
    autoTranslate?: boolean;
    className?: string;
}
```

## 💡 使用示例

### 基础用法

```jsx
import TranslationPanel from '@/components/organisms/TranslationPanel';

// 基础翻译面板
<TranslationPanel />

// 带初始文本的翻译面板
<TranslationPanel
    initialText="Hello World"
    initialSourceLang="en"
    initialTargetLang="zh"
/>
```

### 回调处理

```jsx
const MyTranslationApp = () => {
    const [translationHistory, setTranslationHistory] = useState([]);

    const handleTranslationComplete = (result) => {
        // 保存到历史记录
        setTranslationHistory((prev) => [result, ...prev]);

        // 发送分析数据
        analytics.track('translation_completed', {
            sourceLanguage: result.sourceLanguage,
            targetLanguage: result.targetLanguage,
            service: result.service,
            textLength: result.sourceText.length,
        });

        // 显示成功提示
        toast.success('翻译完成');
    };

    const handleTextChange = (text) => {
        // 实时保存草稿
        localStorage.setItem('draft_text', text);
    };

    return (
        <TranslationPanel
            onTranslationComplete={handleTranslationComplete}
            onTextChange={handleTextChange}
            enableHistory
            enableTTS
            autoTranslate={false}
        />
    );
};
```

### 嵌入式使用

```jsx
// 在配置页面中嵌入
const TranslationSettings = () => {
    return (
        <div className='space-y-6'>
            <Card>
                <CardHeader>
                    <h2>翻译测试</h2>
                </CardHeader>
                <CardBody>
                    <TranslationPanel
                        initialText='Test translation'
                        enableHistory={false}
                        className='border-none shadow-none'
                    />
                </CardBody>
            </Card>
        </div>
    );
};
```

### 高级配置

```jsx
const AdvancedTranslationPanel = () => {
    const [config] = useConfig();
    const { availableServices } = useTranslationServices();

    return (
        <TranslationPanel
            initialSourceLang={config.defaultSourceLanguage}
            initialTargetLang={config.defaultTargetLanguage}
            autoTranslate={config.autoTranslate}
            enableTTS={config.enableTTS}
            onTranslationComplete={(result) => {
                // 自动复制结果
                if (config.autoCopy) {
                    navigator.clipboard.writeText(result.targetText);
                }

                // 自动播放语音
                if (config.autoSpeak) {
                    speakText(result.targetText, result.targetLanguage);
                }
            }}
        />
    );
};
```

## 🎨 样式定制

### 布局变体

```jsx
// 垂直布局（默认）
<TranslationPanel className="max-w-4xl mx-auto" />

// 紧凑布局
<TranslationPanel className="max-w-2xl" />

// 全宽布局
<TranslationPanel className="w-full" />
```

### 主题适配

```jsx
// 深色主题优化
<TranslationPanel className="
    bg-gray-900
    border-gray-700
    text-gray-100
" />

// 自定义配色
<TranslationPanel className="
    bg-gradient-to-br from-blue-50 to-indigo-100
    dark:from-gray-900 dark:to-gray-800
" />
```

## 🔧 内部实现

### 状态管理

```jsx
const TranslationPanel = (props) => {
    // 文本状态
    const [sourceText, setSourceText] = useState(props.initialText || '');
    const [targetText, setTargetText] = useState('');

    // 语言状态
    const [sourceLanguage, setSourceLanguage] = useState(props.initialSourceLang || 'auto');
    const [targetLanguage, setTargetLanguage] = useState(props.initialTargetLang || 'zh');

    // UI 状态
    const [isLoading, setIsLoading] = useState(false);
    const [error, setError] = useState(null);
    const [isPlaying, setIsPlaying] = useState(false);

    // 服务状态
    const { translate } = useTranslationService();
    const { speak } = useTTS();
    const { copyToClipboard } = useClipboard();

    // ... 组件逻辑
};
```

### 事件处理

```jsx
// 翻译处理
const handleTranslate = useCallback(async () => {
    if (!sourceText.trim()) return;

    setIsLoading(true);
    setError(null);

    try {
        const result = await translate({
            text: sourceText,
            from: sourceLanguage,
            to: targetLanguage,
        });

        setTargetText(result.text);

        // 触发回调
        props.onTranslationComplete?.(result);

        // 保存历史
        if (props.enableHistory) {
            addToHistory(result);
        }
    } catch (err) {
        setError(err.message);
    } finally {
        setIsLoading(false);
    }
}, [sourceText, sourceLanguage, targetLanguage, translate, props]);

// 语言交换
const handleSwapLanguages = useCallback(() => {
    if (sourceLanguage === 'auto') return;

    setSourceLanguage(targetLanguage);
    setTargetLanguage(sourceLanguage);
    setSourceText(targetText);
    setTargetText(sourceText);
}, [sourceLanguage, targetLanguage, sourceText, targetText]);

// 复制结果
const handleCopyResult = useCallback(async () => {
    if (!targetText) return;

    try {
        await copyToClipboard(targetText);
        toast.success('已复制到剪贴板');
    } catch (err) {
        toast.error('复制失败');
    }
}, [targetText, copyToClipboard]);
```

## ♿ 可访问性

### ARIA 属性

```jsx
<div
    role='region'
    aria-label='翻译面板'
    aria-describedby='translation-help'
>
    <Textarea
        aria-label='源文本输入'
        aria-describedby='source-text-help'
        value={sourceText}
        onChange={setSourceText}
    />

    <Button
        aria-label='开始翻译'
        aria-describedby='translate-help'
        onClick={handleTranslate}
        isLoading={isLoading}
    >
        翻译
    </Button>

    <Textarea
        aria-label='翻译结果'
        aria-describedby='target-text-help'
        value={targetText}
        isReadOnly
    />
</div>
```

### 键盘导航

-   **Tab**: 在输入框、按钮间导航
-   **Ctrl+Enter**: 快速翻译
-   **Ctrl+C**: 复制翻译结果
-   **Ctrl+S**: 保存到历史记录

### 屏幕阅读器支持

```jsx
// 状态播报
useEffect(() => {
    if (isLoading) {
        announceToScreenReader('正在翻译，请稍候...');
    } else if (targetText) {
        announceToScreenReader(`翻译完成：${targetText}`);
    } else if (error) {
        announceToScreenReader(`翻译失败：${error}`);
    }
}, [isLoading, targetText, error]);
```

## 🧪 测试

### 单元测试

```javascript
describe('TranslationPanel Component', () => {
    it('should render input areas', () => {
        render(<TranslationPanel />);

        expect(screen.getByLabelText(/源文本/)).toBeInTheDocument();
        expect(screen.getByLabelText(/翻译结果/)).toBeInTheDocument();
    });

    it('should handle translation', async () => {
        const mockTranslate = jest.fn().mockResolvedValue({
            text: '你好世界',
            from: 'en',
            to: 'zh',
        });

        render(<TranslationPanel />);

        const input = screen.getByLabelText(/源文本/);
        fireEvent.change(input, { target: { value: 'Hello World' } });

        const translateButton = screen.getByRole('button', { name: /翻译/ });
        fireEvent.click(translateButton);

        await waitFor(() => {
            expect(screen.getByDisplayValue('你好世界')).toBeInTheDocument();
        });
    });
});
```

### 集成测试

```javascript
describe('TranslationPanel Integration', () => {
    it('should integrate with translation service', async () => {
        const { result } = renderHook(() => useTranslationService());

        render(<TranslationPanel />);

        // 模拟用户操作
        await userEvent.type(screen.getByLabelText(/源文本/), 'Hello World');

        await userEvent.click(screen.getByRole('button', { name: /翻译/ }));

        // 验证服务调用
        expect(result.current.translate).toHaveBeenCalledWith({
            text: 'Hello World',
            from: 'auto',
            to: 'zh',
        });
    });
});
```

## 📊 性能优化

### React.memo 优化

```jsx
const TranslationPanel = React.memo(
    ({ initialText, onTranslationComplete, ...props }) => {
        // 组件实现
    },
    (prevProps, nextProps) => {
        // 自定义比较函数
        return (
            prevProps.initialText === nextProps.initialText &&
            prevProps.enableHistory === nextProps.enableHistory &&
            prevProps.enableTTS === nextProps.enableTTS
        );
    }
);
```

### 懒加载优化

```jsx
// 懒加载语音功能
const TTSControls = lazy(() => import('./TTSControls'));

const TranslationPanel = () => {
    return (
        <div>
            {/* 主要功能 */}
            <TranslationInputs />

            {/* 按需加载语音控制 */}
            <Suspense fallback={<div>Loading TTS...</div>}>{enableTTS && <TTSControls />}</Suspense>
        </div>
    );
};
```

## 🔍 故障排除

### 常见问题

#### 翻译按钮无响应

**可能原因**:

-   输入文本为空
-   翻译服务未配置
-   网络连接问题

**解决方案**:

```jsx
// 添加调试日志
const handleTranslate = async () => {
    console.log('Translation started:', { sourceText, sourceLanguage, targetLanguage });

    if (!sourceText.trim()) {
        console.warn('Empty text, translation skipped');
        return;
    }

    // ... 翻译逻辑
};
```

#### 语音播放失败

**可能原因**:

-   TTS 服务未启用
-   音频权限被拒绝
-   浏览器不支持

**解决方案**:

```jsx
const handleSpeak = async (text, language) => {
    try {
        await speak(text, language);
    } catch (error) {
        console.error('TTS failed:', error);
        toast.error('语音播放失败，请检查设置');
    }
};
```

## 📚 相关组件

-   [LanguageSelect](../molecules/LanguageSelect.md) - 语言选择器
-   [TextArea](../atoms/TextArea.md) - 文本输入区域
-   [Button](../atoms/Button.md) - 操作按钮
-   [LoadingSpinner](../atoms/LoadingSpinner.md) - 加载指示器

---

_TranslationPanel 是 Pot 的核心组件，承载着主要的用户交互，如有改进建议请提交 Issue。_
