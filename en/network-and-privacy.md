# Network, Broadcasting & Anti‑MEV

Dagobang separates “read/quote” traffic from “transaction broadcast” traffic. You can read chain state from public RPC endpoints while broadcasting signed transactions to a more private/faster set of endpoints.

This chapter explains:

- What `RPC URLs` vs `Protected RPC URLs` do
- What Anti‑MEV changes in practice
- How optional bloXroute private broadcasting works

## 1. Two RPC Lists: Read vs Broadcast

In settings (per chain) you configure two RPC lists:

- RPC URLs:
  - Used for reading chain state, quoting, balance queries, contract calls
  - Supports multiple URLs with latency ranking and fallback
- Protected RPC URLs:
  - Used for broadcasting signed transactions (`sendRawTransaction`)
  - Broadcast is done in parallel (“race”): first success wins

## 2. What Anti‑MEV Means Here

In the current implementation, broadcasting depends on `Protected RPC URLs`:

- If Anti‑MEV is enabled and Protected RPC URLs are non-empty: transactions are broadcast to the Protected list (racing)
- If Protected RPC URLs are empty: broadcasting fails with `No RPC URLs configured (check Anti-MEV settings)`

Practical recommendation:

- Keep Anti‑MEV enabled and configure at least 1 working Protected RPC URL
- Still configure 2–4 stable RPC URLs for reading and quoting

## 3. Multi-Endpoint Racing (Higher Chance to Reach Mempool Fast)

When broadcasting, Dagobang sends your signed transaction concurrently to:

- bloXroute (optional, next section)
- Every Protected RPC URL you configured

Any one success returns a txHash. If all endpoints fail, the error aggregates each endpoint’s failure reason to help troubleshooting.

Common behaviors:

- If an endpoint replies `already known / already imported`, the tx hash is derived from `keccak(signedTx)` and treated as success
- If one endpoint times out, others can still succeed

## 4. bloXroute Private Broadcast (Optional)

If you set `bloxrouteAuthHeader` in settings (the value used as the HTTP `Authorization` header):

- Broadcasting additionally submits the tx to bloXroute’s `bsc_private_tx` endpoint
- Uses `mev_builders: ["all"]` to maximize builder coverage

Notes:

- Without auth header, bloXroute is not used
- bloXroute and RPC broadcasting are parallel; whichever succeeds first is returned

## 5. Latency Testing & Maintenance

The floating panel’s RPC panel can measure latency of RPC URLs / Protected RPC URLs (via `getBlockNumber`):

- Lower and more stable latency endpoints are better candidates for the Protected list
- Keep multiple read RPC URLs to reduce single-point failures

## 6. Common Issues

- `No RPC URLs configured (check Anti-MEV settings)`:
  - Check if Protected RPC URLs is empty in Popup → Settings
  - Ensure the URL is reachable and does not require extra auth/whitelisting
- Slow fills / frequent failures:
  - Add more Protected RPC endpoints
  - Increase gas preset (Fast/Turbo) or raise gwei in Settings
  - Increase slippage (especially for low-liquidity / high-volatility meme tokens)

Next:

- For wallet/gas/slippage/scan interval management: see [Popup Guide](popup.md)
- For automation strategy details: see [Limit Orders & Advanced Auto-Sell](automation.md)

