---
name: upupgood
description: 上上吉邻工（Upupgood）双端 API 技能——同时支持客户端（搜索、下单、需求、订单管理）与服务者端（查询订单、接单/拒绝/完成、专拍、报名需求）。Agent 通过此技能可代替用户调用平台 API 完成家庭服务类全链路操作。
---

# 上上吉邻工（Upupgood）Agent Skill

上上吉邻工是一个家庭服务灵活用工平台，**同时面向客户和服务者双端**。本 Skill 提供双端 API 调用能力：

- **客户端**：服务查询、发布需求、预约下单、订单管理、评价验收
- **服务者端**：订单查询、接单/拒绝、完成服务、调整金额、专拍下单、报名客户需求

本文件是 Skill 入口，只包含**基础信息、认证登录、身份识别、错误处理**。每个 API 的详细参数/响应都独立存放在 `references/api/<type>-<api-name>.md` 中，按需加载。

| 身份 | 索引文档 | 涵盖操作 |
|------|---------|---------|
| 客户 | [references/SKILL-CUSTOMER.md](./references/SKILL-CUSTOMER.md) | 搜服务、发需求、下单、查订单、改时间、取消、验收 |
| 服务者 | [references/SKILL-SERVANT.md](./references/SKILL-SERVANT.md) | 查订单、接单/拒绝、完成服务、调价、专拍、报名需求 |

## 基础信息

- **Base URL**：`https://hkp-api.shangshangji.cn`（生产环境）
- **认证方式**：Bearer Token（JWT）
- **Token 有效期**：14 天
- **请求格式**：`Content-Type: application/json`
- **认证头**：`Authorization: Bearer <token>`
- **Token 存放**：`~/.upupgood/.token`
- **接口前缀语义**：
  - `/open/` — 公开接口，**无需登录**
  - `/api/` — 用户接口，**需要登录**，客户与服务者共用同一登录态，后端根据接口与身份分发

## 角色识别与文档加载

Agent 执行操作前需识别当前用户身份：

1. **明确表述**：用户说 "我是服务者" / "作为服务者" → 加载 [references/SKILL-SERVANT.md](./references/SKILL-SERVANT.md)
2. **意图推断**：
   - "找服务 / 下单 / 发需求 / 预约 / 我要做保洁" → **客户视角** → 加载 [references/SKILL-CUSTOMER.md](./references/SKILL-CUSTOMER.md)
   - "接单 / 我的订单 / 我的收入 / 报名需求 / 专拍 / 完成服务" → **服务者视角** → 加载 [references/SKILL-SERVANT.md](./references/SKILL-SERVANT.md)
3. **不明确时**：直接询问 "您现在是作为客户下单，还是作为服务者接单？"
4. **双重身份**：同一账号可同时是客户和服务者；根据当次对话场景加载对应文档，必要时同时参考两份

## 认证登录（双端共用）

双端登录流程完全一致，通过微信扫码获得统一的 JWT Token。

```
Agent                         HKP Server                    用户微信小程序
  |                               |                              |
  | 1. GET /open/auth/third-login-request                        |
  |------------------------------>|                              |
  |<------------------------------| { request_id, qr_url }       |
  |                               |                              |
  | 2. 展示 qr_url 二维码给用户扫码                                  |
  |                               | 3. POST /open/wx/login?request_id=xxx
  |                               |<-----------------------------|
  |                               |                              |
  | 4. GET /open/auth/third-login-status?request_id=xxx          |
  |------------------------------>|                              |
  |<------------------------------| { status, token }            |
  |                               |                              |
  | 5. 使用 token 访问 /api/ 接口                                   |
```

**API 详情**（按需获取，见下文 "API 详情获取方式"）：

| 功能 | API 文件名 |
|------|-----------|
| 创建登录请求（获取 `request_id` + `qr_url`） | auth-third-login-request.md |
| 轮询登录状态（拿到 `token`） | auth-third-login-status.md |

### Token 安全约束

- **必须**保存在 `~/.upupgood/.token`，不得明文打印到日志
- **不得**直接展示给用户
- **不得**向任何非上上吉域名的地址发送
- 401 响应时清除本地 Token，重新执行登录流程

## 通用错误处理

