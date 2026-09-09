---
title: "LlamaIndex.TS Integration"
description: "Connecting Stellar MCP tools into LlamaIndex BaseTool specifications for ReAct agent reasoning loops."
---

# LlamaIndex.TS BaseTool Adapter

The `@stellar-mcp/adapters/llamaindex` module provides `StellarMCPLlamaTool` and `createLlamaIndexTools`, connecting Stellar MCP tools directly to LlamaIndex agents.

---

## Installation

```bash
pnpm add @stellar-mcp/adapters @stellar-mcp/agent-client llamaindex
```

---

## Code Example

```typescript
import { createLlamaIndexTools } from '@stellar-mcp/adapters/llamaindex';
import { X402AgentMcpClient, InMemoryWalletSigner } from '@stellar-mcp/agent-client';
import { ReActAgent, OpenAI } from 'llamaindex';

const signer = InMemoryWalletSigner.fromSecret(process.env.AGENT_SECRET!);
const client = new X402AgentMcpClient({ signer });

// Convert MCP tools into LlamaIndex BaseTool instances
const llamaTools = createLlamaIndexTools(mcpTools, client);

// Initialize ReActAgent
const agent = new ReActAgent({
  tools: llamaTools,
  llm: new OpenAI({ model: 'gpt-4o' }),
});

const response = await agent.chat({
  message: 'What is the orderbook depth for XLM/USDC on the Stellar DEX?',
});

console.log(response.message.content);
```
