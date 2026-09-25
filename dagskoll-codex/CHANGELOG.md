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
