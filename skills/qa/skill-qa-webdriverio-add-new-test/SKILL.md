---
name: qa-webdriverio-add-new-test
description: Add a new WebdriverIO test (browser, app, or E2E) following project conventions: expect from @wdio/globals, lib/Utils for device access, test-data when applicable, Arrange-Act-Assert. Use when the user asks to add a test, create a test, or add a test for a flow.
---

# Add new test (browser, app, or E2E)

When adding a new test to the QA WebdriverIO project, follow these conventions.

## Page Object Model (POM) — MANDATORY

All browser tests **must** follow the Page Object Model strictly. This is a non-negotiable convention.

### Responsibilities

| Layer | Responsibility |
|---|---|
| **Page Object** (`pageobjects/<dominio>/`) | All DOM interactions: selectors (getters), `click`, `setValue`, `waitForExist`, navigation, form submission, session management |
| **Spec file** (`test/<dominio>/<fluxo>/`) | Orchestration only: call Page Object methods, assert with `expect`, add Allure metadata |

### Rules

- **NEVER** use `browser.$`, `browser.$$`, `$()`, `$$()`, `element.click()`, `element.setValue()`, or any DOM interaction directly in a spec file.
- **NEVER** define helper functions in spec files that interact with the DOM. Move them to the Page Object.
- **ALWAYS** expose public methods (actions) and public getters (elements for assertion) in the Page Object.
- Page Objects should be exported as singletons (`export default new LoginPage()`) when stateless.
- Specs only import Page Objects and call their public API.

### Selectors (inside Page Objects)

Prefer in this order:
1. `aria/<label>` — accessibility label
2. `[data-testid="..."]` or `[data-cy="..."]` — explicit test attributes
3. `#id` — stable IDs
4. `[name="..."]` — form field names
5. `$('..')` — parent traversal (WebdriverIO native)
6. XPath — only as last resort; avoid positional XPath

### Example structure

```typescript
// pageobjects/manager/LoginPage.ts
class LoginPage extends ManagerPage {
  get inputEmail() { return browser.$('#username'); }
  get errorMessage() { return browser.$('#error'); }

  public async fillCredentials(email: string, password: string) {
    await this.inputEmail.setValue(email);
  }
  public async submitForm() {
    await this.buttonSubmit.click();
  }
}
export default new LoginPage();

// test/manager/login/login.spec.ts
it('should show error for empty email @critical', async () => {
  // Arrange
  await LoginPage.open();
  // Act
  await LoginPage.submitForm();
  // Assert
  await expect(LoginPage.errorMessage).toHaveText('Required');
});
```

## Imports

- **Always** import `expect` from `@wdio/globals`.
- Import `allureReporter` from `@wdio/allure-reporter` when you want an elaborated Allure report (steps, epic/feature/story).
- Use `getDeviceFromCapabilities('browser')` or `getDeviceFromCapabilities('mobile')` from [lib/Utils.ts](../../../lib/Utils.ts) when you need the session directly; otherwise use Page Objects (browser) or Screen Objects (app).

## Tags (execução seletiva)

- Marque com **tag de severidade** de acordo com a criticidade daquele caso: `@blocker`, `@critical`, `@normal`, `@minor`, `@trivial` (mesmos níveis do Allure). Opcionalmente use `@<fluxo>`, `@web`, `@app` para contexto.
- Exemplo: `it('Perform login in both browser and app @login @critical', async () => { ... })`.
- Execução: `--suite login` (roda só a suite login); `--mochaOpts.grep=@critical` (roda só testes com essa severidade). Ver scripts em package.json (`test-ci-local:login`, `test-ci-local:critical`).

## Allure

