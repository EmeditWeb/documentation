---
title: "Mainnet Deployment & Security Hardening"
description: "Institutional guide for transitioning Stellar x402 from testnet prototyping to production mainnet execution."
---

# Mainnet Deployment & Operational Hardening

Transitioning autonomous agent settlement infrastructure to Stellar Mainnet requires strict operational guardrails, hardware-backed key protection, and disciplined failover policies.

This guide details the security practices, key rotation procedures, and configuration standards required for enterprise deployments.

---

## 1. Key Management Architecture

In testnet environments, `InMemoryWalletSigner` keeps Ed25519 secret seeds in volatile memory. In production mainnet environments, agents must never possess direct access to raw private seeds.

### KMS and HSM Key Custody

For high-value production agents, implement an institutional key custody layer:

1. **AWS KMS / Google Cloud KMS**: Store agent signing keys within dedicated Cloud KMS keys configured with asymmetric signing (`ECC_ED25519_SHA_512`).
2. **HashiCorp Vault**: Leverage the Vault Transit Secrets Engine for policy-driven agent signing with granular role-based access control (RBAC).
3. **Hardware Security Modules (HSMs)**: Enterprise settlement nodes should sign through PKCS#11 compliant HSM interfaces or Ledger hardware devices.

```typescript
import { KMSClient, SignCommand } from "@aws-sdk/client-kms";
import { Keypair } from "@stellar/stellar-sdk";

export class KMSAgentSigner {
  private kms: KMSClient;
  private keyId: string;
  public publicKey: string;

  constructor(keyId: string, publicKey: string) {
    this.kms = new KMSClient({ region: "us-east-1" });
    this.keyId = keyId;
    this.publicKey = publicKey;
  }

  async sign(payload: Buffer): Promise<Buffer> {
    const command = new SignCommand({
      KeyId: this.keyId,
      Message: payload,
      MessageType: "RAW",
      SigningAlgorithm: "ED25519"
    });
    const response = await this.kms.send(command);
    return Buffer.from(response.Signature!);
  }
}
```

---

## 2. Key Rotation Cadence & Revocation

Automated agent keys should follow strict lifecycle policies:

| Key Tier | Purpose | Recommended Rotation | Revocation Method |
|---|---|---|---|
| **Hot Agent Signer** | Submits micro-payments and state channel vouchers | 30 Days | Stellar `set_options` signer removal |
| **Channel Admin** | Authorizes contract upgrades and channel disputes | 90 Days | Multi-sig threshold update |
| **Vault / Reserve** | Holds primary capital pools | 365 Days | Cold-storage multi-sig rotation |

### Immediate Key Revocation Procedure

If an agent process is compromised or behaves erratically:

1. Execute a Stellar `set_options` transaction from an authorized master key or threshold signer to set the compromised public key weight to `0`.
2. Terminate the agent process container to purge runtime state.
3. Drain any remaining state channel balances via `claim_payment` or channel dispute resolution.
4. Issue a new Ed25519 keypair via KMS and grant minimal operating threshold weight.

---

## 3. Multi-Signature Contract Administration

The `x402_channel` Soroban smart contract should never be controlled by a single private key on Mainnet.

Configure a **2-of-3** or **3-of-5** multi-signature threshold for administrative actions:

```
Contract Admin Scheme:
  Signer A (Operations Lead KMS): Weight 1
  Signer B (Security Officer Hardware Key): Weight 1
  Signer C (Institutional Multi-Sig Safe): Weight 1
  
  Low Threshold (Query / Health): 1
  Medium Threshold (Fee Updates): 2
  High Threshold (Contract Upgrade / Emergency Freeze): 3
```

---

## 4. Horizon & Soroban RPC Failover

Single-node RPC connections create single points of failure that can trigger unintended circuit breaker trips. Production agents must configure redundant endpoint pools with automated health checking.

```typescript
const rpcEndpoints = [
  "https://mainnet.sorobanrpc.com",
  "https://horizon.stellar.org",
  "https://soroban-backup.enterprise.io"
];

// Configure automatic retry and failover across healthy endpoints
const client = new StellarAgentClient({
  network: "mainnet",
  rpcUrls: rpcEndpoints,
  healthCheckIntervalMs: 15000,
  maxConsecutiveFailures: 3
});
```

---

## 5. Mainnet Pre-Flight Checklist

Complete this checklist prior to funding and launching production agent wallets:

- [ ] **Minimal Capital Allocation**: Agent hot wallets are funded with strictly bounded balances (e.g., maximum 48 hours of projected operational spending).
- [ ] **Hard Daily Budget Enforced**: `BudgetTracker` is configured with `dailyBudget` and `maxSpendPerCall` caps that cannot be bypassed via agent prompt injection.
- [ ] **Strict Merchant Whitelist**: `allowedRecipients` contains only verified, audit-approved tool provider addresses.
- [ ] **Circuit Breaker Active**: `failureThreshold: 3` and `coolDownPeriodMs: 60000` verified with automated test triggers.
- [ ] **Replay Store Persistence**: Redis or distributed key-value store is configured with TTL matching ledger finality bounds.
- [ ] **Audit Logging Enabled**: Every transaction hash, nonce, and HTTP 402 challenge receipt is recorded to tamper-evident audit storage.
- [ ] **Contract Verification**: The deployed `x402_channel` contract bytecode hash matches the verified repository build hash.
