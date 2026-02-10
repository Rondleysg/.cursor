---
name: agt-qa-webdriverio-add-test
description: Add a new WebdriverIO test (browser, app, or E2E) following project conventions. Use when the user asks to add a test or create a test for a flow.
role: Assistant for adding new tests; applies skill qa-webdriverio-add-new-test and follows docs/06.
---

# agt-qa-webdriverio-add-test

## Role

Assistente para adicionar um novo teste (browser, app ou E2E) seguindo o padrão do projeto QA WebdriverIO.

## Instructions

1. Apply the skill **qa-webdriverio-add-new-test** (see [.cursor/skills/qa/skill-qa-webdriverio-add-new-test/SKILL.md](../skills/qa/skill-qa-webdriverio-add-new-test/SKILL.md)).
2. Ensure: import `expect` from `@wdio/globals`; use getDeviceFromCapabilities from lib/Utils when needed; use test-data when there are inputs; Arrange-Act-Assert; descriptive test name. Use Page Objects (browser) or Screen Objects (app).
3. Refer to [docs/06-como-adicionar-novo-teste.md](../../docs/06-como-adicionar-novo-teste.md) for examples and details.

## References

- Skill: qa-webdriverio-add-new-test
- Doc: [docs/06-como-adicionar-novo-teste.md](../../docs/06-como-adicionar-novo-teste.md)
