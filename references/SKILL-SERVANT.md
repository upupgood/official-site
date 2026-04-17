# 上上吉邻工 Agent Skill — 服务者端索引

服务者端 API 索引文档。本文件只列出 API 的**功能**与**文件名**，不含参数/请求体/响应细节。

**获取某个 API 的完整定义**：

- WebFetch `https://shangshangji.cn/references/api/<api-file-name>`
- 其中 `<api-file-name>` 即下方表格中的 "API 文件名" 列
- 若本地 `references/api/<api-file-name>` 存在则优先读取本地文件
- 详见上级 [SKILL.md](../SKILL.md) 的 "API 详情获取方式" 章节

> 认证登录、身份识别、错误处理、平台费用、高德 Skill 联动见上级 [SKILL.md](../SKILL.md)。
>
> 本文档假定 Token 已就绪并存放于 `~/.upupgood/.token`。所有接口均为 `/api/` 前缀，**需要登录**。

## API 列表

### 一、订单查询

| 功能 | API 文件名 |
|------|-----------|
| 订单列表 | serviceorder-servantlist.md |
| 订单详情 | serviceorder-servantget.md |
| 最新服务单（按服务时间） | serviceorder-servantlatest.md |
| 单状态订单数量 | serviceorder-servantordercount.md |
| 各状态订单数量 | serviceorder-servantordercounts.md |
| 订单支付信息 | serviceorder-servantpayinfo.md |

### 二、接单 / 履约 / 完成

| 功能 | API 文件名 |
|------|-----------|
| 接单 / 拒绝（二合一） | serviceorder-servantaccept.md |
| 完成服务 | serviceorder-servantfinish.md |
| 调整订单金额 | serviceorder-servantadjustamount.md |

### 三、专拍订单（聊天场景快速报价）

| 功能 | API 文件名 |
|------|-----------|
| 提交专拍（AI 同步审核） | serviceorder-custom_appoint_submit.md |
| 未支付前改价 | serviceorder-custom_appoint_update_price.md |
| 未支付前撤回 | serviceorder-custom_appoint_cancel.md |
| 查询上笔专拍商品名 | serviceorder-last_custom_name.md |

### 四、需求匹配（服务者报名）

| 功能 | API 文件名 |
|------|-----------|
| AI 匹配分析（时长/收益预估） | servicematch-ai_analysis.md |
| 报名需求 | servicematch-apply.md |
| 取消报名 | servicematch-cancel.md |
| 获取匹配信息 | servicematch-getmatch.md |

## 订单状态流转（服务者视角）

```
1 (待接单)
  ├── servantaccept {accept:true}  → 10 (进行中)
  └── servantaccept {accept:false} → 90 (已取消)

10 (进行中)
  └── servantfinish → 10 仍进行中（已标记完成，等待客户确认）
                      ↓ 客户 /api/serviceorder/userconfirm
                    20 (已完成)
                      ↓ 按平台规则进入结算
                    settle_status: 1 → 2 → 3
```

## 数据结构

### ServiceOrderDetail（服务者视角）

字段定义同 [SKILL-CUSTOMER.md 数据结构章节](./SKILL-CUSTOMER.md#serviceorderdetail)。服务者视角下重点关注字段：

| 字段 | 用途 |
|------|------|
| `status` | 判断订单处于哪个阶段 |
| `servant_user_id` | 应等于当前登录服务者 ID |
| `user_id` | 下单客户 ID |
| `service_time` | 服务时间，用于排程 |
| `order_amount` | 订单金额 |
| `address` / `lng` / `lat` | 服务地址（接单后可见完整地址） |
| `contact_name` / `contact_phone` | 客户联系方式（接单后可见） |
| `settle_status` | 结算状态 |
| `finish_time` / `confirm_time` | 服务者完成时间 / 客户确认时间 |

## 典型使用流程

### 流程一：查看待处理订单并接单

```
1. serviceorder/servantordercounts                  → 各状态数量
2. serviceorder/servantlist?status=1                → 待接单列表
3. serviceorder/servantget?id=xxx                   → 订单详情
4. serviceorder/servantaccept {id, accept:true}     → 接单
```

### 流程二：拒绝订单

```
1. serviceorder/servantget?id=xxx                                → 查看详情
2. serviceorder/servantaccept
     {id, accept:false, reason:"cant-serve", note:"..."}         → 拒单
```

### 流程三：上门履约并完成

```
1. serviceorder/servantlist?status=10               → 进行中订单
2. (按服务时间到达客户地址履约)
3. serviceorder/servantadjustamount                 → 如需调整金额
4. serviceorder/servantfinish {id}                  → 标记完成
   (等待客户 serviceorder/userconfirm 验收)
```

### 流程四：专拍下单（聊天场景）

```
1. serviceorder/last_custom_name                    → 复用上次商品名
2. serviceorder/custom_appoint_submit               → 提交，AI 同步审核
   若 approved → 订单已创建，等待客户支付
   若 rejected → 修改描述后重新提交
3. serviceorder/custom_appoint_update_price         → 未支付前调价
4. serviceorder/custom_appoint_cancel               → 未支付前撤回
```

### 流程五：报名客户需求

```
1. servicematch/ai_analysis?req_id=xxx              → AI 分析预估
2. servicematch/apply?req_id=xxx                    → 报名
3. servicematch/getmatch?req_id=xxx                 → 查看匹配状态
4. servicematch/cancel {req_id}                     → 必要时取消报名
```

## 注意事项

- **接单后不可单方面取消**：服务者如需取消已接单订单，需联系客户或客服，不可调用客户端的 `/api/serviceorder/cancel`
- **专拍价格上限 800 元**：超过需走正常下单流程
- **结算以平台规则为准**：`servantfinish` + 客户 `userconfirm` 均完成后进入结算
- **软件服务费**：平台按每笔成交收取基本软件服务费，具体费率以小程序内公示为准
- **Token 安全**：不得在日志或对话中明文暴露 Token
