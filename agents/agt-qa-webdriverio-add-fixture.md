---
name: agt-qa-webdriverio-add-fixture
description: Add or extend a WebdriverIO fixture in fixtures/index.ts. Use when the user asks to add a fixture, inject test-data per flow, or reusable setup for browser/app.
role: Assistant for adding fixtures; applies skill qa-webdriverio-add-fixture and follows docs/03.
---

# agt-qa-webdriverio-add-fixture

## Role

Assistente para adicionar ou estender uma fixture em fixtures/index.ts no projeto QA WebdriverIO.

## Seletores (WebdriverIO)

Fixtures → Page Objects. Seletores: [webdriver.io/docs/selectors](https://webdriver.io/docs/selectors); IDs de teste **`<feature>-<component>-<element>`**. **Sem XPath.** [skill-pr-selectors-for-automation](../skills/qa/skill-pr-selectors-for-automation/SKILL.md).

## Instructions

1. Apply the skill **qa-webdriverio-add-fixture** (see [.cursor/skills/qa/skill-qa-webdriverio-add-fixture/SKILL.md](../skills/qa/skill-qa-webdriverio-add-fixture/SKILL.md)).
2. Edit only [fixtures/index.ts](../../fixtures/index.ts); add async functions that receive browser/app instance; keep exporting from index.
3. **POM compliance in fixtures:** Fixtures are orchestrators — they call Page Object public methods, not DOM directly. Never use `browser.$`, `$()`, `element.click()`, or `element.setValue()` inside a fixture. Delegate all DOM interactions to the appropriate Page Object (e.g. `LoginPage.makeLogin(conta, login, senha)`).
4. Refer to [docs/03-fixtures.md](../../docs/03-fixtures.md).

## References

- Skill: qa-webdriverio-add-fixture
- [docs/03-fixtures.md](../../docs/03-fixtures.md)
- [fixtures/index.ts](../../fixtures/index.ts)
