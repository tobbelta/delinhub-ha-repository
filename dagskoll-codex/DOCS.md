# Dagskoll Codex

Runs Codex CLI with ChatGPT subscription authentication for Dagskoll analysis.
Requires a compatible ChatGPT account with available Codex usage.

The image is built and tested on GitHub for ARM64 and AMD64. Home Assistant only
downloads the ready image. Do not install the old 0.1.0 local-build prototype.

## Setup

1. Install this app from the Dagskoll repository.
2. Set a long random `access_token`. This protects the internal bridge; it is not
   an OpenAI API key. Store the same value in Dagskoll's bridge configuration.
3. Start the app and open its log. Follow the ChatGPT device-code login link.
4. Complete login using the ChatGPT account with the subscription. Enable device
   code login in account security settings if necessary.

Login credentials are stored privately in this app's configuration directory.
Never include them in images, GitHub, or support messages. The app has Home
Assistant API permission for explicit read-only MCP context questions. It has no
Home Assistant configuration mount, Supervisor API permission or host Docker socket.

Only one analysis runs at a time. A simultaneous request waits at most eight
seconds, then returns an uncharged `busy` response. Ordinary execution times out
after three minutes; explicit HA-context jobs use 170 seconds after fetching
context. There is no fallback to paid OpenAI API requests. The bridge
listens on internal port 8787 with no host port exposed. Installing it alone
does not switch Dagskoll's provider.

The default model is `gpt-6-luna` with `reasoning_effort: low`. Model access and
supported reasoning levels must be verified with the actual account; `medium`
is an explicit alternative. Fast mode and subagents are disabled. Models still
run in OpenAI's cloud and need internet. A ChatGPT subscription has shared usage
limits; this bridge cannot report a reliable percentage of the plan's allowance.

## Sparse operation and usage

`daily_start_limit` defaults to 40 and can be reduced to 1–40. It counts CLI
launch attempts, including errors, timeouts and interrupted runs, for each
calendar day in Europe/Stockholm. A news refresh may use several AI runs; it is
not counted as one refresh. Authentication failures, invalid input and rejected
busy/paused requests do not use a start. The three-hour news schedule is set in
Dagskoll separately.

The root-owned private `/config/usage-budget.json` is atomically saved and fsynced
before every launch attempt. Limits and token counters survive restarts. After
three consecutive failures the bridge pauses new starts for 30 minutes. A
successful run clears the failure streak. An interrupted reservation is counted
as a failure after restart. Corrupt, inconsistent or unwritable state blocks
starts; do not delete it to bypass the daily allowance. Inspect and repair it
while the app is stopped, preserving the used-start count.

Authenticated `GET /status` on the internal port returns today's starts,
successes, failures, remaining starts, reset time, cooldown and CLI-reported
usage including cached tokens. Use the same Bearer token as for `/v1/responses`.
It returns no prompts, credentials or account identity. Missing token usage on a
failed or interrupted CLI run means unknown usage, not zero actual consumption.
Incoming `max_output_tokens` is not a hard Codex CLI output-token limit.

Failed runs report only sanitized diagnostic labels: failure category, process
return code, known event counts, known MCP tool status and allowlisted HTTP error
codes. These appear in the authenticated error response and private app log.
Raw CLI stderr, prompts, answers, context contents and credentials are not logged.

## Explicit Home Assistant questions

For a request that needs live home state, send
`"metadata": {"homeassistant_context": true}` with the response request. The
credential-owning bridge parent performs MCP `initialize`, then exactly one
`tools/call` for `homeassistant__GetLiveContext` at the fixed
`http://supervisor/core/api/mcp`. It never calls actuation tools. The integration
must already be enabled in HA. The parent uses canonical JSON HTTP headers and
verifies the result directly, avoiding dependence on native CLI MCP calls.

The parent disables proxies and redirects, validates HTTP 200 JSON, matching
JSON-RPC IDs, no RPC error and a single text result containing HA
`success: true` with nonempty context. HTTP responses are limited to 128 KiB and
context to 32,768 characters; oversize responses fail rather than being silently
truncated. Each of the two HTTP calls has a five-second socket timeout. A failure
returns only safe stage/reason/status diagnostics before any AI start is counted.

The verified snapshot is passed in memory as labeled source data. Successful
responses include `homeassistant_context` metadata with `verified`, `fetched_at`,
`source` and `tool`, but no raw snapshot. The child model does not make this MCP
call itself, cannot actuate HA and never receives the Supervisor token. No
snapshot is written to disk or logs. The model's answer may describe home state
as requested by the caller.

All workers leave the controlled Home Assistant MCP server explicitly disabled.
News, quotes and ordinary requests do not fetch HA context. Unexpected MCP
servers in dedicated user/system config files block starts before quota
reservation. API keys and HA credentials are never forwarded to workers.
The app has Home Assistant API permission for the explicitly requested context
connection; it has no Supervisor API permission, host networking, host Docker
socket or Home Assistant configuration mount.

Live login, model availability, source-event compatibility and resource usage
must be verified on the target machine. Unit tests use synthetic CLI events;
they do not prove a real account can run the selected model.
