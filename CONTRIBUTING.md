# Contributing to OmniStatus

Thanks for helping. This project is intentionally small and strict, so a little preparation makes review quick.

## Before you start

- Node.js 24 or newer.
- `npm install` then `npm run verify` must pass on your machine.
- Run `npm run guard` before every commit. It rejects source comments, em dashes, placeholder markers, and accidental secrets.

## Ways to contribute

1. **New integrations.** One website per pull request. Follow [Creating integrations](docs/creating-integrations.md). Include fixture tests and set `verifiedAt` only after checking the live site.
2. **Selector fixes.** Sites change markup. Include the date you checked, update `verifiedAt`, and add or adjust a fixture so the expectation is documented.
3. **Bridge and protocol work.** Every change to validation, authentication, or framing needs a test that fails without the change.
4. **Documentation.** Corrections are welcome, especially when they remove a mismatch between docs and code.
5. **Bugs.** A minimal reproduction with the diagnostics report is more useful than a long description.

## Ground rules

- No source comments. Code should explain itself through names and structure. Reasoning goes in `docs/`.
- No em dashes in code, docs, UI text, or commit messages.
- No unfinished-work markers or placeholder text. Unfinished work stays out of a pull request.
- No stub functions, fake behavior, or disabled tests.
- No new runtime dependencies without a written justification in the pull request. The extension must stay dependency free.
- No telemetry, analytics, remote configuration, remote code, or accounts.
- Do not claim an integration works if you did not test it. Leave `verifiedAt` null and explain what could not be checked.
- Do not weaken validation, origin checks, or rate limits to make a test pass.

## Workflow

```bash
git checkout -b feature/short-name
npm install
npm run verify
git commit -m "Add Bandcamp integration"
git push origin feature/short-name
```

Open a pull request describing the behavior change, the tests you ran, and anything you could not verify.

## Adding an integration

1. Add `src/integrations/<id>.ts` using `defineIntegration`.
2. Register it in `src/integrations/registry.ts`.
3. Add fixture tests in `tests/integrations.test.ts`.
4. Run `npm test` and `npm run typecheck`.
5. Load the extension, enable the integration, and check the card on the live site.
6. Set `verifiedAt` to today's date if it works, or leave it null with a `verificationNote`.

## Adding a bridge or protocol feature

- Put shared logic in `src/protocol` so both sides use one implementation.
- Extend `tests/protocol.test.ts` for validation rules.
- Extend `tests/bridge.test.ts` for anything that touches the socket, authentication, or Discord IPC.
- Update `docs/protocol.md` in the same pull request.

## Commit messages

Short imperative subject. Body explains why. Example:

```text
Rate limit SET_ACTIVITY in the bridge

Discord closes connections that exceed five updates per 20 seconds.
Coalescing in the extension is not enough when two tabs fight over
presence, so the bridge now paces the final write.
```

## Review

A pull request is ready when:

- `npm run verify` is green.
- The behavior matches the documentation in the same change.
- New listeners, timers, and observers have cleanup.
- Tests would fail without the change.
- No personal data appears in fixtures, logs, or screenshots.

## Code of conduct

Participation is covered by [CODE_OF_CONDUCT.md](CODE_OF_CONDUCT.md).

## Security

Do not report vulnerabilities in public issues. Follow [SECURITY.md](SECURITY.md).

## License

By contributing you agree that your work is licensed under the project's MIT license.
