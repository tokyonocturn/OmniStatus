# Security policy

## Supported versions

| Version | Supported |
| --- | --- |
| 1.0.x | Yes |
| Older | No |

## Reporting a vulnerability

Do not open a public issue.

Use GitHub's private vulnerability reporting on the repository, or contact the maintainers listed in the repository profile. Include:

- OmniStatus version and browser target
- Operating system and Node.js version
- Reproduction steps, ideally a minimal test
- Impact and affected component
- Any suggested fix or mitigation

You can expect an acknowledgement within a few days. Confirmed issues are fixed privately, released, then disclosed with credit unless you ask otherwise.

## Scope

In scope:

- The bridge WebSocket server: authentication bypass, origin bypass, payload handling, denial of service reachable from a local process or web page
- The extension: permission bypass, unintended page access, injection, activity spoofing across origins
- Discord IPC handling: framing, handshake, error handling
- The build: accidental inclusion of secrets, unsigned remote code, manifest misconfiguration
- The website: content injection or third-party request introduced by the build

Out of scope:

- Attacks that require code execution as the same user, since that user can already talk to Discord's IPC socket
- Discord client vulnerabilities
- Browser sandbox escapes
- Social engineering
- Missing hardening headers on a static site with no dynamic content

## Security model summary

The bridge listens on `127.0.0.1` only. It requires a 256-bit pairing token, compared in constant time, and accepts connections only from `chrome-extension://`, `moz-extension://`, and `safari-web-extension://` origins. Activities are validated in the extension and again in the bridge. Discord updates are rate limited. No credentials are collected, and no remote code is loaded.

Full documentation: [docs/security.md](docs/security.md).

## Reporting a privacy issue

If you find a path where browsing data could leave the machine, treat it as a security issue and report it privately. The privacy model is documented in [docs/privacy.md](docs/privacy.md).

## Dependency policy

The project has two runtime dependencies: `ws` for the bridge and `marked` for the site build. Both are reviewed for install scripts and transitive weight. Extension code has no dependencies. Reports about a dependency vulnerability should include the advisory identifier.
