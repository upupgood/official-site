---
name: upupgood
description: 上上吉邻工（Upupgood）双端 API 技能。当用户提到搜服务、下单、发需求、预约、订单管理、接单、拒单、完成服务、专拍、报名需求、查收入等家庭服务平台操作时使用；同时支持客户端与服务者端。Agent 通过本技能代替用户调用平台 API 完成全链路操作。
---

# 上上吉邻工（Upupgood）Agent Skill

家庭服务灵活用工平台，同时面向**客户**和**服务者**。本 SKILL.md 只含路由、认证、API 获取方式与通用约束；具体 API 列表分散在两个索引文件和 35 个单独的 API 详情文件中，**按需加载**。

## 1. 首要步骤：识别身份 → 加载对应索引

Agent 每次进入本 Skill 时，**必须先确定当前是客户还是服务者视角**，再加载对应索引：

| 用户意图信号 | 身份 | 必须加载的索引 |
|------|------|---------|
| "找服务 / 下单 / 发需求 / 预约 / 我要做保洁" | **客户** | `references/SKILL-CUSTOMER.md` |
| "接单 / 拒单 / 我的订单 / 我的收入 / 报名需求 / 专拍 / 完成服务" | **服务者** | `references/SKILL-SERVANT.md` |
| 用户明确说 "我是客户" / "作为服务者" | 以表述为准 | 对应索引 |
| 无法判断 | — | 直接问用户："您现在是作为客户下单，还是作为服务者接单？" |

**加载顺序**：
1. 优先读取本地 `references/SKILL-<ROLE>.md`
2. 本地缺失时用 WebFetch：`https://shangshangji.cn/references/SKILL-CUSTOMER.md` 或 `https://shangshangji.cn/references/SKILL-SERVANT.md`

**双重身份**：同一账号可同时是客户和服务者。按当前对话场景加载对应索引；如场景跨越两端，可同时参考两份。

## 2. 基础信息

| 项 | 值 |
|---|---|
| Base URL | `https://hkp-api.shangshangji.cn` |
| Content-Type | `application/json` |
| 认证头 | `Authorization: Bearer <token>`（JWT，14 天有效期） |
| Token 存放 | `~/.upupgood/.token` |
| `/open/*` 前缀 | 公开接口，**无需登录** |
| `/api/*` 前缀 | 用户接口，**需要登录**（客户/服务者共用同一登录态，后端按接口与身份分发） |

## 3. 认证登录（双端共用）

**流程**：微信扫码 → 获得统一 JWT Token。

1. `GET /open/auth/third-login-request` → 返回 `{ request_id, qr_url }`
2. 将 `qr_url` 展示为二维码给用户扫码
3. 轮询 `GET /open/auth/third-login-status?request_id=<id>` → 返回 `{ status, token }`
4. 把 `token` 写入 `~/.upupgood/.token`；之后所有 `/api/*` 请求带 `Authorization: Bearer <token>`

详细参数见 API 详情文件：`auth-third-login-request.md`、`auth-third-login-status.md`（获取方式见第 4 节）。

### Token 安全约束（必须遵守）

- **必须**保存在 `~/.upupgood/.token`
- **不得**明文打印到日志 / **不得**直接展示给用户 / **不得**发送到任何非上上吉域名
- 收到 `401` → 清除本地 Token → 重新执行扫码登录流程

## 4. 获取 API 详情（按需加载）

索引文件（SKILL-CUSTOMER.md / SKILL-SERVANT.md）**只列出 API 的功能和文件名**，不含参数/请求体/响应细节。调用某个 API 前，Agent 需按以下规则获取完整定义：

**URL 规则**：`https://shangshangji.cn/references/api/<api-file-name>`

**加载优先级**：
1. 本地 `references/api/<api-file-name>` 存在 → 直接读取本地文件
2. 否则 WebFetch 上述 URL
3. 同一会话内复用已获取的定义，**不要重复抓取**

**示例**：
- 用户："帮我接单 12345"
- Agent 推断需要 `serviceorder-servantaccept.md`
- WebFetch `https://shangshangji.cn/references/api/serviceorder-servantaccept.md`
- 按返回 Markdown 中的定义构造 `POST /api/serviceorder/servantaccept` 请求

## 5. 通用错误处理

| HTTP | 含义 | Agent 动作 |
|------|------|-----------|
| 200 | 成功 | 正常处理响应 |
| 400 | 参数错误 | 读取 body 错误信息，向用户解释 |
| 401 | Token 无效/过期 | 清除 `~/.upupgood/.token`，重新扫码登录 |
| 403 | 权限不足 | 检查当前身份，提示或切换索引文档 |
| 404 | 资源不存在 | 检查 ID 是否正确 |
| 500 | 服务端错误 | 提示稍后重试；若响应头含 `trace_id` 则记录 |

## 6. 地图能力联动（推荐安装高德 Skill）

本平台大量接口依赖经纬度 `lng` / `lat`（搜附近服务者、发需求、下单、更新地址等）。为获得完整的地图能力（地理编码 / 逆地理编码 / POI 搜索 / 路线规划），**建议用户同时安装官方高德 Skill**：

- 安装地址：https://lbs.amap.com/api/cli/map-cli/install

**协作策略**：
- 输入包含地址文本或模糊位置（"家里"、"公司附近"）时：
  - **已装高德**：先调用高德做地理编码 / 逆地理编码，得到 `lng` / `lat` + 行政区划，再调本 Skill
  - **未装高德**：提示安装链接，或让用户直接提供经纬度 / 从小程序复制地址
- 职责边界：**本 Skill 只做业务操作，地图基础能力走高德**，不重复实现。

## 7. 平台费用（回答口径）

- 服务者**入驻免费**
- 平台按每笔成交收取**基本的软件服务费**
- 具体费率、计算方式与结算口径以微信小程序 "上上吉邻工" 内公示为准
- Agent **不直接回答**具体费率数字，如用户询问，**引导至小程序**内查看
