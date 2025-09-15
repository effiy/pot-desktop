# LanguageSelect 组件

LanguageSelect 是用于选择语言的下拉选择器组件。

## 📋 概述

LanguageSelect 组件专门用于语言选择场景，集成了语言标识、国旗图标和多语言显示名称，为用户提供直观的语言选择体验。

### 何时使用

-   翻译功能中选择源语言和目标语言
-   应用设置中选择界面语言
-   OCR 功能中指定识别语言
-   TTS 功能中选择语音语言

## 🔧 API 参考

### 属性

| 属性              | 类型                     | 默认值              | 描述                 |
| ----------------- | ------------------------ | ------------------- | -------------------- |
| `value`           | `string`                 | -                   | 当前选中的语言代码   |
| `onChange`        | `(code: string) => void` | -                   | 语言变化回调函数     |
| `languages`       | `Language[]`             | -                   | 可选语言列表         |
| `placeholder`     | `string`                 | `'Select language'` | 占位符文本           |
| `size`            | `SelectSize`             | `'md'`              | 选择器尺寸           |
| `variant`         | `SelectVariant`          | `'bordered'`        | 选择器样式变体       |
| `isDisabled`      | `boolean`                | `false`             | 是否禁用             |
| `allowAutoDetect` | `boolean`                | `false`             | 是否允许自动检测选项 |
| `showFlag`        | `boolean`                | `true`              | 是否显示国旗图标     |
| `className`       | `string`                 | -                   | 自定义 CSS 类名      |

### 类型定义

```typescript
interface Language {
    code: string; // ISO 639-1 语言代码
    name: string; // 语言显示名称
    nativeName?: string; // 语言原生名称
    flag?: string; // 国旗 emoji 或图标
    region?: string; // 地区标识
}

type SelectSize = 'sm' | 'md' | 'lg';
type SelectVariant = 'flat' | 'bordered' | 'faded' | 'underlined';
```

## 💡 使用示例

### 基础用法

```jsx
import LanguageSelect from '@/components/molecules/LanguageSelect';

const [sourceLanguage, setSourceLanguage] = useState('en');
const [targetLanguage, setTargetLanguage] = useState('zh');

const languages = [
    { code: 'en', name: 'English', nativeName: 'English', flag: '🇺🇸' },
    { code: 'zh', name: 'Chinese', nativeName: '中文', flag: '🇨🇳' },
    { code: 'ja', name: 'Japanese', nativeName: '日本語', flag: '🇯🇵' },
    { code: 'ko', name: 'Korean', nativeName: '한국어', flag: '🇰🇷' },
];

<div className='flex gap-4'>
    <LanguageSelect
        value={sourceLanguage}
        onChange={setSourceLanguage}
        languages={languages}
        placeholder='选择源语言'
        allowAutoDetect
    />

    <LanguageSelect
        value={targetLanguage}
        onChange={setTargetLanguage}
        languages={languages}
        placeholder='选择目标语言'
    />
</div>;
```

### 翻译场景用法

```jsx
const TranslationLanguageSelector = () => {
    const { t } = useTranslation();
    const [config, setConfig] = useConfig();
    const { supportedLanguages } = useSupportedLanguages();

    const handleSourceLanguageChange = (code) => {
        setConfig((prev) => ({
            ...prev,
            sourceLanguage: code,
        }));
    };

    const handleTargetLanguageChange = (code) => {
        setConfig((prev) => ({
            ...prev,
            targetLanguage: code,
        }));
    };

    return (
        <div className='grid grid-cols-1 md:grid-cols-2 gap-4'>
            <LanguageSelect
                value={config.sourceLanguage}
                onChange={handleSourceLanguageChange}
                languages={supportedLanguages}
                placeholder={t('translation.select_source_language')}
                allowAutoDetect
                size='lg'
            />

            <LanguageSelect
                value={config.targetLanguage}
                onChange={handleTargetLanguageChange}
                languages={supportedLanguages}
                placeholder={t('translation.select_target_language')}
                size='lg'
            />
        </div>
    );
};
```

### 自定义渲染

```jsx
// 自定义语言项渲染
<LanguageSelect
    value={selectedLanguage}
    onChange={setSelectedLanguage}
    languages={languages}
    renderValue={(items) => {
        return items.map((item) => (
            <div
                key={item.key}
                className='flex items-center gap-2'
            >
                <span className='text-2xl'>{item.data.flag}</span>
                <div className='flex flex-col'>
                    <span className='font-medium'>{item.data.name}</span>
                    <span className='text-xs text-gray-500'>{item.data.nativeName}</span>
                </div>
            </div>
        ));
    }}
    renderItem={(language) => (
        <div className='flex items-center gap-3 p-2'>
            <span className='text-xl'>{language.flag}</span>
            <div className='flex flex-col'>
                <span className='font-medium'>{language.name}</span>
                <span className='text-sm text-gray-500'>{language.nativeName}</span>
                <span className='text-xs text-gray-400'>{language.code}</span>
            </div>
        </div>
    )}
/>
```

### 带搜索功能

