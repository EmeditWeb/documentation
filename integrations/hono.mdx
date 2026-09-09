---
title: "Hono Edge Middleware"
description: "Protecting modern edge APIs on Cloudflare Workers, Deno, and Bun using Hono and x402."
---

# Hono Edge Integration

Hono provides ultra-fast web standards routing on edge runtimes (Cloudflare Workers, Vercel Edge, Deno, Bun).

---

## Code Example

```typescript
import { Hono } from 'hono';
import { createHonoPaywall, OnChainTransactionVerifier } from '@stellar-mcp/paywall';

const app = new Hono();

const verifier = new OnChainTransactionVerifier({ network: 'stellar:testnet' });

const paywall = createHonoPaywall({
  price: '0.05',
  asset: 'USDC',
  recipient: 'GCU3OLWOTN7JWBCVSQHOZQ45YFSCXZA3NIIGS774T4Z6DFSVDKCBQ465',
  verifier,
});

app.post('/api/ai/agent-tool', paywall, async (c) => {
  return c.json({
    message: 'Payment confirmed. Tool execution unlocked.',
    result: { data: 42 },
  });
});

export default app;
```
