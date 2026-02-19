---
name: qa-webdriverio-maintain-conventions
description: Enforce QA WebdriverIO project conventions when editing tests or project files: expect from @wdio/globals, lib/Utils for device access, test-data for inputs, follow directory structure. Use when editing specs, refactoring, or when the user asks to align code with project standards.
---

# Maintain conventions (WebdriverIO)

When editing specs or other project files, ensure the QA WebdriverIO conventions are followed.

## Specs (test/)

- **Imports:** Import `expect` from `@wdio/globals`. Use `getDeviceFromCapabilities('browser')` or `getDeviceFromCapabilities('mobile')` from [lib/Utils.ts](../../../lib/Utils.ts) to get the session; do not rely on global `driver`/`browser` without proper typing.
- **Data:** Prefer [test-data](../../../test-data/) (inputs.json, builder.ts, Constants.ts) over hardcoding payloads in specs; use [lib/data-factory.ts](../../../lib/data-factory.ts) for varying data (randomEmail, randomString, randomNumber).
- **Structure:** Organização por **domínio** (app-cliente, log, manager, partners). Specs em `test/<dominio>/<fluxo>/*.ts` (ex.: `test/manager/login/`); dados em `test-data/<dominio>/<fluxo>/`; Page Objects em `pageobjects/<dominio>/`; Screen Objects em `screenobjects/<dominio>/` e `screenobjects/<dominio>/components/`. Cada fluxo tem **suite** correspondente em [configs/wdio.shared.conf.ts](../../../configs/wdio.shared.conf.ts) (ex.: `suites: { 'manager/login': ['../test/manager/login/**/*.spec.ts'] }`).
- **Tags (execução seletiva):** Inclua no nome do `it()` uma **tag de severidade** de acordo com a criticidade daquele caso: `@blocker`, `@critical`, `@normal`, `@minor`, `@trivial` (alinhado ao Allure). Ex.: `it('... @login @critical', async () => { ... })`. Opcionalmente `@web`/`@app` para contexto. Uso: `--mochaOpts.grep=@critical` ou `--suite login` (ver package.json: `test-ci-local:critical`, `test-ci-local:login`).
- **baseURL / env:** Use [lib/env.ts](../../../lib/env.ts) when baseURL or URLs need to be read from env; override via `.env` (see `.env.example`).
- **Allure:** Use Allure for elaborated reports: structure (`addEpic`, `addFeature`, `addStory` in `beforeEach`), `allureReporter.step()` for blocks; when relevant use classification (addSeverity, addTag), evidence (addAttachment), integration (addIssue, addTestId), context (addArgument). See [docs/10-allure-reporter.md](../../../docs/10-allure-reporter.md) — table "API Allure – quando usar".

## Checklist when editing

- [ ] Specs import `expect` from `@wdio/globals`; device access via `lib/Utils` (getDeviceFromCapabilities)
- [ ] Reusable or scenario-specific inputs are in test-data, not inline in specs
- [ ] New tests go under `test/<dominio>/<fluxo>/` (e.g. test/manager/login/); new data under `test-data/<dominio>/<fluxo>/`; Page Objects in `pageobjects/<dominio>/`; Screen Objects in `screenobjects/<dominio>/` (and components); new flow has entry in `suites` in wdio.shared.conf
- [ ] Test names include a severity tag per case (@blocker, @critical, @normal, @minor, @trivial); optionally @fluxo, @web, @app
- [ ] Browser tests use Page Objects from `pageobjects/<dominio>/`; app tests use Screen Objects from `screenobjects/<dominio>/` or helpers from `lib/Utils` (getElementByTestIDApp, etc.)
- [ ] Fixtures (e.g. loginFixture) are imported from `fixtures/` when reusing flows
- [ ] Allure: epic/feature/story, steps; severity/tag/attachment/issue/testId/argument when applicable (see doc table)

## References

[AGENTS.md](../../../AGENTS.md) — conventions summary  
[docs/02-estrutura-de-diretórios.md](../../../docs/02-estrutura-de-diretórios.md) — role of each directory
