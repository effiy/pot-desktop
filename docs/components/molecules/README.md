# 分子组件 (Molecules)

分子组件是由原子组件组合而成的功能单元，具有特定的用途和交互逻辑。

## 📋 组件列表

### 表单组件

-   [LanguageSelect](LanguageSelect.md) - 语言选择器
-   [ServiceSelect](ServiceSelect.md) - 服务选择器
-   [SearchBox](SearchBox.md) - 搜索输入框
-   [ConfigItem](ConfigItem.md) - 配置项组件
-   [FormField](FormField.md) - 表单字段组件

### 卡片组件

-   [ServiceCard](ServiceCard.md) - 服务卡片
-   [HistoryCard](HistoryCard.md) - 历史记录卡片
-   [FeatureCard](FeatureCard.md) - 功能介绍卡片

### 导航组件

-   [ToolbarButton](ToolbarButton.md) - 工具栏按钮
-   [TabItem](TabItem.md) - 标签页项目
-   [BreadcrumbItem](BreadcrumbItem.md) - 面包屑项目

### 状态组件

-   [StatusIndicator](StatusIndicator.md) - 状态指示器
-   [ConnectionStatus](ConnectionStatus.md) - 连接状态
-   [LoadingCard](LoadingCard.md) - 加载卡片

## 🎯 设计原则

### 功能聚合

分子组件将相关的原子组件组合在一起，形成有意义的功能单元：

```jsx
// ✅ 好的分子组件 - 功能聚合
const SearchBox = ({ onSearch, placeholder }) => {
    const [query, setQuery] = useState('');

    return (
        <div className='flex items-center gap-2'>
            <Input
                value={query}
                onChange={setQuery}
                placeholder={placeholder}
                startContent={<SearchIcon />}
            />
            <Button onClick={() => onSearch(query)}>搜索</Button>
        </div>
    );
};
```

### 内聚性

分子组件内部的元素应该紧密相关：

```jsx
// ✅ 高内聚的组件
const LanguageSelect = () => {
    // 语言选择相关的所有逻辑都在这里
    return (
        <Select
            startContent={<FlagIcon />}
            placeholder='选择语言'
        >
            {languages.map((lang) => (
                <SelectItem key={lang.code}>{lang.name}</SelectItem>
            ))}
        </Select>
    );
};
```

### 可配置性

通过属性提供灵活的配置选项：

```jsx
const ServiceCard = ({ service, showStatus = true, showActions = true, size = 'md', onConfigure, onTest }) => {
    return (
        <Card size={size}>
            <CardBody>
                <ServiceInfo service={service} />
                {showStatus && <ServiceStatus service={service} />}
                {showActions && (
                    <ServiceActions
                        onConfigure={onConfigure}
                        onTest={onTest}
                    />
                )}
            </CardBody>
        </Card>
    );
};
```

## 🔧 开发指南

### 组件组合

#### 组合模式

```jsx
// 使用组合模式构建分子组件
const ConfigItem = ({ label, description, children, error, required = false }) => {
    return (
        <div className='space-y-2'>
            <div className='flex items-center gap-2'>
                <Text weight='medium'>{label}</Text>
                {required && <Text color='danger'>*</Text>}
            </div>

            {description && (
                <Text
                    size='sm'
                    color='secondary'
                >
                    {description}
                </Text>
            )}

            <div className='space-y-1'>
                {children}
                {error && (
                    <Text
                        size='sm'
                        color='danger'
                    >
                        {error}
                    </Text>
                )}
            </div>
        </div>
    );
};

// 使用
<ConfigItem
    label='API 密钥'
    description='从服务提供商获取的认证密钥'
    required
    error={apiKeyError}
>
    <Input
        type='password'
        value={apiKey}
        onChange={setApiKey}
        placeholder='请输入 API 密钥'
    />
</ConfigItem>;
```

#### 状态管理

```jsx
// 分子组件的状态管理
const SearchBox = ({ onSearch, debounceMs = 300 }) => {
    const [query, setQuery] = useState('');
    const [isSearching, setIsSearching] = useState(false);

    // 防抖搜索
    const debouncedSearch = useMemo(
        () =>
            debounce(async (searchQuery) => {
                if (!searchQuery.trim()) return;

                setIsSearching(true);
                try {
                    await onSearch(searchQuery);
                } finally {
                    setIsSearching(false);
                }
            }, debounceMs),
        [onSearch, debounceMs]
    );

    useEffect(() => {
        debouncedSearch(query);
    }, [query, debouncedSearch]);

    return (
        <Input
            value={query}
            onValueChange={setQuery}
            placeholder='搜索...'
            startContent={isSearching ? <Loading size='sm' /> : <SearchIcon />}
            endContent={
                query && (
                    <Button
                        isIconOnly
                        variant='light'
                        size='sm'
                        onClick={() => setQuery('')}
                    >
                        <CloseIcon />
                    </Button>
                )
            }
        />
    );
};
```

