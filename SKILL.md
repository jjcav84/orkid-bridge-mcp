---
name: orkid-bridge-mcp
description: Cross-chain bridge quotes and routing for EVM tokens via the Orkid solver network — bridge USDC, WETH, and other tokens between Base, Ethereum, Arbitrum, Polygon, and Robinhood Chain (4663). Use when a user or agent needs to bridge crypto across chains, get an Across bridge quote, estimate bridge fill time, or move funds to/from Robinhood Chain tokenized equities — non-custodial, the depositor wallet signs.
license: MIT
metadata:
  author: orkid-labs
  version: "1.0"
  mcp-endpoint: https://orkidlabs.xyz/api/mcp
  npm: orkid-swap-mcp
  registry: xyz.orkidlabs/orkid-swap
---

# Orkid Cross-Chain Bridge

Bridge EVM tokens across chains through Orkid's `orkid_bridge_quote` tool —
Across protocol under the hood, non-custodial (unsigned tx data returned;
the depositor wallet signs and broadcasts).

## Endpoint

- **Streamable HTTP:** `https://orkidlabs.xyz/api/mcp`
- **stdio bridge:** `npx -y orkid-swap-mcp`

Discovery methods are unauthenticated. `tools/call` requires
`X-ORKID-API-Key` (free key at `https://orkidlabs.xyz/partner`) or x402
payment — `$0.002` USDC on Base per call, `scheme: "exact"`, EIP-3009.
On PaymentRequired, sign and retry with `_meta["x402/payment"]`.

## Tool: `orkid_bridge_quote`

Args:

- `originChainId` / `destinationChainId` — `1 | 8453 | 42161 | 137 | 4663`
  (Ethereum, Base, Arbitrum, Polygon, Robinhood Chain)
- `inputToken` / `outputToken` — symbol or contract address
- `amount` — positive integer in base units (e.g. `"25000000"` = 25 USDC)
- `depositor` — the wallet that will sign (required; validated)
- `recipient` — optional, defaults to depositor

Returns expected output amount, fill time, approval transactions, and the
unsigned swap transaction for the depositor to sign. Orkid never touches
the funds.

## Coverage notes

- **Robinhood Chain inbound verified**: Arbitrum → Robinhood and
  Base → Robinhood for ETH, WETH, and USDC → USDG — the on-ramp for
  tokenized equities.
- **Polygon** is bridge-only: no same-chain swaps on Polygon, but bridging
  in and out is live.
- **Same chain?** Use the swap tool instead — see the sibling skill
  `orkid-swap-mcp` for same-chain routing on Base, Ethereum, Arbitrum,
  Unichain, BSC, and Robinhood.

## Usage pattern

1. User states intent ("bridge 100 USDC from Base to Arbitrum").
2. Resolve token symbols to amounts in base units (USDC = 6 decimals).
3. Call `orkid_bridge_quote` with the user's wallet as `depositor`.
4. Return the unsigned transactions to the user for signing — never sign
   on their behalf.

## Links

- Docs: `https://docs.orkidlabs.xyz/docs/x402`
- Site: `https://orkidlabs.xyz`
- Official MCP Registry: `xyz.orkidlabs/orkid-swap`
