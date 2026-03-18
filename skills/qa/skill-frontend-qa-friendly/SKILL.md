---
name: frontend-qa-friendly
description: Guides frontend markup for QA automation: web (hooks para WebdriverIO — data-testid, texto, aria) and React Native (testID, accessibilityLabel). Use when writing or reviewing UI that will be tested by QA.
---

# Frontend QA-friendly

Garantir markup que permita localizar elementos de forma **estável** nos testes. **Web:** automação E2E do projeto usa **WebdriverIO** (`$` / `browser.$`), não Playwright — ver mapeamento abaixo. **React Native:** Appium / WebdriverIO.

---

## Web — o que expor para **WebdriverIO**

Os testes usam seletores documentados em [WebdriverIO — Selectors](https://webdriver.io/docs/selectors). **Não** assumir APIs do Playwright (`getByRole`, `getByLabel`, `getByTestId`) no código de teste deste projeto.

| Markup / conteúdo | Como o QA localiza (WebdriverIO) |
| ----------------- | -------------------------------- |
| `data-testid="x"` | `$('[data-testid="x"]')` |
| Texto visível no botão/link | `$('button=Salvar')`, `$('a=Texto')` |
| Nome acessível | `$('aria/Nome')` ou `[aria-label="..."]` |
| `label for` + `id` no input | `$('#idDoInput')` |
| `name` no form | `$('[name="campo"]')` |

### Goal

Frontend que permita localizar e preencher elementos **sem XPath** e sem depender só de classes de estilo.

### Forms

- Associar cada input a label (`for`/`id` ou input dentro de `<label>`).
- **Não** descrever para o QA como `getByLabel` — descrever: id estável ou `data-testid` ou `$('aria/...')` conforme o markup.

### Buttons and links

- Texto visível estável ou `aria-label` (ícones) para permitir `$('button=...')` ou `[aria-label="..."]`.

### Checkboxes e radios

- Label associada ou `aria-label` para localizar com CSS/`aria/` ou `data-testid` quando necessário (não depender de XPath).

### Combos e selects

- `<select>` nativo ou componente com nome acessível; evitar dropdown só localizável por ordem no DOM — preferir `data-testid` no controle.

### data-testid (web) / testID (RN)

- **Prioridade** quando o time controla o markup — [skill-pr-selectors-for-automation](../skill-pr-selectors-for-automation/SKILL.md).
- **Padrão obrigatório:** **`<feature>-<component>-<element>`** (kebab-case). Ex.: `login-form-email`, `checkout-modal-confirm` — não usar só `btn-submit` ou `input-1`.

## Evitar (web)

- **XPath posicional** como única forma de achar elemento (o projeto **proíbe XPath** nos testes).
- Classes minificadas/hash como único hook.
- Placeholder só com i18n sem `data-testid` ou `id` estável.

## Loading and feedback

- Estados detectáveis: `aria-busy`, `data-testid` em spinner/mensagem, etc.

---

## React Native

- **Prioridade:** `testID` no padrão **`<feature>-<component>-<element>`** (ex.: `orders-item-list-row-${id}`); `accessibilityLabel` como complemento.
- Listas: `testID` no item (`orders-item-list-row-${id}`).
- Telas/modais: `testID` no container quando útil para esperas.
- Evitar depender só de texto i18n sem `testID`.

---

## Referências

- [WebdriverIO — Selectors](https://webdriver.io/docs/selectors)
- [skill-pr-selectors-for-automation](../skill-pr-selectors-for-automation/SKILL.md)
