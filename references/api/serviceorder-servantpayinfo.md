# GET /api/serviceorder/servantpayinfo

服务者查询订单的支付信息。

**前缀**：`/api/`（需要登录）

## 参数

| 参数 | 类型 | 必填 | 说明 |
|------|------|------|------|
| id | int64 | 是 | 服务单 ID |

## 响应

支付状态相关字段，包括但不限于：

| 字段 | 说明 |
|------|------|
| pay_status | 支付状态：0=未支付, 1=支付中, 2=支付成功, 3=支付取消, 4=支付失败 |
| pay_amount | 实付金额 |
| pay_time | 支付时间 |

## 用途

- 服务者在接单 / 履约前确认客户是否已支付
- 对账 / 结算准备
- 与客户端 `/api/serviceorder/payinfo` 对应

## 结算链路

```
客户支付 → pay_status=2
  ↓
服务完成（servantfinish）+ 客户验收（userconfirm）
  ↓
按平台规则进入结算（ServiceOrderDetail.settle_status: 1→2→3）
  ↓
T+1 到账
```
