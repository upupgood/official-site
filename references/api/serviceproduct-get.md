# GET /open/serviceproduct/get

获取服务产品详情。

**前缀**：`/open/`（无需登录）

## 参数

| 参数 | 类型 | 必填 | 说明 |
|------|------|------|------|
| product_id | int64 | 是 | 服务产品 ID |

## 响应

产品详情对象，主要字段：

| 字段 | 类型 | 说明 |
|------|------|------|
| id | int64 | 产品 ID |
| name | string | 产品名称 |
| price | float | 价格 |
| unit | string | 计价单位（次 / 小时 / 平米 / 月 等） |
| content | string | 产品详情描述 |
| servant_user_id | int64 | 服务者 ID |

## 用途

- 下单前向用户展示产品详情
- 作为 `/api/serviceorder/appoint` 的 `product_id` 来源
- 作为 `/api/serviceorder/wxacreateorderlink` 的输入
