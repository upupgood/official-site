# GET /api/servicematch/ai_analysis

服务者获取指定需求的 AI 匹配分析（预估服务时长、预估收益、是否推荐等）。

**前缀**：`/api/`（需要登录）

## 参数

| 参数 | 类型 | 必填 | 说明 |
|------|------|------|------|
| req_id | int64 | 是 | 需求 ID |

## 响应

`AIAnalysisResult`，主要字段：

| 字段 | 说明 |
|------|------|
| analysis_result | AI 分析结果文本（自然语言描述） |
| estimated_duration | 预估服务时长 |
| estimated_income | 预估收益 |

## 使用场景

- 服务者决策是否 `/api/servicematch/apply` 报名该需求前，先看 AI 建议
- 服务者工作台上对推送的需求做快速筛选
