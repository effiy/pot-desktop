# 功能路由和菜单系统

本文档详细说明了 Pot 项目的路由结构和菜单系统的实现。

## 📋 窗口结构概览

Pot 应用包含多个独立的窗口，每个窗口都有自己的功能和路由：

| 窗口名称 | 标识符       | 主要功能       | 路由类型     |
| -------- | ------------ | -------------- | ------------ |
| 配置窗口 | `config`     | 应用设置和配置 | React Router |
| 翻译窗口 | `translate`  | 文本翻译功能   | 单页面       |
| 识别窗口 | `recognize`  | OCR 文字识别   | 单页面       |
| 截图窗口 | `screenshot` | 截图选择       | 单页面       |
| 更新窗口 | `updater`    | 应用更新       | 单页面       |

## 🔧 配置窗口路由系统

配置窗口是最复杂的窗口，使用 React Router 实现多页面导航。

### 路由配置

**文件位置**: `src/window/Config/routes/index.jsx`

```javascript
const routes = [
    {
        path: '/general', // 通用设置
        element: <General />,
    },
    {
        path: '/translate', // 翻译设置
        element: <Translate />,
    },
    {
        path: '/recognize', // 识别设置
        element: <Recognize />,
    },
    {
        path: '/hotkey', // 快捷键设置
        element: <Hotkey />,
    },
    {
        path: '/service', // 服务管理
        element: <Service />,
    },
    {
        path: '/history', // 历史记录
        element: <History />,
    },
    {
        path: '/backup', // 备份设置
        element: <Backup />,
    },
    {
        path: '/about', // 关于页面
        element: <About />,
    },
    {
        path: '/', // 默认重定向
        element: <Navigate to='/general' />,
    },
];
```

### 路由页面详解

#### 1. 通用设置 (`/general`)

**组件**: `src/window/Config/pages/General/index.jsx`

**功能**:

-   应用语言设置
-   主题配置
-   字体设置
-   自动启动
-   代理配置
-   服务器端口
-   透明度设置

**主要配置项**:

```javascript
const [appLanguage, setAppLanguage] = useConfig('app_language', 'en');
const [appTheme, setAppTheme] = useConfig('app_theme', 'system');
const [appFont, setAppFont] = useConfig('app_font', 'default');
const [autoStart, setAutoStart] = useState(false);
const [serverPort, setServerPort] = useConfig('server_port', 60828);
const [transparent, setTransparent] = useConfig('transparent', true);
```

#### 2. 翻译设置 (`/translate`)

**组件**: `src/window/Config/pages/Translate/index.jsx`

**功能**:

-   翻译引擎配置
-   默认语言设置
-   翻译快捷键
-   翻译窗口设置

#### 3. 识别设置 (`/recognize`)

**组件**: `src/window/Config/pages/Recognize/index.jsx`

**功能**:

-   OCR 引擎配置
-   识别语言设置
-   截图 OCR 设置
-   识别结果处理

#### 4. 快捷键设置 (`/hotkey`)

**组件**: `src/window/Config/pages/Hotkey/index.jsx`

**功能**:

-   全局快捷键配置
-   功能快捷键绑定
-   快捷键冲突检测

#### 5. 服务管理 (`/service`)

**组件**: `src/window/Config/pages/Service/index.jsx`

**功能**:

-   翻译服务管理
-   OCR 服务管理
-   TTS 服务管理
-   插件配置

**子路由结构**:

```javascript
// 服务页面包含多个标签页
-翻译服务(Translate) - 识别服务(Recognize) - 语音服务(TTS) - 收藏服务(Collection);
```

#### 6. 历史记录 (`/history`)

**组件**: `src/window/Config/pages/History/index.jsx`

**功能**:

-   翻译历史查看
-   历史记录管理
-   搜索和过滤

#### 7. 备份设置 (`/backup`)

**组件**: `src/window/Config/pages/Backup/index.jsx`

**功能**:

-   配置备份
-   数据同步
-   导入导出

