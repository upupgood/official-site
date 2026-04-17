# POST /api/serviceorder/cancel

客户取消服务单。

**前缀**：`/api/`（需要登录）

## 请求体

```json
{
  "id": 12345,
  "reason": "not-need",
  "note": "临时有事"
}
```

| 参数 | 类型 | 必填 | 说明 |
|------|------|------|------|
| id | int64 | 是 | 服务单 ID |
| reason | string | 否 | 取消原因：`not-need` / `cant-serve` / `system-cancel` |
| note | string | 否 | 取消原因备注 |

## 响应

`ServiceOrderDetail` 对象。取消成功后 `status=90`（已取消）。

## 注意

- 此接口为**客户视角**的取消
- 服务者如需拒绝待接单订单应调用 `/api/serviceorder/servantaccept` 并设置 `accept=false`
- 已接单并在进行中的订单由服务者单方面取消需走客服流程，不走本接口
