# 上上吉邻工 Agent Skill — 客户端索引

客户端 API 索引文档。本文件只列出 API 的**功能**与**文件名**，不含参数/请求体/响应细节。

**获取某个 API 的完整定义**：

- WebFetch `https://shangshangji.cn/references/api/<api-file-name>`
- 其中 `<api-file-name>` 即下方表格中的 "API 文件名" 列
- 若本地 `references/api/<api-file-name>` 存在则优先读取本地文件
- 详见上级 [SKILL.md](../SKILL.md) 的 "API 详情获取方式" 章节

> 认证登录、身份识别、错误处理、平台费用、高德 Skill 联动见上级 [SKILL.md](../SKILL.md)。
>
> 本文档假定 Token 已就绪并存放于 `~/.upupgood/.token`。

## API 列表

### 一、服务发现（无需登录）

| 功能 | API 文件名 |
|------|-----------|
| 查询服务类型列表 | discovery-top-product-types.md |
| 搜索附近服务者 | discovery-recommend-servant-list.md |
| 获取服务产品详情 | serviceproduct-get.md |

### 二、服务需求（需登录）

| 功能 | API 文件名 |
|------|-----------|
| 创建服务需求 | servicerequirement-create.md |
| 查询需求列表 | servicerequirement-list.md |
| 取消服务需求 | servicerequirement-cancel.md |

### 三、下单与订单管理（需登录）

| 功能 | API 文件名 |
|------|-----------|
| 预约下单（直接下单） | serviceorder-appoint.md |
| 生成小程序下单链接 | serviceorder-wxacreateorderlink.md |
| 查询订单列表 | serviceorder-list.md |
| 查询订单详情 | serviceorder-get.md |
| 生成小程序订单查看链接 | serviceorder-wxagetorderlink.md |
| 客户取消订单 | serviceorder-cancel.md |
| 验收订单（确认完成） | serviceorder-userconfirm.md |
| 查询各状态订单数量 | serviceorder-ordercounts.md |
| 修改服务时间 | serviceorder-changeservicetime.md |
| 更新联系信息 | serviceorder-updatecontact.md |

## 数据结构

### ServiceOrderDetail

| 字段 | 类型 | 说明 |
|------|------|------|
| id | int64 | 服务单 ID |
| user_id | int64 | 用户 ID（下单的客户） |
| req_id | int64 | 关联需求 ID |
| status | int | 0=未生效, 1=待接单, 10=进行中, 20=已完成, 90=已取消 |
| product_id | int64 | 产品 ID |
| product_name | string | 产品名称 |
| product_price | float | 产品价格 |
| price_unit | string | 价格单位 |
| quantity | int | 数量 |
| order_amount | float | 订单金额 |
| service_time | string | 服务时间 |
| servant_user_id | int64 | 服务者 ID |
| servant_name | string | 服务者姓名 |
| contact_name | string | 联系人姓名 |
| contact_phone | string | 联系人手机 |
| address | string | 详细地址 |
| lng / lat | float | 经度 / 纬度 |
| province / city / district | string | 省 / 市 / 区 |
| service_distance | float | 服务距离（km） |
| note | string | 备注 |
| remind | string | 叮嘱内容 |
| create_time / modify_time | string | 创建 / 修改时间 |
| accept_time / finish_time / confirm_time | string | 接单 / 完成 / 确认时间 |
| cancel_time | string | 取消时间 |
| cancel_reason / cancel_note | string | 取消原因 / 备注 |
| settle_status | int | 结算：0=无需结算, 1=待结算, 2=结算中, 3=已结算 |
| has_evaluation | bool | 是否有评价 |
| change_time_count | int | 修改时间次数 |

### ServiceRequirementInfo

| 字段 | 类型 | 说明 |
|------|------|------|
| id | int64 | 需求 ID |
| user_id | int64 | 用户 ID |
| title | string | 需求标题 |
| content | string | 需求内容 |
| address | string | 服务地址 |
| lng / lat | float | 经度 / 纬度 |
| service_time | string | 服务时间 |
| type_id | int64 | 服务类型 ID |
| remark | string | 备注 |
| status | int | 0=未提交, 1=进行中, 2=已接单, 3=已完成, 9=已取消 |
| tags | array | 服务标签 |
| priority | int | 优先级 |
| cancel_reason / cancel_note | string | 取消原因 / 备注 |
| cancel_time | datetime | 取消时间 |
| images | array | 图片列表 |
| create_time | datetime | 创建时间 |

## 典型使用流程

### 流程一：搜索服务并下单

```
1. top-product-types              → 获取服务类型列表
2. recommend-servant-list         → 按关键词 + 位置搜索服务者
3. serviceproduct/get             → 查看产品详情
4. serviceorder/appoint           → 直接下单（API）, 然后生成小程序订单查看链接
   或
4. serviceorder/wxacreateorderlink → 生成小程序下单链接
```

### 流程二：发布需求等待匹配

```
1. servicerequirement/create        → 发布需求
2. servicerequirement/list?status=1 → 查看进行中的需求
   → 等待服务者报名（服务者用 servicematch/apply）
   → 客户选择后平台生成订单，从订单接口查询
```

### 流程三：订单全生命周期

```
1. serviceorder/list?status=10      → 查看进行中的订单
2. serviceorder/get?id=xxx          → 订单详情
3. serviceorder/changeservicetime   → 如需调整服务时间
4. serviceorder/userconfirm         → 服务完成后验收
   或 serviceorder/cancel           → 必要时取消
```
