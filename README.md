# Inkloom MCP Server

**Inkloom** is an AI image & video generation studio. This repository documents the **remote Model Context Protocol (MCP)** server so directories and clients can discover and connect to it.

> Product: [https://inkloom.im](https://inkloom.im) · MCP landing: [https://inkloom.im/mcp](https://inkloom.im/mcp)

The production application source is **private**. This public repo exists for MCP directory listings (mcp.so, Glama, Smithery, Cline, awesome-mcp-servers, and the official MCP Registry).

## MCP endpoint

| | |
| --- | --- |
| **URL** | `https://inkloom.im/api/mcp` |
| **Transport** | Streamable HTTP |
| **Auth** | OAuth 2.1 (browser sign-in + consent). No API keys. |
| **Scopes** | `mcp:read` (list/quote/get), `mcp:write` (create generations that spend credits) |

OAuth discovery follows the usual MCP / RFC 9728 paths on `inkloom.im`:

- `/.well-known/oauth-protected-resource`
- `/.well-known/oauth-authorization-server`

## Connect in clients

### Cursor

Add to MCP settings (or `.cursor/mcp.json`):

```json
{
  "mcpServers": {
    "inkloom": {
      "url": "https://inkloom.im/api/mcp"
    }
  }
}
```

Then authenticate when prompted — your browser opens inkloom.im for sign-in and Allow access.

### Claude (desktop / app)

Settings → Connectors → Add custom connector → paste `https://inkloom.im/api/mcp` → Authenticate.

### Claude Code

```bash
claude mcp add --transport http inkloom https://inkloom.im/api/mcp
```

### Codex

```bash
codex mcp add inkloom --url https://inkloom.im/api/mcp
codex mcp login inkloom
```

### ChatGPT

Settings → Connectors → custom connector with `https://inkloom.im/api/mcp` (enable developer mode if required) → Authenticate.

### Agent setup prompt

You can also paste this into a coding agent:

```text
Please add the Inkloom MCP server to this client.
Type: remote HTTP (Streamable HTTP). URL: https://inkloom.im/api/mcp.
When it is added, start Inkloom's sign-in flow: open my default browser to inkloom.im so I can log in and click Allow access.
```

## Exposed tools

| Tool | Capability |
| --- | --- |
| `inkloom_list_models` | List active generation models, modes, options, SKUs, and credit pricing. |
| `inkloom_quote_generation` | Price a generation without creating it or spending credits. |
| `inkloom_create_generation` | Start an image/video generation (requires `mcp:write`). Pass `maxCredits` from a prior quote. |
| `inkloom_list_generations` | List generation tasks for the signed-in user. |
| `inkloom_get_generation` | Fetch one generation (including output URLs when complete). |

Server identity advertised at runtime: name `Inkloom`, version `1.1.0`.

Browsing models, quoting, and checking results are free. Generations cost the same credits as on the website, only after the client confirms the price via `maxCredits`.

## Discovery / verification files (production)

After deploy, these URLs on the product domain should return 200:

| Path | Purpose |
| --- | --- |
| https://inkloom.im/.well-known/mcp-registry-auth | Official MCP Registry HTTP domain proof |
| https://inkloom.im/mcp/server-card.json | Server card for clients / directories |
| https://inkloom.im/glama.json | Glama ownership / maintainers claim |

This repo also includes `server-card.json` and `glama.json` at the root for crawlers that read the GitHub repository directly.

## License

Documentation in this repository is provided for discovery and integration. The Inkloom product and private application code remain proprietary © Inkloom.
