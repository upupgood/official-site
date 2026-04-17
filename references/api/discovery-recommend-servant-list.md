# GET /open/discovery/recommend-servant-list

根据关键词与位置搜索附近的服务者和产品（客户端搜索主入口）。

**前缀**：`/open/`（无需登录）

## 参数

| 参数 | 类型 | 必填 | 说明 |
|------|------|------|------|
| lng | float | 是 | 经度 |
| lat | float | 是 | 纬度 |
| name | string | 否 | 搜索关键词 |
| type_id | int64 | 否 | 产品类型 ID（见 `/open/discovery/top-product-types`） |
| category_id | int64 | 否 | 产品分类 ID |
| sort_type | string | 否 | 排序方式：`default` / `distance` / `goodprice` / `radio_vip` / `active` |
| scene | string | 否 | 场景：`search`（搜索）/ `goodprice`（好价） |
| from | int64 | 否 | 分页游标 |

## 响应

推荐服务者数组。每项包含服务者基本信息（ID、姓名、VIP 级别、评分、距离）和关联产品列表（产品 ID、名称、价格、单位）。

## 常见流程

```
1. 高德 Skill 地理编码将 "朝阳区" 转为 lng/lat
2. /open/discovery/top-product-types                  → 拿到 type_id（如 "保洁"=1）
3. /open/discovery/recommend-servant-list?type_id=1   → 获取候选服务者
4. /open/serviceproduct/get?product_id=xxx            → 查看具体产品详情
5. /api/serviceorder/appoint                          → 下单
```
