# GET /api/servicematch/apply

服务者报名客户的服务需求。

**前缀**：`/api/`（需要登录）

## 参数

| 参数 | 类型 | 必填 | 说明 |
|------|------|------|------|
| req_id | int64 | 是 | 需求 ID |

## 响应

`ApplyResult` 对象。

## 业务流程

```
客户发布需求 (servicerequirement/create)
     ↓
平台推送/服务者主动浏览
     ↓
服务者 AI 分析 (servicematch/ai_analysis)
     ↓
服务者报名 (本接口 servicematch/apply)
     ↓
客户选择服务者 → 平台生成订单（status=1 待接单）
     ↓
服务者接单 (servantaccept accept=true) → status=10 进行中
```

## 要点

- 报名后等待客户选择，客户可能选择多个报名者中的任一个
- 报名后可通过 `/api/servicematch/getmatch` 查询匹配状态
- 未被选中时可通过 `/api/servicematch/cancel` 取消报名
