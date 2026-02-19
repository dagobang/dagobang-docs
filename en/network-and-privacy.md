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

Quick intuition:

- `RPC URLs` are for reads; you can keep more of them (2–6) for stability
- `Protected RPC URLs` are for sending transactions; keep a small, high-quality set (usually 3–4). Since they only carry broadcast traffic, free plans are often enough

## 2. Protect RPC (Privacy Nodes) Guide

### 2.1 Why Protect RPC

Switching broadcast traffic from public RPCs to Protect RPC endpoints usually improves:

- Rate-limit resilience: public RPCs often throttle during peak hours
- Observability risk: public endpoints are easier to monitor and correlate
- Delivery probability: multiple Protect endpoints race the same signed tx

### 2.2 Where to Configure

In Popup → Settings → Network:

- `RPC URLs`: one per line, used for reads/quotes
- `Protected RPC URLs`: one per line, used for broadcasting (recommended 3–4, mix providers)
- `Anti‑MEV`: recommended on (broadcasting uses Protected list when enabled)

### 2.3 Selection & Quantity Recommendations

- Region: prefer endpoints close to your network egress (e.g., if you use a Singapore VPN, prioritize SG; add 1 HK as fallback if needed)
- Mix: 1 stable “tier-1” provider + 1 trading-oriented node + 1–2 paid multi-region nodes
- Count: 3–4 is enough; too many endpoints can trigger throttling and increases maintenance
- Plan: if you only use Protect RPC for broadcasting, you usually don’t need a high-tier plan (unless you do very high-frequency automation)

### 2.4 Recommended Providers (Template)

In the current version, `Protected RPC URLs` are validated with a whitelist. Only these privacy RPC providers are supported (other domains are ignored on save; if the list becomes empty after filtering, Anti‑MEV will be turned off automatically):

- 48.club
- Blockrazor
- GetBlock

Recommended setup (fill by purpose):

RPC URLs (reads/quotes):

```
https://1rpc.io/bnb
https://bsc.rpc.blxrbdn.com
https://bsc-dataseed.ninicoin.io
https://bsc-dataseed.defibit.io
https://bsc-dataseed-public.bnbchain.org
```

Protected RPC URLs (private broadcast / Anti‑MEV):

```
https://bscrpc.pancakeswap.finance
https://pancake.rpc.48.club
https://four.rpc.48.club
https://bsc.blockrazor.xyz/YOUR_KEY
https://go.getblock.asia/YOUR_KEY
```

### 2.5 Application & Troubleshooting Tips

- When creating endpoints, choose “BSC Mainnet / HTTPS”, and check if it requires extra auth headers or IP whitelisting
- After configuring, use the floating panel’s RPC latency test and remove unstable endpoints
- If you see `No RPC URLs configured (check Anti-MEV settings)`, verify Protected RPC URLs is not empty and URLs are correct
- If privacy matters, prefer trusted providers or self-hosted nodes; avoid unknown endpoints for broadcasting

## 3. What Anti‑MEV Means Here

In the current implementation, broadcasting depends on `Protected RPC URLs`:

- If Anti‑MEV is enabled and Protected RPC URLs are non-empty: transactions are broadcast to the Protected list (racing)
- If Protected RPC URLs are empty: broadcasting fails with `No RPC URLs configured (check Anti-MEV settings)`

Practical recommendation:

- Keep Anti‑MEV enabled and configure at least 1 working Protected RPC URL
- Still configure 2–4 stable RPC URLs for reading and quoting

## 4. Multi-Endpoint Racing (Higher Chance to Reach Mempool Fast)

When broadcasting, Dagobang sends your signed transaction concurrently to:

- bloXroute (optional, next section)
- Every Protected RPC URL you configured

Any one success returns a txHash. If all endpoints fail, the error aggregates each endpoint’s failure reason to help troubleshooting.

Common behaviors:

- If an endpoint replies `already known / already imported`, the tx hash is derived from `keccak(signedTx)` and treated as success
- If one endpoint times out, others can still succeed

## 5. bloXroute Private Broadcast (Optional)

If you set `bloxrouteAuthHeader` in settings (bloxrouter / bloXroute auth; the value used as the HTTP `Authorization` header):

- Broadcasting additionally submits the tx to bloXroute’s `bsc_private_tx` endpoint
- Uses `mev_builders: ["all"]` to maximize builder coverage

Notes:

- Without auth header, bloXroute is not used
- bloXroute and RPC broadcasting are parallel; whichever succeeds first is returned

## 6. Latency Testing & Maintenance

The floating panel’s RPC panel can measure latency of RPC URLs / Protected RPC URLs (via `getBlockNumber`):

- Lower and more stable latency endpoints are better candidates for the Protected list
- Keep multiple read RPC URLs to reduce single-point failures

## 7. Common Issues

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
