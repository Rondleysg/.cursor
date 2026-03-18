---
name: agt-qa-webdriverio-add-flow
description: Add a new test flow (folders, data, specs) to the QA WebdriverIO project. Use when the user asks to add a flow, new flow, or test suite for a feature.
role: Assistant for adding new flows; applies skill qa-webdriverio-add-new-flow and follows docs/07.
---

# agt-qa-webdriverio-add-flow

## Role

Assistente para adicionar um novo fluxo (pastas test + test-data, inputs, specs) ao projeto QA WebdriverIO. A estrutura é por **domínio** (app-cliente, log, manager, partners).

## Seletores (WebdriverIO)

Page Objects: [webdriver.io/docs/selectors](https://webdriver.io/docs/selectors) + [skill-pr-selectors-for-automation](../skills/qa/skill-pr-selectors-for-automation/SKILL.md). **`data-testid`/`testID`:** `<feature>-<component>-<element>`. **Sem XPath / Playwright.**

## Instructions

1. Apply the skill **qa-webdriverio-add-new-flow** (see [.cursor/skills/qa/skill-qa-webdriverio-add-new-flow/SKILL.md](../skills/qa/skill-qa-webdriverio-add-new-flow/SKILL.md)).
2. Follow the checklist: (1) create test/<dominio>/<fluxo>/ and/or test/e2e/; (2) create test-data/<dominio>/<fluxo>/ when there are inputs; (3) create or use pageobjects/<dominio>/ and screenobjects/<dominio>/ (e screenobjects/<dominio>/components/) when the flow belongs to that domain; (4) inputs.ts and optionally builder.ts; (5) specs importing expect from @wdio/globals and using lib/Utils; (6) baseURL or app path in configs if another domain/app.
3. **Enforce POM strictly:** All DOM interactions (selectors, clicks, setValue, navigation, waits) must be in the Page Object. Spec files only call Page Object public methods and assert with `expect`. Never place `browser.$`, helper functions, or selectors in spec files.
4. Refer to [docs/07-como-adicionar-novo-fluxo.md](../../docs/07-como-adicionar-novo-fluxo.md).

## References

- Skill: qa-webdriverio-add-new-flow
- Doc: [docs/07-como-adicionar-novo-fluxo.md](../../docs/07-como-adicionar-novo-fluxo.md)
