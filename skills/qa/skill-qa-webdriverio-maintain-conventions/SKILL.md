---
name: qa-webdriverio-maintain-conventions
description: Enforce QA WebdriverIO project conventions when editing tests or project files: expect from @wdio/globals, lib/Utils for device access, test-data for inputs, follow directory structure. Use when editing specs, refactoring, or when the user asks to align code with project standards.
---

# Maintain conventions (WebdriverIO)

When editing specs or other project files, ensure the QA WebdriverIO conventions are followed.

## Specs (test/)

- **Imports:** Import `expect` from `@wdio/globals`. Use `getDeviceFromCapabilities('browser')` or `getDeviceFromCapabilities('mobile')` from [lib/Utils.ts](../../../lib/Utils.ts) to get the session; do not rely on global `driver`/`browser` without proper typing.
- **Data:** Prefer [test-data](../../../test-data/) (inputs.json, builder.ts, Constants.ts) over hardcoding payloads in specs; use [lib/data-factory.ts](../../../lib/data-factory.ts) for varying data (randomEmail, randomString, randomNumber).
- **Structure:** Specs live in `test/specs/<fluxo>/*.ts` or `test/e2e/*.ts`; data in `test-data/<fluxo>/` or `test-data/e2e/`; Page Objects in `pageobjects/`, Screen Objects in `screenobjects/`.
- **baseURL / env:** Use [lib/env.ts](../../../lib/env.ts) when baseURL or URLs need to be read from env; override via `.env` (see `.env.example`).
- **Allure:** Use Allure for elaborated reports: structure (`addEpic`, `addFeature`, `addStory` in `beforeEach`), `allureReporter.step()` for blocks; when relevant use classification (addSeverity, addTag), evidence (addAttachment), integration (addIssue, addTestId), context (addArgument). See [docs/10-allure-reporter.md](../../../docs/10-allure-reporter.md) — table "API Allure – quando usar".

## Checklist when editing

- [ ] Specs import `expect` from `@wdio/globals`; device access via `lib/Utils` (getDeviceFromCapabilities)
- [ ] Reusable or scenario-specific inputs are in test-data, not inline in specs
- [ ] New tests go under `test/specs/<fluxo>/` or `test/e2e/`; new data under `test-data/<fluxo>/`
- [ ] Browser tests use Page Objects; app tests use Screen Objects or helpers from `lib/Utils` (getElementByTestIDApp, etc.)
- [ ] Fixtures (e.g. loginFixture) are imported from `fixtures/` when reusing flows
- [ ] Allure: epic/feature/story, steps; severity/tag/attachment/issue/testId/argument when applicable (see doc table)

## References

[AGENTS.md](../../../AGENTS.md) — conventions summary  
[docs/02-estrutura-de-diretórios.md](../../../docs/02-estrutura-de-diretórios.md) — role of each directory
