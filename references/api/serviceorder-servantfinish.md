# POST /api/serviceorder/servantfinish

服务者标记服务已完成。客户仍需调用 `/api/serviceorder/userconfirm` 验收后才进入结算。

**前缀**：`/api/`（需要登录）

## 请求体

```json
{ "id": 12345 }
```

| 参数 | 类型 | 必填 | 说明 |
|------|------|------|------|
| id | int64 | 是 | 服务单 ID |

## 响应

`ServiceOrderDetail` 对象，`finish_time` 被填充。

## 状态流转

```
servantfinish                → status 保持 10（进行中），但标记服务者已完成
     ↓
客户 userconfirm             → status = 20（已完成）
     ↓
平台规则进入结算              → settle_status: 1 → 2 → 3
     ↓
T+1 到账
```

## 注意

- 仅 `status=10`（进行中）可调用
- 调用前确认客户已支付（见 `/api/serviceorder/servantpayinfo`）
- 调用后不可撤销；如误操作需联系客服
