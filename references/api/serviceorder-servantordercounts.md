# GET /api/serviceorder/servantordercounts

服务者查询各状态订单数量（一次返回全部）。

**前缀**：`/api/`（需要登录）

## 参数

无

## 响应

```json
[
  { "status": 1,  "count": 2 },
  { "status": 10, "count": 1 },
  { "status": 20, "count": 15 },
  { "status": 90, "count": 2 }
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

- 展示服务者工作台概览："待接单 2 · 进行中 1 · 累计完成 15"
- 相比 `/api/serviceorder/servantordercount`（单状态），本接口一次返回全部状态，是首选概览接口
