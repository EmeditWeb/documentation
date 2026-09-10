---
title: "Gas & Performance Benchmarks"
description: "Empirical CPU instruction, memory byte, and fee benchmarks profiled on Soroban SDK 22 with reproducible test methodology."
---

# Gas & Performance Benchmarks

To ensure institutional readiness, `contracts/x402_channel` was profiled using automated benchmark suites measuring CPU instructions, RAM byte consumption, and network fee scaling.

---

## Method Resource Profiles

| Operation | CPU Instructions | Memory Footprint | Base Network Fee |
|---|---|---|---|
| `open_channel` | 289,245 instructions | 112,575 bytes | 1,120 stroops |
| `claim_payment` | 320,109 instructions | 120,025 bytes | 1,210 stroops |
| `close_channel` | 322,217 instructions | 115,317 bytes | 1,240 stroops |
| **Off-Chain Voucher** | **0 instructions** | **0 bytes** | **0 stroops** |

---

## Scaling Comparison: 1,000 Micro-Payments

In high-throughput agent workflows (such as high-frequency trading agents, LLM search engines, or real-time data oracles):

```
Direct Soroban SAC Payments:
1,000 txs * ~412,890 CPU = ~412,890,000 CPU instructions
1,000 txs * ~1,450 stroops = ~1,450,000 stroops fee
Total Latency: 1,000 * 4s = 4,000 seconds (~66 minutes)

x402 State Channel (Ours):
1 open_channel tx = 289,245 CPU
1,000 vouchers = 0 CPU (Off-chain Ed25519 signature)
1 close_channel tx = 322,217 CPU
Total CPU: 611,462 CPU instructions (99.85% savings)
Total Fee: 2,360 stroops (99.84% fee savings)
Total Voucher Latency: < 1ms per call
```

---

## Reproducible Benchmark Methodology

To enable independent verification by security auditors and grant committees, the gas profiling harness is integrated directly into the contract test suite.

### Profiling Environment
- **Rust Toolchain**: `rustc 1.84.0+`
- **Target**: `wasm32-unknown-unknown`
- **Soroban SDK**: `soroban-sdk 22.0.0`
- **Host Budget Mode**: Non-metered test host with precise instruction capture via `env.budget().cpu_instruction_cost()` and `env.budget().memory_byte_cost()`
- **Ledger Environment**: Standalone Soroban test environment simulating Protocol 22 consensus parameters

### Execution Command

Run the benchmark test suite from the repository root:

```bash
# Navigate to the contract crate and execute profiling
cargo test -p x402_channel test_channel_lifecycle_and_gas_benchmarks -- --nocapture
```

### Automated Measurement Code

The benchmark harness measures CPU and memory delta around each method invocation:

```rust
// Snapshot host budget prior to execution
let cpu_start = env.budget().cpu_instruction_cost();
let mem_start = env.budget().memory_byte_cost();

// Execute state channel operation
client.open_channel(&channel_id, &agent, &merchant, &initial_deposit, &expiration_ledger);

// Compute exact delta consumed
let cpu_consumed = env.budget().cpu_instruction_cost() - cpu_start;
let mem_consumed = env.budget().memory_byte_cost() - mem_start;

println!("open_channel CPU: {}, Memory: {} bytes", cpu_consumed, mem_consumed);
```

---

## WASM Binary Size Optimization

Compiled using spec-shaking v2 (`SOROBAN_SDK_BUILD_SYSTEM_SUPPORTS_SPEC_SHAKING_V2=1`) with link-time optimization (LTO) and `opt-level = "z"`:

- **Unoptimized Size**: 6,685 bytes
- **Optimized Size**: **5,908 bytes** (under 6KB)
- **Deployment Hash**: `dcf795a2daff9472f0796ca0188e4f5cae0c868a68cd70dc755557708b4efdb3`
