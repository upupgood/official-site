# POST /api/servicerequirement/create

发布服务需求。平台会向匹配的服务者推送通知。

**前缀**：`/api/`（需要登录）

## 请求体

```json
{
  "title": "需要保洁服务",
  "content": "3室2厅深度保洁",
  "address": "xx市xx区xx路xx号",
  "lng": 116.397428,
  "lat": 39.90923,
  "service_time": "2026-04-01 10:00:00",
  "type_id": 1,
  "remark": "家里有宠物"
}
```

| 参数 | 类型 | 必填 | 说明 |
|------|------|------|------|
| title | string | 是 | 需求标题 |
| content | string | 否 | 需求详细内容 |
| address | string | 是 | 服务地址 |
| lng | float | 是 | 经度 |
| lat | float | 是 | 纬度 |
| service_time | string | 是 | 服务时间（`yyyy-MM-dd HH:mm:ss`） |
| type_id | int64 | 否 | 服务类型 ID |
| remark | string | 否 | 备注 |
| service_tags | array | 否 | 服务标签，如 `[{"tag":"面积","values":["100平"]}]` |

## 响应

`ServiceRequirementInfo` 对象（结构见 `SKILL-CUSTOMER.md` 数据结构章节）。

## 联动

- `lng` / `lat` 建议通过高德 Skill 地理编码获得
- 发布后使用 `/api/servicerequirement/list?status=1` 查看进行中需求
- 服务者可通过 `/api/servicematch/apply` 报名该需求
