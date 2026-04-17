# POST /api/serviceorder/servantaccept

服务者接单 / 拒绝（二合一接口，通过 `accept` 字段区分）。

**前缀**：`/api/`（需要登录）

## 请求体

**接单**：
```json
{ "id": 12345, "accept": true }
```

**拒绝**：
```json
{
  "id": 12345,
  "accept": false,
  "reason": "cant-serve",
  "note": "当天有其他预约"
}
```

| 参数 | 类型 | 必填 | 说明 |
|------|------|------|------|
| id | int64 | 是 | 服务单 ID |
| accept | bool | 否 | 是否同意接单，默认 `true` |
| reason | string | 否 | 拒绝原因：`not-need` / `cant-serve` / `system-cancel` |
| note | string | 否 | 拒绝备注 |

## 响应

`ServiceOrderDetail` 对象。

## 状态流转

| 操作 | 结果 |
|------|------|
| `accept=true` | `status: 1 → 10`（进行中），服务者可见完整地址与联系方式 |
| `accept=false` | `status: 1 → 90`（已取消），订单关闭 |

## 注意

- 仅 `status=1`（待接单）可调用
- 拒绝时建议填写 `reason`，平台会用于匹配算法迭代
- 接单后如需反悔，不能走本接口——需走客服流程
