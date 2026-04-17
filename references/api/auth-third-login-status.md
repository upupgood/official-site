# GET /open/auth/third-login-status

轮询扫码登录状态。用户扫码确认后返回 JWT Token。

**前缀**：`/open/`（无需登录）

## 参数

| 参数 | 类型 | 必填 | 说明 |
|------|------|------|------|
| request_id | string | 是 | 由 `/open/auth/third-login-request` 返回的登录请求 ID |

## 响应

```json
{
  "status": "pending",
  "token": ""
}
```

| status 值 | 含义 | token |
|-----------|------|-------|
| `pending` | 等待用户扫码 | 空 |
| `confirmed` | 用户已确认 | JWT token |
| `expired` | 请求已过期（5 分钟） | 空 |

## 使用方式

- 建议轮询间隔 2–3 秒，最多轮询 5 分钟
- 收到 `confirmed` 后保存 token 到 `~/.upupgood/.token`，后续所有 `/api/` 请求带 `Authorization: Bearer <token>`
- 收到 `expired` 需重新调用 `/open/auth/third-login-request`

## 安全约束

- Token 不得明文打印到日志或展示给用户
- Token 仅用于上上吉域下的 API，不得向第三方泄露
