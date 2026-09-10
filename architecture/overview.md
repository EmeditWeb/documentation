---
title: "Architecture Overview"
description: "Comprehensive 4-layer system design, security model, and transaction lifecycle for Stellar x402."
---

# System Architecture

Stellar x402 is structured as a 4-layer modular system designed for separation of concerns, zero private key leakage, and maximum fault tolerance under high-frequency autonomous agent workloads.

```mermaid
flowchart TD
    subgraph Layer1["1. Agent & Framework Layer"]
        LLM["Autonomous Agent / LLM Client"]
        Adapters["@stellar-mcp/adapters (Vercel, LangChain, LlamaIndex)"]
        Client["@stellar-mcp/agent-client"]
        Signer["InMemoryWalletSigner (Ed25519)"]
        Breaker["CircuitBreaker (Fail-Fast)"]
        Policy["BudgetPolicyTracker"]
    end

    subgraph Layer2["2. MCP Protocol & Transport Layer"]
        JSONRPC["MCP JSON-RPC 2.0 Engine"]
        Stdio["StdioServerTransport"]
        SSE["SSEServerTransport (HTTP / EventSource)"]
    end

    subgraph Layer3["3. Server & Paywall Engine Layer"]
        Server["@stellar-mcp/server (17+ Core Tools)"]
        Decorator["@x402Tool Decorator"]
        Verifier["OnChainTransactionVerifier"]
        Replay["ReplayProtector (LRU / Redis)"]
        Pricing["DynamicPricingEngine"]
        Registry["Universal 250 Error Codes Registry"]
    end

    subgraph Layer4["4. Settlement & Blockchain Layer"]
        Horizon["Stellar Horizon (DEX, Paths, SSE Streams)"]
        RPC["Soroban RPC (Simulation, Storage, Events)"]
        Channel["x402_channel Contract (CDAVUNF5...)"]
    end

    LLM --> Adapters
    Adapters --> Client
    Client --> Signer
    Client --> Breaker
    Client --> Policy
    Client <--> JSONRPC
    JSONRPC <--> Stdio
    JSONRPC <--> SSE
    Stdio <--> Server
    SSE <--> Server
    Server --> Decorator
    Decorator --> Verifier
    Decorator --> Replay
    Decorator --> Pricing
    Decorator --> Registry
    Verifier <--> Horizon
    Verifier <--> RPC
    Client -.-> Channel
```

---

## The Four Architecture Layers

### 1. Agent & Framework Layer (`@stellar-mcp/agent-client`, `@stellar-mcp/adapters`)
Responsible for managing agent credentials, spending policies, and framework bridges:
- **`InMemoryWalletSigner`**: Holds Stellar Keypairs strictly in memory. Private keys are never serialized to disk, never printed in error logs, and never sent over the network.
- **`BudgetTracker`**: Enforces strict financial limits (`maxSpendPerCall`, `dailyBudget`, and `allowedRecipients`). If an agent attempts to call a tool costing more than its budget, execution halts with error code `1192` (`ERR_CLIENT_BUDGET_DAILY_EXCEEDED`).
- **`CircuitBreaker`**: Detects repeated upstream RPC failures and opens the circuit, failing fast with 0 network calls until recovery.
- **Framework Adapters**: Bridges tools seamlessly into **Vercel AI SDK Core**, **LangChain.js**, and **LlamaIndex.TS**.

### 2. Transport Layer (`@modelcontextprotocol/sdk`)
Manages bidirectional communication between AI agent runtime processes and the MCP server:
- **`stdio` Transport**: Standard input/output pipes used by local desktop clients (Claude Desktop, Cursor, local CLI scripts).
- **`SSE` Transport**: Server-Sent Events over HTTP with JSON-RPC message framing, enabling remote multi-tenant agent deployments with bearer token authentication and session-isolated execution contexts.

### 3. Server & Paywall Engine (`@stellar-mcp/server`, `@stellar-mcp/paywall`)
Provides 17+ Stellar and Soroban tools and wraps handlers with cryptographic verification:
- **`@x402Tool` Decorator**: Intercepts unauthenticated tool calls, calculates dynamic pricing, and issues structured HTTP 402 challenges.
- **`OnChainTransactionVerifier`**: Queries Horizon or Soroban RPC to cryptographically verify:
  1. Transaction submission status is successful.
  2. Transaction operations contain a valid payment to the designated recipient.
  3. Paid asset and amount match or exceed the challenge price.
  4. Nonce or memo matches the expected challenge identifier.
- **`ReplayProtector`**: In-memory LRU cache with Redis cluster backend support. Stores every redeemed transaction hash with time-to-live (TTL) expiration, preventing replay attacks across distributed server nodes.
- **`Universal 250 Error Codes Registry`**: Standardized directory categorizing all failure modes with HTTP status codes and deterministic remediation guides.

### 4. Settlement Layer (Horizon, Soroban RPC, and `x402_channel`)
The underlying decentralized ledger execution environment:
- **Stellar Horizon**: Sub-second payment processing, DEX orderbook liquidity, path payment discovery, and ledger event streaming.
- **Soroban RPC**: Smart contract simulation, state storage inspection, and event indexing.
- **`x402_channel` Smart Contract**: Deployed on Stellar Testnet (`CDAVUNF5...`). Implements bilateral state channels where agents sign zero-fee off-chain vouchers, settling on-chain only at closing.

