# POST /api/serviceorder/appoint

客户直接下单。无需跳转小程序。

**前缀**：`/api/`（需要登录）

## 请求体

```json
{
  "product_id": 12345,
  "servant_user_id": 67890,
  "contact_name": "张三",
  "contact_phone": "13800138000",
  "address": "xx市xx区xx路xx号",
  "service_time": "2026-04-01T10:00:00+08:00",
  "quantity": 1,
  "note": "请准时到达",
  "lng": 116.397428,
  "lat": 39.90923,
  "province": "北京市",
  "city": "北京市",
  "district": "东城区"
}
```

| 参数 | 类型 | 必填 | 说明 |
|------|------|------|------|
| product_id | int64 | 是 | 产品 ID |
| servant_user_id | int64 | 是 | 服务者 ID |
| contact_name | string | 是 | 联系人姓名 |
| contact_phone | string | 是 | 联系人手机 |
| address | string | 是 | 详细地址 |
| service_time | datetime | 是 | 服务时间（ISO8601） |
| req_id | int64 | 否 | 关联需求 ID |
| quantity | int | 否 | 数量 |
| note | string | 否 | 备注 |
| remind | string | 否 | 叮嘱内容 |
| lng | float | 否 | 经度 |
| lat | float | 否 | 纬度 |
| province | string | 否 | 省份 |
| city | string | 否 | 城市 |
| district | string | 否 | 区县 |

## 响应

`ServiceOrderDetail` 对象。

## 联动

- 下单后订单初始 `status=1`（待接单），等待服务者 `/api/serviceorder/servantaccept`
- `province` / `city` / `district` 建议通过高德 Skill 逆地理编码从 lng/lat 解析
- 替代方案：使用 `/api/serviceorder/wxacreateorderlink` 生成跳转链接让用户在小程序内下单