#### 8. 关于页面 (`/about`)

**组件**: `src/window/Config/pages/About/index.jsx`

**功能**:

-   应用信息
-   版本信息
-   开源许可
-   更新检查

## 🎨 侧边栏菜单系统

### 菜单组件

**文件位置**: `src/window/Config/components/SideBar/index.jsx`

### 菜单结构

```javascript
const menuItems = [
    {
        path: '/general',
        icon: <AiFillAppstore />,
        labelKey: 'config.general.label',
        title: '通用设置',
    },
    {
        path: '/translate',
        icon: <PiTranslateFill />,
        labelKey: 'config.translate.label',
        title: '翻译设置',
    },
    {
        path: '/recognize',
        icon: <PiTextboxFill />,
        labelKey: 'config.recognize.label',
        title: '识别设置',
    },
    {
        path: '/hotkey',
        icon: <MdKeyboardAlt />,
        labelKey: 'config.hotkey.label',
        title: '快捷键',
    },
    {
        path: '/service',
        icon: <MdExtension />,
        labelKey: 'config.service.label',
        title: '服务管理',
    },
    {
        path: '/history',
        icon: <FaHistory />,
        labelKey: 'config.history.label',
        title: '历史记录',
    },
    {
        path: '/backup',
        icon: <AiFillCloud />,
        labelKey: 'config.backup.label',
        title: '备份设置',
    },
    {
        path: '/about',
        icon: <BsInfoSquareFill />,
        labelKey: 'config.about.label',
        title: '关于',
    },
];
```

### 菜单图标说明

| 菜单项   | 图标组件           | 图标库           | 含义          |
| -------- | ------------------ | ---------------- | ------------- |
| 通用设置 | `AiFillAppstore`   | Ant Design Icons | 应用程序/设置 |
| 翻译设置 | `PiTranslateFill`  | Phosphor Icons   | 翻译功能      |
| 识别设置 | `PiTextboxFill`    | Phosphor Icons   | 文本框/OCR    |
| 快捷键   | `MdKeyboardAlt`    | Material Design  | 键盘/快捷键   |
| 服务管理 | `MdExtension`      | Material Design  | 扩展/插件     |
| 历史记录 | `FaHistory`        | Font Awesome     | 历史记录      |
| 备份设置 | `AiFillCloud`      | Ant Design Icons | 云存储/备份   |
| 关于     | `BsInfoSquareFill` | Bootstrap Icons  | 信息/关于     |

### 菜单状态管理

```javascript
function setStyle(pathname) {
    return location.pathname.includes(pathname) ? 'flat' : 'light';
}
```

**状态说明**:

-   `flat`: 当前激活的菜单项（深色背景）
-   `light`: 非激活状态（浅色背景）

## 🌍 国际化支持

### 多语言标签

菜单标签通过国际化键值对应：

**配置文件**: `src/i18n/locales/zh_CN.json`

```json
{
    "config": {
        "general": {
            "label": "通用"
        },
        "translate": {
            "label": "翻译"
        },
        "recognize": {
            "label": "识别"
        },
        "hotkey": {
            "label": "快捷键"
        },
        "service": {
            "label": "服务"
        },
        "history": {
            "label": "历史"
        },
        "backup": {
            "label": "备份"
        },
        "about": {
            "label": "关于"
        }
    }
}
```

### 支持的语言

| 语言代码 | 语言名称 | 菜单显示                          |
| -------- | -------- | --------------------------------- |
| `zh_cn`  | 简体中文 | 通用、翻译、识别...               |
| `zh_tw`  | 繁體中文 | 通用、翻譯、識別...               |
| `en`     | English  | General, Translate, Recognize...  |
| `ja`     | 日本語   | 一般、翻訳、認識...               |
| `ko`     | 한국어   | 일반, 번역, 인식...               |
| `fr`     | Français | Général, Traduire, Reconnaître... |
| `es`     | Español  | General, Traducir, Reconocer...   |
| `ru`     | Русский  | Общие, Перевод, Распознавание...  |

