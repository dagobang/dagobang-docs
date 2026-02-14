# FAQ & Troubleshooting

## 1) The floating panel says locked / clicking does nothing

- Symptom: an overlay appears saying the wallet is locked
- Cause: the wallet is locked
- Fix:
  - Click the overlay to open the popup
  - Unlock in the popup, then return to the page

## 2) Order fails with: No RPC URLs configured (check Anti-MEV settings)

- Cause: transaction broadcasting depends on Protected RPC URLs; empty config fails immediately
- Fix:
  - Popup → Settings → add at least 1 working Protected RPC URL
  - Keep Anti‑MEV enabled and put high-quality endpoints into Protected
  - Keep multiple RPC URLs for read/quote

## 3) Limit orders do not execute automatically

Checklist:

- Is the wallet unlocked? Locked wallets do not execute orders
- Do you still have open orders? If none are open, scans are not scheduled
- Is the scan interval too large? Popup → Settings → limitOrderScanIntervalMs
- Is the trigger direction correct? High/Low, take-profit/stop-loss trigger on different sides
- Are your RPC endpoints healthy? Use the floating panel RPC latency test and remove unstable endpoints

## 4) Slow fills / frequent failures

Suggestions:

- Add 2–5 Protected RPC URLs, prefer lower-latency endpoints
- Increase gas preset (Fast/Turbo) or raise gwei values in Settings
- Increase slippage (especially for low-liquidity meme tokens)
- Configure bloXroute private broadcast (requires a valid auth header)

## 5) Popup says: Invalid password

- Cause: password mismatch
- Fix: verify keyboard layout/case; if you forgot the password you must re-import/re-create (make sure your mnemonic backup is correct first)

## 6) Popup says: Service unavailable

- Cause: background/service worker is not responding or crashed
- Fix:
  - Check service worker logs in the extensions page
  - Reload the extension
  - Restart the browser

