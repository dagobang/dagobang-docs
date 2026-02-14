# Popup Guide

The popup is Dagobang’s wallet and global settings hub. It’s responsible for:

- Creating/importing/unlocking the wallet
- Managing accounts and selecting the active address
- Configuring RPC, Anti‑MEV, gas, slippage, presets, and automation parameters

## 1. Views and Navigation

The popup automatically switches views based on wallet state:

- Welcome: first install, create or import a wallet
- Unlock: wallet exists but is locked
- Home: unlocked wallet, shows accounts and balances
- Settings: global settings page (enter from Home)
- Backup: shows mnemonic words (after create/import, or triggered from Settings)

## 2. Welcome: Create/Import Wallet

Two flows:

- Create:
  - Set password (min 6 chars) → create → mnemonic backup view
- Import:
  - Set password (min 6 chars)
  - Paste mnemonic (12/24 words) or a 0x-prefixed private key

Recommendations:

- The password is used locally for encryption/unlocking only
- Back up your mnemonic/private key offline

## 3. Unlock: Locking and Auto-Lock

- Unlock: enter password
- Lock: lock from the Home view
- Auto-lock: configurable as `autoLockSeconds` (default 30 minutes)

Automation dependency:

- When the wallet is locked, the limit order scanner will not execute transactions

## 4. Home: Accounts and Balances

Home shows all accounts and supports:

- Select active address: click an address to set it as current (last selected)
- Copy address: one-click copy
- Refresh balances: reads BNB balance from chain and updates the list

Account aliases:

- You can set an alias for an address for easier identification
- Aliases are stored in `accountAliases` in local settings

## 5. Settings: Key Parameters

Commonly used settings:

### 5.1 Chain and RPC

- Chain: currently BSC (56)
- RPC URLs: used for read/quote/query
- Protected RPC URLs: used for broadcasting transactions (paired with Anti‑MEV)
- Anti‑MEV: when enabled, broadcasting uses Protected RPC; empty Protected list will fail broadcasting

### 5.2 Gas Presets and gwei

Each chain has four gas presets:

- slow / standard / fast / turbo

Buy and sell can be configured separately:

- buyGasPreset / sellGasPreset
- buyGasGwei / sellGasGwei (gwei per preset)

### 5.3 Trade Parameters

- slippageBps: slippage (bps)
- deadlineSeconds: tx deadline (seconds)
- executionMode: default / turbo

### 5.4 Floating Panel Presets

- buyPresets: buy button amounts (BNB)
- sellPresets: sell button percentages (%)

### 5.5 Automation

- limitOrderScanIntervalMs: limit order scan interval
- advancedAutoSell: advanced auto-sell rules and trailing stop config

## 6. Backup Mnemonic

The Backup view shows your mnemonic in plain text for offline backup.

Recommendations:

- Open Backup only in a trusted environment
- Close the popup after backing up

Next:

- For limit orders and auto-sell logic: see [Limit Orders & Advanced Auto-Sell](automation.md)
- For RPC/Anti‑MEV/bloXroute: see [Network, Broadcasting & Anti‑MEV](network-and-privacy.md)

