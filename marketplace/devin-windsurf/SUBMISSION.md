# QVeris submission packet for Devin Desktop and Windsurf

Last reviewed: 2026-09-03

## Recommended submission order

1. Submit the published stdio MCP package to the Devin Desktop / Windsurf MCP Marketplace.
2. Keep the hosted Streamable HTTP connection as a secondary install option until production is redeployed from the current MCP release.
3. Offer the Devin plugin to organizations that have access to the closed plugin beta.

The public MCP package is already registered in the official MCP Registry as `io.github.QVerisAI/mcp`. The public Devin documentation does not currently provide a self-service form for adding a vendor to the default MCP Marketplace. Use **Suggest MCP Integration** from the MCP settings page or contact `support@cognition.ai` and include the material below.

## Marketplace listing metadata

| Field | Value |
| --- | --- |
| Product name | QVeris |
| Server ID | `qveris` |
| Publisher | QVeris AI, LLC |
| Short description | Discover, inspect, validate, and call real-world API capabilities through one MCP server. |
| Category | Developer Tools / Data & Analytics / Productivity |
| Repository | https://github.com/QVerisAI/qveris-agent-toolkit |
| Documentation | https://qveris.ai/hosted-mcp |
| Website | https://qveris.ai |
| Privacy policy | https://qveris.ai/privacy |
| Terms | https://qveris.ai/terms |
| Security | https://qveris.ai/security |
| Support | support@qveris.ai |
| Icon | `assets/logo-color.png` |
| Official MCP Registry ID | `io.github.QVerisAI/mcp` |
| npm package | `@qverisai/mcp@0.14.0` |
| License | MIT |

## Primary Marketplace configuration

Use the published stdio package for the first review. It lists tools without credentials and gives actionable errors when a call is attempted before a key is configured.

```json
{
  "mcpServers": {
    "qveris": {
      "command": "npx",
      "args": ["-y", "@qverisai/mcp@0.14.0"],
      "env": {
        "QVERIS_API_KEY": "<user-provided secret>"
      }
    }
  }
}
```

Credential prompt:

- Name: `QVERIS_API_KEY`
- Label: QVeris API key
- Required: yes
- Secret: yes
- Help text: Create an API key at https://qveris.ai/account?page=api-keys. The key is sent only to QVeris APIs.

## Secondary Streamable HTTP configuration

Use this after the hosted deployment version and tool surface match the current release.

```json
{
  "mcpServers": {
    "qveris": {
      "serverUrl": "https://mcp.qveris.ai/mcp",
      "headers": {
        "Authorization": "Bearer <user-provided secret>"
      }
    }
  }
}
```

The equivalent Devin custom MCP form values are:

- Server Name: QVeris
- Icon: https://raw.githubusercontent.com/QVerisAI/qveris-agent-toolkit/main/assets/logo-color.png
- Short Description: Discover, inspect, validate, and call real-world API capabilities through one MCP server.
- Transport: HTTP
- Server URL: https://mcp.qveris.ai/mcp
- Authentication: Auth Header
- Header name: Authorization
- Header value: `Bearer <QVERIS_API_KEY>`

## Reviewer test prompts

1. Find two capabilities for current weather in Tokyo and compare their required parameters. Do not call either capability.
2. Find a read-only capability for recent United States inflation data, inspect it, and explain its expected cost.
3. Validate the parameters for a public company data capability before executing it.
4. Summarize QVeris usage for the last seven days.

Reviewers should expect the `call` tool to be marked destructive because it can route to both read-only and mutating third-party capabilities. QVeris requires the selected `tool_id` and `search_id` to come from discovery, and paid calls expose billing information that can be reconciled through usage history and the credits ledger.

## Current validation evidence

- Official MCP Registry record: `io.github.QVerisAI/mcp`
- Published npm package: `@qverisai/mcp@0.14.0`
- Hosted endpoint negotiates MCP protocol version `2025-11-25`
- Hosted endpoint returns HTTP 401 with `WWW-Authenticate: Bearer` when the credential is missing
- Authenticated initialization and `tools/list` succeed
- Current hosted tool count: eight (five canonical tools plus three deprecated aliases), below Cascade's 100-tool total limit
- Published stdio package tool count: nine (six canonical tools plus three deprecated aliases), also below the limit
- The current hosted deployment includes tool titles and read/write safety annotations

## Pre-submission readiness items

Resolve these before asking Cognition to test the hosted HTTP option:

1. Redeploy the hosted MCP from the current release. On 2026-09-03 it reported server version `0.10.1`, while the published package and repository metadata are `0.14.0`.
2. Align the hosted tool list with the documentation. The hosted endpoint did not expose the documented `probe` tool during validation.
3. Publish the Bearer credential input in the hosted Server Card. The current card advertises the remote endpoint but does not include the required Authorization header template.
4. Publish a new npm release from the current source before relying on tool annotations in the stdio review. The current repository source includes the annotations, but the published `0.14.0` `tools/list` response did not emit them during validation.
5. Decide whether Marketplace users may access mutating and billable capabilities. If the review policy requires a read-only integration, deploy a separate allowlisted endpoint instead of weakening annotations on the general-purpose `call` tool.

## Request to Cognition

Subject: QVeris MCP Marketplace listing request

Hello Cognition team,

We would like to list the official QVeris MCP server in the Devin Desktop / Windsurf MCP Marketplace.

QVeris gives agents a compact Discover → Inspect → Probe → Call workflow over 10,000+ external API capabilities, together with bounded usage and credit audit tools. The open-source server is published as `@qverisai/mcp@0.14.0` and is registered in the official MCP Registry as `io.github.QVerisAI/mcp`.

For the initial Marketplace review, we recommend the stdio configuration in this packet. It exposes fewer than 100 tools, lists tools before credentials are configured, and stores the user-supplied API key in `QVERIS_API_KEY`. The current source marks the general-purpose call tool as destructive/open-world; we can publish a patch release carrying those annotations before the technical review. A hosted Streamable HTTP endpoint is also available at `https://mcp.qveris.ai/mcp`.

Could you confirm the process for adding QVeris to the default Marketplace and whether you prefer the stdio or hosted HTTP configuration for review? We can provide a bounded test key and any additional security, privacy, or billing details privately.

Repository: https://github.com/QVerisAI/qveris-agent-toolkit  
Documentation: https://qveris.ai/hosted-mcp  
Privacy: https://qveris.ai/privacy  
Security: https://qveris.ai/security  
Support: support@qveris.ai

Thank you,  
QVeris AI

## Devin plugin fallback

This repository now contains a Devin-native plugin manifest at `.devin-plugin/plugin.json` and a root `.mcp.json`. The plugin bundles the existing `qveris` and `qveris-cli` skills with the pinned MCP package.

Plugins are currently in closed beta and do not apply to the classic Cascade agent. After Cognition enables plugin beta access, test locally with:

```bash
export QVERIS_API_KEY="<your-key>"
devin plugins install ./qveris-agent-toolkit
devin plugins list
```

For a managed cloud install, store `QVERIS_API_KEY` as a Devin secret and map it into the plugin environment. Never commit a real API key.
