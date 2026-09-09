---
title: "Custom Tools & @x402Tool"
description: "How to monetize custom MCP tools and functions using the @x402Tool decorator."
---

# Custom Tools with `@x402Tool`

The `@x402Tool` decorator is a higher-order function that wraps any async TypeScript function, MCP tool, or API handler with automatic HTTP 402 payment challenge verification.

---

## Basic Usage

```typescript
import { x402Tool, OnChainTransactionVerifier } from '@stellar-mcp/paywall';

const verifier = new OnChainTransactionVerifier({
  network: 'stellar:testnet',
});

// Protect a custom machine learning inference tool
export const runInference = x402Tool({
  price: '0.02',
  asset: 'USDC',
  recipient: 'GCU3OLWOTN7JWBCVSQHOZQ45YFSCXZA3NIIGS774T4Z6DFSVDKCBQ465',
  verifier,
})(async (input: { prompt: string; maxTokens: number }) => {
  // Handler logic executes only after payment is verified on-chain
  return {
    completion: `Inference result for "${input.prompt}"`,
    tokensUsed: 42,
  };
});
```

---

## Dynamic Pricing Support

Instead of a fixed price, `@x402Tool` can compute dynamic prices based on request arguments, token counts, or compute time:

```typescript
export const premiumSearch = x402Tool({
  dynamicPrice: (args: { query: string; depth: number }) => {
    // Charge 0.01 USDC base + 0.005 USDC per search depth level
    const calculatedPrice = 0.01 + args.depth * 0.005;
    return calculatedPrice.toFixed(4);
  },
  asset: 'USDC',
  recipient: 'GCU3OLWOTN7JWBCVSQHOZQ45YFSCXZA3NIIGS774T4Z6DFSVDKCBQ465',
  verifier,
})(async (args) => {
  return executeSearch(args);
});
```

---

## How It Protects Handlers

1. **Unpaid Request**: When called without an authorization header or transaction hash, `@x402Tool` immediately throws a structured `PaymentRequiredError` embedding the challenge headers.
2. **Paid Request**: When an agent provides a receipt (transaction hash or state channel voucher), `@x402Tool` passes it to `OnChainTransactionVerifier`. If verified, the wrapped handler executes and returns results.
3. **Double Spend Protection**: Once a transaction hash is used, `ReplayProtector` marks it spent. Subsequent attempts throw error code `1160` (`ERR_REPLAY_HASH_ALREADY_USED`).
