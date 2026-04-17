# GET /api/serviceorder/servantget

服务者查询订单详情。

**前缀**：`/api/`（需要登录）

## 参数

| 参数 | 类型 | 必填 | 说明 |
|------|------|------|------|
| id | int64 | 是 | 服务单 ID |

## 响应

`ServiceOrderDetail` 对象。

## 服务者关注字段

| 字段 | 用途 |
|------|------|
| `status` | 判断订单所处阶段 |
| `service_time` | 服务时间（用于排程） |
| `address` / `lng` / `lat` | 服务地址（接单后可见完整地址） |
| `contact_name` / `contact_phone` | 客户联系方式（接单后可见） |
| `order_amount` | 订单金额 |
| `note` / `remind` | 客户备注与叮嘱 |
| `settle_status` | 结算状态 |
