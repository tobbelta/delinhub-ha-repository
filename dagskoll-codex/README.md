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

`daily_start_limit` defaults to 40 and can be set to 1–200. It limits CLI
launch attempts since the start of the Europe/Stockholm calendar day or the
last explicit manual allowance reset. Actual daily history always counts all
attempts, including errors, timeouts and interrupted runs. A news refresh may use several AI runs; it is
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

Authenticated `POST /settings` accepts exactly
`{"daily_start_limit": 100}` with an integer from 1 to 200 and returns the same
status object as `GET /status`. Booleans, strings, fractional numbers and extra
fields are rejected. Status includes `settings_supported: true` and
`max_daily_start_limit: 200` so clients can detect edit support. Settings requests never start an AI run. Home Assistant's
backend can proxy this internal endpoint for the phone without exposing the
bridge token to it; Supervisor API permission is not needed.

The selected limit is atomically saved with mode 0600 in
`/config/ai-settings.json`. This saved limit takes precedence over the addon's
`daily_start_limit` configuration; the addon value is the fallback only while no
saved setting exists. Both changes and daily rollover preserve this selection.
A limit change never rewrites the separate usage ledger, refunds starts, clears
failures or removes a cooldown. Lowering the limit below the current allowance's used count
shows zero remaining starts and blocks the next start; an already running job
can finish. Raising the limit permits additional starts, subject to the existing
cooldown. Changes are accepted while an AI job is running.

Invalid or unreadable saved settings block new AI starts. A failed settings
write also blocks new starts until a subsequent valid save or restart verifies
persisted state. An explicit valid settings save may replace corrupt settings,
but never repairs or replaces a corrupt usage ledger.

### Explicit manual allowance reset

Authenticated `POST /settings/reset` accepts exactly `{}`. It restores the full
selected allowance and records the reset atomically in the same ledger, without
starting AI. Actual `started`, `succeeded`, `failed`, token usage, task metrics,
failure streak and cooldown are preserved. A reset does not bypass the failure
circuit. It is refused with HTTP 409 `busy` while an AI request or pending
reservation exists. Unreadable settings, corrupt ledger and backward clock
movement fail closed; reset is never a ledger repair operation.

Status includes `quota_reset_supported: true`, `quota_started` (starts since the
last manual reset, or day start), `quota_reset_at` (nullable ISO timestamp), and
`manual_reset_count` for the current local day. `remaining_starts` is
`max(0, daily_start_limit - quota_started)`. `started` remains the honest actual
daily total and can exceed the selected limit after explicit manual resets.
At local midnight the allowance and reset count begin a new day; the selected
limit persists. A phone reset is a deliberate action with confirmation, never
an automatic retry. The backend reads status before and after resetting and
confirms the exact reset generation rather than assuming delivery succeeded.
A queued AI start may appear in the POST status before it is returned, so reset
confirmation checks the generation, timestamp, preserved daily history and
matching allowance offset rather than requiring the returned used count to be zero.

`task_metrics` contains only fixed workload labels and per-day started/succeeded/
failed counts, CLI-reported token usage, aggregate queue seconds and aggregate
execution seconds. Historical starts before this metric was introduced are not
retroactively classified. Task metrics survive restart and reset, and roll over
with the daily ledger. Missing CLI token counts still mean unknown consumption.
Neither these metrics nor the quota are a plan-percentage or token guarantee.

Failed runs report only sanitized diagnostic labels: failure category, process
return code, known event counts, known MCP tool status and allowlisted HTTP error
codes. These appear in the authenticated error response and private app log.
Raw CLI stderr, prompts, answers, context contents and credentials are not logged.

## Explicit read-only context profiles

`metadata.context_profile` is a fixed allowlist: `none` for ordinary jobs,
`dagskoll_status` for bounded status explanations, and `assist_snapshot` for the
existing explicitly requested home snapshot. It never accepts entity IDs, URLs,
tool names or general HA commands from the model. News and quotes keep their
existing behavior and receive no home context.

### Dagskoll status questions

