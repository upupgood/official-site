# POST /api/servicerequirement/cancel

取消服务需求。**仅支持 `status=0`（未提交）或 `status=1`（进行中）的需求**，已匹配或已完成的需求不可取消。

**前缀**：`/api/`（需要登录）

## 请求体

```json
{
  "id": 12345,
  "cancel_reason": "不需要了",
  "cancel_note": "已自行解决"
}
```

| 参数 | 类型 | 必填 | 说明 |
|------|------|------|------|
| id | int64 | 是 | 需求 ID |
| cancel_reason | string | 是 | 取消原因 |
| cancel_note | string | 否 | 取消备注 |

## 响应

```json
{ "success": true }
```

## 注意

- 已接单（`status=2`）的需求需改走订单取消流程：`/api/serviceorder/cancel`
