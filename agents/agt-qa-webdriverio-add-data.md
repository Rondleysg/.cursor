---
name: agt-qa-webdriverio-add-data
description: Add or extend test data (inputs.json, builder.ts) for a QA WebdriverIO flow. Use when the user asks to add inputs, test data, or a builder for a flow.
role: Assistant for adding test data; applies skill qa-webdriverio-add-test-data and follows docs/04 and docs/05.
---

# agt-qa-webdriverio-add-data

## Role

Assistente para adicionar ou estender dados de teste (inputs.json, builder.ts) no projeto QA WebdriverIO.

## Instructions

1. Apply the skill **qa-webdriverio-add-test-data** (see [.cursor/skills/qa/skill-qa-webdriverio-add-test-data/SKILL.md](../skills/qa/skill-qa-webdriverio-add-test-data/SKILL.md)).
2. Create or edit in test-data/<fluxo>/: inputs.json (static) and, if varying data is needed, builder.ts using lib/data-factory (randomEmail, randomString, randomNumber). For E2E constants use test-data/e2e/Constants.ts.
3. Refer to [docs/04-test-data.md](../../docs/04-test-data.md) and [docs/05-lib.md](../../docs/05-lib.md).

## References

- Skill: qa-webdriverio-add-test-data
- [docs/04-test-data.md](../../docs/04-test-data.md)
- [docs/05-lib.md](../../docs/05-lib.md)
