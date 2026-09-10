---
title: "Quickstart"
description: "Get started with Stellar x402 in under 5 minutes: set up a paywalled MCP server and query it with an auto-paying agent client."
---

# Quickstart Guide

This guide walks you through setting up a paywalled Stellar Model Context Protocol (MCP) server, configuring an auto-settling AI agent client, and executing a monetized tool call.

---

## 1. Prerequisites

Before starting, ensure you have:
- **Node.js**: Version 20.x or 22.x LTS (`node -v`)
- **Package Manager**: `pnpm`, `npm`, or `yarn`
- **Stellar Account**: A funded Stellar Testnet keypair. You can generate one via our CLI (`stellar-mcp wallet generate && stellar-mcp wallet fund <address>`) or using the Stellar Laboratory.

---

## 2. Installation

Install the required packages in your TypeScript project:

**Using pnpm:**
```bash
pnpm add @stellar-mcp/server @stellar-mcp/paywall @stellar-mcp/agent-client @stellar/stellar-sdk
```

**Using npm:**
```bash
npm install @stellar-mcp/server @stellar-mcp/paywall @stellar-mcp/agent-client @stellar/stellar-sdk
```

**Using yarn:**
```bash
yarn add @stellar-mcp/server @stellar-mcp/paywall @stellar-mcp/agent-client @stellar/stellar-sdk
```

---

## 3. Create a Paywalled MCP Tool

Create a server file `server.ts` that defines a tool monetized at 0.05 USDC per call:

```typescript
import { createServer } from 'node:http';
import { createStellarMcpServer } from '@stellar-mcp/server';
import { x402Tool, OnChainTransactionVerifier } from '@stellar-mcp/paywall';
import { StdioServerTransport } from '@modelcontextprotocol/sdk/server/stdio.js';

// 1. Initialize MCP Server with Testnet Horizon & Soroban RPC
const mcpServer = createStellarMcpServer({
  horizonUrl: 'https://horizon-testnet.stellar.org',
  sorobanRpcUrl: 'https://soroban-testnet.stellar.org',
});

// 2. Configure on-chain transaction verifier
const verifier = new OnChainTransactionVerifier({
  network: 'stellar:testnet',
  horizonUrl: 'https://horizon-testnet.stellar.org',
});

// 3. Define a paywalled tool handler using @x402Tool
export const getMarketAnalysis = x402Tool({
  price: '0.05',
  asset: 'USDC',
  recipient: 'GCU3OLWOTN7JWBCVSQHOZQ45YFSCXZA3NIIGS774T4Z6DFSVDKCBQ465',
  verifier,
})(async (args: { pair: string }) => {
  return {
    pair: args.pair,
    summary: 'Bullish volume expansion detected on Stellar DEX orderbooks.',
    timestamp: new Date().toISOString(),
  };
});

// 4. Connect via Standard I/O for AI desktop agents
async function main() {
  const transport = new StdioServerTransport();
  await mcpServer.connect(transport);
  console.error('[Quickstart] Stellar x402 MCP Server running on stdio');
}

main().catch(console.error);
```

---

## 4. Connect with an Auto-Paying AI Agent Client

In your agent script `agent.ts`, initialize the `@stellar-mcp/agent-client` with budget guardrails and in-memory Ed25519 signing:

```typescript
import { X402AgentMcpClient, InMemoryWalletSigner } from '@stellar-mcp/agent-client';
import { getMarketAnalysis } from './server.js';

async function runAgent() {
  // 1. Configure in-memory signer with agent secret seed
  const signer = InMemoryWalletSigner.fromSecret(process.env.AGENT_STELLAR_SECRET!);

  // 2. Initialize client with daily and per-call budget protection
  const client = new X402AgentMcpClient({
    signer,
    budgetPolicy: {
      maxSpendPerCall: 0.10, // Max 0.10 USDC per call
      dailyBudget: 2.00,     // Max 2.00 USDC per day
      allowedRecipients: ['GCU3OLWOTN7JWBCVSQHOZQ45YFSCXZA3NIIGS774T4Z6DFSVDKCBQ465'],
    },
  });

  console.log('Invoking paywalled tool...');

  // 3. Execute tool: 402 challenge is handled and settled automatically
  const result = await client.invokeTool(getMarketAnalysis, {
    pair: 'XLM/USDC',
  });

  console.log('Result received successfully:');
  console.log(result);
}

runAgent().catch(console.error);
```

---

## 5. Running the CLI Simulation

You can also test the entire end-to-end payment cycle instantly without writing code using our CLI:

```bash
npx @stellar-mcp/cli simulate --tool market_analysis --price 0.05 --asset USDC
```

Output:
```text
⚡ Simulating x402 Agent Payment Flow for [market_analysis]

[Step 1] Agent invokes tool without payment header...
[Step 2] Paywall halts execution with HTTP 402 Payment Required
  WWW-Authenticate: X402 realm="Stellar MCP Paywall", network="stellar:testnet", asset="USDC", price="0.05"
[Step 3] Agent Client signs challenge authorization and prepares settlement...
  Authorization Signed by Agent: GADYYB2CXK...
  Generated Settlement Tx Hash: 3d8b76437655a8b8b27c10a8b5b90b4d37686780931eeeb4d9ae2cec7eda4925
[Step 4] Agent resubmits tool call with X-Payment-Receipt...
[Step 5] Paywall verifier validated payment receipt!
  Tool execution unlocked. Duration: 142ms. Finality: Confirmed.
```

---

## Next Steps

- Learn about [Payment Modalities](/payments/overview) to support native XLM, SAC tokens, and off-chain state channels.
- Integrate with [Vercel AI SDK](/agent-frameworks/vercel-ai-sdk) or [LangChain](/agent-frameworks/langchain).
- Explore [Claude Desktop and Cursor Setup](/cli/overview#ide-configuration-templates).
