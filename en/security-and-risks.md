# Security, Privacy & Disclaimer

This chapter focuses on three things:

- How Dagobang handles your keys and transactions (security/privacy boundaries)
- Minimal safety practices you should follow
- Risks and disclaimer

## 1. Non-Custodial and Local Encryption

- Private keys / mnemonics are stored locally and encrypted by your password
- The extension does not upload your keys to any server
- After unlocking, signing is performed locally in memory, and the signed transaction is broadcast to endpoints you configure

You should assume:

- Anyone who obtains your mnemonic/private key can fully control your funds
- Anyone who controls your browser environment (malicious extensions, malware, remote control) may indirectly steal assets

## 2. Meme Trading Risks

- High volatility: slippage, drawdowns, wicks, and liquidity dries can cause unexpected losses
- Adversarial environment: sandwich/MEV competition impacts price and execution probability
- Contract risks: taxes, blacklists, transfer limits, honeypots can prevent selling or cause large losses
- RPC risks: unstable RPC can cause stale quotes, broadcast failures, or delays

## 3. Minimal Safety Practices (Strongly Recommended)

- Password: use a strong, unique password; do not reuse passwords
- Backup: keep mnemonic offline (paper/hardware); do not screenshot or upload to cloud/chat apps
- Environment: use only trusted devices and browsers; avoid unknown extensions
- Funds: start small, use multiple wallets, avoid keeping all funds in one address
- Nodes: use trusted Protected RPC endpoints; unknown nodes may log/inspect your transactions

## 4. Disclaimer

- Dagobang is a tool and does not provide investment advice
- You bear all risks from trading losses, node failures, contract risks, and market volatility
- Any “guaranteed profit” claims are unrelated to Dagobang

