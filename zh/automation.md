# 限价挂单与高级自动卖出

本章介绍打狗棒的两套自动化能力：

* 限价挂单（Limit Order）：到价后自动买入/卖出
* 高级自动卖出（Advanced Auto‑Sell）：基于“涨跌幅规则”自动生成多档卖出挂单，并可选跟踪止损（Trailing Stop）

<div align="center"><figure><img src=".gitbook/assets/Frame 7.png" alt=""><figcaption></figcaption></figure></div>

## 1. 限价挂单的工作方式

限价挂单分为买入与卖出两侧：

* Buy：
  * Low Buy：价格跌到触发价（≤）后买入
  * High Buy：价格涨到触发价（≥）后买入
* Sell：
  * Take Profit：价格涨到触发价（≥）后卖出
  * Stop Loss：价格跌到触发价（≤）后卖出
  * Trailing Stop：根据峰值价格动态更新触发价，回撤到触发价（≤）后卖出

触发判断规则可理解为：

* “向上突破”触发：High Buy、Take Profit（当前价 ≥ 触发价）
* “向下跌破”触发：Low Buy、Stop Loss、Trailing Stop（当前价 ≤ 触发价）

## 2. 扫描与执行（什么时候会自动触发）

限价单是否会自动执行，取决于“后台扫描器”是否在运行：

* 扫描间隔：默认 3秒，可选 1s / 3s / 5s / 10s / 30s / 60s / 120s（在 Popup → Settings 中配置）
* 仅在存在“open 状态的订单”时才会调度下一次扫描
* 钱包必须处于解锁态且有选中地址；钱包锁定时不会触发订单执行

价格来源：

* 扫描器会按 token 聚合订单，使用 RPC 从链上获取代币价格（USD）
* 同一个 token 在一个扫描周期内会复用缓存（TTL ≈ 扫描间隔）

执行流程（到价后）：

1. 订单状态从 open → triggered
2. 发送买入/卖出交易
3. 成功：状态更新为 executed，并记录 txHash
4. 失败：状态更新为 failed，并写入 lastError

## 3. 高级自动卖出（多档止盈/止损）

高级自动卖出本质上是在“你买入成功后”，自动创建一组卖出限价单：

* 止盈（Take Profit）：以买入时的基准价格为 100%，例如 +50% 触发时卖出 25%
* 止损（Stop Loss）：例如 -30% 触发时卖出 100%

要点：

* 规则由多条组成，每条包含“触发涨跌幅（%）”与“卖出比例（%）”
* 卖出比例会被约束在 0–100% 范围，并以 bps 存储（1% = 100 bps）
* 只有在高级自动卖出开启时，买入成交后才会自动创建这些订单

## 4. 跟踪止损（Trailing Stop）

跟踪止损会随着价格创新高而更新“峰值价格”，并把触发价维持在“峰值价格回撤 X%”的位置：

* Callback（回撤百分比）：例如 15%，表示触发价 = 峰值价 × (1 - 15%)
* 峰值价：在扫描期间随价格上行而更新

激活时机（Activation Mode）：

* immediate：买入成交后立即创建跟踪止损单
* after\_first\_take\_profit：首次止盈单成交后创建（若同 token 没有未成交的 trailing stop）
* after\_last\_take\_profit：最后一档止盈成交后创建（判断逻辑是：该 token 不再存在“触发价更高的止盈单”）

## 5. 订单与持仓的联动

* 卖出 100% 后：插件会尝试取消该 token 的所有“卖出侧限价单”（避免清仓后仍残留挂单）
* 部分止盈：如果你启用了“止盈后再启用 trailing stop”，会在满足激活条件后补创建 trailing stop 单

下一步：

* 想优化到价执行的成功率与隐私：见 [网络、广播与 Anti‑MEV](network-and-privacy.md)
* 想管理扫描间隔、RPC、gas、滑点：见 [Popup（弹窗）使用指南](popup.md)
