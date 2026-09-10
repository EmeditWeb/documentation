---
title: "CLI Reference"
description: "Command line interface reference for the stellar-mcp CLI binary."
---

# `stellar-mcp` CLI Reference

The `@stellar-mcp/cli` package provides a standalone binary for running MCP servers, inspecting Stellar and Soroban addresses, creating testnet wallets, and simulating payments.

---

## Installation

Run directly with `npx` or install globally:

```bash
# Direct execution with npx
npx @stellar-mcp/cli --help

# Global installation
pnpm add -g @stellar-mcp/cli
```

---

## Commands

### `serve`
Runs the Model Context Protocol server.

```bash
# Run on standard input/output (for Claude Desktop / Cursor)
stellar-mcp serve --transport stdio --network testnet

# Run over Server-Sent Events (SSE) on port 3000
stellar-mcp serve --transport sse --port 3000 --network testnet
```

Options:
- `-t, --transport <transport>`: Transport protocol (`stdio` or `sse`). Default: `stdio`.
- `-n, --network <network>`: Stellar network (`testnet` or `pubnet`). Default: `testnet`.
- `-p, --port <port>`: HTTP port for SSE. Default: `3000`.

---

### `inspect <address>`
Inspects a Stellar account (`G...`) or Soroban contract (`C...`).

```bash
# Inspect classic account
stellar-mcp inspect GDCU3C3O7J2D4XJBE7FHD25VDLCPWZVDHCSH5XVLF7ZUZLFEE6KRS6RX

# Inspect Soroban contract
stellar-mcp inspect CDAVUNF5DHX2MWF33XDMY7WKVBSQZ3SXZDT2TPSNZPEB3Z4HHPPKTVGY
```

---

### `wallet`
Generates and funds testnet keypairs.

```bash
# Generate a fresh Ed25519 keypair
stellar-mcp wallet generate

# Fund testnet account via Friendbot
stellar-mcp wallet fund GDCU3C3O7J2D4XJBE7FHD25VDLCPWZVDHCSH5XVLF7ZUZLFEE6KRS6RX
```

---

### `simulate`
Simulates an end-to-end x402 payment challenge and settlement cycle.

```bash
stellar-mcp simulate --tool soroban_execute_settlement --price 0.05 --asset USDC
```

---

### `benchmark`
Displays gas, CPU instruction, and fee benchmark tables comparing direct payments vs state channels.

```bash
stellar-mcp benchmark
```
