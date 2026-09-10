---
title: "Path Payments & DEX Routing"
description: "Multi-hop decentralized exchange conversions allowing agents to pay in any asset while merchants receive their desired token."
---

# Path Payments (Strict Send & Strict Receive)

One of Stellar's greatest superpowers is its built-in decentralized exchange (DEX) and automated path payment routing engine. In Stellar x402, path payments solve the **asset mismatch** problem for autonomous agents.

---

## The Problem Solved

Suppose a merchant API tool charges **0.05 USDC**, but an autonomous agent only has **native XLM** or an algorithmic credit token. Under typical blockchain architectures, the agent would need to:
1. Approve a DEX router contract.
2. Execute a swap transaction.
3. Wait for finality.
4. Approve the merchant.
5. Execute the payment transaction.

This creates multi-step latency, multiple gas fees, and failure risks.

With **Stellar Path Payments**, the swap and payment occur in a **single atomic transaction**:
- The agent sends XLM.
- The Stellar protocol routes the order through DEX orderbooks and liquidity pools.
- The merchant receives exact USDC.
- Any unspent source asset is returned to the agent.

```mermaid
flowchart LR
    A[Agent Wallet (XLM)] -->|Step 1: Strict Receive Path| B[Stellar DEX / AMM Pool]
    B -->|Step 2: Auto-Convert| C[Merchant Wallet (USDC)]
```

---

## Code Example: Path Payment Settlement

```typescript
import { MultiPaymentSettlementEngine, InMemoryWalletSigner } from '@stellar-mcp/agent-client';

const signer = InMemoryWalletSigner.fromSecret(process.env.AGENT_SECRET!);
const engine = new MultiPaymentSettlementEngine(signer);

// Settle USDC challenge by spending XLM via strict-receive path payment
const settlement = await engine.settleChallenge(challenge, {
  method: 'path_payment',
});

console.log(`Path Payment Transaction Hash: ${settlement.txHash}`);
```

---

## Server Verification

The `OnChainTransactionVerifier` recognizes `path_payment_strict_receive` operations:
- Verifies that the recipient received at least `challenge.price` of `challenge.asset`.
- Disregards the source asset sent by the agent, ensuring merchant revenue is never exposed to slippage or conversion loss.
