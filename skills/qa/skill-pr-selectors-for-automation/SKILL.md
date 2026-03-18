---
name: pr-selectors-for-automation
description: Verifies that PRs of web or React Native projects expose correct, stable selectors for automated tests (WebdriverIO web + Appium/WebdriverIO mobile). Use when reviewing a PR to ensure new or changed UI can be automated; outputs a structured report with blockers and suggestions.
---

# Revisão de seletores em PR para automação

Objetivo: avaliar se os arquivos alterados em uma PR expõem **seletores estáveis e adequados** para criação de testes automatizados. **Web (browser):** [WebdriverIO](https://webdriver.io/docs/selectors) (`$` / `browser.$`). **React Native:** Appium / WebdriverIO (`testID`, `accessibilityLabel`). O agent pode obter o escopo via GitHub CLI (`gh pr diff`) ou `git diff`.

---

## Importante: WebdriverIO ≠ Playwright

Na **web**, os testes E2E do projeto usam **WebdriverIO**. Na revisão de PR e na documentação para o QA:

- **Não** citar como “como vamos localizar no teste” APIs do **Playwright** (`getByRole`, `getByLabel`, `getByTestId`, `page.locator`, etc.) — **não existem** no WebdriverIO.
- Sempre indicar o equivalente em **WebdriverIO** (tabela abaixo) ou remeter à [doc oficial de seletores](https://webdriver.io/docs/selectors).

---

## XPath — proibido no padrão de automação

- **Proibido** orientar ou aceitar **XPath** nos testes WebdriverIO (ex.: `//body/div[3]/span`, `//*[@class='...']`). É frágil, difícil de manter e **fora do padrão** do projeto QA.
- Na revisão de PR: tratar como **bloqueador** quando um elemento crítico **só** poderia ser automatizado com XPath posicional ou XPath profundo; o dev deve expor `data-testid`, texto/label acessível, `aria-label` ou outro hook compatível com **CSS ou seletores nativos WDIO** (texto, `aria/`).

---

## Escopo da revisão

1. Identificar arquivos de UI alterados na PR (componentes, páginas, telas).
2. Para cada elemento interativo ou relevante para assertiva, verificar se existe um hook estável mapeável para **WebdriverIO (web)** ou **testID/accessibilityLabel (RN)**.
3. Classificar achados em **bloqueador** ou **sugestão**.
4. Entregar relatório no formato definido abaixo.

---

## Web (HTML / React / Vue / etc.) — seletores para **WebdriverIO**

Hierarquia alinhada à [documentação WebdriverIO — Selectors](https://webdriver.io/docs/selectors) e ao padrão do projeto (data-testid prioritário quando o time controla o markup).

### Nomenclatura obrigatória: `<feature>-<component>-<element>`

Todo **`data-testid`** (e equivalente **`data-cy`**) deve seguir **três segmentos** em kebab-case, minúsculas:

| Segmento | Significado | Exemplo |
|----------|-------------|---------|
| **feature** | Fluxo ou área | `login`, `checkout` |
| **component** | Bloco de UI | `form`, `modal`, `header` |
| **element** | Campo, botão, região | `email`, `submit`, `close` |

Exemplos válidos: `login-form-email`, `login-form-submit`, `checkout-modal-close`. Listas: prefixo de três segmentos + sufixo opcional (ex.: `orders-item-list-row-${id}`).

- **Sugestão / bloqueador leve:** `data-testid` genérico (`submit`, `btn-ok`) sem feature/component — pedir alinhamento ao padrão.

| Prioridade | O que expor no DOM | Uso em testes WebdriverIO | Exemplo no código (UI) |
| ---------- | ------------------ | ------------------------- | ---------------------- |
| 1 | `data-testid` | `$('[data-testid="..."]')` ou `browser.$('...')` | `data-testid="login-form-submit"` |
| 2 | Texto visível estável + elemento | `tag=texto` (recomendado pela WDIO quando o texto é estável) | Botão "Salvar" → `$('button=Salvar')` |
| 3 | Nome acessível | `$('aria/NomeAcessível')` | Equivalente a localizar por nome acessível |
| 4 | `aria-label` | `$('[aria-label="..."]')` | Ícone com `aria-label="Fechar"` |
| 5 | `label for` + `id` no input | `$('#idEstável')` | `<label for="email">` + `id="email"` |
| 6 | `name` em forms | `$('[name="campo"]')` | `<input name="email" />` |
| 7 | `id` estável (não gerado) | `$('#form-login')` | Só se estável entre builds |

Para cada elemento, recomendar **apenas um** identificador principal (o de maior prioridade aplicável). Não pedir `data-testid` e `aria-label` no mesmo elemento sem necessidade.

### Formulários

- **Bloqueador:** input sem label associada (`for`/`id` ou dentro de `<label>`) e sem `data-testid`/`aria-label`.
- **Sugestão:** campos com i18n — preferir `data-testid` ou `id` estável; texto via `button=...` quebra se a tradução mudar sem estratégia (arquivos de tradução alinhados aos testes, como recomenda a WDIO).

### Botões e links

- **Bloqueador:** botão/link só ícone sem `aria-label` e sem `data-testid`.
- **Sugestão:** texto visível estável permite `$('button=Texto')` ou `$('a=Texto')` na WDIO — **não** `getByRole('button', { name })`.

### Listas e itens dinâmicos

- **Bloqueador:** itens clicáveis só com classe CSS ou ordem no DOM.
- **Sugestão:** `data-testid` no padrão `<feature>-<component>-<element>`; itens dinâmicos: ex. `orders-item-list-row-${id}`.

### Evitar (web)

- **XPath** (qualquer forma) como estratégia esperada para o QA.
- Classes como único seletor (minificadas/hash).
- IDs ou textos gerados automaticamente que mudam a cada build.

### Checklist rápido (web)

- [ ] Inputs com label/`data-testid`/`aria-label`.
- [ ] Botões/links com texto acessível ou `aria-label` ou `data-testid`.
- [ ] Nada crítico depende só de ordem de divs ou classes de estilo.
- [ ] Estados loading/erro detectáveis (`data-testid`, `aria-*`, texto estável).
- [ ] Nenhum achado depende de XPath para ser automatizado.
- [ ] `data-testid` segue `<feature>-<component>-<element>`.

---

## React Native

O **`testID`** segue o **mesmo padrão** que na web: **`<feature>-<component>-<element>`** (ex.: `login-form-submit`, `profile-header-avatar`).

### Seletores preferidos

| Prioridade | Propriedade | Uso em testes (Appium/WebdriverIO) | Exemplo no código |
| ---------- | ----------- | ----------------------------------- | ----------------- |
| 1 | `testID` | `by.id('...')` / helpers do projeto | `testID="login-form-submit"` |
| 2 | `accessibilityLabel` | Localização por acessibilidade | `accessibilityLabel="Enviar"` |
| 3 | `accessibilityHint` | Contexto adicional | Opcional |

Um identificador principal por elemento (prioridade 1 → 2 → 3).

### Componentes interativos

- **Bloqueador:** `Button` / `Pressable` etc. sem `testID` e sem `accessibilityLabel`.
- **Sugestão:** `testID` estável para E2E.

### Inputs (TextInput)

- **Bloqueador:** sem `testID` e sem `accessibilityLabel` (e sem placeholder estável documentado).

### Listas

- **Bloqueador:** itens sem `testID` no item ou container identificável.

### Evitar (React Native)

- Só texto i18n sem `testID`/`accessibilityLabel`.
- Depender só da ordem de filhos.

### Checklist rápido (React Native)

- [ ] Interativos com `testID` ou `accessibilityLabel`.
- [ ] `testID` no padrão `<feature>-<component>-<element>`.
- [ ] Listas com hook no item.
- [ ] Telas/modais com `testID` quando útil.

---

## Formato do relatório

```markdown
# Revisão de seletores para automação

**Escopo:** [Web | React Native | Ambos]
**Stack:** Web (WebdriverIO) / RN (Appium+WDIO)
**Nomenclatura:** `data-testid`/`testID` = `<feature>-<component>-<element>`
**Arquivos revisados:** [lista]

## Bloqueadores

- [ ] **Arquivo:** `...` — [descrição]. Sugestão: [correção]. (Se aplicável: nunca XPath; usar data-testid / texto / aria.)

## Sugestões

- **Arquivo:** `...` — [descrição].

## Resumo

- Bloqueadores: N | Sugestões: M
- **Veredito:** [PR pronta | Ajustes necessários]
```

---

## Referências

- [WebdriverIO — Selectors](https://webdriver.io/docs/selectors) — hierarquia oficial (ex.: `data-testid`, `aria/`, `tag=texto`).
- **Frontend / acessibilidade:** [skill-frontend-qa-friendly](../skill-frontend-qa-friendly/SKILL.md) — o markup continua valendo para **expor** nomes estáveis; nos relatórios, traduzir para **sintaxe WebdriverIO**, não Playwright.
