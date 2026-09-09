# Stellar x402 MCP Documentation

Official Mintlify documentation repository for `stellar-x402-mcp`.

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
- `governance/`: Open source contribution standards modeled after Stellar-IndigoPay PR #1211.
