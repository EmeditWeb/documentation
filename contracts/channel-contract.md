---
title: "x402_channel Smart Contract"
description: "Architecture, data structures, and Soroban methods of the x402_channel state channel contract."
---

# `x402_channel` Smart Contract

The `x402_channel` contract is written in Rust using the Soroban SDK. It manages the lifecycle of bilateral state channel deposits, off-chain voucher claims, cooperative closures, and dispute timeouts.

---

## Contract Methods

### 1. `open_channel`
Initializes a new channel session by locking funds from the payer:
```rust
pub fn open_channel(
    env: Env,
    payer: Address,
    merchant: Address,
    token: Address,
    deposit: i128,
    expiry: u64,
) -> u64
```
- **`payer`**: Public address of the funding agent.
- **`merchant`**: Public address of the paywalled tool author.
- **`token`**: SAC token contract address (e.g. USDC).
- **`deposit`**: Total funding allocated for micro-payments.
- **`expiry`**: Unix timestamp when the channel expires if inactive.
- **Returns**: Unique numeric channel ID.

---

### 2. `claim_payment`
Redeems accumulated earnings on-chain without closing the channel:
```rust
pub fn claim_payment(
    env: Env,
    channel_id: u64,
    amount: i128,
    nonce: u64,
    signature: BytesN<64>,
)
```
- Requires valid Ed25519 signature from the payer over `(channel_id, amount, nonce)`.
- Enforces strictly increasing nonces to prevent replay of earlier voucher states.

---

### 3. `close_channel`
Cooperatively closes the channel, disbursing earned funds to the merchant and returning unspent balance to the payer:
```rust
pub fn close_channel(
    env: Env,
    channel_id: u64,
    final_amount: i128,
    signature: BytesN<64>,
)
```

---

### 4. `refund_expired`
Allows the payer to recover their remaining deposit if the merchant becomes unresponsive and the channel has passed its `expiry` timestamp:
```rust
pub fn refund_expired(env: Env, channel_id: u64)
```

---

## On-Chain State Storage Optimization

The contract stores state using Soroban **instance storage** with persistent time-to-live (TTL) bump policies. This minimizes state serialization costs and keeps gas overhead to a minimum.
