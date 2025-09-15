# Button 组件

Button 是最基础的交互组件，用于触发操作和导航。

## 📋 概述

Button 组件基于 NextUI Button 构建，提供了统一的按钮样式和交互行为，支持多种样式变体、尺寸和状态。

### 何时使用

-   用户需要执行操作时（如提交表单、确认操作）
-   导航到其他页面或视图时
-   触发功能或切换状态时

### 何时不使用

-   纯展示文本时（使用 Text 组件）
-   链接到外部页面时（使用 Link 组件）
-   切换开关状态时（使用 Switch 组件）

## 🔧 API 参考

### 属性

| 属性           | 类型            | 默认值      | 描述                   |
| -------------- | --------------- | ----------- | ---------------------- |
| `children`     | `ReactNode`     | -           | 按钮内容               |
| `variant`      | `ButtonVariant` | `'solid'`   | 按钮样式变体           |
| `size`         | `ButtonSize`    | `'md'`      | 按钮尺寸               |
| `color`        | `ButtonColor`   | `'primary'` | 按钮颜色主题           |
| `isLoading`    | `boolean`       | `false`     | 是否显示加载状态       |
| `isDisabled`   | `boolean`       | `false`     | 是否禁用按钮           |
| `startContent` | `ReactNode`     | -           | 前置内容（通常是图标） |
| `endContent`   | `ReactNode`     | -           | 后置内容（通常是图标） |
| `className`    | `string`        | -           | 自定义 CSS 类名        |
| `onClick`      | `() => void`    | -           | 点击事件处理函数       |

### 类型定义

```typescript
type ButtonVariant = 'solid' | 'bordered' | 'light' | 'flat' | 'faded' | 'shadow' | 'ghost';
type ButtonSize = 'sm' | 'md' | 'lg';
type ButtonColor = 'primary' | 'secondary' | 'success' | 'warning' | 'danger';

interface ButtonProps {
    children: ReactNode;
    variant?: ButtonVariant;
    size?: ButtonSize;
    color?: ButtonColor;
    isLoading?: boolean;
    isDisabled?: boolean;
    startContent?: ReactNode;
    endContent?: ReactNode;
    className?: string;
    onClick?: () => void;
}
```

## 💡 使用示例

### 基础用法

```jsx
import Button from '@/components/atoms/Button';

// 基础按钮
<Button onClick={handleClick}>
    点击我
</Button>

// 主要操作按钮
<Button color="primary" variant="solid">
    确认
</Button>

// 次要操作按钮
<Button color="secondary" variant="bordered">
    取消
</Button>
```

### 带图标的按钮

```jsx
import { MdTranslate, MdContentCopy, MdDownload } from 'react-icons/md';

// 前置图标
<Button startContent={<MdTranslate />}>
    翻译
</Button>

// 后置图标
<Button endContent={<MdDownload />}>
    下载
</Button>

// 仅图标按钮
<Button isIconOnly startContent={<MdContentCopy />} />
```

### 不同尺寸

```jsx
// 小尺寸
<Button size="sm">小按钮</Button>

// 中等尺寸（默认）
<Button size="md">中按钮</Button>

// 大尺寸
<Button size="lg">大按钮</Button>
```

### 样式变体

```jsx
// 实心按钮（默认）
<Button variant="solid">实心</Button>

// 边框按钮
<Button variant="bordered">边框</Button>

// 轻量按钮
<Button variant="light">轻量</Button>

// 扁平按钮
<Button variant="flat">扁平</Button>

// 淡化按钮
<Button variant="faded">淡化</Button>

// 阴影按钮
<Button variant="shadow">阴影</Button>

// 幽灵按钮
<Button variant="ghost">幽灵</Button>
```

### 颜色主题

```jsx
// 主要颜色
<Button color="primary">主要</Button>

// 次要颜色
<Button color="secondary">次要</Button>

// 成功颜色
<Button color="success">成功</Button>

// 警告颜色
<Button color="warning">警告</Button>

// 危险颜色
<Button color="danger">危险</Button>
```

### 状态

```jsx
// 加载状态
<Button isLoading>
    加载中...
</Button>

// 禁用状态
<Button isDisabled>
    已禁用
</Button>

// 加载状态自定义文本
<Button isLoading loadingText="处理中...">
    提交
</Button>
```

### 高级用法

```jsx
// 异步操作按钮
const AsyncButton = () => {
    const [isLoading, setIsLoading] = useState(false);

    const handleAsyncAction = async () => {
        setIsLoading(true);
        try {
            await performAsyncOperation();
            toast.success('操作成功');
        } catch (error) {
            toast.error('操作失败');
        } finally {
            setIsLoading(false);
        }
    };

    return (
        <Button
            isLoading={isLoading}
            onClick={handleAsyncAction}
        >
            执行操作
        </Button>
    );
};

// 确认操作按钮
const ConfirmButton = ({ onConfirm, children, ...props }) => {
    const handleClick = () => {
        if (window.confirm('确定要执行此操作吗？')) {
            onConfirm?.();
        }
    };

    return (
        <Button
            onClick={handleClick}
            {...props}
        >
            {children}
        </Button>
    );
};
```

## 🎨 样式定制

### 自定义样式

