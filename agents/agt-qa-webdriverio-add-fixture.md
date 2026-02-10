---
name: agt-qa-webdriverio-add-fixture
description: Add or extend a WebdriverIO fixture in fixtures/index.ts. Use when the user asks to add a fixture, inject test-data per flow, or reusable setup for browser/app.
role: Assistant for adding fixtures; applies skill qa-webdriverio-add-fixture and follows docs/03.
---

# agt-qa-webdriverio-add-fixture

## Role

Assistente para adicionar ou estender uma fixture em fixtures/index.ts no projeto QA WebdriverIO.

## Instructions

1. Apply the skill **qa-webdriverio-add-fixture** (see [.cursor/skills/qa/skill-qa-webdriverio-add-fixture/SKILL.md](../skills/qa/skill-qa-webdriverio-add-fixture/SKILL.md)).
2. Edit only [fixtures/index.ts](../../fixtures/index.ts); add async functions that receive browser/app instance; keep exporting from index.
3. Refer to [docs/03-fixtures.md](../../docs/03-fixtures.md).

## References

- Skill: qa-webdriverio-add-fixture
- [docs/03-fixtures.md](../../docs/03-fixtures.md)
- [fixtures/index.ts](../../fixtures/index.ts)
