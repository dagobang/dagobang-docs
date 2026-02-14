# Limit Orders & Advanced Auto-Sell

This chapter covers two automation systems:

- Limit orders: auto buy/sell when the price hits your trigger
- Advanced Auto‑Sell: generate multi-step sell limit orders based on % change rules, with optional trailing stop

## 1. How Limit Orders Work

Limit orders include buy-side and sell-side types:

- Buy:
  - Low Buy: buy when price drops to (≤) the trigger price
  - High Buy: buy when price rises to (≥) the trigger price
- Sell:
  - Take Profit: sell when price rises to (≥) the trigger price
  - Stop Loss: sell when price drops to (≤) the trigger price
  - Trailing Stop: dynamically updates the trigger based on peak price, sells when price falls back to (≤) the trigger

Trigger rule summary:

- Upward break: High Buy, Take Profit (current price ≥ trigger price)
- Downward break: Low Buy, Stop Loss, Trailing Stop (current price ≤ trigger price)

## 2. Scanning & Execution (When Orders Actually Trigger)

Whether a limit order will execute automatically depends on the background scanner:

- Scan interval: default 3s; options 1s / 3s / 5s / 10s / 30s / 60s / 120s (configured in Popup → Settings)
- The next scan is scheduled only when there are “open” orders
- The wallet must be unlocked and have a selected address; locked wallets will not execute orders

Price source:

- Orders are grouped by token; the scanner reads token price (USD) from RPC
- Within a scan cycle, token prices are cached (TTL ≈ scan interval)

Execution flow (once hit):

1. Status moves from open → triggered
2. A buy/sell transaction is sent
3. Success: status becomes executed and txHash is stored
4. Failure: status becomes failed and lastError is stored

## 3. Advanced Auto-Sell (Multi-Step TP/SL)

Advanced auto-sell creates a set of sell limit orders after your buy is confirmed:

- Take-profit: base price is the buy price (100%), e.g. +50% triggers selling 25%
- Stop-loss: e.g. -30% triggers selling 100%

Key points:

- Multiple rules are supported; each rule has a trigger % and a sell %
- Sell % is clamped to 0–100 and stored in bps (1% = 100 bps)
- Orders are created only when Advanced Auto‑Sell is enabled

## 4. Trailing Stop

Trailing stop tracks a moving peak price and keeps the trigger at “peak minus X%”:

- Callback: e.g. 15% means trigger = peak × (1 - 15%)
- Peak: updated during scanning when price makes new highs

Activation modes:

- immediate: create the trailing stop right after the buy fills
- after_first_take_profit: create after the first take-profit fills (if no open trailing stop exists for the token)
- after_last_take_profit: create after the last take-profit fills (when there is no remaining TP with a higher trigger)

## 5. Interaction With Your Position

- After a 100% sell: the extension attempts to cancel all sell-side limit orders for the token
- Partial take-profit: if you configured “enable trailing after TP”, the trailing stop order may be created once activation conditions are met

Next:

- For execution rate and privacy tuning: see [Network, Broadcasting & Anti‑MEV](network-and-privacy.md)
- For scan interval / RPC / gas / slippage: see [Popup Guide](popup.md)

