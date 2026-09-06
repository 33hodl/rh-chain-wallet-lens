# 🦅 Robinhood Chain · Wallet Lens

A read-only, **client-side** portfolio viewer + cost-basis PnL tool for [Robinhood Chain](https://robinhoodchain.blockscout.com/) (Arbitrum Orbit L2, chain ID **4663**). Paste any wallet address — or open a `?wallet=0x…` link — and see live on-chain balances with market prices, mcap, liquidity, volume, 24h change, and token-safety flags. Optionally reconstruct the wallet's **cost-basis PnL** from its on-chain history.

No server, no keys, no analytics. All data is fetched straight from public endpoints into your browser.

**Try it:** enter any RH Chain wallet address above, or share a link like `…/?wallet=0x<address>` so someone else sees exactly what you see.

## Features

| Feature | Source |
|---|---|
| ERC-20 + native ETH balances | Blockscout explorer API (`/api/v2`), RPC |
| Price, mcap, liquidity, vol, Δ24h | DexScreener (`/tokens/v1/robinhood/{ca}`), Blockscout rate fallback |
| Token-safety badges (honeypot, tax, blacklist, pausable, owner powers, verified) | GoPlus `token_security/4663` |
| 📊 Cost-basis PnL (per wallet) | Blockscout transfer history, reconstructed in-page |
| Sortable table, share links, 30s auto-refresh | — |

Click any column header to sort. Symbol links go to the pair's DexScreener page. Risk badges carry hover tooltips.

## 📊 How the PnL reconstruction works

Click **⚡ Load PnL** (or append `&pnl=1` to auto-run). The page reads the wallet's ERC-20 transfer history and groups legs **per transaction**:

- **Buy** = token in + stables out → cost basis (USDG/WETH legs valued at $1 / live ETH price)
- **Sell** = token out + stables in → realized PnL vs. moving-average cost of the sold qty
- **Deposits/withdrawals** = single stable-leg transfers with no trade counterparty
- **LP activity** = transfers to/from pool-manager / position-wrapper contracts → excluded (funds sit outside the wallet balance)
- **Unclassified** = token↔token swaps (not priced), counted and disclosed

Totals: Realized + Unrealized (held tokens valued at the live snapshot price) vs. deposited capital.

### Honest limits (v1)
- **Gas fees are not included** (typically a few $ per wallet; transfer history carries no fee data without per-tx lookups).
- **Received tokens** (airdrops/transfers-in) carry **$0 cost basis** — gains on them are overstated by their full value.
- **Tokens in LP positions are excluded** — the wallet no longer holds them, so they're absent from both balances and PnL. LP/position value (Uniswap v3/v4, Revert, hook wrappers) is not tracked client-side.
- **Deep wallets** (hundreds of txs) are scanned up to ~1,250 newest transfers; results say so and can be re-scanned.
- Stablecoins price at $1; dust holdings are dropped. Historic token prices aren't fetched — cost/proceeds come from the stable legs of each trade, which is exact; only *current* unrealized values use live prices.
- The explorer API intermittently 500s under load; fetches auto-retry with backoff and the status line shows a live retry countdown.

## Privacy

Everything runs in your browser tab: the page never sends your wallet address anywhere except the public read-only APIs it queries, and nothing is stored server-side. Your address is kept in `localStorage` only so a reload remembers it.

## Run it anywhere

It's one static HTML file. Host on GitHub Pages, Cloudflare Pages, any static host — or just open `index.html` from disk. Nothing else is required.

## Data endpoints

- Public RPC: `https://robinhood-rpc.publicnode.com` (CORS `*`)
- Blockscout v2: `https://robinhoodchain.blockscout.com/api/v2` (CORS `*`, flaky under load)
- DexScreener: `https://api.dexscreener.com/tokens/v1/robinhood/{ca}` (CORS `*`)
- GoPlus security: `https://api.gopluslabs.io/api/v1/token_security/4663?contract_addresses={ca}` (CORS echoes origin)

## Roadmap

- [ ] Live trader tape / top-PnL panel — robinhoodtrenches.com API has no CORS headers; needs a tiny proxy (Cloudflare Worker) first
- [ ] LP position value detection (Uni v3/v4, Revert, hook wrappers) — needs the same worker for Blockscout position endpoints
- [ ] Gas inclusion via per-tx fee lookups

## License

MIT
