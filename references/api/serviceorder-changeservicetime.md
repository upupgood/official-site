# POST /api/serviceorder/changeservicetime

客户修改订单的服务时间。

**前缀**：`/api/`（需要登录）

## 请求体

```json
{
  "id": 12345,
  "service_time": "2026-04-02T14:00:00+08:00"
}
```

| 参数 | 类型 | 必填 | 说明 |
|------|------|------|------|
| id | int64 | 是 | 服务单 ID |
| service_time | datetime | 是 | 新的服务时间（ISO8601） |

## 响应

`ServiceOrderDetail` 对象。成功后 `service_time` 更新、`change_time_count` 加 1。

## 注意

- 修改次数可能有平台限制（见 `ServiceOrderDetail.change_time_count`）
- 服务者已开始履约后一般不再允许修改时间
- 调整需要变更联系地址时使用 `/api/serviceorder/updatecontact`
