# 外部调用 API

Pot 支持通过多种方式被外部程序调用，实现与其他软件的集成。

## 🚀 调用方式概览

| 调用方式   | 协议 | 端口  | 用途         | 状态      |
| ---------- | ---- | ----- | ------------ | --------- |
| HTTP API   | HTTP | 60828 | Web 服务调用 | ✅ 稳定   |
| 命令行接口 | CLI  | -     | 命令行调用   | ✅ 稳定   |
| WebSocket  | WS   | 60829 | 实时通信     | 🚧 开发中 |

## 🌐 HTTP API

### 基础信息

-   **协议**: HTTP/1.1
-   **默认端口**: 60828
-   **Content-Type**: `application/json`
-   **字符编码**: UTF-8

### 启用 HTTP 服务

在 Pot 设置中启用"外部调用"功能，或通过配置文件：

```json
{
    "external_call": {
        "enabled": true,
        "port": 60828,
        "host": "127.0.0.1"
    }
}
```

### API 端点

#### 1. 翻译文本

**POST** `/translate`

**请求体**:

```json
{
    "text": "Hello World",
    "from": "en",
    "to": "zh",
    "service": "google" // 可选，指定翻译服务
}
```

**响应**:

```json
{
    "code": 200,
    "message": "success",
    "data": {
        "text": "Hello World",
        "from": "en",
        "to": "zh",
        "result": "你好世界",
        "service": "google",
        "timestamp": 1640995200000
    }
}
```

#### 2. 语言检测

**POST** `/detect`

**请求体**:

```json
{
    "text": "Hello World"
}
```

**响应**:

```json
{
    "code": 200,
    "message": "success",
    "data": {
        "text": "Hello World",
        "language": "en",
        "confidence": 0.99
    }
}
```

#### 3. OCR 文字识别

**POST** `/ocr`

**请求体**:

```json
{
    "image": "base64_encoded_image_data",
    "service": "baidu" // 可选，指定 OCR 服务
}
```

**响应**:

```json
{
  "code": 200,
  "message": "success",
  "data": {
    "text": "识别出的文字内容",
    "service": "baidu",
    "confidence": 0.95,
    "regions": [
      {
        "text": "文字块1",
        "bbox": [x, y, width, height],
        "confidence": 0.98
      }
    ]
  }
}
```

#### 4. 获取支持的服务

**GET** `/services`

**响应**:

```json
{
    "code": 200,
    "message": "success",
    "data": {
        "translate": ["google", "baidu", "tencent", "deepl"],
        "ocr": ["baidu", "tencent", "tesseract"],
        "tts": ["edge", "azure"]
    }
}
```

#### 5. 获取支持的语言

**GET** `/languages`

**响应**:

```json
{
    "code": 200,
    "message": "success",
    "data": {
        "languages": [
            { "code": "en", "name": "English" },
            { "code": "zh", "name": "中文" },
            { "code": "ja", "name": "日本語" }
        ]
    }
}
```

### 错误处理

所有 API 在出错时返回统一格式：

```json
{
    "code": 400,
    "message": "参数错误",
    "error": {
        "type": "INVALID_PARAMETER",
        "detail": "缺少必需参数 'text'"
    }
}
```

**常见错误代码**:

-   `400`: 请求参数错误
-   `401`: 认证失败（如果启用了认证）
-   `429`: 请求频率限制
-   `500`: 服务器内部错误
-   `503`: 服务不可用

## 💻 命令行接口

### 基本语法

```bash
pot [命令] [选项] [参数]
```

### 翻译命令

```bash
# 基本翻译
pot translate "Hello World" --from=en --to=zh

# 指定翻译服务
pot translate "Hello World" --from=en --to=zh --service=google

# 从文件翻译
pot translate --file=input.txt --output=output.txt

# 批量翻译
pot translate "Hello" "World" "Test" --to=zh
```

### OCR 命令

```bash
# 截图 OCR
pot ocr --screenshot

# 文件 OCR
pot ocr --file=image.png

# 指定 OCR 服务
pot ocr --file=image.png --service=baidu
```

### 配置命令

```bash
# 查看配置
pot config --get

# 设置配置
pot config --set external_call.enabled=true

# 重置配置
pot config --reset
```

