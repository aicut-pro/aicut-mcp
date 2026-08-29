# aicut MCP server

Generate AI **story episodes, videos, images and audio** straight from Claude, Claude Code, or any MCP client - no API key, no dashboard round-trip.

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

30 tools. Read tools are annotated `readOnlyHint`; the tools that spend carry `destructiveHint` and
`idempotentHint: false`, so a client asks before spending. Every `generate_*` tool takes
`estimate_only: true` to price a request without creating anything. Generations spend tokens from
the connected aicut account at the same prices as the app, and land in that account's library.

### Catalog and account

| Tool | What it does |
| --- | --- |
| `list_models` | The curated video, image and audio catalog with the exact cost of every settings combination. Call this first - model ids and their accepted values cannot be guessed. |
| `get_balance` | Tokens left on the account, and its plan tier. |
| `list_series` | The AI Video Story catalog: which series (niches) an episode can be made in, with their episode-length ladder and prices. |
| `list_series_ideas` | One series' curated episode ideas. |
| `list_characters` | The account's uploaded characters and generated story cast members. |

### Video, image and audio

| Tool | What it does |
| --- | --- |
| `generate_video` | Starts a video generation. Asynchronous: returns a job id. |
| `get_video` / `list_videos` | One video by id; the account's recent videos. |
| `delete_video` | Deletes one of the caller's own videos. |
| `generate_image` | Generates an image, and usually returns it finished. |
| `get_image` / `list_images` | One image by id; recent images. |
| `generate_audio` | Speech, music or a sound effect, chosen by `model`. |
| `get_audio` / `list_audio` | One audio job by id; recent audio. |
| `wait_for_generation` | The long poll: waits server-side and answers whether a job is terminal. |
| `show_generation` | Replays an earlier generation the user asks to see again. |

### Video analysis

| Tool | What it does |
| --- | --- |
| `analyze_video` | Reads an existing video instead of making one. Spends no tokens; capped per account per day. |
| `get_analysis` / `list_analyses` | One analysis by id; recent analyses. |

### AI Video Story - the staged episode flow

An episode is made in stages, and each stage has its own price, so nothing large is bought in one
blind step: the cast is written free and its portraits are bought; the episode's start frames are
charged and PARK for review; firing buys the scene videos; rendering buys the final file.

| Tool | What it does |
| --- | --- |
| `generate_cast` | FREE. Writes a story cast for a series and returns it to read. Mints nothing. |
| `generate_cast_portraits` | Buys one image per cast member and saves the roster to the library. |
| `regenerate_cast_portrait` | Redraws one cast member's face. |
| `describe_cast_member` | Changes what one cast member is, then redraws them. |
| `generate_story_video` | Stage 1: writes the episode, generates the start frames, and parks for review. |
| `regenerate_story_frame` | Redraws one parked start frame. |
| `change_story_scene` | Changes what happens in one parked scene, then redraws its frame. |
| `set_scene_kept` | FREE. Removes a parked scene, so its video is never generated and never charged. |
| `fire_story_video` | Stage 2: generates the scene videos for the scenes you kept. |
| `render_story_video` | Stage 3: the final render. Free-tier renders carry a watermark. |

## Docs and policies

- Setup and FAQ: https://www.aicut.pro/mcp
- Privacy policy: https://www.aicut.pro/privacy
- Terms: https://www.aicut.pro/terms
- Support: support@aicut.pro
