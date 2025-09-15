# Input 组件

Input 是用于用户文本输入的基础表单组件。

## 📋 概述

Input 组件基于 NextUI Input 构建，提供了统一的输入框样式和验证功能，支持多种输入类型和状态。

### 何时使用

-   收集用户输入的文本信息
-   表单数据录入
-   搜索功能输入
-   配置参数设置

## 🔧 API 参考

### 属性

| 属性            | 类型                       | 默认值       | 描述                   |
| --------------- | -------------------------- | ------------ | ---------------------- |
| `label`         | `string`                   | -            | 输入框标签             |
| `placeholder`   | `string`                   | -            | 占位符文本             |
| `value`         | `string`                   | -            | 受控组件的值           |
| `defaultValue`  | `string`                   | -            | 非受控组件的默认值     |
| `onChange`      | `(e: ChangeEvent) => void` | -            | 值变化回调（原生事件） |
| `onValueChange` | `(value: string) => void`  | -            | 值变化回调（仅值）     |
| `type`          | `InputType`                | `'text'`     | 输入类型               |
| `size`          | `InputSize`                | `'md'`       | 输入框尺寸             |
| `variant`       | `InputVariant`             | `'bordered'` | 输入框样式变体         |
| `color`         | `InputColor`               | `'primary'`  | 主题颜色               |
| `isRequired`    | `boolean`                  | `false`      | 是否必填               |
| `isDisabled`    | `boolean`                  | `false`      | 是否禁用               |
| `isReadOnly`    | `boolean`                  | `false`      | 是否只读               |
| `isInvalid`     | `boolean`                  | `false`      | 是否显示错误状态       |
| `errorMessage`  | `string`                   | -            | 错误信息               |
| `description`   | `string`                   | -            | 描述信息               |
| `startContent`  | `ReactNode`                | -            | 前置内容               |
| `endContent`    | `ReactNode`                | -            | 后置内容               |

### 类型定义

```typescript
type InputType = 'text' | 'password' | 'email' | 'number' | 'url' | 'search' | 'tel';
type InputSize = 'sm' | 'md' | 'lg';
type InputVariant = 'flat' | 'bordered' | 'faded' | 'underlined';
type InputColor = 'primary' | 'secondary' | 'success' | 'warning' | 'danger';
```

## 💡 使用示例

### 基础用法

```jsx
import Input from '@/components/atoms/Input';

// 基础输入框
<Input
    label="用户名"
    placeholder="请输入用户名"
    value={username}
    onValueChange={setUsername}
/>

// 必填输入框
<Input
    label="邮箱地址"
    placeholder="请输入邮箱"
    type="email"
    isRequired
    value={email}
    onValueChange={setEmail}
/>
```

### 带验证的输入框

```jsx
const [email, setEmail] = useState('');
const [emailError, setEmailError] = useState('');

const validateEmail = (value) => {
    const emailRegex = /^[^\s@]+@[^\s@]+\.[^\s@]+$/;
    if (!emailRegex.test(value)) {
        setEmailError('请输入有效的邮箱地址');
        return false;
    }
    setEmailError('');
    return true;
};

<Input
    label='邮箱地址'
    placeholder='example@domain.com'
    type='email'
    value={email}
    onValueChange={(value) => {
        setEmail(value);
        validateEmail(value);
    }}
    isInvalid={!!emailError}
    errorMessage={emailError}
    description='用于接收通知和找回密码'
/>;
```

### 带图标的输入框

```jsx
import { MdSearch, MdVisibility, MdVisibilityOff } from 'react-icons/md';

// 搜索输入框
<Input
    placeholder='搜索翻译历史...'
    startContent={<MdSearch className='text-gray-400' />}
    value={searchQuery}
    onValueChange={setSearchQuery}
/>;

// 密码输入框
const [isVisible, setIsVisible] = useState(false);

<Input
    label='密码'
    placeholder='请输入密码'
    type={isVisible ? 'text' : 'password'}
    endContent={
        <button
            type='button'
            onClick={() => setIsVisible(!isVisible)}
            className='focus:outline-none'
        >
            {isVisible ? <MdVisibilityOff className='text-gray-400' /> : <MdVisibility className='text-gray-400' />}
        </button>
    }
/>;
```

### 数字输入框

```jsx
const [timeout, setTimeout] = useState(30);

<Input
    label='超时时间'
    placeholder='30'
    type='number'
    value={timeout.toString()}
    onValueChange={(value) => setTimeout(parseInt(value) || 0)}
    endContent={<span className='text-sm text-gray-500'>秒</span>}
    description='请求超时时间，范围 1-300 秒'
    min={1}
    max={300}
/>;
```

### 文本域

```jsx
<Textarea
    label='翻译内容'
    placeholder='请输入要翻译的文本...'
    value={text}
    onValueChange={setText}
    minRows={3}
    maxRows={10}
    description='支持多行文本输入'
/>
```