```jsx
// 使用 className 自定义样式
<Button className='min-w-[120px] font-bold'>自定义按钮</Button>;

// 使用 CSS-in-JS
const StyledButton = styled(Button)`
    background: linear-gradient(45deg, #ff6b6b, #4ecdc4);
    border: none;

    &:hover {
        transform: translateY(-2px);
        box-shadow: 0 4px 12px rgba(0, 0, 0, 0.15);
    }
`;
```

### 响应式设计

```jsx
<Button className='w-full md:w-auto md:min-w-[120px]'>响应式按钮</Button>
```

### 主题适配

```jsx
// 自动适配主题
<Button
    className='
    bg-white dark:bg-gray-800
    text-gray-900 dark:text-gray-100
    border-gray-300 dark:border-gray-600
'
>
    主题适配按钮
</Button>
```

## ♿ 可访问性

### ARIA 属性

Button 组件自动包含以下可访问性特性：

-   **role="button"**: 明确按钮角色
-   **aria-disabled**: 禁用状态声明
-   **aria-pressed**: 切换按钮的按下状态
-   **aria-label**: 仅图标按钮的标签

### 键盘导航

-   **Enter/Space**: 激活按钮
-   **Tab**: 聚焦到按钮
-   **Shift+Tab**: 反向聚焦

### 使用建议

```jsx
// ✅ 好的可访问性实践
<Button
    aria-label="翻译选中的文本"
    startContent={<MdTranslate />}
>
    翻译
</Button>

// ✅ 仅图标按钮必须有标签
<Button
    isIconOnly
    aria-label="复制到剪贴板"
    startContent={<MdContentCopy />}
/>

// ❌ 避免的做法
<Button>
    点击这里 {/* 不够具体 */}
</Button>
```

## 🧪 测试

### 单元测试

```javascript
import { render, screen, fireEvent } from '@testing-library/react';
import Button from '../Button';

describe('Button Component', () => {
    it('should render with correct text', () => {
        render(<Button>Test Button</Button>);
        expect(screen.getByRole('button')).toHaveTextContent('Test Button');
    });

    it('should handle click events', () => {
        const handleClick = jest.fn();
        render(<Button onClick={handleClick}>Click me</Button>);

        fireEvent.click(screen.getByRole('button'));
        expect(handleClick).toHaveBeenCalledTimes(1);
    });

    it('should show loading state', () => {
        render(<Button isLoading>Loading</Button>);
        expect(screen.getByRole('button')).toBeDisabled();
    });

    it('should be disabled when isDisabled is true', () => {
        render(<Button isDisabled>Disabled</Button>);
        expect(screen.getByRole('button')).toBeDisabled();
    });
});
```

### 视觉测试

```javascript
import { render } from '@testing-library/react';
import { toMatchImageSnapshot } from 'jest-image-snapshot';

expect.extend({ toMatchImageSnapshot });

describe('Button Visual Tests', () => {
    it('should match default button snapshot', () => {
        const { container } = render(<Button>Default Button</Button>);
        expect(container.firstChild).toMatchImageSnapshot();
    });

    it('should match all variants snapshot', () => {
        const variants = ['solid', 'bordered', 'light', 'flat'];
        const { container } = render(
            <div className='flex gap-2'>
                {variants.map((variant) => (
                    <Button
                        key={variant}
                        variant={variant}
                    >
                        {variant}
                    </Button>
                ))}
            </div>
        );
        expect(container.firstChild).toMatchImageSnapshot();
    });
});
```

## 📊 性能考虑

### 优化建议

1. **避免内联函数**: 使用 useCallback 缓存事件处理函数
2. **减少重渲染**: 使用 React.memo 包装按钮组件
3. **懒加载图标**: 大型图标库使用动态导入

```jsx
// ✅ 性能优化示例
const OptimizedButton = React.memo(({ onClick, children, ...props }) => {
    const handleClick = useCallback(() => {
        onClick?.();
    }, [onClick]);

    return (
        <Button
            onClick={handleClick}
            {...props}
        >
            {children}
        </Button>
    );
});
```

## 🔍 故障排除

### 常见问题

#### 按钮点击无响应

**可能原因**:

-   onClick 函数未正确传递
-   按钮被其他元素遮挡
-   按钮处于禁用状态

**解决方案**:

```jsx
// 检查事件处理函数
<Button onClick={() => console.log('Button clicked')}>
    测试按钮
</Button>

// 检查 z-index 层级
<Button className="relative z-10">
    确保可点击
</Button>
```

#### 样式不生效

**可能原因**:

-   TailwindCSS 类名被覆盖
-   CSS 优先级问题
-   主题配置错误

**解决方案**:

```jsx
// 使用 !important 强制应用样式
<Button className="!bg-red-500">
    强制红色背景
</Button>

// 使用内联样式
<Button style={{ backgroundColor: 'red' }}>
    内联样式
</Button>
```

## 📚 相关组件

-   [IconButton](IconButton.md) - 仅图标按钮
-   [LinkButton](LinkButton.md) - 链接样式按钮
-   [ToolbarButton](../molecules/ToolbarButton.md) - 工具栏按钮

---

_Button 组件是 UI 系统的基础，如有改进建议请提交 Issue 或 PR。_