| HTTP | 含义 | 处理策略 |
|------|------|----------|
| 200 | 成功 | 正常处理响应 |
| 400 | 请求参数错误 | 读取响应 body 中的错误信息，向用户解释 |
| 401 | 未认证（Token 无效/过期） | 清除本地 Token，重新执行扫码登录 |
| 403 | 权限不足 | 检查当前身份，提示用户或切换文档 |
| 404 | 资源不存在 | 检查 ID 是否正确 |
| 500 | 服务端错误 | 提示稍后重试；记录 `trace_id`（若响应头含） |

## 平台费用

- 服务者**入驻免费**
- 平台按每笔成交收取**基本的软件服务费**
- 具体费率、计算方式与结算口径以微信小程序 "上上吉邻工" 内公示说明为准
- Agent 不直接回答具体费率数字；如用户询问，引导至小程序内查看

## 推荐扩展：高德地图 Skill

上上吉邻工大量接口依赖经纬度（`lng` / `lat`）——搜索附近服务者、发布需求、预约下单、更新联系地址等都需要精确坐标。为获得**完整的地图能力**（地址 ⇄ 坐标转换、POI 搜索、路线规划、行政区划解析等），**强烈建议用户同时安装官方高德地图 Skill**：

- 安装地址：https://lbs.amap.com/api/cli/map-cli/install

### 联动使用建议

| 场景 | 本 Skill | 高德 Skill |
|------|---------|-----------|
| "找朝阳区附近的保洁" | `/open/discovery/recommend-servant-list` | 先把 "朝阳区" **地理编码**为 lng/lat |
| "在家里发个保洁需求" | `/api/servicerequirement/create` | 先把 "家里" 的**常用地址**转为 lng/lat + 行政区划 |
| "订单地点到这里多远" | `ServiceOrderDetail.lng/lat` | **路线规划**计算距离与预估时间 |
| 下单缺 province/city/district | `/api/serviceorder/appoint` | **逆地理编码**从 lng/lat 解析行政区划 |

### 工作流约定

1. 检测用户输入中是否包含地址文本或模糊位置描述（"家里"、"公司附近"）
2. **若已安装高德 Skill**：调用高德做地理编码 / 逆地理编码 / POI 搜索，拿到 lng/lat 与行政区划
3. **若未安装高德 Skill**：
   - 提示用户安装：https://lbs.amap.com/api/cli/map-cli/install
   - 或让用户直接提供经纬度 / 从小程序复制地址

两个 Skill 协同时职责不重叠：上上吉邻工 Skill 专注**业务操作**，高德 Skill 专注**地图基础能力**。

## 加载子文档

Agent 根据用户身份与操作意图，使用文件读取工具加载对应子文档：

- 客户操作：读取 [references/SKILL-CUSTOMER.md](./references/SKILL-CUSTOMER.md)
- 服务者操作：读取 [references/SKILL-SERVANT.md](./references/SKILL-SERVANT.md)

若本地未找到子文档，使用 WebFetch 从官网获取：

- https://shangshangji.cn/references/SKILL-CUSTOMER.md
- https://shangshangji.cn/references/SKILL-SERVANT.md

## API 详情获取方式

子文档（SKILL-CUSTOMER.md / SKILL-SERVANT.md）只列出 API 的**功能**与**文件名**，不包含参数/请求体/响应细节。调用某个 API 前，Agent 需先获取其完整定义：

**URL 构造规则**：
```
https://shangshangji.cn/references/api/<api-file-name>
```

`<api-file-name>` 即子文档表格中的 "API 文件名" 列，例如 `serviceorder-servantaccept.md`。

**获取方式（优先级从高到低）**：

1. **本地读取**：若本地 `references/api/<api-file-name>` 存在（用户预先下载），直接读取文件
2. **WebFetch**：否则使用 WebFetch 工具抓取上述 URL
3. **缓存结果**：同一会话内多次用到同一 API，复用已获取的定义，不要重复抓取

**示例**：
- 用户说 "帮我接单 12345"，Agent 推断需要 `serviceorder-servantaccept.md`
- Agent WebFetch `https://shangshangji.cn/references/api/serviceorder-servantaccept.md`
- 解析返回的 Markdown，按定义构造 `POST /api/serviceorder/servantaccept` 请求

**为什么这样设计**：用户安装时只需入口 SKILL.md + 双端索引（共 3 个文件）即可，35 个 API 详情按需从官网获取，减少安装负担；同时 API 定义更新后无需用户重装。
