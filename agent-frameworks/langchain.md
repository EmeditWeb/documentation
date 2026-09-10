---
title: "LangChain.js Integration"
description: "Wrapping Stellar MCP tools as LangChain StructuredTool instances with auto-paying capabilities."
---

# LangChain.js StructuredTool Adapter

The `@stellar-mcp/adapters/langchain` module exports `StellarMCPLangChainTool` and `createLangChainTools`, allowing Stellar MCP tools to be invoked by LangChain ReAct agents.

---

## Installation

```bash
pnpm add @stellar-mcp/adapters @stellar-mcp/agent-client @langchain/core langchain
```

---

## Code Example

```typescript
import { createLangChainTools } from '@stellar-mcp/adapters/langchain';
import { X402AgentMcpClient, InMemoryWalletSigner } from '@stellar-mcp/agent-client';
import { ChatOpenAI } from '@langchain/openai';
import { initializeAgentExecutorWithOptions } from 'langchain/agents';

// 1. Initialize agent client with signer
const signer = InMemoryWalletSigner.fromSecret(process.env.AGENT_SECRET!);
const client = new X402AgentMcpClient({ signer });

// 2. Convert MCP tools to LangChain structured tools
const langchainTools = createLangChainTools(mcpTools, client);

// 3. Initialize LangChain Agent Executor
const llm = new ChatOpenAI({ modelName: 'gpt-4o', temperature: 0 });
const executor = await initializeAgentExecutorWithOptions(langchainTools, llm, {
  agentType: 'structured-chat-zero-shot-react-description',
  verbose: true,
});

const result = await executor.invoke({
  input: 'Inspect Soroban contract CDAVUNF5DHX2MWF33XDMY7WKVBSQZ3SXZDT2TPSNZPEB3Z4HHPPKTVGY and report its latest state.',
});

console.log(result.output);
```