---

## Distributed Replay Protection Architecture

For distributed server deployments behind load balancers, `ReplayProtector` supports two storage backends:

1. **In-Memory LRU Cache (Default)**: Optimized for single-instance or local stdio servers. Retains 100,000 hashes with automatic TTL garbage collection.
2. **Redis / Distributed Key-Value Store**: Uses atomic `SET key value NX EX <ttl>` operations. If a concurrent request attempts to redeem an identical transaction hash across any cluster node, the atomic reservation fails with error code `1160` (`ERR_REPLAY_HASH_ALREADY_USED`).

---

## Remote SSE Authentication & Session Isolation

When exposing MCP servers over HTTP/SSE:

- **Bearer Token Authorization**: Incoming requests must provide an `Authorization: Bearer <token>` header verified before JSON-RPC parsing.
- **Session Isolation**: Each connected SSE client receives an isolated connection ID. Replay nonces, budget counters, and active state channel references are scoped to the authenticated session context, preventing cross-agent crosstalk.
- **Transport Rate Limiting**: Token-bucket rate limiters enforce maximum requests per minute per IP and per API key.

---

## Budget Policy Evaluation Hierarchy

To prevent runaway agent spending or accidental drain, spending limits are evaluated in a strict hierarchy before any cryptographic signing takes place:

```mermaid
flowchart TD
    Req["Agent Initiates Tool Call"] --> Whitelist{"Recipient in allowedRecipients?"}
    Whitelist -- No --> ErrW["Reject: ERR_CLIENT_PROVIDER_NOT_WHITELISTED (1193)"]
    Whitelist -- Yes --> CallCap{"Cost <= maxSpendPerCall?"}
    CallCap -- No --> ErrC["Reject: ERR_CLIENT_BUDGET_CALL_EXCEEDED (1190)"]
    CallCap -- Yes --> DailyCap{"Cumulative Spend + Cost <= dailyBudget?"}
    DailyCap -- No --> ErrD["Reject: ERR_CLIENT_BUDGET_DAILY_EXCEEDED (1192)"]
    DailyCap -- Yes --> Circuit{"Circuit Breaker CLOSED?"}
    Circuit -- Open --> ErrB["Reject: ERR_CLIENT_CIRCUIT_OPEN (1198)"]
    Circuit -- Closed --> Sign["Produce Signature / Settle Payment"]
```

---

## Dynamic Pricing Derivation

Tools decorated with `@x402Tool` calculate required payment dynamically based on operational factors:

$$\text{Final Price} = (\text{Base Tool Price} + \text{Gas Overhead Buffer}) \times \text{Surge Multiplier}$$

- **Base Tool Price**: Configured per tool based on computational weight.
- **Gas Overhead Buffer**: Automatically added when settlement requires on-chain contract execution.
- **Asset Conversion**: When payment is requested in a non-native asset (e.g. USDC), the pricing engine queries the Stellar DEX orderbook to quote real-time exchange rates with strict slippage limits.

---

## The x402 Transaction Lifecycle

```mermaid
sequenceDiagram
    autonumber
    actor Agent as Autonomous Agent
    participant Client as @stellar-mcp/agent-client
    participant Server as @stellar-mcp/server
    participant Verifier as OnChainTransactionVerifier
    participant Ledger as Stellar Testnet / Soroban

    Agent->>Client: invokeTool("stellar_get_orderbook", args)
    Client->>Server: tools/call (no payment header)
    Server-->>Client: 402 Payment Required (Price: 0.05 USDC, Recipient, Nonce)
    Client->>Client: Check BudgetPolicy & Whitelist
    Client->>Ledger: Submit Payment / State Channel Voucher
    Ledger-->>Client: Transaction Confirmed (Hash: 0xabc...)
    Client->>Server: tools/call + X-Payment-Receipt (Hash, Proof)
    Server->>Verifier: verifyPayment(Hash, Nonce)
    Verifier->>Ledger: Validate On-Chain Payment
    Ledger-->>Verifier: Confirmed (Amount, Asset, Recipient OK)
    Verifier-->>Server: Payment Verified
    Server->>Server: Execute Tool Handler
    Server-->>Client: Tool Result Payload
    Client-->>Agent: Formatted Tool Response
```

---

## Security & Threat Model

| Threat | Mitigation Strategy | Implemented In |
|---|---|---|
| **Private Key Exposure** | Signer stores Ed25519 secret seed purely in volatile RAM. `toJSON()` sanitizes all key structures. | `InMemoryWalletSigner` |
| **Replay Attacks** | Every transaction hash is registered upon verification via atomic cache. Subsequent attempts with the same hash fail with code `1160`. | `ReplayProtector` |
| **Agent Infinite Loops** | Hard daily spending limit and per-call spending limit enforced client-side before any signature is produced. | `BudgetTracker` |
| **Network Outage Draining** | Circuit breaker enters `OPEN` state after consecutive network timeouts, preventing repeated wasted retries. | `CircuitBreaker` |
| **Price Slippage** | Dynamic pricing engine computes deterministic fees; challenges specify maximum valid timestamp (`validUntil`). | `DynamicPricingEngine` |
| **Unauthorized Tool Scraping** | Bearer token authorization and token-bucket rate limiting enforce authentication on remote SSE endpoints. | `SSEServerTransport` |
