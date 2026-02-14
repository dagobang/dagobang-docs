# Popup（弹窗）使用指南

Popup 是打狗棒的钱包与全局设置中心，负责：

- 创建/导入/解锁钱包
- 管理账户与切换默认地址
- 配置 RPC、Anti‑MEV、gas、滑点、预设、自动化参数

## 1. 视图与入口

Popup 会根据钱包状态自动进入不同页面：

- Welcome：首次安装，创建或导入钱包
- Unlock：已有钱包但处于锁定状态
- Home：钱包解锁后显示账户列表与余额
- Settings：全局设置页（Home 右上角进入）
- Backup：展示助记词（Welcome 创建/导入后，或 Settings 中触发）

## 2. Welcome：创建/导入钱包

Welcome 页有两条路径：

- 创建（Create）：
  - 输入密码（至少 6 位）→ 创建成功后会展示助记词用于备份
- 导入（Import）：
  - 输入密码（至少 6 位）
  - 粘贴助记词（12/24 词）或 0x 开头的私钥

建议：

- 密码仅用于本地加密与解锁，不会上传
- 助记词/私钥只建议离线备份

## 3. Unlock：解锁与锁定

- 解锁：输入密码解锁
- 锁定：Home 页可一键锁定
- 自动锁定：Settings 可配置 `autoLockSeconds`（默认 30 分钟）

与自动化联动：

- 钱包锁定时，限价单扫描器不会执行交易

## 4. Home：账户与余额

Home 页展示所有账户，并支持：

- 选择默认地址：点击某个地址会把它设为当前地址（last selected）
- 复制地址：一键复制到剪贴板
- 刷新余额：从链上读取 BNB 余额并刷新列表

账户别名（Alias）：

- 可为地址设置别名，便于识别多账号
- 别名会保存到本地 settings 的 `accountAliases`

## 5. Settings：关键参数解释

Settings 中最常用的配置项如下。

### 5.1 链与 RPC

- Chain：目前主链为 BSC（56）
- RPC URLs：读链/报价/查询使用
- Protected RPC URLs：广播交易使用（与 Anti‑MEV 联动）
- Anti‑MEV：开启后会走 Protected RPC 广播；Protected 为空会导致广播报错

### 5.2 Gas 档位与 gwei

每条链都有四档 gas preset：

- slow / standard / fast / turbo

并且买入与卖出可以分别设置：

- buyGasPreset / sellGasPreset
- buyGasGwei / sellGasGwei（为每个 preset 配置具体 gwei）

### 5.3 交易参数

- slippageBps：滑点（bps）
- deadlineSeconds：交易 deadline（秒）
- executionMode：default / turbo

### 5.4 悬浮窗预设

- buyPresets：买入按钮的金额（BNB）
- sellPresets：卖出按钮的百分比（%）

### 5.5 自动化

- limitOrderScanIntervalMs：限价单扫描间隔
- advancedAutoSell：高级自动卖出规则与 trailing stop 配置

## 6. 备份助记词（Backup）

Backup 页面会直接展示助记词明文，用于你离线备份。

建议：

- 只在可信环境打开 Backup 页面
- 备份完成后及时关闭 Popup

下一步：

- 想了解限价与高级自动卖出逻辑：见 [限价挂单与高级自动卖出](automation.md)
- 想了解 RPC/Anti‑MEV/bloXroute：见 [网络、广播与 Anti‑MEV](network-and-privacy.md)

