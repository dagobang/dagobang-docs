# 网络、广播与 Anti‑MEV

打狗棒把“读链/报价”与“广播交易”分开设计：你可以用一组公共 RPC 来读链，同时用另一组更私密/更快的 RPC 来广播交易。

本章解释三件事：

- RPC URLs 与 Protected RPC URLs 分别做什么
- Anti‑MEV 与多节点竞速广播如何影响成交
- bloXroute 私有广播如何配置与工作

## 1. 两类 RPC：读链 vs 广播

在 Settings 里，每条链都有两组 RPC 配置：

- RPC URLs：
  - 用途：读链、报价、查询余额、查询合约状态等
  - 特性：支持多条 URL，内部会按延迟做排序与 fallback
- Protected RPC URLs：
  - 用途：广播已签名交易（sendRawTransaction）
  - 特性：会对多条 URL 并发广播，拿到“最先成功的返回”即认为广播成功

## 2. Anti‑MEV（防夹）开关的实际含义

在当前实现中，“广播交易”依赖 Protected RPC URLs：

- 当 Anti‑MEV 开启且 Protected RPC URLs 非空：交易会广播到 Protected 列表（并发竞速）
- 当 Protected RPC URLs 为空：广播会直接报错 `No RPC URLs configured (check Anti-MEV settings)`

因此实操建议是：

- 打开 Anti‑MEV，并至少配置 1 条可用的 Protected RPC URL
- RPC URLs 仍然建议配置 2–4 条稳定节点，用于读链与报价

## 3. 多节点竞速广播（提高“更快入池”的概率）

广播时会并发向以下目标发送已签名交易：

- bloXroute（可选，见下一节）
- 你配置的每一条 Protected RPC URL

只要任意一个目标成功返回 txHash，就认为广播成功；如果全部失败，会把每个节点的错误拼接到异常详情中，便于排障。

常见现象：

- 某个节点返回 `already known / already imported`：会把签名交易 keccak 计算得到的 hash 当作 txHash 返回（视为已广播成功）
- 某个节点“偶发超时”：只要其他节点成功，整体仍算成功

## 4. bloXroute 私有广播（可选）

你可以在 Settings 中填写 `bloxrouteAuthHeader`（形如 `Authorization: ...` 的值）。启用后：

- 广播会额外并发向 bloXroute 的 `bsc_private_tx` 接口提交交易
- 使用 `mev_builders: ["all"]`，尽可能覆盖更多 builder

注意：

- 未配置 auth header 时不会走 bloXroute
- bloXroute 与 RPC 广播是并发的，谁先成功就返回谁

## 5. RPC 测速与日常维护建议

悬浮窗的 RPC 面板支持对 RPC URLs / Protected RPC URLs 做延迟测量（getBlockNumber）：

- 延迟低且稳定的节点更适合放到 Protected 列表
- 公共 RPC 节点建议保留多条，避免单点故障

## 6. 常见问题

- 广播提示 `No RPC URLs configured (check Anti-MEV settings)`：
  - 检查 Settings → Protected RPC URLs 是否为空
  - 确保 URL 可用且无鉴权/白名单限制
- 成交慢/容易失败：
  - 增加 Protected RPC 节点数量
  - 提高 gas 档位（Fast/Turbo）或在 Settings 里提高对应 gwei
  - 适当增大滑点（尤其在流动性差或波动剧烈时）

下一步：

- 需要查看并管理钱包、gas、滑点、扫描间隔：见 [Popup（弹窗）使用指南](popup.md)
- 需要了解自动化策略：见 [限价挂单与高级自动卖出](automation.md)

