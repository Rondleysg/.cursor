---
name: qa-webdriverio-add-test-data
description: Add or extend test data for a QA WebdriverIO flow: create or edit inputs.json and optional builder.ts in test-data, use lib/data-factory for generated data. Use when the user asks to add inputs, test data, or a builder for a flow.
---

# Add test data (inputs and builders)

When adding or extending test data for a flow, follow this structure.

## Where

- **Per flow:** `test-data/<fluxo>/`
- **E2E constants:** `test-data/e2e/Constants.ts` (e.g. BUNDLE_ID, PACKAGE_NAME)
- **Optional split:** `test-data/api/<fluxo>/` or `test-data/ui/<fluxo>/` if the project separates by type

## Static inputs (inputs.json)

- Create or edit `inputs.json` (or `inputs.ts`) with entries per scenario (e.g. `login`, `checkout`).
- In the spec, import: `import inputs from '../../test-data/<fluxo>/inputs.json';` (path relative to spec) and use (e.g. `inputs.login`, `inputs.checkout`).

**Example:** [docs/04-test-data.md](../../../docs/04-test-data.md), [docs/07-como-adicionar-novo-fluxo.md](../../../docs/07-como-adicionar-novo-fluxo.md).

## Generated data (builder.ts, optional)

- When you need to vary data (e.g. unique email per run), add `builder.ts` in the same flow folder.
- Export a function that builds a default object and accepts overrides; use [lib/data-factory.ts](../../../lib/data-factory.ts) (randomEmail, randomString, randomNumber) inside the builder.
- In the spec, import the builder and call it (e.g. `createFormInput({ email: randomEmail() })`).

**Example:** [docs/04-test-data.md](../../../docs/04-test-data.md) — builder using data-factory.

## Constants (e2e)

- For app identifiers and shared constants: `test-data/e2e/Constants.ts` (BUNDLE_ID, PACKAGE_NAME). Used by `lib/Utils` and configs.

## Checklist

- [ ] Folder exists under `test-data/<fluxo>/` (or `test-data/e2e/` for constants)
- [ ] inputs.json (or inputs.ts) defines static cases; builder.ts only if varying data is needed
- [ ] Spec imports from path relative to spec (e.g. `../../test-data/<fluxo>/inputs.json`)

## Full reference

[docs/04-test-data.md](../../../docs/04-test-data.md) — inputs and builders  
[docs/05-lib.md](../../../docs/05-lib.md) — lib/data-factory (randomString, randomEmail, randomNumber)
