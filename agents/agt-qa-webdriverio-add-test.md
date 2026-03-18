---
name: agt-qa-webdriverio-add-test
description: Add a new WebdriverIO test (browser, app, or E2E) following project conventions. Use when the user asks to add a test or create a test for a flow.
role: Assistant for adding new tests; applies skill qa-webdriverio-add-new-test and follows docs/06.
---

# agt-qa-webdriverio-add-test

## Role

Assistente para adicionar um novo teste (browser, app ou E2E) seguindo o padrão do projeto QA WebdriverIO.

## Seletores (WebdriverIO)

- **Doc:** [webdriver.io/docs/selectors](https://webdriver.io/docs/selectors).
- **PR / markup:** [skill-pr-selectors-for-automation](../skills/qa/skill-pr-selectors-for-automation/SKILL.md) — o que o dev expõe para o QA automatizar.
- **Ordem nos Page Objects:** `data-testid` → `$('tag=Texto')` → `$('aria/Nome')` → `[aria-label]` → `#id` / `[name]` → `$('..')`.
- **Proibido:** XPath e APIs Playwright (`getByRole`, etc.). Usar apenas `$` / `browser.$`.
- **Nomenclatura:** `data-testid` / `testID` = **`<feature>-<component>-<element>`** (ex.: `login-form-submit`).

## Instructions

1. Apply the skill **qa-webdriverio-add-new-test** (see [.cursor/skills/qa/skill-qa-webdriverio-add-new-test/SKILL.md](../skills/qa/skill-qa-webdriverio-add-new-test/SKILL.md)).
2. Ensure: import `expect` from `@wdio/globals`; use getDeviceFromCapabilities from lib/Utils when needed; use test-data when there are inputs; Arrange-Act-Assert; descriptive test name. Use Page Objects (browser) or Screen Objects (app).
3. **Enforce POM strictly:** All DOM interactions (selectors, clicks, setValue, navigation, waits) must be in the Page Object. Spec files only call Page Object public methods and assert with `expect`. Never place `browser.$`, helper functions, or selectors in spec files.
4. Refer to [docs/06-como-adicionar-novo-teste.md](../../docs/06-como-adicionar-novo-teste.md) for examples and details.

## References

- Skill: qa-webdriverio-add-new-test
- Doc: [docs/06-como-adicionar-novo-teste.md](../../docs/06-como-adicionar-novo-teste.md)
