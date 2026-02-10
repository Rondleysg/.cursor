---
name: qa-webdriverio-add-new-test
description: Add a new WebdriverIO test (browser, app, or E2E) following project conventions: expect from @wdio/globals, lib/Utils for device access, test-data when applicable, Arrange-Act-Assert. Use when the user asks to add a test, create a test, or add a test for a flow.
---

# Add new test (browser, app, or E2E)

When adding a new test to the QA WebdriverIO project, follow these conventions.

## Imports

- **Always** import `expect` from `@wdio/globals`.
- Use `getDeviceFromCapabilities('browser')` or `getDeviceFromCapabilities('mobile')` from [lib/Utils.ts](../../../lib/Utils.ts) when you need the session directly; otherwise use Page Objects (browser) or Screen Objects (app).

## Browser test

- Use Page Objects (e.g. `LoginPage`, `SecurePage`) that internally use the browser session, or call `getDeviceFromCapabilities('browser')` in the spec.
- Use data from `test-data/<fluxo>/inputs.json` when applicable; import from test-data path relative to spec.
- Arrange-Act-Assert; descriptive test name (scenario + expected result).
- baseURL comes from config (lib/env when needed).

**Example:** See [docs/06-como-adicionar-novo-teste.md](../../../docs/06-como-adicionar-novo-teste.md) (browser example).

## App test (mobile)

- Use Screen Objects (e.g. `TabBar`, `LoginScreen`, `NativeAlert`) or `getDeviceFromCapabilities('mobile')` and helpers from `lib/Utils` (getElementByTestIDApp, getElementByAccessibilityLabelApp).
- Use data from `test-data/<fluxo>/inputs.json` or a builder when applicable; use [lib/data-factory.ts](../../../lib/data-factory.ts) for varying data.
- Arrange-Act-Assert; descriptive test name.

**Example:** See [docs/06-como-adicionar-novo-teste.md](../../../docs/06-como-adicionar-novo-teste.md) (app example).

## E2E test (browser + app)

- Place in `test/e2e/`; use both `getDeviceFromCapabilities('browser')` and `getDeviceFromCapabilities('mobile')`; optionally `reLaunchApp(emulator)` from `lib/Utils` or fixtures.
- Can run steps in sequence or in parallel (`Promise.all`).

## Checklist

- [ ] File under `test/specs/<fluxo>/` or `test/e2e/` with extension `.ts` (and spec pattern in wdio.shared.conf)
- [ ] Import `expect` from `@wdio/globals`; device access via Page/Screen Objects or `lib/Utils`
- [ ] Use test-data when there are reusable inputs; use builder or data-factory when varying data
- [ ] Test name describes scenario and expected result

## Full reference

[docs/06-como-adicionar-novo-teste.md](../../../docs/06-como-adicionar-novo-teste.md)
