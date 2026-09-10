---
title: "Fee-Bump Sponsored Transactions"
description: "Gasless autonomous agent operations through relayer-sponsored fee-bump transactions."
---

# Fee-Bump Sponsored Transactions

Autonomous AI agents often manage token balances (such as USDC) but may not possess native XLM to pay transaction fees. Stellar's protocol-level **Fee-Bump Transactions** allow a third-party relayer or sponsoring entity to pay network fees on behalf of an agent without accessing its private keys.

---

## How Fee-Bumps Work

1. The agent signs an inner transaction executing the payment (e.g. 0.05 USDC to the tool merchant) with `fee: 0`.
2. The agent submits the inner transaction envelope to a relayer service or fee sponsor.
3. The sponsor wraps the inner transaction in a `FeeBumpTransaction`, provides the sponsor keypair to pay the network fee, signs, and broadcasts the envelope to Stellar.

```mermaid
flowchart LR
    Agent[Agent Wallet (USDC only)] -->|1. Inner Tx (0 XLM gas)| Relayer[Fee-Bump Relayer]
    Relayer -->|2. Wraps & Pays Gas (XLM)| Network[Stellar Ledger]
    Network -->|3. Settles USDC to Merchant| Merchant[Tool Merchant]
```

---

## Code Example: Constructing a Fee-Bump

```typescript
import { MultiPaymentSettlementEngine, InMemoryWalletSigner } from '@stellar-mcp/agent-client';
import { Keypair } from '@stellar/stellar-sdk';

const agentSigner = InMemoryWalletSigner.fromSecret(process.env.AGENT_SECRET!);
const engine = new MultiPaymentSettlementEngine(agentSigner);

const sponsorKeypair = Keypair.fromSecret(process.env.SPONSOR_SECRET!);

// Settle challenge with gas sponsored by the relayer
const settlement = await engine.settleChallenge(challenge, {
  method: 'fee_bump',
  feeSourceKeypair: sponsorKeypair,
  sponsoredFee: '200', // stroops
});

console.log(`Sponsored Fee-Bump Tx Hash: ${settlement.txHash}`);
```

---

## Benefits for Agent Deployment

- **Zero Gas Onboarding**: Agents can be provisioned with token grants without needing to maintain XLM minimum account reserves.
- **Congestion Escalation**: If network traffic spikes, the fee sponsor can bump the transaction fee dynamically without requiring the agent to re-sign the inner payload.