```jsx
const SearchableLanguageSelect = () => {
    const [searchValue, setSearchValue] = useState('');
    const [languages] = useState(allLanguages);

    const filteredLanguages = useMemo(() => {
        if (!searchValue) return languages;

        return languages.filter(
            (lang) =>
                lang.name.toLowerCase().includes(searchValue.toLowerCase()) ||
                lang.nativeName?.toLowerCase().includes(searchValue.toLowerCase()) ||
                lang.code.toLowerCase().includes(searchValue.toLowerCase())
        );
    }, [languages, searchValue]);

    return (
        <LanguageSelect
            value={selectedLanguage}
            onChange={setSelectedLanguage}
            languages={filteredLanguages}
            placeholder='搜索或选择语言...'
            onSearchChange={setSearchValue}
            searchValue={searchValue}
            isSearchable
        />
    );
};
```

## 🌍 国际化支持

### 语言数据结构

```javascript
// 标准语言数据
export const SUPPORTED_LANGUAGES = [
    {
        code: 'auto',
        name: 'Auto Detect',
        nativeName: '自动检测',
        flag: '🔍',
        isAutoDetect: true,
    },
    {
        code: 'en',
        name: 'English',
        nativeName: 'English',
        flag: '🇺🇸',
        region: 'US',
    },
    {
        code: 'zh-cn',
        name: 'Chinese (Simplified)',
        nativeName: '简体中文',
        flag: '🇨🇳',
        region: 'CN',
    },
    {
        code: 'zh-tw',
        name: 'Chinese (Traditional)',
        nativeName: '繁體中文',
        flag: '🇹🇼',
        region: 'TW',
    },
    {
        code: 'ja',
        name: 'Japanese',
        nativeName: '日本語',
        flag: '🇯🇵',
        region: 'JP',
    },
    {
        code: 'ko',
        name: 'Korean',
        nativeName: '한국어',
        flag: '🇰🇷',
        region: 'KR',
    },
    // ... 更多语言
];
```

### 语言工具函数

```javascript
// 语言工具函数
export const LanguageUtils = {
    // 根据代码获取语言信息
    getLanguageByCode(code) {
        return SUPPORTED_LANGUAGES.find((lang) => lang.code === code);
    },

    // 获取语言显示名称
    getDisplayName(code, locale = 'en') {
        const language = this.getLanguageByCode(code);
        if (!language) return code;

        if (locale === 'native') {
            return language.nativeName || language.name;
        }

        return language.name;
    },

    // 获取语言国旗
    getFlag(code) {
        const language = this.getLanguageByCode(code);
        return language?.flag || '🌐';
    },

    // 按地区分组语言
    groupByRegion(languages) {
        const groups = {};

        languages.forEach((lang) => {
            const region = lang.region || 'Other';
            if (!groups[region]) {
                groups[region] = [];
            }
            groups[region].push(lang);
        });

        return groups;
    },

    // 搜索语言
    searchLanguages(languages, query) {
        const lowerQuery = query.toLowerCase();

        return languages.filter(
            (lang) =>
                lang.name.toLowerCase().includes(lowerQuery) ||
                lang.nativeName?.toLowerCase().includes(lowerQuery) ||
                lang.code.toLowerCase().includes(lowerQuery)
        );
    },
};
```

## 🎨 样式定制

### 自定义样式

```jsx
// 紧凑样式
<LanguageSelect
    className="min-w-[150px]"
    size="sm"
    variant="flat"
    languages={languages}
/>

// 大尺寸样式
<LanguageSelect
    className="min-w-[200px]"
    size="lg"
    variant="bordered"
    languages={languages}
/>
```

### 主题适配

```jsx
// 深色主题适配
<LanguageSelect
    className='
        bg-white dark:bg-gray-800
        border-gray-300 dark:border-gray-600
        text-gray-900 dark:text-gray-100
    '
    languages={languages}
/>
```

## 🧪 测试

### 单元测试

```javascript
describe('LanguageSelect Component', () => {
    const mockLanguages = [
        { code: 'en', name: 'English', flag: '🇺🇸' },
        { code: 'zh', name: 'Chinese', flag: '🇨🇳' },
    ];

    it('should render language options', () => {
        render(
            <LanguageSelect
                languages={mockLanguages}
                value=''
                onChange={() => {}}
            />
        );

        fireEvent.click(screen.getByRole('button'));

        expect(screen.getByText('English')).toBeInTheDocument();
        expect(screen.getByText('Chinese')).toBeInTheDocument();
    });

    it('should handle language selection', () => {
        const handleChange = jest.fn();

        render(
            <LanguageSelect
                languages={mockLanguages}
                value=''
                onChange={handleChange}
            />
        );

        fireEvent.click(screen.getByRole('button'));
        fireEvent.click(screen.getByText('English'));

        expect(handleChange).toHaveBeenCalledWith('en');
    });

    it('should show auto detect option when enabled', () => {
        render(
            <LanguageSelect
                languages={mockLanguages}
                allowAutoDetect
                value=''
                onChange={() => {}}
            />
        );

        fireEvent.click(screen.getByRole('button'));

        expect(screen.getByText('Auto Detect')).toBeInTheDocument();
    });
});
```

## 📚 相关组件

-   [Select](../atoms/Select.md) - 基础选择器
-   [SearchBox](SearchBox.md) - 搜索输入框
-   [ServiceSelect](ServiceSelect.md) - 服务选择器

---

_LanguageSelect 组件是翻译功能的核心组件，如有改进建议请提交 Issue。_
