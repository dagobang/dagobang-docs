# Floating Trading Panel

The floating panel is the main trading entrypoint on chart pages: buy, sell, approve, and quickly switch execution parameters without changing tabs.

## 1. Basic Interactions

- Drag to move: hold the header bar and drag
- Minimize: the top-right button collapses the panel into a round icon (click to restore)
- Locked overlay: if the wallet is locked, the panel is covered by an overlay; click it to open the popup and unlock
- Supported chain: currently BSC (chainId 56)

## 2. Header Shortcuts

From left to right:

- Limit orders: open/close the limit order panel and order list
- RPC panel: view and latency-test RPC / Protect RPC lists
- Daily analysis: view per-day PnL charts for the current address (data from a third-party API)
- Preset editing: pencil to enter edit mode, checkmark to save presets
- Minimize: collapse the panel

## 3. Buy

The buy section is denominated in BNB (preset buttons are common amounts):

- Balance: shows BNB balance of the current address
- Amount presets: click a preset to buy with that amount
- Execution mode:
  - Default: standard execution path
  - Turbo: optimized for faster trading (requires deeper understanding of risks)
- Gas preset: cycles Slow / Standard / Fast / Turbo (exact gwei can be configured in Popup → Settings)
- Slippage: cycles 30% / 40% / 50% / 90% (bps: 3000/4000/5000/9000)
- Advanced auto-sell: enable and configure “create sell limit orders after buy fills”; see [Limit Orders & Advanced Auto-Sell](automation.md)

Notes:

- After a successful buy, the extension may try to pre-handle sell approval (Approve) if needed
- During execution you’ll see toast notifications, including broadcast source (RPC / bloXroute)

## 4. Sell

The sell section is denominated in position percentage:

- Balance: shows token balance (simplified display)
- Percentage presets: 10% / 25% / 50% / 100% (editable)
- Approve: your first sell may require approving the token to the router contract
- Submit sell: click a preset to sell that percentage

Automation interaction:

- When you sell 100%, the extension attempts to cancel all limit orders for that token (to avoid leftover sell orders after full exit)

## 5. Editing Buy/Sell Presets

Click the pencil icon in the header:

- Edit buy presets (BNB) and sell presets (%)
- Click the checkmark to save; values are written to local settings

More details:

- [Network, Broadcasting & Anti‑MEV](network-and-privacy.md)
- [Popup Guide](popup.md)