## 🎨 样式变体

### 不同变体

```jsx
// 扁平样式
<Input variant="flat" placeholder="扁平输入框" />

// 边框样式
<Input variant="bordered" placeholder="边框输入框" />

// 淡化样式
<Input variant="faded" placeholder="淡化输入框" />

// 下划线样式
<Input variant="underlined" placeholder="下划线输入框" />
```

### 颜色主题

```jsx
// 不同颜色主题
<Input color="primary" placeholder="主要颜色" />
<Input color="secondary" placeholder="次要颜色" />
<Input color="success" placeholder="成功颜色" />
<Input color="warning" placeholder="警告颜色" />
<Input color="danger" placeholder="危险颜色" />
```

## ♿ 可访问性

### ARIA 属性

-   **aria-label**: 输入框标签
-   **aria-describedby**: 关联描述信息
-   **aria-invalid**: 验证状态
-   **aria-required**: 必填状态

### 键盘导航

-   **Tab**: 聚焦到输入框
-   **Shift+Tab**: 反向聚焦
-   **Escape**: 清除输入（可选）

### 最佳实践

```jsx
// ✅ 良好的可访问性
<Input
    label="API 密钥"
    placeholder="请输入您的 API 密钥"
    type="password"
    isRequired
    aria-describedby="api-key-help"
    errorMessage={error}
    description="从服务提供商获取的认证密钥"
/>

// ✅ 关联帮助信息
<div id="api-key-help" className="text-sm text-gray-600">
    API 密钥用于身份验证，请妥善保管
</div>
```

## 🧪 测试

### 单元测试

```javascript
describe('Input Component', () => {
    it('should render with label and placeholder', () => {
        render(
            <Input
                label='测试标签'
                placeholder='测试占位符'
            />
        );

        expect(screen.getByLabelText('测试标签')).toBeInTheDocument();
        expect(screen.getByPlaceholderText('测试占位符')).toBeInTheDocument();
    });

    it('should handle value changes', () => {
        const handleChange = jest.fn();
        render(
            <Input
                value=''
                onValueChange={handleChange}
            />
        );

        const input = screen.getByRole('textbox');
        fireEvent.change(input, { target: { value: 'test input' } });

        expect(handleChange).toHaveBeenCalledWith('test input');
    });

    it('should show error state', () => {
        render(
            <Input
                isInvalid
                errorMessage='这是错误信息'
            />
        );

        expect(screen.getByText('这是错误信息')).toBeInTheDocument();
        expect(screen.getByRole('textbox')).toHaveAttribute('aria-invalid', 'true');
    });
});
```

### 集成测试

```javascript
describe('Input Integration', () => {
    it('should work in form context', () => {
        const handleSubmit = jest.fn();

        render(
            <form onSubmit={handleSubmit}>
                <Input
                    name='username'
                    label='用户名'
                />
                <Button type='submit'>提交</Button>
            </form>
        );

        const input = screen.getByLabelText('用户名');
        fireEvent.change(input, { target: { value: 'testuser' } });
        fireEvent.click(screen.getByRole('button', { name: '提交' }));

        expect(handleSubmit).toHaveBeenCalled();
    });
});
```

## 🔧 自定义 Hook

### useInput Hook

```javascript
// 自定义输入框 Hook
export const useInput = (initialValue = '', validator = null) => {
    const [value, setValue] = useState(initialValue);
    const [error, setError] = useState('');
    const [isTouched, setIsTouched] = useState(false);

    const handleChange = useCallback(
        (newValue) => {
            setValue(newValue);
            setIsTouched(true);

            if (validator) {
                const validation = validator(newValue);
                setError(validation.error || '');
            }
        },
        [validator]
    );

    const reset = useCallback(() => {
        setValue(initialValue);
        setError('');
        setIsTouched(false);
    }, [initialValue]);

    return {
        value,
        error,
        isTouched,
        isValid: !error && isTouched,
        onChange: handleChange,
        reset,
        inputProps: {
            value,
            onValueChange: handleChange,
            isInvalid: !!error && isTouched,
            errorMessage: error,
        },
    };
};

// 使用示例
const MyForm = () => {
    const username = useInput('', (value) => ({
        error: value.length < 3 ? '用户名至少 3 个字符' : '',
    }));

    return (
        <Input
            label='用户名'
            placeholder='请输入用户名'
            {...username.inputProps}
        />
    );
};
```

## 📚 相关组件

-   [Textarea](Textarea.md) - 多行文本输入
-   [Select](Select.md) - 下拉选择器
-   [SearchBox](../molecules/SearchBox.md) - 搜索输入框

---

_Input 组件文档会随着功能更新持续完善，欢迎反馈使用问题和改进建议。_
