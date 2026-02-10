---
name: qa-webdriverio-add-fixture
description: Add or extend a WebdriverIO fixture in fixtures/index.ts (e.g. loginFixture, setup per device). Use when the user asks to add a fixture, inject test-data per flow, or reusable setup for browser/app.
---

# Add or extend fixture (WebdriverIO)

When adding or extending a fixture for the QA WebdriverIO project, edit [fixtures/index.ts](../../../fixtures/index.ts).

## Where

- **File:** [fixtures/index.ts](../../../fixtures/index.ts)
- **Pattern:** Fixtures are **async functions** that receive the browser or app instance (e.g. from `getDeviceFromCapabilities('browser')` or `getDeviceFromCapabilities('mobile')`). Export each function from `index.ts`; specs import and call them.

## Examples

**Fixture `loginFixture(browser)`:** Receives the app/browser session, fills login fields, clicks login, waits for home. See [docs/03-fixtures.md](../../../docs/03-fixtures.md).

**Fixture with test-data:** A function that loads inputs from `test-data/<fluxo>/inputs.json` and performs a flow (e.g. `checkoutFixture(browser, inputs.checkout)`).

**Fixture for E2E setup:** e.g. `reLaunchApp(emulator)` (already present) or a function that opens a given URL in browser and waits for a condition.

## Rules

- Keep exporting from `fixtures/index.ts`; specs import from `../fixtures` or `../../fixtures` (path relative to spec).
- Fixtures receive the WebdriverIO session (browser or mobile); use `lib/Utils` for selectors when needed (e.g. getElementByTestIDApp).
- Do not duplicate logic that belongs in Page Objects or Screen Objects; fixtures orchestrate steps.

## Full reference

[docs/03-fixtures.md](../../../docs/03-fixtures.md) — fixtures, how to add a new one, examples
