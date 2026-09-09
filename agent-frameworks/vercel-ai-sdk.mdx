---
title: "Vercel AI SDK Core Integration"
description: "Native adapter connecting Stellar MCP tools directly to Vercel AI SDK with automatic x402 payment settlement."
---

# Vercel AI SDK Core Adapter

The `@stellar-mcp/adapters` package provides first-class compatibility with the Vercel AI SDK Core (`ai` package). It transforms any collection of MCP tools into Vercel AI `tool()` definitions compatible with `generateText` and `streamText`.

---

## Installation

```bash
pnpm add @stellar-mcp/adapters @stellar-mcp/agent-client ai @ai-sdk/openai
```

---

## Code Example

```typescript
import { generateText } from 'ai';
import { openai } from '@ai-sdk/openai';
import { createVercelAITools } from '@stellar-mcp/adapters/vercel';
import { X402AgentMcpClient, InMemoryWalletSigner } from '@stellar-mcp/agent-client';

// 1. Initialize client with agent wallet and spending policies
const signer = InMemoryWalletSigner.fromSecret(process.env.AGENT_SECRET!);
const client = new X402AgentMcpClient({
  signer,
  budgetPolicy: {
    maxSpendPerCall: 0.10,
    dailyBudget: 2.00,
  },
});

// 2. Wrap Stellar MCP tools
const rawTools = [
  {
    name: 'stellar_get_balance',
    description: 'Check account XLM and token balances on Stellar Testnet',
    inputSchema: {
      type: 'object',
      properties: { accountAddress: { type: 'string' } },
      required: ['accountAddress'],
    },
    execute: async (args: { accountAddress: string }) => {
      // Tool invocation
      return { address: args.accountAddress, balance: '100.5 XLM' };
    },
  },
];

const tools = createVercelAITools(rawTools, client);

// 3. Pass tools directly into Vercel AI SDK agent loop
async function runAgent() {
  const { text } = await generateText({
    model: openai('gpt-4o'),
    tools,
    prompt: 'Check the balance for account GDCU3C3O7J2D4XJBE7FHD25VDLCPWZVDHCSH5XVLF7ZUZLFEE6KRS6RX',
  });

  console.log(text);
}

runAgent();
```

---

## Features

- **Schema Translation**: Automatically translates MCP JSON Schema into Zod objects required by Vercel AI SDK.
- **Silent Micro-Settlement**: If a tool returns an HTTP 402 challenge, the adapter negotiates and settles the payment via `@stellar-mcp/agent-client` before returning data to the language model.
- **Error Propagation**: Any budget overruns or RPC faults are mapped to the Universal 250 Error Codes Registry with remediation advice.
