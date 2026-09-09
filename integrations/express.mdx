---
title: "Express.js Middleware"
description: "Protecting Express.js HTTP and SSE endpoints with x402 payment challenges."
---

# Express.js Integration

The `@stellar-mcp/paywall` package exports native Express middleware for paywalling REST endpoints and SSE streams.

---

## Installation

```bash
pnpm add @stellar-mcp/paywall @stellar/stellar-sdk express
```

---

## Code Example

```typescript
import express from 'express';
import { createExpressPaywall, OnChainTransactionVerifier } from '@stellar-mcp/paywall';

const app = express();
app.use(express.json());

const verifier = new OnChainTransactionVerifier({ network: 'stellar:testnet' });

// Create paywall middleware requiring 0.05 USDC
const paywall = createExpressPaywall({
  price: '0.05',
  asset: 'USDC',
  recipient: 'GCU3OLWOTN7JWBCVSQHOZQ45YFSCXZA3NIIGS774T4Z6DFSVDKCBQ465',
  verifier,
});

// Protect premium data endpoint
app.post('/api/premium-data', paywall, (req, res) => {
  res.json({
    status: 'success',
    data: 'Proprietary institutional market depth insights.',
  });
});

app.listen(3000, () => {
  console.log('Paywalled Express API listening on port 3000');
});
```

---

## HTTP Response Behavior

- **Without Payment**: Returns `HTTP 402 Payment Required` with `WWW-Authenticate: X402 ...` and `X-Payment-Challenge` base64 headers.
- **With Valid `X-Payment-Receipt`**: Bypasses the paywall, calls `next()`, and attaches payment verification details to `req.paymentReceipt`.
