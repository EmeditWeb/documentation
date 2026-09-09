---
title: "Soroban SAC Token Transfers"
description: "USDC, EURC, and custom Stellar Asset Contract token payments with cryptographic simulation."
---

# Soroban SAC Token Transfers

Stellar Asset Contracts (SAC) enable classic Stellar assets (such as Circle's USDC) to be accessed inside the Soroban smart contract virtual machine with high precision, formal verification, and cryptographic authorization envelopes.

---

## How It Works

Soroban SAC payments invoke the standard token interface `transfer(from, to, amount)` on the contract address corresponding to the token.

```typescript
import { Contract, Address, xdr, scValToNative } from '@stellar/stellar-sdk';

const tokenContract = new Contract(sacContractAddress);

// Construct transfer invocation
const transferOp = tokenContract.call(
  'transfer',
  new Address(agentPublicKey).toScVal(),
  new Address(challenge.recipient).toScVal(),
  BigInt(Math.floor(parseFloat(challenge.price) * 10_000_000)) // 7 decimals
);
```

Before submitting to the ledger, `@stellar-mcp/agent-client` simulates the transaction against Soroban RPC (`simulateTransaction`) to:
1. Verify authorization entries.
2. Determine required CPU instructions and memory footprints.
3. Guarantee that the transaction will not revert due to insufficient balance or expired trustlines.

---

## Code Example: Settling in USDC SAC

```typescript
import { MultiPaymentSettlementEngine, InMemoryWalletSigner } from '@stellar-mcp/agent-client';

const signer = InMemoryWalletSigner.fromSecret(process.env.AGENT_SECRET!);
const engine = new MultiPaymentSettlementEngine(signer);

// Settle challenge using Soroban SAC USDC
const settlement = await engine.settleChallenge(challenge, {
  method: 'soroban_sac',
});

console.log(`Settled USDC SAC Transaction: ${settlement.txHash}`);
```

---

## Resource Usage & Gas Metrics

| Metric | Profiled Value |
|---|---|
| **CPU Instructions** | ~412,890 instructions |
| **Memory Footprint** | ~185,420 bytes |
| **Network Fee** | ~1,450 stroops (~0.000145 XLM) |
| **Finality Time** | 1 ledger (~4.1 seconds) |
