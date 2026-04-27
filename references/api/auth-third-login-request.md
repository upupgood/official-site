# GET /open/auth/third-login-request

创建第三方扫码登录请求。Agent 调用后拿到二维码 URL，引导用户用微信扫码授权。

**前缀**：`/open/`（无需登录）

## 参数

无

## 响应

```json
{
  "request_id": "<uuid>",
  "qr_url": "weixin://dl/business/?t=xxx"
}
```

| 字段 | 类型 | 说明 |
|------|------|------|
| request_id | string | 登录请求 ID，后续用于轮询状态 |
| qr_url | string | 微信登录链接（微信小程序 URL Scheme） |

## 使用方式

- Agent 将 `qr_url` 展示在微信中，用户点击该链接进行登录
- 用户用微信登录后自动进入小程序，小程序登录时会携带 `request_id` 完成授权
- 随后使用 `/open/auth/third-login-status?request_id=xxx` 轮询登录状态

## 约束

- 单个 `request_id` 有效期 5 分钟
- 超时后需重新调用本接口
