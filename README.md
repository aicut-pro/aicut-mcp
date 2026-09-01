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

50 tools. Read tools are annotated `readOnlyHint`; the tools that spend carry `destructiveHint` and
`idempotentHint: false`, so a client asks before spending. Every `generate_*` tool takes
`estimate_only: true` to price a request without creating anything - and on audio that is the only
reliable quote, because audio models publish a per-character or per-second RATE rather than a price
table. Generations spend tokens from the connected aicut account at the same prices as the app, and
land in that account's library.

### Catalog and account

| Tool | What it does |
| --- | --- |
| `list_models` | The curated video, image and audio catalog with the exact cost of every settings combination. Call this first - model ids and their accepted values cannot be guessed. Searchable and paged. |
| `get_balance` | Tokens left on the account, and its plan tier. |
| `list_voices` | The whole voice catalogue - ElevenLabs (stock, plus this account's cloned and custom voices), OpenAI and Amazon Polly in one shape, each with a verdict on what it can be used for. Paged. |
| `list_characters` | The account's uploaded characters and generated story cast members, in one list. |
| `list_series` | The AI Video Story catalog: which series (niches) an episode can be made in, with their episode-length ladder, model defaults and prices. |
| `list_series_ideas` | One series' curated episode ideas. |
| `browse_series` | Puts the series catalog on screen as a grid of covers whose demo clips play in place, on clients that render cards. Tapping selects, one Confirm writes the pick. |

### Video, image and audio

| Tool | What it does |
| --- | --- |
| `generate_video` | Starts a video generation. Asynchronous: returns a job id. |
| `get_video` | One video by id, with its status and finished url. |
| `list_videos` | The account's recent videos, paginated. |
| `delete_video` | Deletes one of the caller's own videos. No refund. |
| `generate_image` | Generates an image, and usually returns it finished in the same call. |
| `get_image` | One image by id. |
| `list_images` | Recent images, paginated. |
| `generate_audio` | Speech, music or a sound effect, chosen by `model`. |
| `get_audio` | One audio job by id. |
| `list_audio` | Recent audio, paginated. |
| `wait_for_generation` | The long poll: waits server-side and answers whether a job is terminal. |
| `show_generation` | Replays an earlier generation the user asks to see again. |

### Transforms - working from media you already have

What the server measures IS the price: a clip's length, a picture's dimensions, an audio's length.
Media the server cannot measure is refused before anything is charged rather than charged and then
failed.

| Tool | What it does |
| --- | --- |
| `upscale_video` | 2x or 4x an existing video. |
| `upscale_image` | More pixels in an existing image, from three upscalers: a general-purpose one, one that goes to 10x and is priced per output megapixel, and a creative one that repaints detail as it enlarges. Priced from the picture's measured dimensions. |
| `extend_video` | Continues an existing video by a chosen number of seconds. |
| `motion_control` | A character picture plus a reference clip, and the character performs that clip's motion. The reference clip's length is the price. |
| `generate_lipsync` | A character picture plus audio generated on this account, and the character speaks or sings it, lip-synced. The result is exactly as long as the audio, and the audio's measured length is the price. |

### Video analysis

| Tool | What it does |
| --- | --- |
| `analyze_video` | Reads an existing video instead of making one. Spends no tokens; capped per account per day. |
| `get_analysis` | One analysis by id. |
| `list_analyses` | Recent analyses, paginated. |

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
| `generate_story_video` | Stage 1: writes the episode, generates the start frames, and parks for review. `estimate_only` quotes the whole episode with its stage split. |
| `regenerate_story_frame` | Redraws one parked start frame. |
| `change_story_scene` | Changes what happens in one parked scene, then redraws its frame. |
| `set_scene_kept` | FREE. Removes a parked scene, so its video is never generated and never charged. |
| `fire_story_video` | Stage 2: generates the scene videos for the scenes you kept. Irreversible, and an episode cannot fire twice. |
| `render_story_video` | Stage 3: the final render. Free-tier renders carry a watermark. |

### One-call formats

These two write and render in a single call: no fire step, no render step.

| Tool | What it does |
| --- | --- |
| `generate_fake_text_video` | The fake-text chat-story format. The agent writes the message script; the server speaks it, measures the bubbles and renders it. The voice provider moves the price, so re-quote after changing it. |
| `generate_image_story` | The AI image story format. The agent writes the narration; the server segments it, writes the image prompts, generates every scene image, speaks it and renders it. Price is driven by the image count. |
| `list_image_story_styles` | aicut's authored image-story looks. Optional on a create, and picking one forces the pricier edit-capable image model - re-quote after adding one. |

### Publishing to social

| Tool | What it does |
| --- | --- |
| `list_social_accounts` | The TikTok, YouTube and Instagram accounts this user has connected. An empty list means they must connect one in a browser - this API cannot. |
| `prepare_post` | Stages a post of one of the account's finished videos, named by aicut id. Publishes nothing, and expires after 30 minutes. |
| `review_post` | The consent card: shows the actual video, the destination account and the caption, visibility and disclosure controls, and re-stages the person's own choices. Publishes nothing. |
| `publish_post` | IRREVERSIBLE: sends or schedules the prepared post. States the per-account rate ceiling rather than making an agent discover it. |
| `get_post_status` | What the platform says happened, from `prepared` through to `posted` or `failed`. |

### Trends

| Tool | What it does |
| --- | --- |
| `list_trends` | aicut's published trend feed. Read-only. |
| `get_recreate_brief` | How to recreate one trending video. Names the niche it can prove, and deliberately names no series. |

### Uploads - using your own footage and images

| Tool | What it does |
| --- | --- |
| `upload_media_widget` | The in-card file picker. The person chooses a file, the card uploads and confirms it, and posts the finished url back. The agent's whole job is to call this and wait. |
| `upload_media` | A signed upload target for a file on the user's own machine, plus the curl that fills it. |
| `confirm_upload` | The second half: re-reads the uploaded object, checks its magic bytes against the declared type and measures a video, refusing anything that fails. |

## Docs and policies

- Setup and FAQ: https://www.aicut.pro/mcp
- Privacy policy: https://www.aicut.pro/privacy
- Terms: https://www.aicut.pro/terms
- Support: support@aicut.pro
