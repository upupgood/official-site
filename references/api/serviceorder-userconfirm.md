# POST /api/serviceorder/userconfirm

客户确认服务完成（验收）。服务者完成服务（`servantfinish`）后需客户验收才进入结算流程。

**前缀**：`/api/`（需要登录）

## 请求体

```json
{ "id": 12345 }
```

| 参数 | 类型 | 必填 | 说明 |
|------|------|------|------|
| id | int64 | 是 | 服务单 ID |

## 响应

`ServiceOrderDetail` 对象。确认后 `status=20`（已完成），随后按平台规则进入结算（`settle_status`: 1→2→3）。

## 完成流程

```
服务者 /api/serviceorder/servantfinish      → 标记已完成，等待客户确认
客户   /api/serviceorder/userconfirm        → 确认验收，订单 status=20
                                              ↓
                                            进入结算流程 T+1 到账
```
