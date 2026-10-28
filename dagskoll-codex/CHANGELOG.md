# 0.2.0

- Add a fixed log_review profile with balanced, bounded LogReviewOverview evidence from native HA MCP, fetched before AI quota reservation.
- Validate the closed review schema and every proposal citation against actual evidence. Return coverage and trusted source metadata without raw logs in report history.
- Keep the AI worker free of HA credentials, MCP, shell, web and repair tools. Safe manual collector reconnection is authorized and executed separately by Home Assistant.

# 0.1.9

- Add a fixed read-only log_snapshot profile that reads SearchLogs through Home Assistant MCP before reserving AI quota. Keep CLI tools disabled, preserve subscription login and all budget settings.
- Bound queries to 31 days/50 events, validate MCP schema and constrain answer citations to actual returned events. Log text is treated as untrusted data; no device actions or log changes are available.

# 0.1.8

- Add explicit authenticated quota reset while retaining real daily starts, usage, failure streaks and cooldowns. A separate quota epoch determines the remaining allowance; reset refuses active or corrupt state and never launches AI.
- Add bounded task-labelled usage, queue and execution metrics to the persistent ledger without logging prompts or home data.
- Support a fixed read-only Dagskoll status profile with bounded saved diagnostic metadata, no tools and no hidden source refresh. Keep ordinary news/quote and explicit HA MCP paths separate.
- Bound the aggregate HA-context wait with a single guarded worker and retain strict response/identity checks, subscription authentication and existing concurrency controls.

# 0.1.7

- Add authenticated `POST /settings` for changing the daily AI start limit from 1 to 200, with a default of 40. Return the effective status and counters for the phone's usage controls.
- Persist the selected limit atomically in a private settings file without modifying, refunding or resetting usage, failure streaks or cooldowns. Changes take effect during active runs and survive restarts and day rollover.
- Block new starts if saved settings are corrupt or cannot be persisted; valid explicit settings updates preserve the separate usage ledger.

# 0.1.6

- Fetch explicitly requested HA context deterministically in the credential-owning parent through MCP initialize and the fixed read-only GetLiveContext call; no Home Assistant credential or active MCP server reaches the AI worker.
- Use canonical JSON HTTP headers, no proxy or redirects, strict RPC/result validation and bounded response/context sizes. Context failures return safe stage/status diagnostics before any AI start is counted.
- Attach only verified/fetched-at/source/tool metadata to successful responses and label the in-memory snapshot as source data. Context jobs use a 170-second CLI timeout; ordinary jobs retain 180 seconds.

# 0.1.5

- Report sanitized CLI failure categories, return codes, event counts, known MCP tool status and allowlisted HTTP error codes to the protected response and app log.
- Distinguish CLI exit, missing answer, failed turn and missing successful HA context without logging stderr, prompts, context contents, credentials or arbitrary tool names.

# 0.1.4

- Restore Home Assistant's injected environment through `with-contenv` when s6 starts the app, so explicitly requested MCP context can receive its authorized Supervisor token.
- Report context credential availability as a boolean in authenticated `/status`; return a specific uncharged Home Assistant context error instead of a misleading ChatGPT login error when the token is missing.

# 0.1.3

- Add a persistent ceiling of 40 CLI start attempts per Stockholm calendar day, including failures, timeouts and interrupted runs. Corrupt or unwritable budget state blocks new runs.
- Pause new starts for 30 minutes after three consecutive failures; preserve usage and cached-token counters across restarts.
- Add an authenticated internal `/status` endpoint and an eight-second concurrency wait before an uncharged busy response.
- Select the configured model explicitly (default `gpt-6-luna`, low reasoning) with Fast and subagents disabled; keep subscription-only authentication and no paid API fallback.
- Use a separate working directory for each run and retain structured search-result evidence without treating model-written URLs as verified sources.
- Allow explicit Home Assistant context requests through MCP with only `homeassistant__GetLiveContext`. Other workloads receive no Home Assistant credential and leave that MCP server disabled; unexpected configured MCP servers block starts.

# 0.1.2

- Use Home Assistant's base image and distribution Python, matching Dagskoll's runtime approach, after Python exited with SIGBUS on the HA host.
- Print startup progress and enable Python fault diagnostics without logging credentials.
- Test the actual container startup and HTTP authentication in both architecture builds.

# 0.1.1

- Build and test ARM64 and AMD64 images in GitHub Actions before publishing Home Assistant metadata.
- Home Assistant downloads a finished image; no local build is needed.
- Manual startup until subscription login and configuration are completed.
- Preserve actual CLI web-search events for source validation.

# 0.1.0

- Initial local prototype. Withdrawn: do not build on the Home Assistant host.