### 服务管理

```bash
# 启动服务
pot service start

# 停止服务
pot service stop

# 重启服务
pot service restart

# 查看状态
pot service status
```

## 📝 使用示例

### JavaScript/Node.js

```javascript
// 使用 fetch API
async function translate(text, from, to) {
    const response = await fetch('http://localhost:60828/translate', {
        method: 'POST',
        headers: {
            'Content-Type': 'application/json',
        },
        body: JSON.stringify({
            text: text,
            from: from,
            to: to,
        }),
    });

    const result = await response.json();
    return result.data.result;
}

// 使用示例
translate('Hello World', 'en', 'zh').then((result) => {
    console.log(result); // 输出: 你好世界
});
```

### Python

```python
import requests
import json

def translate(text, from_lang, to_lang):
    url = 'http://localhost:60828/translate'
    data = {
        'text': text,
        'from': from_lang,
        'to': to_lang
    }

    response = requests.post(url, json=data)
    result = response.json()

    if result['code'] == 200:
        return result['data']['result']
    else:
        raise Exception(f"翻译失败: {result['message']}")

# 使用示例
result = translate('Hello World', 'en', 'zh')
print(result)  # 输出: 你好世界
```

### cURL

```bash
# 翻译请求
curl -X POST http://localhost:60828/translate \
  -H "Content-Type: application/json" \
  -d '{
    "text": "Hello World",
    "from": "en",
    "to": "zh"
  }'

# OCR 请求
curl -X POST http://localhost:60828/ocr \
  -H "Content-Type: application/json" \
  -d '{
    "image": "base64_encoded_image_data"
  }'
```

### PowerShell

```powershell
# 翻译函数
function Invoke-PotTranslate {
    param(
        [string]$Text,
        [string]$From = "auto",
        [string]$To = "zh"
    )

    $body = @{
        text = $Text
        from = $From
        to = $To
    } | ConvertTo-Json

    $response = Invoke-RestMethod -Uri "http://localhost:60828/translate" -Method POST -Body $body -ContentType "application/json"

    return $response.data.result
}

# 使用示例
Invoke-PotTranslate -Text "Hello World" -To "zh"
```

## 🔐 安全考虑

### 访问控制

-   默认只监听本地地址 (127.0.0.1)
-   可以通过配置限制访问来源
-   支持 API Token 认证（可选）

### 配置认证

```json
{
    "external_call": {
        "enabled": true,
        "auth": {
            "enabled": true,
            "token": "your_api_token_here"
        }
    }
}
```

使用认证时，需要在请求头中添加：

```
Authorization: Bearer your_api_token_here
```

### 频率限制

-   默认限制：每分钟 100 次请求
-   可在配置中调整
-   超出限制返回 429 错误

## 🔧 配置选项

完整的外部调用配置：

```json
{
    "external_call": {
        "enabled": true,
        "http": {
            "enabled": true,
            "host": "127.0.0.1",
            "port": 60828,
            "cors": false
        },
        "auth": {
            "enabled": false,
            "token": ""
        },
        "rate_limit": {
            "enabled": true,
            "requests_per_minute": 100
        },
        "allowed_origins": ["*"],
        "timeout": 30000
    }
}
```

## 🐛 故障排除

### 常见问题

**连接被拒绝**

-   检查 Pot 是否运行
-   确认外部调用功能已启用
-   验证端口是否被占用

**请求超时**

-   检查网络连接
-   增加超时时间配置
-   确认翻译服务可用

**认证失败**

-   检查 API Token 是否正确
-   确认请求头格式正确

### 调试技巧

1. **启用调试日志**:

    ```bash
    pot --debug service start
    ```

2. **检查服务状态**:

    ```bash
    curl http://localhost:60828/health
    ```

3. **查看详细错误**:
    ```bash
    curl -v http://localhost:60828/translate -d '{"text":"test"}'
    ```

## 📚 相关文档

-   [插件 API](plugin-api.md) - 插件开发接口
-   [配置 API](config-api.md) - 配置系统接口
-   [用户手册](../user-guides/user-manual.md) - 完整使用指南

---

_如有疑问或发现问题，请在 GitHub 提交 Issue 或查看社区讨论。_
