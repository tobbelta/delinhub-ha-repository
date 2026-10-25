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
