# GET /api/serviceorder/servantordercount

服务者查询指定状态下订单数量。

**前缀**：`/api/`（需要登录）

## 参数

| 参数 | 类型 | 必填 | 说明 |
|------|------|------|------|
| status | int | 是 | 订单状态：`0`=未生效, `1`=待接单, `10`=进行中, `20`=已完成, `90`=已取消 |
| finished | bool | 否 | 是否已完成（待客户确认） |

## 响应

整数类型的数量。

## 用途

- 精确查询单个状态的订单数量
- 与 `/api/serviceorder/servantordercounts`（返回全部状态数量）相对，后者无需多次调用即可拿到完整概览

## 组合用法

- `status=1, finished=false` → 待接单数量
- `status=10, finished=false` → 进行中未标记完成数量
- `status=10, finished=true` → 已标记完成、等待客户确认数量
