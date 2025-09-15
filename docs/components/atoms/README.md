# 原子组件 (Atoms)

原子组件是设计系统中最小的构建块，不可再分解的基础 UI 元素。

## 📋 组件列表

### 基础交互组件

-   [Button](Button.md) - 按钮组件，用于触发操作
-   [Input](Input.md) - 文本输入框组件
-   [Switch](Switch.md) - 开关切换组件
-   [Slider](Slider.md) - 滑块选择组件

### 显示组件

-   [Text](Text.md) - 文本显示组件
-   [Icon](Icon.md) - 图标组件
-   [Avatar](Avatar.md) - 头像组件
-   [Badge](Badge.md) - 徽章组件
-   [Chip](Chip.md) - 标签组件

### 反馈组件

-   [Loading](Loading.md) - 加载指示器
-   [Progress](Progress.md) - 进度条组件
-   [Skeleton](Skeleton.md) - 骨架屏组件

### 布局组件

-   [Divider](Divider.md) - 分割线组件
-   [Spacer](Spacer.md) - 间距组件

## 🎯 设计原则

### 单一职责

每个原子组件只负责一个特定的 UI 功能：

```jsx
// ✅ 好的设计 - 单一职责
const Button = ({ children, onClick, variant }) => {
    // 只处理按钮相关逻辑
};

// ❌ 不好的设计 - 职责过多
const ButtonWithEverything = ({ children, onClick, onHover, onFocus, tooltip, modal }) => {
    // 包含太多不相关的功能
};
```

### 高度可复用

原子组件应该在各种场景下都能使用：

```jsx
// ✅ 可复用的设计
<Button variant="solid" color="primary">主要操作</Button>
<Button variant="bordered" color="secondary">次要操作</Button>
<Button variant="ghost" color="danger">危险操作</Button>
```

### 最小 API 表面

只暴露必要的属性，保持 API 简洁：

```jsx
// ✅ 简洁的 API
const Text = ({ children, size, weight, color, className }) => {
    // 简单明了的属性
};
```

## 🔧 开发规范

### 命名约定

-   组件名使用 PascalCase
-   属性名使用 camelCase
-   布尔属性使用 is/has/can 前缀

```jsx
// ✅ 良好的命名
const Button = ({
    isLoading, // 布尔属性
    isDisabled, // 布尔属性
    startContent, // 内容属性
    onClick, // 事件处理
}) => {
    // 组件实现
};
```

### 属性设计

#### 必需属性 vs 可选属性

```jsx
// 明确区分必需和可选属性
interface ButtonProps {
    // 必需属性
    children: ReactNode;

    // 可选属性（提供默认值）
    variant?: 'solid' | 'bordered' | 'ghost';
    size?: 'sm' | 'md' | 'lg';
    color?: 'primary' | 'secondary' | 'danger';
}
```

#### 属性验证

```jsx
import PropTypes from 'prop-types';

Button.propTypes = {
    children: PropTypes.node.isRequired,
    variant: PropTypes.oneOf(['solid', 'bordered', 'ghost']),
    size: PropTypes.oneOf(['sm', 'md', 'lg']),
    onClick: PropTypes.func,
};

Button.defaultProps = {
    variant: 'solid',
    size: 'md',
};
```

### 样式系统

#### 使用设计令牌

```jsx
// 使用 TailwindCSS 设计令牌
const Button = ({ variant, size, color }) => {
    const variants = {
        solid: 'bg-primary text-primary-foreground',
        bordered: 'border-2 border-primary text-primary',
        ghost: 'text-primary hover:bg-primary/10',
    };

    const sizes = {
        sm: 'h-8 px-3 text-sm',
        md: 'h-10 px-4 text-base',
        lg: 'h-12 px-6 text-lg',
    };

    return (
        <button
            className={cn(
                'inline-flex items-center justify-center rounded-md font-medium',
                'transition-colors focus-visible:outline-none focus-visible:ring-2',
                variants[variant],
                sizes[size]
            )}
        >
            {children}
        </button>
    );
};
```

## 🧪 测试策略

### 单元测试

每个原子组件都应该有全面的单元测试：

```jsx
describe('Atom Component Tests', () => {
    // 渲染测试
    it('should render correctly', () => {});

    // 属性测试
    it('should handle all props correctly', () => {});

    // 事件测试
    it('should handle events properly', () => {});

    // 状态测试
    it('should manage internal state', () => {});

    // 边界情况测试
    it('should handle edge cases', () => {});
});
```

### 视觉回归测试

```jsx
// Storybook 故事用于视觉测试
export default {
    title: 'Atoms/Button',
    component: Button,
    parameters: {
        chromatic: {
            viewports: [320, 768, 1200], // 多尺寸截图
        },
    },
};

export const AllVariants = () => (
    <div className='grid grid-cols-3 gap-4'>
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
```

## 📚 使用指南

### 组合使用

原子组件通常与其他组件组合使用：

```jsx
// 在分子组件中使用原子组件
const SearchBox = () => {
    return (
        <div className='flex items-center gap-2'>
            <Input
                placeholder='搜索...'
                startContent={<Icon name='search' />}
            />
            <Button variant='solid'>搜索</Button>
        </div>
    );
};
```

### 主题一致性

确保所有原子组件遵循统一的主题系统：

```jsx
// 主题变量
const theme = {
    colors: {
        primary: 'blue',
        secondary: 'gray',
        success: 'green',
        warning: 'yellow',
        danger: 'red',
    },
    sizes: {
        sm: '0.875rem',
        md: '1rem',
        lg: '1.125rem',
    },
};

// 在组件中使用主题
const Text = ({ size = 'md', color = 'primary' }) => {
    return <span className={`text-${theme.colors[color]} text-${theme.sizes[size]}`}>{children}</span>;
};
```

---

_原子组件是整个设计系统的基础，确保它们的质量和一致性至关重要。_
