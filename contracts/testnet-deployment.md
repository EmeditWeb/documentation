---
title: "Testnet Deployment & Verification"
description: "Live Stellar Testnet deployment details, contract IDs, and verification explorer links."
---

# Testnet Deployment

The `x402_channel` smart contract is live and deployed on the **Stellar Testnet**.

---

## On-Chain Contract Information

| Parameter | Value |
|---|---|
| **Contract ID** | `CDAVUNF5DHX2MWF33XDMY7WKVBSQZ3SXZDT2TPSNZPEB3Z4HHPPKTVGY` |
| **WASM Bytecode Hash** | `dcf795a2daff9472f0796ca0188e4f5cae0c868a68cd70dc755557708b4efdb3` |
| **WASM Upload Tx** | `b5aaea8e4a17638fad11ed5ab4a5a06d6172d87d5785f2167e81e8d44f2aab8c` |
| **Contract Deploy Tx** | `55324f4c7277b8596452723f894fce3061a019e7cf98d080f2dfea65f5144935` |
| **Network Passphrase** | `Test SDF Network ; September 2015` |
| **RPC Endpoint** | `https://soroban-testnet.stellar.org` |

---

## Verification Links

- **Stellar.expert Explorer**: [Inspect Contract on Stellar.expert](https://stellar.expert/explorer/testnet/contract/CDAVUNF5DHX2MWF33XDMY7WKVBSQZ3SXZDT2TPSNZPEB3Z4HHPPKTVGY)
- **Stellar Laboratory**: [Inspect on Stellar Lab](https://lab.stellar.org/r/testnet/contract/CDAVUNF5DHX2MWF33XDMY7WKVBSQZ3SXZDT2TPSNZPEB3Z4HHPPKTVGY)
- **Deployment Transaction**: [View Deployment Tx on Stellar.expert](https://stellar.expert/explorer/testnet/tx/55324f4c7277b8596452723f894fce3061a019e7cf98d080f2dfea65f5144935)

---

## Deploying Your Own Channel Contract

To deploy a custom instance of `x402_channel` to Testnet:

```bash
# 1. Build optimized WASM
stellar contract build --manifest-path contracts/x402_channel/Cargo.toml

# 2. Run automated provisioning script
./scripts/deploy_testnet.sh
```
