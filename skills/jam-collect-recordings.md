---
name: Collect customer screen recordings with Recording Links
description: Create a reusable Jam Recording Link, verify the capture domain, harvest the Jams recorded through it, and revoke the link when the collection window closes.
api: mcp/jam-mcp.yml
operations: [createRecordingLink, listRecordingLinks, listRecordingLinkJams, getRecordingUrlVerifyLink, revokeRecordingLink]
---

# Collect customer screen recordings with Recording Links

Use the Jam MCP server (`https://mcp.jam.dev/mcp`) or the CLI `jam recording-links …`. Recording Links collect screen recordings + technical context from anyone with no install or account.

## Steps

1. **(Optional) Verify a capture domain.** If you want console logs and network requests captured from your own domain, call `getRecordingUrlVerifyLink` and complete the domain verification flow first (`listRecordingUrls` shows connected domains).
2. **Create the link.** Call `createRecordingLink` with a `name` (and optionally a folder, recording-url-id, reference, expiry, or metadata). Share the returned URL with customers.
3. **Track issuance.** Call `listRecordingLinks` (paginated via cursor) to see the workspace's active links and confirm yours is live.
4. **Harvest recordings.** Poll `listRecordingLinkJams` for the link to pull each new Jam as customers submit recordings; hand each Jam off to the *Triage a Jam bug report into a fix* skill.
5. **Close out.** When the collection window ends, call `revokeRecordingLink` to soft-delete the link so no further recordings are accepted.

## Rules
- `createRecordingLink`, `updateRecordingLink`, and `revokeRecordingLink` need `mcp:write`; the list/get reads need `mcp:read`.
- Auto-blur removes passwords, personal info, and secrets during Recording Link sessions before they reach Jam's servers — rely on it, don't disable it for customer captures.
