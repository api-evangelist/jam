---
name: Triage a Jam bug report into a fix
description: Given a Jam recording link, pull the full technical context (details, console errors, failed network requests, user events, video) and turn it into an actionable root-cause summary, then leave a comment on the Jam.
api: mcp/jam-mcp.yml
operations: [getDetails, getConsoleLogs, getNetworkRequests, getUserEvents, getVideoTranscript, analyzeVideo, createComment]
---

# Triage a Jam bug report into a fix

Use the Jam MCP server (`https://mcp.jam.dev/mcp`, OAuth2 `mcp:read`/`mcp:write`, or a `jam_pat_` Bearer token). Equivalent CLI: `jam get …` / `jam create comment`.

## Steps

1. **Orient.** Call `getDetails` for the Jam to learn who filed it, the URL, and what happened.
2. **Read the errors.** Call `getConsoleLogs` filtered to `error`/`warn` levels to surface exceptions and stack traces.
3. **Inspect the network.** Call `getNetworkRequests` filtered to failing statuses (e.g. `5xx`) to find failed API calls, then correlate their timing with the console errors.
4. **Reconstruct the repro.** Call `getUserEvents` to get the ordered clicks/inputs/navigation that led to the bug. For video Jams, call `getVideoTranscript` and/or `analyzeVideo` for a structured account of the observed issue.
5. **Root-cause.** Combine the above into a hypothesis: the triggering user action, the failing request/response, and the console error it produced.
6. **Report back.** Call `createComment` on the Jam with a Markdown summary — root cause, evidence timestamps, and suggested fix. Pin it to the relevant video timestamp when known.

## Rules
- Read-only steps need `mcp:read`; `createComment` needs `mcp:write`.
- Treat an intents/transcript `status: not_requested` or `null vtt` as absence, not an error.
- Jam strips secrets/tokens from captured network data before it leaves the browser — do not expect raw credentials in payloads.