The phone's Systemkontroll action publishes `ask_status` with a question of
1–400 characters and its normal unique command ID. The backend's
`DiagnosticsManager` reads already observed snapshots only. Its fixed profile
contains service/job status, source freshness, next-run time, cache/stale flags,
bounded counts, and categorized errors. Raw errors, article text, bookings,
health, portfolio amounts, holdings, accounts and device data are excluded.
Fund metadata is read from the existing retained Core publication, without a
new NAV request. Observation/attempt timestamps stay distinct from NAV dates.

The bridge validates this profile before reserving a start, uses a fixed
instruction/schema, and disables all external tools including web search and HA
MCP. The child has no HA credentials. Answers are limited to 2400 characters;
the caller's output-token hint is still not a hard CLI token ceiling. Errors
clear the prior answer. The sensor's `request_id` and a completed command receipt
must both match before the phone presents a new answer. A read is never a hidden
refresh or repair. Ordinary status/count displays require no model.

Identical in-flight questions share one execution when their sanitized context
revision and freshness minute match. Successful explanations can be reused for
at most 60 seconds in memory, with original observation and answer timestamps;
changed context invalidates the result. Cache, questions and answers are not
written by this manager or included in operational metrics. The result sensor
is published without MQTT retain; HA may still record entity state under its
normal recorder/access rules. No diagnostic polling starts AI automatically.

### Balanced log reviews

Home Assistant owns the daily 08:00 Stockholm schedule, durable manual requests
and report history. It requests `metadata.task="logs"`,
`metadata.context_profile="log_review"` and an optional `metadata.log_review`
object containing only `since` and `until` (ISO8601 with timezone, at most seven
days; default last 24 hours). The bridge parent fetches exactly the read-only
`LogReviewOverview` tool at `/api/mcp/dagskoll_logs` before reserving AI quota.
The fixed receipt-time window includes exact retained per-source/level counts,
bounded balanced evidence, current source health and explicit retention limits.
Historical severity artifacts and quiet-stream timeouts do not prove device failure.

The worker receives no HA credential and cannot use shell, MCP, web or repair
tools. Its closed JSON response contains `summary` (1200 characters), up to five
text-only `proposals` and up to six `limitations`. Every proposal's 1–8 evidence
IDs must match actual overview evidence. An empty proposal list is valid. The
prompt targets roughly 2500 output tokens; the caller's token hint is not a hard
CLI ceiling. Existing model, subscription authentication and budgets are preserved.

The protected response's `homeassistant_context.review` contains verified window,
coverage, counts, bounded trusted health and citation/source/time metadata, without
raw messages or attributes. HA validates it again before storing the report.
The model cannot select an action: HA may independently offer a narrowly fixed
manual collector-reconnect action from current trusted source metadata. That
separate admin flow never passes through this bridge or the read-only MCP API.

### Explicit home snapshot

For a request that needs live home state, send
`"metadata": {"homeassistant_context": true}` with the response request. The
credential-owning bridge parent performs MCP `initialize`, then exactly one
`tools/call` for `homeassistant__GetLiveContext` at the fixed
`http://supervisor/core/api/mcp`. It never calls actuation tools. The integration
must already be enabled in HA. The parent uses canonical JSON HTTP headers and
verifies the result directly, avoiding dependence on native CLI MCP calls.
The existing boolean selects `assist_snapshot`; specifying another profile
along with it is rejected. This Assist tool does not support an entity filter,
so it remains a deliberate broader snapshot, rather than a status-question path.

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

Total caller wait for MCP is limited to ten seconds, including both RPCs. At
most one private daemon reader can remain after timeout; late results are
discarded and further context reads fail before AI reservation while it is
still active. Socket timeouts remain five seconds. No late snapshot is reused.

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

The working pinned CLI remains in use. Per-task queue/execution/token metrics
provide evidence for any later warm-process investigation; no App Server
migration or promised token saving is implied. Official guidance distinguishes
[non-interactive jobs](https://learn.chatgpt.com/docs/non-interactive-mode) from
the richer [App Server protocol](https://learn.chatgpt.com/docs/app-server).