- **Estrutura:** `addEpic` (macro área), `addFeature` (funcionalidade), `addStory` (user story); use in `beforeEach` per suite.
- **Steps:** wrap logical blocks in `allureReporter.step('step name', async () => { ... })`.
- **Quando usar o resto da API:** `addSeverity` (criticidade: trivial/minor/normal/critical/blocker); `addTag` (smoke, regression, e2e); `addAttachment` (evidências extras); `addIssue`/`addTestId` (integração Jira/TMS se configurado); `addArgument` (parâmetros no report para debug). See [docs/10-allure-reporter.md](../../../docs/10-allure-reporter.md) for the full "API Allure – quando usar" table.

## Browser test

- Use Page Objects (e.g. `LoginPage`, `SecurePage`) that internally use the browser session. **All DOM interactions go in the Page Object.**
- Use data from `test-data/<dominio>/<fluxo>/inputs.ts` when applicable; import from path relative to spec (e.g. `../../../test-data/manager/login/inputs`).
- Arrange-Act-Assert; descriptive test name (scenario + expected result).
- baseURL comes from config (lib/env when needed).

**Example:** See [docs/06-como-adicionar-novo-teste.md](../../../docs/06-como-adicionar-novo-teste.md) (browser example).

## App test (mobile)

- Use Screen Objects (e.g. `TabBar`, `LoginScreen`, `NativeAlert`) or `getDeviceFromCapabilities('mobile')` and helpers from `lib/Utils` (getElementByTestIDApp, getElementByAccessibilityLabelApp).
- Use data from `test-data/<dominio>/<fluxo>/inputs.ts` or a builder when applicable; use [lib/data-factory.ts](../../../lib/data-factory.ts) for varying data.
- Arrange-Act-Assert; descriptive test name.

**Example:** See [docs/06-como-adicionar-novo-teste.md](../../../docs/06-como-adicionar-novo-teste.md) (app example).

## E2E test (browser + app)

- Place in `test/<dominio>/<fluxo>/` (e.g. test/manager/login/) or `test/e2e/`; use both `getDeviceFromCapabilities('browser')` and `getDeviceFromCapabilities('mobile')`; optionally `reLaunchApp(emulator)` from `lib/Utils` or fixtures. Imports: Page Objects from `pageobjects/<dominio>/`, Screen Objects from `screenobjects/<dominio>/`, test-data from `test-data/<dominio>/<fluxo>/`.
- Can run steps in sequence or in parallel (`Promise.all`).
- Add a severity tag in the test name (@blocker, @critical, @normal, @minor, @trivial) according to that case; optionally @fluxo, @web, @app.

## Checklist

- [ ] File under `test/<dominio>/<fluxo>/` (e.g. test/manager/login/) or `test/e2e/` with extension `.ts` (specs pattern in wdio.shared.conf; flow must have suite in `suites`)
- [ ] Import `expect` from `@wdio/globals`; device access via Page/Screen Objects from `pageobjects/<dominio>/` and `screenobjects/<dominio>/` or `lib/Utils`; test-data from `test-data/<dominio>/<fluxo>/` with correct relative path
- [ ] **POM enforced:** NO `browser.$`, `$()`, `click()`, `setValue()`, or DOM helpers in spec files — all in Page Objects
- [ ] Page Objects expose public methods for actions and public getters for assertions
- [ ] Selectors use stable attributes (`data-testid`, `data-cy`, `#id`, `[name]`, `aria/`) — no fragile class names or positional XPath
- [ ] Use test-data when there are reusable inputs; use builder or data-factory when varying data
- [ ] Test name describes scenario and expected result; **include severity tag** (@blocker, @critical, @normal, @minor, @trivial) for that case; optionally @fluxo, @web, @app
- [ ] (Recomendado) Allure: import `@wdio/allure-reporter`; estrutura (epic/feature/story), steps; opcional: severity, tag, attachment, issue/testId, argument — ver [docs/10-allure-reporter.md](../../../docs/10-allure-reporter.md)

## Full reference

[docs/06-como-adicionar-novo-teste.md](../../../docs/06-como-adicionar-novo-teste.md)
