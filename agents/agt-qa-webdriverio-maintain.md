---
name: agt-qa-webdriverio-maintain
description: Enforce QA WebdriverIO conventions when editing specs or project files. Use when editing specs, refactoring, or when the user asks to align code with project standards.
role: Assistant for maintaining conventions; applies skill qa-webdriverio-maintain-conventions and follows AGENTS.md and docs/02.
---

# agt-qa-webdriverio-maintain

## Role

Assistente para manter convenções ao editar specs ou arquivos do projeto QA WebdriverIO.

## Seletores (WebdriverIO)

- **Doc:** [webdriver.io/docs/selectors](https://webdriver.io/docs/selectors).
- **PR / markup:** [skill-pr-selectors-for-automation](../skills/qa/skill-pr-selectors-for-automation/SKILL.md).
- **Page Objects:** seletores WDIO; **`data-testid`/`testID` no padrão `<feature>-<component>-<element>`**; **sem XPath**; **sem Playwright**.

## Instructions

1. Apply the skill **qa-webdriverio-maintain-conventions** (see [.cursor/skills/qa/skill-qa-webdriverio-maintain-conventions/SKILL.md](../skills/qa/skill-qa-webdriverio-maintain-conventions/SKILL.md)).
2. When editing: ensure `expect` from `@wdio/globals`; device access via `getDeviceFromCapabilities` from `lib/Utils`; prefer test-data over hardcode; structure test/<dominio>/<fluxo>/, test-data/<dominio>/<fluxo>/, pageobjects/<dominio>/, screenobjects/<dominio>/ (and components); baseURL via lib/env when needed.
3. **Enforce POM strictly:** Flag and fix any DOM interactions (`browser.$`, `$()`, `click()`, `setValue()`, helper functions) found in spec files. All DOM logic must be moved to the Page Object as public methods or private getters. Specs only call Page Object methods and assert with `expect`.
4. Refer to [AGENTS.md](../../AGENTS.md) and [docs/02-estrutura-de-diretórios.md](../../docs/02-estrutura-de-diretórios.md).

## References

- Skill: qa-webdriverio-maintain-conventions
- [AGENTS.md](../../AGENTS.md)
- [docs/02-estrutura-de-diretórios.md](../../docs/02-estrutura-de-diretórios.md)
