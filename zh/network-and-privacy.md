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

补充理解：

- `RPC URLs` 用于“读”，数量可以多一些（2–6 条都可以），主要追求稳定与可用性
- `Protected RPC URLs` 用于“发”，数量建议少而精（通常 3–4 条足够），主要追求低延迟与低丢包；因为只承担“提交交易”这一步，很多服务商的免费计划也能满足

## 2. 隐私节点（Protect RPC）使用指南

### 2.1 为什么需要 Protect RPC

把广播从公共 RPC 切到 Protect RPC，通常能带来：

- 更低的限流/封禁风险：公共节点高峰期容易限流，导致广播失败或延迟
- 更低的可观测性：公共节点更容易被监控/统计，隐私节点相对更可控
- 更好的到达率：多条 Protect 节点并发竞速广播，谁先返回就算成功

### 2.2 在哪里配置

在 Popup → Settings 的 Network 区域：

- `RPC URLs`：每行一条，用于读链/报价
- `Protected RPC URLs`：每行一条，用于广播交易（建议 3–4 条，不同服务商混搭）
- `Anti‑MEV`：建议开启（开启后会使用 Protect 列表进行广播）

### 2.3 选型与数量建议

- 地理位置：优先选择与你网络出口一致的区域（例如你常用新加坡 VPN，就优先 SG；必要时加 1 条 HK 做容灾）
- 组合思路：1 个稳定大厂节点 + 1 个交易向节点 + 1–2 个多地域付费节点
- 数量建议：3–4 条足够；太多更容易触发风控/限流，维护成本更高
- 购买建议：如果 Protect 只用来“提交交易”，通常不需要高配套餐；除非你有非常高频的自动化交易/批量挂单

### 2.4 推荐 Protect RPC（示例模板）

下面是“可照抄的模板”，把 `YOUR_KEY` / `YOUR_TOKEN` 替换成你自己控制台里生成的值即可。不要把真实 Key 写进仓库或截图公开。

主力（建议至少 2 条）：

- NodeReal（SG/HK，偏稳定托底）
  - `https://bsc-mainnet.nodereal.io/v1/YOUR_KEY`
- Chainstack（SG/HK（如可选），偏稳定，适合作为主力或强冗余）
  - `https://bsc-mainnet.core.chainstack.com/YOUR_KEY`（示例格式，以控制台实际为准）

补充（再加 1–2 条）：

- Blockrazor（交易向节点，建议作为补充而不是唯一依赖）
  - `https://bsc.blockrazor.xyz/YOUR_KEY`
- GetBlock（可选区域，适合补充）
  - `https://go.getblock.io/YOUR_KEY`

一套可用的 Protected RPC 样例（4 条）：

```
https://bsc-mainnet.nodereal.io/v1/YOUR_KEY
https://bsc-mainnet.core.chainstack.com/YOUR_KEY
https://bsc.blockrazor.xyz/YOUR_KEY
https://go.getblock.io/YOUR_KEY
```

### 2.5 申请与排障建议

- 申请时优先选“BSC Mainnet / HTTPS endpoint”，并确认是否需要额外的 Header 鉴权或 IP 白名单
- 填完后用悬浮窗 RPC 面板测速，删除延迟异常/经常超时的节点
- 如果出现 `No RPC URLs configured (check Anti-MEV settings)`，优先检查 Protect 列表是否为空或 URL 是否写错
- 如果你担心隐私：Protect 节点尽量选可信服务商或自建；避免把交易广播到来源不明的节点

## 3. Anti‑MEV（防夹）开关的实际含义

在当前实现中，“广播交易”依赖 Protected RPC URLs：

- 当 Anti‑MEV 开启且 Protected RPC URLs 非空：交易会广播到 Protected 列表（并发竞速）
- 当 Protected RPC URLs 为空：广播会直接报错 `No RPC URLs configured (check Anti-MEV settings)`

因此实操建议是：

- 打开 Anti‑MEV，并至少配置 1 条可用的 Protected RPC URL
- RPC URLs 仍然建议配置 2–4 条稳定节点，用于读链与报价

## 4. 多节点竞速广播（提高“更快入池”的概率）

广播时会并发向以下目标发送已签名交易：

- bloXroute（可选，见下一节）
- 你配置的每一条 Protected RPC URL

只要任意一个目标成功返回 txHash，就认为广播成功；如果全部失败，会把每个节点的错误拼接到异常详情中，便于排障。

常见现象：

- 某个节点返回 `already known / already imported`：会把签名交易 keccak 计算得到的 hash 当作 txHash 返回（视为已广播成功）
- 某个节点“偶发超时”：只要其他节点成功，整体仍算成功

## 5. bloXroute 私有广播（可选）

你可以在 Settings 中填写 `bloxrouteAuthHeader`（形如 `Authorization: ...` 的值）。启用后：

- 广播会额外并发向 bloXroute 的 `bsc_private_tx` 接口提交交易
- 使用 `mev_builders: ["all"]`，尽可能覆盖更多 builder

注意：

- 未配置 auth header 时不会走 bloXroute
- bloXroute 与 RPC 广播是并发的，谁先成功就返回谁

## 6. RPC 测速与日常维护建议

悬浮窗的 RPC 面板支持对 RPC URLs / Protected RPC URLs 做延迟测量（getBlockNumber）：

- 延迟低且稳定的节点更适合放到 Protected 列表
- 公共 RPC 节点建议保留多条，避免单点故障

## 7. 常见问题

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