## 🔄 路由导航机制

### 编程式导航

```javascript
import { useNavigate } from 'react-router-dom';

const navigate = useNavigate();

// 导航到指定路由
navigate('/general');
navigate('/translate');
```

### 路由状态检测

```javascript
import { useLocation } from 'react-router-dom';

const location = useLocation();

// 检查当前路由
const isActive = location.pathname.includes('/general');
```

### 默认路由

应用启动时默认导航到通用设置页面：

```javascript
{
    path: '/',
    element: <Navigate to='/general' />,
}
```

## 🎛️ 服务管理页面子路由

服务管理页面使用标签页（Tab）方式组织子功能：

### 标签页结构

```javascript
const serviceTabs = [
    {
        key: 'translate',
        label: '翻译服务',
        component: <TranslateService />,
    },
    {
        key: 'recognize',
        label: '识别服务',
        component: <RecognizeService />,
    },
    {
        key: 'tts',
        label: '语音服务',
        component: <TTSService />,
    },
    {
        key: 'collection',
        label: '收藏服务',
        component: <CollectionService />,
    },
];
```

### 服务配置结构

每个服务都有统一的配置结构：

```javascript
{
    "service_name": {
        "title": "服务显示名称",
        "param1": "参数1描述",
        "param2": "参数2描述"
    }
}
```

## 🛠️ 开发指南

### 添加新的路由页面

1. **创建页面组件**

    ```bash
    src/window/Config/pages/NewPage/index.jsx
    ```

2. **添加路由配置**

    ```javascript
    // src/window/Config/routes/index.jsx
    import NewPage from '../pages/NewPage';

    const routes = [
        // ... 现有路由
        {
            path: '/newpage',
            element: <NewPage />,
        },
    ];
    ```

3. **添加菜单项**

    ```javascript
    // src/window/Config/components/SideBar/index.jsx
    <Button
        fullWidth
        size='lg'
        variant={setStyle('/newpage')}
        className='mb-[5px]'
        onPress={() => navigate('/newpage')}
        startContent={<NewIcon className='text-[24px]' />}
    >
        <div className='w-full'>{t('config.newpage.label')}</div>
    </Button>
    ```

4. **添加国际化标签**
    ```json
    // src/i18n/locales/zh_CN.json
    {
        "config": {
            "newpage": {
                "label": "新页面"
            }
        }
    }
    ```

### 路由守卫和权限控制

目前项目没有复杂的权限控制，但可以通过以下方式实现：

```javascript
// 路由守卫示例
function ProtectedRoute({ children, condition }) {
    return condition ? children : <Navigate to="/general" />;
}

// 在路由配置中使用
{
    path: '/advanced',
    element: (
        <ProtectedRoute condition={devMode}>
            <AdvancedPage />
        </ProtectedRoute>
    ),
}
```

### 路由参数传递

```javascript
// URL 参数
navigate('/service?tab=translate');

// 状态传递
navigate('/history', {
    state: { searchQuery: 'hello' },
});

// 接收参数
const location = useLocation();
const searchParams = new URLSearchParams(location.search);
const tab = searchParams.get('tab');
```

## 🐛 常见问题

### 路由不生效

1. 检查路由配置是否正确
2. 确认组件导入路径
3. 验证路由路径拼写

### 菜单样式异常

1. 检查 CSS 类名是否正确
2. 确认主题配置
3. 验证图标组件导入

### 国际化显示异常

1. 检查翻译键值是否存在
2. 确认语言文件格式
3. 验证 i18n 配置

## 📚 相关文档

-   [项目结构](project-structure.md) - 整体项目结构
-   [开发环境搭建](development-setup.md) - 开发环境配置
-   [配置 API](../api/config-api.md) - 配置系统 API

---

_本文档详细说明了 Pot 项目的路由和菜单系统，如有疑问请参考相关源码或提交 Issue。_
