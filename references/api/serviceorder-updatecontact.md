# POST /api/serviceorder/updatecontact

客户更新订单的联系信息与服务地址。

**前缀**：`/api/`（需要登录）

## 请求体

```json
{
  "id": 12345,
  "contact_name": "李四",
  "contact_phone": "13900139000",
  "address": "xx市xx区xx路xx号",
  "lng": 116.397428,
  "lat": 39.90923,
  "province": "北京市",
  "city": "北京市",
  "district": "东城区"
}
```

| 参数 | 类型 | 必填 | 说明 |
|------|------|------|------|
| id | int64 | 是 | 服务单 ID |
| contact_name | string | 是 | 联系人姓名 |
| contact_phone | string | 是 | 联系人手机 |
| address | string | 是 | 详细地址 |
| lng | float | 否 | 经度 |
| lat | float | 否 | 纬度 |
| province | string | 否 | 省份 |
| city | string | 否 | 城市 |
| district | string | 否 | 区县 |

## 响应

`ServiceOrderDetail` 对象。

## 联动

- `lng` / `lat` / `province` / `city` / `district` 建议通过高德 Skill 地理编码 + 逆地理编码获得
- 地址大幅变化可能需要与服务者协商距离/加价问题，建议先沟通
