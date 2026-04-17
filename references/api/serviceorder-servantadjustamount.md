# POST /api/serviceorder/servantadjustamount

服务者调整订单金额（如客户临时增加工作量）。

**前缀**：`/api/`（需要登录）

## 请求体

```json
{
  "id": 12345,
  "order_amount": 256.00,
  "price_unit": "次",
  "quantity": 1
}
```

| 参数 | 类型 | 必填 | 说明 |
|------|------|------|------|
| id | int64 | 是 | 服务单 ID |
| order_amount | float | 是 | 调整后的订单金额 |
| price_unit | string | 否 | 价格单位 |
| quantity | int | 否 | 数量 |

## 响应

`ServiceOrderDetail` 对象，`order_amount` 已更新。

## 适用场景

- 客户临时增加房间数、面积、时长
- 发现额外工作（如客户家具需要移动等）

## 注意

- 调价应**事先与客户沟通并取得同意**，调价本身不自动通知客户
- 客户可在小程序内查看新金额，如不同意可联系客服
- 平台建议：调价幅度较大时，通过即时通讯说明原因
