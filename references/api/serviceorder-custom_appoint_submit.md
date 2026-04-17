# POST /api/serviceorder/custom_appoint_submit

服务者提交专拍（custom appoint）：在聊天场景下为特定客户快速报价下单。**AI 同步审核**，通过后自动创建订单并推送双端聊天卡片。

**前缀**：`/api/`（需要登录）

## 请求体

```json
{
  "chat_user_id": 67890,
  "product_name": "上门深度保洁 3 室 2 厅",
  "product_price": 388.00
}
```

| 参数 | 类型 | 必填 | 说明 |
|------|------|------|------|
| chat_user_id | int64 | 是 | 客户用户 ID（聊天对方） |
| product_name | string | 是 | 专拍商品名称（1–30 字符） |
| product_price | float | 是 | 专拍价格（0, 800] |

## 响应

**审核通过**：
```json
{
  "review_status": "approved",
  "order_id": 98765
}
```

**审核未通过**：
```json
{
  "review_status": "rejected",
  "reject_reason": "商品描述不合规"
}
```

| 字段 | 说明 |
|------|------|
| review_status | `approved` / `rejected` |
| order_id | 仅 approved 时返回，已创建的订单 ID |
| reject_reason | 仅 rejected 时返回 |

## 要点

- **`approved` 与 `rejected` 均返回 HTTP 200**，`rejected` 不是 HTTP 错误
- 价格上限 **800 元**，超出需走标准下单流程
- 商品名称需合规，避免违禁词
- 审核通过后订单立即创建，双端聊天卡片自动推送
- 如需修改：使用 `/api/serviceorder/custom_appoint_update_price`
- 如需撤回：使用 `/api/serviceorder/custom_appoint_cancel`
