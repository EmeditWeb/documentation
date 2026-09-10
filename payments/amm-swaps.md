---
title: "AMM Liquidity Swaps"
description: "Automated market maker liquidity pool swaps for optimal on-chain payment execution."
---

# AMM Liquidity Swaps

Stellar protocol incorporates on-chain constant-product Automated Market Maker (AMM) liquidity pools directly into the ledger consensus layer. When routing micro-payments, Stellar x402 can route settlement through AMM pools for optimal pricing and minimal slippage.

---

## How It Works

Stellar x402 tools like `stellar_swap_tokens` and `stellar_get_liquidity_pools` inspect available liquidity pool reserves and calculate pool exchange rates:

```typescript
// Query pool liquidity reserves
const poolInfo = await mcpClient.callTool('stellar_get_liquidity_pools', {
  poolId: '04a56a64f...',
});
```

When settling a payment challenge, the `MultiPaymentSettlementEngine` can directly swap tokens against the pool reserves, ensuring immediate execution even if orderbook depth is thin.

---

## Code Example: AMM Swap Settlement

```typescript
import { MultiPaymentSettlementEngine, InMemoryWalletSigner } from '@stellar-mcp/agent-client';

const signer = InMemoryWalletSigner.fromSecret(process.env.AGENT_SECRET!);
const engine = new MultiPaymentSettlementEngine(signer);

const settlement = await engine.settleChallenge(challenge, {
  method: 'amm_swap',
});

console.log(`AMM Swap Payment Hash: ${settlement.txHash}`);
```
