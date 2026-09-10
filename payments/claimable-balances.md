---
title: "Claimable Balances & Escrow"
description: "Asynchronous, conditional, and time-locked multi-agent payments using Stellar Claimable Balances."
---

# Claimable Balances & Escrows

Stellar Claimable Balances enable payments that are not deposited directly into an account, but held in protocol escrow until specific claimants satisfy predetermined cryptographic or temporal predicates (such as `before_relative_time` or `not_before_absolute_time`).

---

## Use Case: Multi-Agent Workflow Coordination

In multi-agent collaborative networks (such as research agents delegating subtasks to verification agents):
1. **Agent A** creates a claimable balance for **Agent B**.
2. **Agent B** is granted 10 minutes to execute a compute job and claim the balance.
3. If **Agent B** fails to complete the task before the deadline, the funds automatically revert to **Agent A**.

---

## Code Example: Claimable Balance Settlement

```typescript
import { MultiPaymentSettlementEngine, InMemoryWalletSigner } from '@stellar-mcp/agent-client';

const signer = InMemoryWalletSigner.fromSecret(process.env.AGENT_SECRET!);
const engine = new MultiPaymentSettlementEngine(signer);

// Construct a claimable balance payment for the merchant
const settlement = await engine.settleChallenge(challenge, {
  method: 'claimable_balance',
});

console.log(`Claimable Balance Transaction: ${settlement.txHash}`);
```

---

## Server Verification

The `OnChainTransactionVerifier` confirms:
1. The transaction created a claimable balance where the server's public key is listed as an authorized claimant.
2. The claimant predicate allows immediate claiming (`Claimant.predicateUnconditional()`).
3. The escrowed asset and amount match or exceed the challenge requirement.
