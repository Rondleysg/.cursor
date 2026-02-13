---
name: qa-webdriverio-maintain-conventions
description: Enforce QA WebdriverIO project conventions when editing tests or project files: expect from @wdio/globals, lib/Utils for device access, test-data for inputs, follow directory structure. Use when editing specs, refactoring, or when the user asks to align code with project standards.
---

# Maintain conventions (WebdriverIO)

When editing specs or other project files, ensure the QA WebdriverIO conventions are followed.

## Specs (test/)

- **Imports:** Import `expect` from `@wdio/globals`. Use `getDeviceFromCapabilities('browser')` or `getDeviceFromCapabilities('mobile')` from [lib/Utils.ts](../../../lib/Utils.ts) to get the session; do not rely on global `driver`/`browser` without proper typing.
- **Data:** Prefer [test-data](../../../test-data/) (inputs.json, builder.ts, Constants.ts) over hardcoding payloads in specs; use [lib/data-factory.ts](../../../lib/data-factory.ts) for varying data (randomEmail, randomString, randomNumber).
- **Structure:** Specs live in `test/<fluxo>/*.ts` (e.g. `test/login/`, `test/register/`); data in `test-data/<fluxo>/`; Page Objects in `pageobjects/`, Screen Objects in `screenobjects/`. Each flow has a corresponding **suite** in [configs/wdio.shared.conf.ts](../../../configs/wdio.shared.conf.ts) (`suites: { login: [...], register: [...] }`).
- **Tags (execução seletiva):** Inclua no nome do `it()` uma **tag de severidade** de acordo com a criticidade daquele caso: `@blocker`, `@critical`, `@normal`, `@minor`, `@trivial` (alinhado ao Allure). Ex.: `it('... @login @critical', async () => { ... })`. Opcionalmente `@web`/`@app` para contexto. Uso: `--mochaOpts.grep=@critical` ou `--suite login` (ver package.json: `test-ci-local:critical`, `test-ci-local:login`).
- **baseURL / env:** Use [lib/env.ts](../../../lib/env.ts) when baseURL or URLs need to be read from env; override via `.env` (see `.env.example`).
- **Allure:** Use Allure for elaborated reports: structure (`addEpic`, `addFeature`, `addStory` in `beforeEach`), `allureReporter.step()` for blocks; when relevant use classification (addSeverity, addTag), evidence (addAttachment), integration (addIssue, addTestId), context (addArgument). See [docs/10-allure-reporter.md](../../../docs/10-allure-reporter.md) — table "API Allure – quando usar".

## Checklist when editing

- [ ] Specs import `expect` from `@wdio/globals`; device access via `lib/Utils` (getDeviceFromCapabilities)
- [ ] Reusable or scenario-specific inputs are in test-data, not inline in specs
- [ ] New tests go under `test/<fluxo>/` (e.g. test/login/, test/register/); new data under `test-data/<fluxo>/`; new flow has entry in `suites` in wdio.shared.conf
- [ ] Test names include a severity tag per case (@blocker, @critical, @normal, @minor, @trivial); optionally @fluxo, @web, @app
- [ ] Browser tests use Page Objects; app tests use Screen Objects or helpers from `lib/Utils` (getElementByTestIDApp, etc.)
- [ ] Fixtures (e.g. loginFixture) are imported from `fixtures/` when reusing flows
- [ ] Allure: epic/feature/story, steps; severity/tag/attachment/issue/testId/argument when applicable (see doc table)

## References

[AGENTS.md](../../../AGENTS.md) — conventions summary  
[docs/02-estrutura-de-diretórios.md](../../../docs/02-estrutura-de-diretórios.md) — role of each directory
