# GET /api/serviceorder/wxagetorderlink

生成微信小程序订单详情页的跳转链接。

**前缀**：`/api/`（需要登录）

## 参数

| 参数 | 类型 | 必填 | 说明 |
|------|------|------|------|
| service_order_id | int64 | 是 | 服务单 ID |

## 响应

```json
{ "link": "weixin://dl/business/?t=xxx" }
```

## 使用方式

将 `link` 展示给用户，用户点击后跳转小程序：
`pages/CustomerOrderPages/CustomerOrders/CustomerOrders?activeTab=InProgress&orderId={service_order_id}`

## 适用场景

- 客户需要在小程序内查看订单图片、评价、支付详情等丰富内容
- 订单需在小程序内完成支付操作
