---
title: "Universal 250 Error Codes Registry"
description: "Standardized, machine-readable error codes directory with deterministic remediation guides across all system layers."
---

# Universal 250 Error Codes Registry

To maintain institutional reliability, `stellar-x402-mcp` implements a standardized directory of **250 unique error codes**. Every error returned by a server or client contains structured metadata explaining the exact failure mode, HTTP status code, retryability flag, and deterministic remediation instructions.

---

## Category Allocation

The 250 error codes are partitioned across seven discrete operational categories:

| Range | Count | Category | Scope |
|---|---|---|---|
| **1000 - 1039** | 40 | `PROTOCOL` | MCP JSON-RPC 2.0 framing, transport disconnection, payload limits |
| **1040 - 1079** | 40 | `HORIZON` | Account not found, sequence mismatch, trustline missing, reserve limits |
| **1080 - 1119** | 40 | `SOROBAN` | Contract traps, host budget exhaustion, storage TTL expiration |
| **1120 - 1159** | 40 | `PAYWALL` | HTTP 402 challenge errors, underpayment, asset mismatch, verifier timeout |
| **1160 - 1189** | 30 | `REPLAY` | Nonce already claimed, duplicate transaction hashes, cache overflow |
| **1190 - 1219** | 30 | `CLIENT` | Agent budget caps exceeded, circuit breaker trips, key derivation |
| **1220 - 1249** | 30 | `DEX` | Path payment no path, slippage exceeded, empty orderbook, pool imbalance |

---

## Error Payload Schema

All error responses comply with the following JSON schema:

```json
{
  "error": {
    "code": 1120,
    "slug": "ERR_PAYWALL_PAYMENT_REQUIRED",
    "category": "PAYWALL",
    "httpStatus": 402,
    "retryable": false,
    "message": "Request requires payment header (HTTP 402 Payment Required)",
    "remedy": "Generate authorization header with transaction receipt and resubmit"
  }
}
```

---

## Primary Error Codes Reference

### Protocol & Transport (1000 - 1039)

| Code | Slug | HTTP | Retryable | Remedy |
|---|---|---|---|---|
| `1000` | `ERR_PROTOCOL_INVALID_JSONRPC` | 400 | No | Verify jsonrpc is 2.0 and request fields comply with MCP standard |
| `1001` | `ERR_PROTOCOL_METHOD_NOT_FOUND` | 404 | No | Query tools/list to inspect all available tools on this server |
| `1002` | `ERR_PROTOCOL_INVALID_PARAMS` | 422 | No | Check argument names, types, and constraints against tool schema |
| `1003` | `ERR_PROTOCOL_INTERNAL_ERROR` | 500 | Yes | Retry with exponential backoff; check server logs if fault persists |
| `1008` | `ERR_PROTOCOL_REQUEST_TIMEOUT` | 504 | Yes | Increase client timeout or inspect network connectivity to server |

---

### Paywall & Verification (1120 - 1159)

| Code | Slug | HTTP | Retryable | Remedy |
|---|---|---|---|---|
| `1120` | `ERR_PAYWALL_PAYMENT_REQUIRED` | 402 | No | Request requires payment header (HTTP 402 Payment Required) |
| `1121` | `ERR_PAYWALL_CHALLENGE_MISSING` | 400 | No | Re-invoke tool without payment header to receive challenge |
| `1122` | `ERR_PAYWALL_CHALLENGE_EXPIRED` | 401 | No | Challenge exceeded validUntil epoch; obtain fresh challenge nonce |
| `1125` | `ERR_PAYWALL_SIGNATURE_INVALID` | 403 | No | Re-sign authorization challenge using authorized agent private key |
| `1126` | `ERR_PAYWALL_AMOUNT_UNDERPAID` | 402 | No | Transferred amount is lower than required challenge price |
| `1127` | `ERR_PAYWALL_ASSET_MISMATCH` | 400 | No | Paid asset does not match the token requested by merchant |

---

### Anti-Replay & Storage (1160 - 1189)

| Code | Slug | HTTP | Retryable | Remedy |
|---|---|---|---|---|
| `1160` | `ERR_REPLAY_HASH_ALREADY_USED` | 409 | No | Transaction hash was already redeemed; generate fresh payment |
| `1161` | `ERR_REPLAY_NONCE_REUSED` | 409 | No | Challenge nonce was previously spent; obtain new challenge |
| `1162` | `ERR_REPLAY_LEDGER_TOO_OLD` | 400 | No | Payment transaction ledger sequence is older than allowed window |

---

### Autonomous Agent Client (1190 - 1219)

| Code | Slug | HTTP | Retryable | Remedy |
|---|---|---|---|---|
| `1190` | `ERR_CLIENT_BUDGET_CALL_EXCEEDED` | 403 | No | Tool price exceeds maxSpendPerCall configured in BudgetPolicy |
| `1192` | `ERR_CLIENT_BUDGET_DAILY_EXCEEDED` | 403 | No | Daily cumulative spend limit reached; reset budget or wait 24h |
| `1193` | `ERR_CLIENT_PROVIDER_NOT_WHITELISTED` | 403 | No | Merchant public key is not present in allowedRecipients whitelist |
| `1198` | `ERR_CLIENT_CIRCUIT_OPEN` | 503 | Yes | Circuit breaker is open due to RPC faults; retry after cool-down |
| `1200` | `ERR_CLIENT_FINALITY_TIMEOUT` | 504 | Yes | Transaction did not reach terminal finality within polling deadline |
