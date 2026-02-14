# 常见问题与排障

## 1) 悬浮窗提示未解锁 / 点击后没反应

- 现象：悬浮窗有遮罩层，提示需要解锁
- 原因：钱包处于锁定态
- 处理：
  - 点击遮罩层会尝试打开 Popup
  - 在 Popup 输入密码解锁后回到页面

## 2) 下单时报错：No RPC URLs configured (check Anti-MEV settings)

- 原因：交易广播依赖 Protected RPC URLs；未配置会直接失败
- 处理：
  - Popup → Settings → Protected RPC URLs 至少填 1 条可用 URL
  - 建议保持 Anti‑MEV 开启，并把高质量节点放到 Protected 列表
  - 读链用的 RPC URLs 仍建议配置多条

## 3) 限价单没有自动成交

排查顺序：

- 钱包是否解锁：锁定时扫描器不会执行交易
- 是否仍有 open 状态订单：没有 open 时不会继续调度扫描
- 扫描间隔是否过长：Popup → Settings → limitOrderScanIntervalMs
- 触发价是否合理：注意 High/Low、止盈/止损的触发方向
- RPC 是否可用：打开悬浮窗 RPC 面板测速，剔除高延迟/不稳定节点

## 4) 成交慢 / 经常失败

优化建议：

- Protected RPC URLs 增加到 2–5 条并选择更低延迟节点
- 提高 gas 档位（Fast/Turbo）或提高对应 gwei
- 增大滑点（尤其是流动性差的 Meme）
- 配置 bloXroute 私有广播（需要有效的 Auth Header）

## 5) Popup 提示 Invalid password

- 原因：密码不匹配
- 处理：确认输入法与大小写；若忘记密码只能重新导入/创建钱包（务必先确保助记词备份可用）

## 6) Popup 提示 Service unavailable

- 原因：background/service worker 未响应或异常
- 处理：
  - 打开扩展管理页，查看该扩展的 service worker 是否报错
  - 重新加载扩展（Reload）
  - 关闭并重新打开浏览器

