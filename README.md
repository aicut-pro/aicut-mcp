# aicut MCP server

Generate AI **videos and images** straight from Claude, Claude Code, or any MCP client - no API key, no dashboard round-trip.

- **Server URL:** `https://mcp.aicut.pro/mcp`
- **Transport:** Streamable HTTP
- **Auth:** OAuth 2.1 with dynamic client registration + PKCE. You sign in with your normal aicut account and approve once.
- **Setup page:** https://www.aicut.pro/mcp

This repository is the public metadata for the hosted server: there is nothing to install and nothing to run locally.

## Install

### Claude Code

```bash
claude mcp add --transport http aicut https://mcp.aicut.pro/mcp
```

Then run `/mcp`, pick `aicut`, choose **Authenticate**, and approve in the browser tab that opens.

### Claude (web and desktop)

Settings -> Customize -> Connectors -> **Add** -> **Add custom connector**, name it `aicut`, paste `https://mcp.aicut.pro/mcp`, hit **Connect** and approve.

### Any other MCP client

Point it at `https://mcp.aicut.pro/mcp` over Streamable HTTP. Clients that implement the MCP OAuth flow register themselves; clients with no browser can use an aicut API key as a bearer token instead (keys are issued by hand - email support@aicut.pro).

## Tools

| Tool | What it does |
| --- | --- |
| `list_models` | The curated video and image catalog with the exact cost of every settings combination. Call this first - model ids and their accepted values cannot be guessed. |
| `get_balance` | Tokens left on the account. |
| `generate_video` | Starts a video generation. Asynchronous: returns a job id. Supports `estimate_only` to price a request without spending. |
| `get_video` | Polls a video job and returns the finished asset. |
| `list_videos` | Recent videos on the account. |
| `generate_image` | Generates an image. Supports `estimate_only`. |
| `get_image` | Fetches an image by id. |
| `list_images` | Recent images on the account. |

Read tools are annotated `readOnlyHint`; the generate tools carry `destructiveHint` and `idempotentHint: false` so a client asks before spending. Generations spend tokens from the connected aicut account at the same prices as the app, and land in that account's library.

## Docs and policies

- Setup and FAQ: https://www.aicut.pro/mcp
- Privacy policy: https://www.aicut.pro/privacy
- Terms: https://www.aicut.pro/terms
- Support: support@aicut.pro
