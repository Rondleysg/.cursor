---
name: qa-webdriverio-code-review
description: Perform QA WebdriverIO code review by applying the perspectives of maintain, add-fixture, and add-flow agents; produce a consolidated improvement summary focused on maintenance, quality, and standardization. Use when the user asks for a code review, review of specs/test-data, or improvement summary.
---

# QA Code Review (WebdriverIO)

This skill defines how to review QA WebdriverIO code (specs, test-data, fixtures, Page/Screen Objects) by **considering the perspective of each QA subagent**, then **consolidating** their concerns into one report focused on **manutenção**, **qualidade** e **padronização**.

## When to Use

- User asks for a code review of specs, test-data, flow, or fixtures.
- User asks "o que acha do código?" or "revise esse teste".
- User wants a consolidated improvement list from the point of view of the project's QA standards (WebdriverIO).

## Process

1. **Identify scope:** What is under review? (single spec, whole flow, test-data, fixtures, Page/Screen Objects.)
2. **Apply each relevant QA perspective** by reading and applying the criteria from:
   - **Conventions (maintain):** [skill-qa-webdriverio-maintain-conventions](../skill-qa-webdriverio-maintain-conventions/SKILL.md) — imports (@wdio/globals), lib/Utils, test-data usage, structure, Page/Screen Objects, **POM compliance**.
   - **Fixtures (add-fixture):** [skill-qa-webdriverio-add-fixture](../skill-qa-webdriverio-add-fixture/SKILL.md) — whether fixtures would reduce duplication or improve structure.
   - **Flow (add-flow):** [skill-qa-webdriverio-add-new-flow](../skill-qa-webdriverio-add-new-flow/SKILL.md) — when reviewing a flow: folders, inputs, baseURL, naming, config.
3. **Optionally** consider:
   - **Test-data:** [skill-qa-webdriverio-add-test-data](../skill-qa-webdriverio-add-test-data/SKILL.md) — if the review involves inputs.ts, builder.ts, Constants.
   - **Frontend-friendly:** [skill-frontend-qa-friendly](../skill-frontend-qa-friendly/SKILL.md) — locators, accessibility, stability (browser/app).
4. **Produce a single report** with the structure below.

## Output Structure

Produce the review in this order:

### 1. Manutenção (conventions)

- **POM compliance (highest priority):** Check for any DOM interactions in spec files:
  - `browser.$`, `$()`, `$$()`, `element.click()`, `element.setValue()` in specs → ❌ violation
  - Helper functions in specs that interact with the DOM → ❌ violation
  - Selectors defined in specs → ❌ violation
  - Page Objects not exposing public methods/getters → ❌ violation
- Checklist from maintain skill: expect from @wdio/globals, device access via lib/Utils, test-data vs hardcode, directory structure by **domain** (`test/<dominio>/<fluxo>/`, `test-data/<dominio>/<fluxo>/`, `pageobjects/<dominio>/`, `screenobjects/<dominio>/`), baseURL/env.
- **Tags e suites:** Specs devem ter **tag de severidade** no nome do teste conforme a criticidade do caso (@blocker, @critical, @normal, @minor, @trivial); opcionalmente @fluxo, @web, @app. Cada fluxo deve ter entrada em `suites` em wdio.shared.conf (ex.: `'manager/login': ['../test/manager/login/**/*.spec.ts']`) para execução seletiva (`--suite`, `--mochaOpts.grep`).
- Explicit: ✅ atende / ⚠️ atenção / ❌ não atende, with short reason.

### 2. Qualidade

- **POM / Page Object design:** Are all DOM interactions encapsulated in Page Objects? Are public methods meaningful and reusable? Are selectors stable?
- **Seletores (WebdriverIO):** [webdriver.io/docs/selectors](https://webdriver.io/docs/selectors). **Nomenclatura** `<feature>-<component>-<element>` em `data-testid`/`data-cy`/`testID`. **XPath proibido**; sem Playwright. ❌ se violar nome ou XPath/classes frágeis.
- Assertions: use of `expect` from `@wdio/globals`, timeouts, clear success criteria. Prefer `toHaveText`, `toBeExisting`, `toBeDisplayed` over manual `waitUntil` + `getText`.
- Legibilidade: Arrange–Act–Assert, comentários úteis, nome do teste descritivo.
- **Allure:** Steps and structure (epic/feature/story); when missing suggest per [docs/10-allure-reporter.md](../../../docs/10-allure-reporter.md): severity for critical flows, tags (smoke/regression/e2e), attachment for evidence, addIssue/addTestId if linked to Jira/TMS, addArgument for debug context.
- **Tags no it():** Test names should include a **severity tag** for that case (@blocker, @critical, @normal, @minor, @trivial) so runs can be filtered by severity; optionally @fluxo, @web, @app. Filter via `--suite` or `--mochaOpts.grep` (e.g. test-ci-local:critical, test-ci-local:login).

### 3. Padronização

- Estrutura por domínio: test/<dominio>/<fluxo>/, test-data/<dominio>/<fluxo>/, pageobjects/<dominio>/, screenobjects/<dominio>/ alinhados ao mesmo domínio (app-cliente, log, manager, partners); cada fluxo com entrada em `suites` em wdio.shared.conf; nomenclatura de pastas e arquivos.
- **POM:** Page Objects como singletons (`export default new Page()`); separação clara entre ação (método público) e seletor (getter privado).
- Tags: testes com tag de severidade conforme o caso (@blocker, @critical, @normal, @minor, @trivial); opcionalmente @fluxo, @web, @app. Execução seletiva: --suite, --mochaOpts.grep; scripts em package.json (test-ci-local:login, test-ci-local:critical) como referência.
- Dados: inputs.ts/builder.ts conforme docs; uso de data-factory quando fizer sentido.
- Fixtures: se faz sentido sugerir fixture (ex.: loginFixture, setup por fluxo) para evitar duplicação.
- Allure: steps e estrutura (epic/feature/story); usar severity/tag/attachment/issue/testId/argument quando fizer sentido; referência à tabela "API Allure – quando usar" em [docs/10-allure-reporter.md](../../../docs/10-allure-reporter.md).

### 4. Resumo de melhorias

- Lista objetiva e acionável (ex.: "Mover `browser.$('#error')` do spec para um getter público no Page Object").
- Priorize: **POM violation** > quebra de convenção > qualidade > padronização > sugestões opcionais.

## Delegation Note

You cannot invoke other agents programmatically. Instead, **apply their criteria yourself**: read the skills listed above (maintain, add-fixture, add-flow, and optionally add-test-data, frontend-qa-friendly) and evaluate the code under review as each of those agents would. Then merge the findings into the single report (maintenance, quality, standardization, summary).

## References

- [AGENTS.md](../../../AGENTS.md) — list of QA agents and skills
- [docs/02-estrutura-de-diretórios.md](../../../docs/02-estrutura-de-diretórios.md) — directory roles
- [docs/03-fixtures.md](../../../docs/03-fixtures.md) — fixtures
- [docs/07-como-adicionar-novo-fluxo.md](../../../docs/07-como-adicionar-novo-fluxo.md) — flow structure
