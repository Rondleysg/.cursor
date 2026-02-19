---
name: qa-webdriverio-add-new-flow
description: Add a new test flow to the QA WebdriverIO project: create test and test-data folders for the flow, add inputs and optional builder, write specs with expect from @wdio/globals and lib/Utils. Use when the user asks to add a flow, new flow, or test suite for a feature.
---

# Add new flow (WebdriverIO)

When adding a new test flow to the QA WebdriverIO project, follow this checklist.

## Checklist

1. **Create flow folders in test:** `test/<dominio>/<fluxo>/` (e.g. `test/manager/login/`, `test/manager/register/`). Domínios: app-cliente, log, manager, partners. Create at least one spec file (e.g. `*.spec.ts`) in the folder. The global `specs` in [configs/wdio.shared.conf.ts](../../../configs/wdio.shared.conf.ts) (`../test/**/*.ts`) already includes all flows.
2. **Register the suite:** In [configs/wdio.shared.conf.ts](../../../configs/wdio.shared.conf.ts), add an entry in `suites` so the flow can be run selectively: e.g. `'manager/register': ['../test/manager/register/**/*.spec.ts']`. This enables `--suite manager/register` and scripts like `test-ci-local:manager-login` (see package.json).
3. **Create flow folders in test-data (if there are inputs):** `test-data/<dominio>/<fluxo>/` with `inputs.json` (or `inputs.ts`); optionally `test-data/<dominio>/api/<fluxo>/` or `test-data/<dominio>/ui/<fluxo>/` if the project separates by type.
4. **Define static inputs:** Create `inputs.json` (or `inputs.ts`) with the cases needed; optionally add `builder.ts` that uses [lib/data-factory.ts](../../../lib/data-factory.ts) for varying data.
5. **Write specs:** Import `expect` from `@wdio/globals`; use Page Objects from `pageobjects/<dominio>/` (e.g. `pageobjects/manager/LoginPage`) and Screen Objects from `screenobjects/<dominio>/` (e.g. `screenobjects/manager/LoginScreen`, `screenobjects/manager/components/TabBar`); use `getDeviceFromCapabilities('browser')` or `getDeviceFromCapabilities('mobile')` from `lib/Utils`; Arrange-Act-Assert; for data, import from test-data or use the builder. Use **tag de severidade** no nome do teste: `@blocker`, `@critical`, `@normal`, `@minor`, `@trivial`; opcionalmente `@<fluxo>`, `@web`, `@app`. Use **Allure**: structure (addEpic, addFeature, addStory), steps; when useful add severity, tag, attachment, issue/testId, argument (see [docs/10-allure-reporter.md](../../../docs/10-allure-reporter.md) — "API Allure – quando usar").
6. **baseURL / app:** If the flow uses another baseURL, configure in [configs/wdio.shared.conf.ts](../../../configs/wdio.shared.conf.ts) or via env ([lib/env.ts](../../../lib/env.ts), [docs/08-ambiente-e-configuração.md](../../../docs/08-ambiente-e-configuração.md)). For another app binary, update `configs/wdio.android.conf.ts` or `wdio.ios.conf.ts`.

## Naming

- **Flow folder:** short, clear name (e.g. `login`, `checkout`, `onboarding`).
- **Spec files:** `*.ts` (pattern from config); name can describe the scenario (e.g. `login.spec.ts`, `checkout.spec.ts`).

## Run only this flow

- **Por suite (recomendado):** após registrar o fluxo em `suites` no wdio.shared.conf, use `--suite <dominio>/<fluxo>` (ex.: `--suite manager/login`, `--suite manager/register`). Scripts em package.json: `npm run test-ci-local:manager-login` (exemplo); pode adicionar script por fluxo.
- **Por spec path:** `wdio run ./configs/wdio.android.conf.ts --spec test/<dominio>/<fluxo>/**/*.ts` (e idem para ios/ci-local).
- **Por tag de severidade:** `wdio run ./configs/wdio.ci-local.conf.ts --mochaOpts.grep=@critical` (só testes com essa severidade); ver `npm run test-ci-local:critical`.

## Full reference

[docs/07-como-adicionar-novo-fluxo.md](../../../docs/07-como-adicionar-novo-fluxo.md)
