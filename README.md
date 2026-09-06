# 🦅 Robinhood Chain · Wallet Lens

A read-only, **client-side** portfolio viewer for [Robinhood Chain](https://robinhoodchain.blockscout.com/) (Arbitrum Orbit L2, chain ID **4663**). Paste any wallet address — or open a `?wallet=0x…` link — and see live on-chain balances with market prices, mcap, liquidity, volume and 24h change.

No server, no keys, no analytics. All data is fetched straight from public endpoints into your browser and auto-refreshes every 30 s.

**Try it:** enter any RH Chain wallet address above, or share a link like `…/?wallet=0x<address>` so someone else sees exactly what you see.

## What it shows

| Column | Source |
|---|---|
| Balances (ERC-20 + native ETH) | Blockscout explorer API (`/api/v2`) |
| Price, mcap, liquidity, vol, Δ24h | DexScreener (`/tokens/v1/robinhood/{ca}`), Blockscout rate fallback |
| Net worth / 24h Δ value | computed in-page |

Click any column header to sort. Symbol links go to the pair's DexScreener page.

## Honest limitations (v1)

A purely client-side snapshot can't know everything:

- **No cost-basis PnL.** Realized/unrealized PnL vs. entry price requires per-wallet transaction-history accounting (buys, sells, deposits). The chain history is public, so this is doable — it just needs a small backend or worker crawler, which is the v2 roadmap.
- **No LP / vault-held positions.** Tokens inside Uniswap v3/v4, hook-based (e.g. Fables) or Revert positions aren't ERC-20 balances and don't appear here.
- **No open orders, no token-security (honeypot/tax) checks** — see the GoPlus integration as a candidate v2 addition.
- Stablecoins price at $1 when no market pair exists; dust holdings are dropped.

## Privacy

Everything runs in your browser tab: the page never sends your wallet address anywhere except the public read-only APIs it queries, and nothing is stored server-side. Your address is kept in `localStorage` only so a reload remembers it.

## Run it anywhere

It's one static HTML file. Host on GitHub Pages, Cloudflare Pages, any static host — or just open `index.html` from disk. Nothing else is required.

## Data endpoints

- Public RPC: `https://robinhood-rpc.publicnode.com` (CORS `*`)
- Blockscout v2: `https://robinhoodchain.blockscout.com/api/v2` (CORS `*`)
- DexScreener: `https://api.dexscreener.com/tokens/v1/robinhood/{ca}` (CORS `*`)

## Roadmap

- [ ] v2: per-wallet cost-basis & realized/unrealized PnL (tx-history accounting service)
- [ ] LP position detection (Uni v3/v4, Revert, hook wrappers)
- [ ] Token security flags (GoPlus)
- [ ] Top-trader tape panel (public fills)

## License

MIT
