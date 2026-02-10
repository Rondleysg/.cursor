---
name: qa-webdriverio-add-new-flow
description: Add a new test flow to the QA WebdriverIO project: create test and test-data folders for the flow, add inputs and optional builder, write specs with expect from @wdio/globals and lib/Utils. Use when the user asks to add a flow, new flow, or test suite for a feature.
---

# Add new flow (WebdriverIO)

When adding a new test flow to the QA WebdriverIO project, follow this checklist.

## Checklist

1. **Create flow folders in test:** `test/specs/<fluxo>/` and/or specs in `test/e2e/`. Create at least one spec file (e.g. `*.spec.ts` or `*.ts`) in each folder you add. Ensure the spec pattern in `configs/wdio.shared.conf.ts` includes these paths.
2. **Create flow folders in test-data (if there are inputs):** `test-data/<fluxo>/` with `inputs.json` (or `inputs.ts`); optionally `test-data/api/<fluxo>/` or `test-data/ui/<fluxo>/` if the project separates by type.
3. **Define static inputs:** Create `inputs.json` (or `inputs.ts`) with the cases needed; optionally add `builder.ts` that uses [lib/data-factory.ts](../../../lib/data-factory.ts) for varying data.
4. **Write specs:** Import `expect` from `@wdio/globals`; use `getDeviceFromCapabilities('browser')` or `getDeviceFromCapabilities('mobile')` from `lib/Utils`; use Page Objects (browser) or Screen Objects (app); Arrange-Act-Assert; for data, import from test-data or use the builder.
5. **baseURL / app:** If the flow uses another baseURL, configure in [configs/wdio.shared.conf.ts](../../../configs/wdio.shared.conf.ts) or via env ([lib/env.ts](../../../lib/env.ts), [docs/08-ambiente-e-configuração.md](../../../docs/08-ambiente-e-configuração.md)). For another app binary, update `configs/wdio.android.conf.ts` or `wdio.ios.conf.ts`.

## Naming

- **Flow folder:** short, clear name (e.g. `login`, `checkout`, `onboarding`).
- **Spec files:** `*.ts` (pattern from config); name can describe the scenario (e.g. `login.spec.ts`, `checkout.spec.ts`).

## Run only this flow

```bash
wdio run ./configs/wdio.android.conf.ts --spec test/specs/<fluxo>/**/*.ts
wdio run ./configs/wdio.ios.conf.ts --spec test/specs/<fluxo>/**/*.ts
# or
npm run test-android
npm run test-ios
```

## Full reference

[docs/07-como-adicionar-novo-fluxo.md](../../../docs/07-como-adicionar-novo-fluxo.md)
