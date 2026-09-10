---
title: "Fastify Middleware"
description: "Protecting Fastify microservices and MCP servers with high-performance x402 hooks."
---

# Fastify Integration

Fastify is commonly chosen for high-throughput microservices. `@stellar-mcp/paywall` provides a native `preHandler` hook.

---

## Code Example

```typescript
import Fastify from 'fastify';
import { createFastifyPaywall, OnChainTransactionVerifier } from '@stellar-mcp/paywall';

const fastify = Fastify({ logger: true });

const verifier = new OnChainTransactionVerifier({ network: 'stellar:testnet' });

const paywall = createFastifyPaywall({
  price: '0.01',
  asset: 'XLM',
  recipient: 'GCU3OLWOTN7JWBCVSQHOZQ45YFSCXZA3NIIGS774T4Z6DFSVDKCBQ465',
  verifier,
});

fastify.post('/v1/tools/oracle', { preHandler: paywall }, async (request, reply) => {
  return {
    rate: '0.2450 XLM/USDC',
    timestamp: Date.now(),
  };
});

fastify.listen({ port: 3000 });
```
