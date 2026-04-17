# GET /api/serviceorder/wxacreateorderlink

生成微信小程序下单页面的跳转链接。用户点击后可直接在微信中打开小程序下单页。

**前缀**：`/api/`（需要登录）

## 参数

| 参数 | 类型 | 必填 | 说明 |
|------|------|------|------|
| product_id | int64 | 是 | 服务产品 ID |

## 响应

```json
{ "link": "weixin://dl/business/?t=xxx" }
```

## 使用方式

- 将 `link` 展示给用户（文本或按钮），用户点击跳转小程序：
  `pages/CustomerOrderPages/CreateOrder/CreateOrder?productId={product_id}`
- 适合场景：AI 不方便代客户填写所有下单信息时，把用户引导回小程序完成填单

## 与 `/api/serviceorder/appoint` 的差异

| 维度 | wxacreateorderlink | appoint |
|------|-------------------|---------|
| 用户体验 | 跳转小程序填表 | Agent 全程代操作 |
| 信息收集 | 用户在小程序内填写 | Agent 预先收集好所有字段 |
| 推荐场景 | 复杂产品、首次下单 | 熟客、信息齐全 |
