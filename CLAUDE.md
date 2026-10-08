# aicut-mcp - agent instructions

THIS REPOSITORY IS GENERATED. `README.md`, `server.json` and this file are copied verbatim from
`aicut-pro/backend` `services/api/mcp-server/public/` by that repo's
`.github/workflows/publish-mcp-public.yml`, which opens or updates one PR here (branch
`generated-from-backend`) after every green CI run on backend `main`. Only `LICENSE`, `.gitignore`
and `.github/CODEOWNERS` are owned here.

A PR that edits any of the three generated files HERE is wrong - the next sync overwrites it. Open it
on the backend instead, where `services/api/mcp-server/src/rosterDrift.test.ts` pins the README's
tool table, counts and server URLs to the server's own roster and constants, and `server.json`'s
fields to the same. Publishing `server.json` to the MCP registry (`mcp-publisher`) stays a manual
step.
