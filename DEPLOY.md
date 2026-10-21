# Deploy & directory notes

## Production verification files

Served from the Inkloom web app (`apps/web/public/…` in the private monorepo):

- `/.well-known/mcp-registry-auth` — plain text `v=MCPv1; k=ed25519; p=…`
- `/mcp/server-card.json` — same payload as `server-card.json` in this repo
- `/glama.json` — same payload as `glama.json` in this repo

## Cloudflare / Smithery crawlers

Smithery scans with User-Agent `SmitheryBot/1.0 (+https://smithery.ai)` from Cloudflare Workers.

If scan returns **403**:

1. Prefer serving the static server card (already at `/mcp/server-card.json`; optionally also `/.well-known/mcp/server-card.json`).
2. Ensure unauthenticated MCP requests return **401** (not 403) so OAuth discovery works.
3. Cloudflare Free + Bot Fight Mode cannot be skipped with WAF custom rules. Options:
   - Security → WAF → Tools → IP Access Rules → Allow (only if you have a stable Smithery IP range — often unavailable for Workers egress)
   - Or disable Bot Fight Mode (site-wide)
   - Or upgrade to Pro and add Custom Rule: `(http.user_agent contains "SmitheryBot")` → Skip → Super Bot Fight Mode

No Cloudflare API credentials were available in this environment; apply the dashboard steps manually if Bot Fight Mode blocks Smithery.
