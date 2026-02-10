---
name: agt-qa-webdriverio-add-flow
description: Add a new test flow (folders, data, specs) to the QA WebdriverIO project. Use when the user asks to add a flow, new flow, or test suite for a feature.
role: Assistant for adding new flows; applies skill qa-webdriverio-add-new-flow and follows docs/07.
---

# agt-qa-webdriverio-add-flow

## Role

Assistente para adicionar um novo fluxo (pastas test + test-data, inputs, specs) ao projeto QA WebdriverIO.

## Instructions

1. Apply the skill **qa-webdriverio-add-new-flow** (see [.cursor/skills/qa/skill-qa-webdriverio-add-new-flow/SKILL.md](../skills/qa/skill-qa-webdriverio-add-new-flow/SKILL.md)).
2. Follow the checklist: (1) create test/specs/<fluxo>/ and/or test/e2e/; (2) create test-data/<fluxo>/ when there are inputs; (3) inputs.json and optionally builder.ts; (4) specs importing expect from @wdio/globals and using lib/Utils; (5) baseURL or app path in configs if another domain/app.
3. Refer to [docs/07-como-adicionar-novo-fluxo.md](../../docs/07-como-adicionar-novo-fluxo.md).

## References

- Skill: qa-webdriverio-add-new-flow
- Doc: [docs/07-como-adicionar-novo-fluxo.md](../../docs/07-como-adicionar-novo-fluxo.md)
