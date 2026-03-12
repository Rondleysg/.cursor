---
name: pr-selectors-for-automation
description: Verifies that PRs of web or React Native projects expose correct, stable selectors for automated tests (E2E/Appium/WebdriverIO). Use when reviewing a pull request to ensure new or changed UI can be automated; outputs a structured report with blockers and suggestions.
---

# Revisão de seletores em PR para automação

Objetivo: avaliar se os arquivos alterados em uma PR expõem **seletores estáveis e adequados** para criação de testes automatizados (web: Playwright/WebdriverIO; React Native: Appium/WebdriverIO).

---

## Escopo da revisão

1. Identificar arquivos de UI alterados na PR (componentes, páginas, telas).
2. Para cada elemento interativo ou relevante para assertiva, verificar se existe um seletor estável e documentável.
3. Classificar achados em **bloqueador** (impede automação confiável) ou **sugestão** (melhora resiliência).
4. Entregar relatório no formato definido abaixo.

---

## Web (HTML / React / Vue / etc.)

### Seletores preferidos (ordem de prioridade)

| Prioridade | Tipo            | Uso em testes                                          | Exemplo no código                    |
| ---------- | --------------- | ------------------------------------------------------ | ------------------------------------ |
| 1          | `data-testid`   | `getByTestId('...')` / `$('[data-testid="..."]')`      | `data-testid="btn-submit"`           |
| 2          | `aria-label`    | `getByRole('button', { name: '...' })`                 | `aria-label="Fechar"`                |
| 3          | label + input   | `getByLabel('...')` / `getByRole('textbox', { name })` | `<label for="email">` + `id="email"` |
| 4          | `id` estável    | `#id`                                                  | `id="form-login"` (não gerado)       |
| 5          | `name` em forms | `[name="campo"]`                                       | `<input name="email" />`             |

Para cada elemento, recomendar ou exigir **apenas um** identificador para uso como seletor nos testes, escolhido pela ordem de prioridade acima: o primeiro da lista que fizer sentido ou que estiver faltando. Não sugerir a adição de mais de um identificador no mesmo elemento (ex.: `data-testid` e `aria-label`); um só é suficiente.

### Formulários

- **Bloqueador:** input sem label associada (`for`/`id` ou input dentro de `<label>`) e sem `data-testid`/`aria-label`.
- **Sugestão:** preferir `data-testid` em campos dinâmicos ou quando o texto do label mudar (i18n).

### Botões e links

- **Bloqueador:** botão/link apenas ícone sem `aria-label` e sem `data-testid`.
- **Sugestão:** botão com texto visível já é localizável por role + name; `data-testid` opcional para desambiguação.

### Listas e itens dinâmicos

- **Bloqueador:** lista de itens clicáveis sem nenhum hook estável (ex.: só classe CSS ou posição).
- **Sugestão:** `data-testid` com sufixo estável (ex.: `data-testid="item-pedido-{id}"` ou convenção documentada).

### Evitar

- XPath por posição (`div[1]/div[2]/span`).
- Classes CSS como único seletor (minificadas/hash por build).
- IDs ou textos gerados automaticamente que mudam a cada build.
- Não sugerir múltiplos identificadores no mesmo elemento — usar só um, o de maior prioridade aplicável.

### Checklist rápido (web)

- [ ] Todo input de formulário tem label associada ou `data-testid`/`aria-label`.
- [ ] Botões e links têm texto visível ou `aria-label` ou `data-testid`.
- [ ] Nenhum seletor crítico depende só de ordem de divs ou de classes de estilo.
- [ ] Estados de loading/erro/sucesso são detectáveis (role, `data-testid` ou texto estável).

---

## React Native

### Seletores preferidos

| Prioridade | Propriedade          | Uso em testes (Appium/WebdriverIO)       | Exemplo no código             |
| ---------- | -------------------- | ---------------------------------------- | ----------------------------- |
| 1          | `testID`             | `by.id('...')` / `element(by.id('...'))` | `testID="btn-submit"`         |
| 2          | `accessibilityLabel` | Localização por acessibilidade           | `accessibilityLabel="Enviar"` |
| 3          | `accessibilityHint`  | Contexto adicional                       | Opcional para ações complexas |

Para cada elemento, recomendar ou exigir **apenas um** identificador para uso como seletor nos testes, escolhido pela ordem de prioridade acima (1 → 2 → 3): o primeiro da lista que fizer sentido ou que estiver faltando. Não sugerir a adição de mais de um identificador no mesmo elemento (ex.: `testID` e `accessibilityLabel`); um só é suficiente.

### Componentes interativos

- **Bloqueador:** `Button`, `TouchableOpacity`, `Pressable` etc. sem `testID` e sem `accessibilityLabel`.
- **Sugestão:** sempre que possível usar `testID` estável (ex.: `testID="login-submit"`) para E2E; `accessibilityLabel` para acessibilidade e fallback de locator.

### Inputs (TextInput)

- **Bloqueador:** `TextInput` sem `testID` e sem `accessibilityLabel` (e sem placeholder estável documentado).
- **Sugestão:** `testID` por tela/campo (ex.: `testID="login-email"`, `testID="login-password"`).

### Listas (FlatList, SectionList)

- **Bloqueador:** itens de lista sem `testID` no item (ou no container do item) que permita identificar o elemento.
- **Sugestão:** `testID` no item com convenção (ex.: `testID={\`item-${item.id}\`}`ou`testID="list-item"` com index quando necessário).

### Navegação e modais

- **Sugestão:** telas/modais com `testID` no container principal (ex.: `testID="screen-home"`, `testID="modal-confirm"`) para esperas e asserts.

### Evitar (React Native)

- Localização apenas por texto que muda com i18n sem fallback (`testID`/`accessibilityLabel`).
- Depender só de ordem de filhos na árvore para cliques.

### Checklist rápido (React Native)

- [ ] Todo elemento interativo (botão, link, input) tem `testID` ou `accessibilityLabel`.
- [ ] Listas expõem `testID` no item ou no container de item.
- [ ] Telas/modais principais têm `testID` quando útil para automação.
- [ ] Nenhum locator crítico depende só de texto traduzido sem fallback estável.

---

## Formato do relatório

Ao concluir a revisão, entregar um único bloco no formato abaixo.

```markdown
# Revisão de seletores para automação

**Escopo:** [Web | React Native | Ambos]
**Arquivos revisados:** [lista resumida]

## Bloqueadores

- [ ] **Arquivo:** `caminho/arquivo` — [descrição]. Sugestão: [o que adicionar/corrigir].

## Sugestões

- **Arquivo:** `caminho/arquivo` — [descrição]. Sugestão: [melhoria opcional].

## Resumo

- Total de bloqueadores: N
- Total de sugestões: M
- **Veredito:** [PR pronta para automação | Ajustes necessários antes de criar testes]
```

---

## Referências

- Para critérios detalhados de markup web (labels, roles, data-testid), usar em conjunto a skill **frontend-qa-friendly**: [.cursor/skills/qa/skill-frontend-qa-friendly/SKILL.md](../skill-frontend-qa-friendly/SKILL.md).
