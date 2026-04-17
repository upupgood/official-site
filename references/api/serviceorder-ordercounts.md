# GET /api/serviceorder/ordercounts

客户查询各状态订单数量（一次返回全部状态）。

**前缀**：`/api/`（需要登录）

## 参数

无

## 响应

```json
[
  { "status": 1,  "count": 2 },
  { "status": 10, "count": 1 },
  { "status": 20, "count": 5 },
  { "status": 90, "count": 3 }
]
```

| status | 含义 |
|--------|------|
| 0 | 未生效 |
| 1 | 待接单 |
| 10 | 进行中 |
| 20 | 已完成 |
| 90 | 已取消 |

## 用途

- 展示订单概览："你有 2 个待接单、1 个进行中、5 个已完成、3 个已取消"
- 对比服务者端请使用 `/api/serviceorder/servantordercounts`
