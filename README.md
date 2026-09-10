# Stellar x402 MCP Documentation

Official documentation source repository for `stellar-x402-mcp`.

[![Live Documentation](https://img.shields.io/badge/Live_Documentation-Vercel_Portal-3e7bfa?logo=vercel)](https://stellar-x402-mcp.vercel.app/docs)
[![Showcase Portal](https://img.shields.io/badge/Showcase_Portal-Vercel-black?logo=vercel)](https://stellar-x402-mcp.vercel.app)
[![GitHub Monorepo](https://img.shields.io/badge/GitHub-Monorepo-181717?logo=github)](https://github.com/stellar-x402-mcp/monorepo)

> **Official Live Portal**: The complete interactive documentation portal with live search, 26 guides across 7 payment modalities, contract benchmarks, and 250 error codes is live at **[https://stellar-x402-mcp.vercel.app/docs](https://stellar-x402-mcp.vercel.app/docs)**.

## Local Development

To run the documentation site locally:

```bash
# Install Mintlify CLI
npm i -g mintlify

# Start local preview server
mintlify dev
```

The documentation preview will be accessible at `http://localhost:3000`.

## Repository Structure

- `mint.json`: Central navigation, branding, and color scheme configuration.
- `introduction.mdx`: Overview, problem statement, and architectural highlights.
- `quickstart.mdx`: 5-minute setup guide.
- `architecture/`: Deep dive into the 4-layer system design and threat model.
- `payments/`: Detailed guides for all 7 payment modalities on Stellar.
- `integrations/`: Express, Fastify, and Hono middleware integration tutorials.
- `agent-frameworks/`: Vercel AI SDK, LangChain, and LlamaIndex adapter guides.
- `contracts/`: Soroban `x402_channel` smart contract specification and gas benchmarks.
- `cli/`: `stellar-mcp` CLI command reference.
- `errors/`: Universal 250 Error Codes Registry with remediation guides.
- `governance/`: Open source contribution standards and pull request verification criteria.
