# Install & Quick Start

This page covers the shortest path for first-time users: install, create/import a wallet, open the floating panel, and complete your first trade.

## 1. Install the Extension

Dagobang is built with WXT. The build output can be installed via “Load unpacked extension”.

1. In `dagobang-extention/`:
   - `npm install`
   - `npm run build`
2. Build output: `dagobang-extention/.output/chrome-mv3/`
3. Open your browser’s extensions page:
   - Chrome: `chrome://extensions/`
   - Edge: `edge://extensions/`
4. Enable “Developer mode”, click “Load unpacked”, and select the `.output/chrome-mv3/` folder.

Optional: `npm run zip` produces a zip artifact; you can unzip it and load it the same way.

## 2. First Launch: Create or Import a Wallet

Open the extension popup (the toolbar icon). On first launch you’ll see the welcome screen:

- Create: set a password (min 6 chars) → create → back up mnemonic
- Import: set a password (min 6 chars) → paste mnemonic or private key → import → back up (if available)

Recommendations:

- The password is used for local encryption and unlocking only
- Back up your mnemonic/private key offline; do not share it or screenshot it

## 3. Unlocking & Auto-Lock

- Unlock: enter your password in the popup
- Auto-lock: defaults to 30 minutes (adjust in Popup → Settings)

Limit orders and auto-execution require the wallet to be unlocked. When locked, limit orders will not be executed automatically.

## 4. Open the Floating Panel

After installation, when you visit a supported chart/order page, Dagobang’s floating panel appears (draggable, can be minimized to a round button).

If the panel shows a locked overlay, click it and it will open the popup for unlocking.

## 5. Your First Trade (Suggested Flow)

1. In Popup → Settings:
   - Configure `RPC URLs` (read/quote/query)
   - If you enable `Anti‑MEV`, configure at least 1 `Protected RPC URL` (for broadcasting)
2. Go back to the chart page, select a buy preset amount (in BNB) in the floating panel, and submit a buy.
3. Before your first sell, if you see an approval prompt, run Approve (there’s a button in the panel).

Next:

- To understand each panel button: see [Floating Trading Panel](floating-panel.md)
- To use automation: see [Limit Orders & Advanced Auto-Sell](automation.md)
- To tune RPC and anti-MEV: see [Network, Broadcasting & Anti‑MEV](network-and-privacy.md)

