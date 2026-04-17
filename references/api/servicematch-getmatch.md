# GET /api/servicematch/getmatch

服务者获取自己在某个需求中的匹配信息。

**前缀**：`/api/`（需要登录）

## 参数

| 参数 | 类型 | 必填 | 说明 |
|------|------|------|------|
| req_id | int64 | 是 | 需求 ID |

## 响应

`MatchInfo` 对象，主要字段：

| 字段 | 说明 |
|------|------|
| status | 匹配状态：0=未通知, 1=已通知, 2=已报名, 3=服务者已拒绝, 4=客户已拒绝, 5=已下单, 6=未下单 |
| apply_time | 报名时间 |
| inform_time | 通知时间 |
| ai_suggestion | AI 匹配建议摘要 |

## 用途

- 查询报名状态：是否已被客户看到、是否已被拒绝、是否已生成订单
- 若 `status=5`（已下单），可通过 `/api/serviceorder/servantlist` 查找对应订单
