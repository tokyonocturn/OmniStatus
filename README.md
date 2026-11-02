<p align="center">
  <img src="assets/brand/icon-256.png" width="96" height="96" alt="OmniStatus icon" />
</p>

<h1 align="center">OmniStatus</h1>

<p align="center">
  <strong>Open source Discord Rich Presence for the web.</strong><br />
  A browser extension and a local bridge. No accounts, no telemetry, no cloud.
</p>

<p align="center">
  <a href="LICENSE"><img src="https://img.shields.io/badge/license-MIT-3ee0a5" alt="MIT license" /></a>
  <img src="https://img.shields.io/badge/node-24%2B-3ee0a5" alt="Node 24 or newer" />
  <img src="https://img.shields.io/badge/firefox-128%2B-3ee0a5" alt="Firefox 128 or newer" />
  <img src="https://img.shields.io/badge/chromium-116%2B-3ee0a5" alt="Chromium 116 or newer" />
  <a href="https://github.com/omnistatus/omnistatus/actions"><img src="https://img.shields.io/badge/CI-workflows-3ee0a5" alt="CI workflows" /></a>
</p>

![OmniStatus social preview](site/public/assets/social-preview.png)

## What it is

OmniStatus shows what you are watching, listening to, or reading as a Discord Rich Presence card. It is an independent, MIT licensed alternative to PreMiD built around one rule: your browsing activity never leaves your machine.

The browser cannot open Discord's local IPC socket, so OmniStatus ships a small bridge process that does. Everything else is deliberately small: a content runtime injected only on sites you enable, a typed integration per website, and a static website.

## Features

- Discord Rich Presence with details, state, elapsed time, artwork, and up to two buttons.
- 15 website integrations across video, music, social, developer, learning, and reading.
- Per-site host permissions. Nothing is granted for all URLs, and no content script runs on pages you did not enable.
- Presence selection across tabs, idle clearing, blocked sites, and privacy mode per integration.
- An authenticated local bridge with constant-time token checks, origin allowlisting, payload limits, and Discord's rate limits respected by design.
- Documentation, an integration directory, and per-integration pages generated from the same source as the extension.
- No analytics, no accounts, no remote code, and no third-party requests from the website.

## How it works

```text
browser page
   |  selectors and media session, only on enabled sites
   v
extension runtime  ->  ws://127.0.0.1:6742  ->  bridge  ->  Discord IPC
   ^                                              |
   |  validated activity, coalesced to 4s          |  SET_ACTIVITY
   +----------------------------------------------+
```

Learn more in [Architecture](docs/architecture.md) and [Protocol](docs/protocol.md).

## Supported sites

| Integration | Category | Status |
| --- | --- | --- |
| YouTube | Video | Verified 2026-11-01 |
| YouTube Music | Music | Verified 2026-11-01 |
| Twitch | Video | Verified 2026-11-01, offline channel |
| Vimeo | Video | Verified 2026-11-01 |
| Netflix | Video | Verified 2026-11-01, public pages |
| SoundCloud | Music | Verified 2026-11-01, public pages |
| Spotify Web Player | Music | Verified 2026-11-01, public pages |
| Bandcamp | Music | Verified 2026-11-01, playback not started |
| Deezer | Music | Verified 2026-11-01, public pages |
| GitHub | Developer | Verified 2026-11-01 |
| MDN Web Docs | Learning | Verified 2026-11-01 |
| Wikipedia | Reading | Verified 2026-11-01 |
| npm | Developer | Verified 2026-11-01 |
| Stack Overflow | Developer | Verified 2026-11-01 |
| Reddit | Social | Unverified, bot challenge blocked automation |

The status column is honest by design. Verification means the integration code was executed against a live DOM snapshot of the site collected on that date, and the resulting activity was inspected. Scoped notes list what a signed-out or automated session could not exercise. Selectors change; the directory on the website shows the same dates and notes.

## Install

Requirements: Node.js 24 or newer, the Discord desktop application, Firefox 128+ or Chromium 116+.

```bash
git clone https://github.com/omnistatus/omnistatus.git
cd omnistatus
npm install
npm run build

node src/bridge/cli.ts config --set-client-id <your Discord application id>
node src/bridge/cli.ts serve
```

The bridge prints a pairing token on first start. Load `dist/extension/chromium` from `chrome://extensions` with developer mode enabled, or `dist/extension/firefox/manifest.json` from `about:debugging`, then paste the token into the extension settings.

Full instructions: [Installation](docs/installation.md).
Discord application setup: [Discord application](docs/discord-application.md).
Problems: [Troubleshooting](docs/troubleshooting.md).

## Development

```bash
npm install
npm run verify        # guard, typecheck, tests, build, package
npm test              # Node test runner
npm run bridge        # start the bridge
npm run build:site    # static website into site/dist
npm run serve:site    # preview the built website
```

The test suite does not need a browser. Bridge tests run against a mock Discord IPC server over a real local socket, including authentication, activity forwarding, clearing, error recovery, and origin rejection. Integration tests run every extractor against selector fixtures.

See [Development](docs/development.md) for layout, conventions, and debugging.

## Architecture

| Path | Responsibility |
| --- | --- |
| `src/protocol` | Activity schema, validation, URL matching, coalescing rate limiter |
| `src/integrations` | One definition per website plus the registry |
| `src/extension` | Background routing and presence, page runtime, popup, options |
| `src/bridge` | Discord IPC transport, presence service, WebSocket server, CLI |
| `site` | Static website templates and styles |
| `docs` | Markdown documentation rendered by the site build |

The extension declares four install-time permissions and optional host access. It injects its page runtime with `scripting.executeScript` after a navigation matches an enabled integration, instead of declaring an always-on content script for all URLs.

Measured sizes for the v1.0.0 production build: 64.1 KB background worker, 47.2 KB page runtime, 17.6 KB options page, 8.9 KB popup, 160 KB packaged extension.

## Security and privacy

- The bridge listens on loopback only and requires a 256-bit token, compared in constant time.
- Connections must come from an extension origin; web pages are rejected.
- Activities are validated in the extension, in the bridge, and against Discord's documented limits.
- No credentials are requested from users. The Discord application id is public; the bridge never sees your Discord login.
- No telemetry, analytics, or crash reporting exists in the codebase.

Read: [SECURITY.md](SECURITY.md), [Privacy](docs/privacy.md), [Security model](docs/security.md).

Report vulnerabilities privately through GitHub security advisories, not public issues.

## Contributing

Pull requests are welcome. Start with [CONTRIBUTING.md](CONTRIBUTING.md) and the [integration authoring guide](docs/creating-integrations.md).

The project enforces a few strict rules: no source comments, no em dashes, no placeholder markers, no new dependencies without justification, and no claims of support for untested integrations.

## License

MIT. See [LICENSE](LICENSE).

OmniStatus is not affiliated with PreMiD, Discord, or any integrated website. All trademarks belong to their owners. No PreMiD source code, artwork, or assets are included.
