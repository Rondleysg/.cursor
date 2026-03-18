---
name: qa-webdriverio-maintain-conventions
description: Enforce QA WebdriverIO project conventions when editing tests or project files: expect from @wdio/globals, lib/Utils for device access, test-data for inputs, follow directory structure. Use when editing specs, refactoring, or when the user asks to align code with project standards.
---

# Maintain conventions (WebdriverIO)

When editing specs or other project files, ensure the QA WebdriverIO conventions are followed.

## Page Object Model (POM) — MANDATORY

All browser tests **must** strictly follow the Page Object Model. This is the most critical convention to enforce.

### Separation of responsibilities

| Layer | Responsibility |
|---|---|
| **Page Object** (`pageobjects/<dominio>/`) | All DOM interactions: selectors (getters), `click`, `setValue`, `waitForExist`, navigation, form submission, session management |
| **Spec file** (`test/<dominio>/<fluxo>/`) | Orchestration only: call Page Object methods, assert with `expect`, add Allure metadata |

### What to flag as violations

- `browser.$`, `browser.$$`, `$()`, `$$()`, `element.click()`, `element.setValue()` in spec files → **move to Page Object**
- Helper functions in spec files that interact with the DOM → **move to Page Object as public methods**
- Selectors defined in spec files → **move to Page Object as private getters**
- Direct assertions on elements obtained in the spec → **expose a public getter in the Page Object, assert in spec**

### Selector quality (inside Page Objects)

Seguir a [documentação WebdriverIO — Selectors](https://webdriver.io/docs/selectors) e alinhar ao que a PR deve expor ([skill-pr-selectors-for-automation](../skill-pr-selectors-for-automation/SKILL.md)). **Ordem preferida:**

1. **`[data-testid="..."]` / `[data-cy="..."]`** — atributos explícitos de teste (prioridade do projeto).
2. **Texto visível + tag** — `$('button=Salvar')`, `$('a=Detalhes')` (WDIO recomenda quando o texto é estável; atenção a i18n).
3. **`$('aria/NomeAcessível')`** — seletor por nome acessível nativo WDIO.
4. **`[aria-label="..."]`** — quando não houver texto visível.
5. **`#id` estável** — não gerado pelo framework.
6. **`[name="..."]`** — campos de formulário.
7. **`$('..')`** — navegação para pai (nativo WDIO).

**XPath — proibido.** Não usar `//...` nos Page Objects nem nos testes. Usar apenas CSS e estratégias suportadas na doc WDIO (incl. `tag=texto`, `aria/...`).

**Evitar:** classes de estilo como único seletor (hash/minify); seletores posicionais frágeis.

### Nomenclatura de identificadores (`data-testid`, `data-cy`, `testID` no app)

Padrão obrigatório: **`<feature>-<component>-<element>`** (kebab-case, minúsculas).

| Segmento | Uso | Exemplos |
|----------|-----|----------|
| **feature** | Fluxo ou área funcional | `login`, `checkout`, `profile` |
| **component** | Bloco de UI (form, modal, lista…) | `form`, `modal`, `header` |
| **element** | Campo, botão ou região | `email`, `submit`, `close` |

Exemplos: `login-form-email`, `login-form-submit`, `checkout-modal-close`. Em listas, manter o prefixo estável e sufixo variável quando necessário: `orders-item-list-row-${id}`.

## Specs (test/)

- **Imports:** Import `expect` from `@wdio/globals`. Use `getDeviceFromCapabilities('browser')` or `getDeviceFromCapabilities('mobile')` from [lib/Utils.ts](../../../lib/Utils.ts) to get the session; do not rely on global `driver`/`browser` without proper typing.
- **Data:** Prefer [test-data](../../../test-data/) (`inputs.ts`, `builder.ts`, `Constants.ts`) over hardcoding payloads in specs; use [lib/data-factory.ts](../../../lib/data-factory.ts) for varying data (randomEmail, randomString, randomNumber).
- **Structure:** Organização por **domínio** (app-cliente, log, manager, partners). Specs em `test/<dominio>/<fluxo>/*.ts` (ex.: `test/manager/login/`); dados em `test-data/<dominio>/<fluxo>/`; Page Objects em `pageobjects/<dominio>/`; Screen Objects em `screenobjects/<dominio>/` e `screenobjects/<dominio>/components/`. Cada fluxo tem **suite** correspondente em [configs/wdio.shared.conf.ts](../../../configs/wdio.shared.conf.ts) (ex.: `suites: { 'manager/login': ['../test/manager/login/**/*.spec.ts'] }`).
- **Tags (execução seletiva):** Inclua no nome do `it()` uma **tag de severidade** de acordo com a criticidade daquele caso: `@blocker`, `@critical`, `@normal`, `@minor`, `@trivial` (alinhado ao Allure). Ex.: `it('... @login @critical', async () => { ... })`. Opcionalmente `@web`/`@app` para contexto. Uso: `--mochaOpts.grep=@critical` ou `--suite login` (ver package.json: `test-ci-local:critical`, `test-ci-local:login`).
- **baseURL / env:** Use [lib/env.ts](../../../lib/env.ts) when baseURL or URLs need to be read from env; override via `.env` (see `.env.example`).
- **Allure:** Use Allure for elaborated reports: structure (`addEpic`, `addFeature`, `addStory` in `beforeEach`), `allureReporter.step()` for blocks; when relevant use classification (addSeverity, addTag), evidence (addAttachment), integration (addIssue, addTestId), context (addArgument). See [docs/10-allure-reporter.md](../../../docs/10-allure-reporter.md) — table "API Allure – quando usar".

## Checklist when editing

- [ ] **POM:** NO `browser.$`, `$()`, `click()`, `setValue()`, or DOM helpers in spec files — all DOM interactions in Page Objects
- [ ] **POM:** Page Objects expose public methods for actions and public getters for assertions; selectors are private getters
- [ ] **POM:** Seletores estáveis; **nomenclatura** `<feature>-<component>-<element>` em `data-testid`/`data-cy`/`testID`; **sem XPath**; sem classes frágeis
- [ ] Specs import `expect` from `@wdio/globals`; device access via `lib/Utils` (getDeviceFromCapabilities)
- [ ] Reusable or scenario-specific inputs are in test-data (`inputs.ts`), not inline in specs
- [ ] New tests go under `test/<dominio>/<fluxo>/` (e.g. test/manager/login/); new data under `test-data/<dominio>/<fluxo>/`; Page Objects in `pageobjects/<dominio>/`; Screen Objects in `screenobjects/<dominio>/` (and components); new flow has entry in `suites` in wdio.shared.conf
- [ ] Test names include a severity tag per case (@blocker, @critical, @normal, @minor, @trivial); optionally @fluxo, @web, @app
- [ ] Browser tests use Page Objects from `pageobjects/<dominio>/`; app tests use Screen Objects from `screenobjects/<dominio>/` or helpers from `lib/Utils` (getElementByTestIDApp, etc.)
- [ ] Fixtures (e.g. loginFixture) are imported from `fixtures/` when reusing flows
- [ ] Allure: epic/feature/story, steps; severity/tag/attachment/issue/testId/argument when applicable (see doc table)

## References

[AGENTS.md](../../../AGENTS.md) — conventions summary  
[docs/02-estrutura-de-diretórios.md](../../../docs/02-estrutura-de-diretórios.md) — role of each directory
