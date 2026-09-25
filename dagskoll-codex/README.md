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
Never include them in images, GitHub, or support messages. The app has no access
to Home Assistant configuration, Supervisor API, or host Docker socket.

Only one analysis runs at a time. Requests time out after three minutes and do
not fall back to paid OpenAI API requests. The bridge listens on internal port
8787 with no host port exposed. Connecting Dagskoll requires its compatible
transport configuration; installing this bridge alone does not switch Dagskoll.

Subscription login and resource usage still require verification on the target
machine. GitHub tests verify the image and CLI without account credentials.
