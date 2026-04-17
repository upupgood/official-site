# GET /open/discovery/top-product-types

获取平台支持的服务产品类型列表，用于后续搜索时指定 `type_id`。

**前缀**：`/open/`（无需登录）

## 参数

| 参数 | 类型 | 必填 | 说明 |
|------|------|------|------|
| lng | float | 是 | 用户经度 |
| lat | float | 是 | 用户纬度 |

## 响应

产品类型数组：

```json
[
  { "id": 1, "name": "保洁", "good_price_count": 10, "product_count": 50 },
  { "id": 2, "name": "搬运", "good_price_count": 5, "product_count": 30 }
]
```

| 字段 | 说明 |
|------|------|
| id | 类型 ID，用于后续接口的 `type_id` 参数 |
| name | 类型名称 |
| good_price_count | 该类型下好价产品数量 |
| product_count | 该类型下产品总数 |