### 事件处理

```jsx
// 事件冒泡和处理
const ServiceCard = ({ service, onToggle, onConfigure }) => {
    const [isEnabled, setIsEnabled] = useState(service.enabled);

    const handleToggle = useCallback(
        async (enabled) => {
            setIsEnabled(enabled);

            try {
                await onToggle?.(service.id, enabled);
            } catch (error) {
                // 回滚状态
                setIsEnabled(!enabled);
                toast.error('操作失败');
            }
        },
        [service.id, onToggle]
    );

    const handleConfigure = useCallback(
        (e) => {
            e.stopPropagation(); // 阻止事件冒泡
            onConfigure?.(service.id);
        },
        [service.id, onConfigure]
    );

    return (
        <Card
            className='cursor-pointer'
            onClick={handleConfigure}
        >
            <CardBody>
                <div className='flex justify-between items-center'>
                    <ServiceInfo service={service} />
                    <Switch
                        isSelected={isEnabled}
                        onValueChange={handleToggle}
                        onClick={(e) => e.stopPropagation()}
                    />
                </div>
            </CardBody>
        </Card>
    );
};
```

## 🧪 测试策略

### 组合测试

测试分子组件时需要验证原子组件的正确组合：

```javascript
describe('ServiceCard Component', () => {
    const mockService = {
        id: 'google-translate',
        name: 'Google Translate',
        description: 'Google 翻译服务',
        enabled: true,
        configured: true,
    };

    it('should render service information', () => {
        render(<ServiceCard service={mockService} />);

        expect(screen.getByText('Google Translate')).toBeInTheDocument();
        expect(screen.getByText('Google 翻译服务')).toBeInTheDocument();
    });

    it('should handle toggle events', async () => {
        const handleToggle = jest.fn();

        render(
            <ServiceCard
                service={mockService}
                onToggle={handleToggle}
            />
        );

        const toggle = screen.getByRole('switch');
        await userEvent.click(toggle);

        expect(handleToggle).toHaveBeenCalledWith('google-translate', false);
    });

    it('should handle configure events', async () => {
        const handleConfigure = jest.fn();

        render(
            <ServiceCard
                service={mockService}
                onConfigure={handleConfigure}
            />
        );

        const configButton = screen.getByRole('button', { name: /配置/ });
        await userEvent.click(configButton);

        expect(handleConfigure).toHaveBeenCalledWith('google-translate');
    });
});
```

### 交互测试

```javascript
describe('SearchBox Interaction', () => {
    it('should debounce search input', async () => {
        const handleSearch = jest.fn();

        render(
            <SearchBox
                onSearch={handleSearch}
                debounceMs={100}
            />
        );

        const input = screen.getByPlaceholderText('搜索...');

        // 快速输入
        await userEvent.type(input, 'test query');

        // 等待防抖
        await waitFor(
            () => {
                expect(handleSearch).toHaveBeenCalledTimes(1);
            },
            { timeout: 200 }
        );

        expect(handleSearch).toHaveBeenCalledWith('test query');
    });
});
```

## 📊 性能考虑

### 渲染优化

```jsx
// 使用 React.memo 优化重渲染
const ServiceCard = React.memo(
    ({ service, onToggle, onConfigure }) => {
        // 组件实现
    },
    (prevProps, nextProps) => {
        // 自定义比较函数
        return (
            prevProps.service.id === nextProps.service.id &&
            prevProps.service.enabled === nextProps.service.enabled &&
            prevProps.service.configured === nextProps.service.configured
        );
    }
);
```

### 事件处理优化

```jsx
// 使用 useCallback 缓存事件处理函数
const LanguageSelect = ({ onLanguageChange }) => {
    const handleChange = useCallback(
        (languageCode) => {
            onLanguageChange?.(languageCode);
        },
        [onLanguageChange]
    );

    return <Select onSelectionChange={handleChange}>{/* 选项 */}</Select>;
};
```

## 📚 最佳实践

### 错误边界

```jsx
// 为分子组件添加错误边界
const SafeServiceCard = ({ service, ...props }) => {
    return (
        <ErrorBoundary
            fallback={
                <Card>
                    <CardBody>
                        <Text color='danger'>服务卡片加载失败</Text>
                    </CardBody>
                </Card>
            }
        >
            <ServiceCard
                service={service}
                {...props}
            />
        </ErrorBoundary>
    );
};
```

### 加载状态

```jsx
// 处理异步加载状态
const AsyncServiceCard = ({ serviceId }) => {
    const { data: service, isLoading, error } = useQuery(['service', serviceId], () => fetchService(serviceId));

    if (isLoading) {
        return <ServiceCardSkeleton />;
    }

    if (error) {
        return <ServiceCardError error={error} />;
    }

    return <ServiceCard service={service} />;
};
```

---

_分子组件通过组合原子组件创建有意义的功能单元，是构建复杂界面的重要基础。_
