---
name: qa-add-data-testids
description: >-
  Adds stable data-testid (web) or testID (React Native) to UI in a sibling
  frontend repo, then creates/updates Page Objects in the QA WebdriverIO repo
  to use those hooks and replace fragile selectors. Use when adding testids,
  fixing selector blockers from a PR review, syncing pageobjects after new
  hooks, or when the frontend app shares the same multi-root workspace as the
  test project.
---

# Adicionar data-testid / testID + sync de Page Objects

Implementa hooks estáveis na UI do **frontend** e sincroniza **Page Objects** no repo de testes (WebdriverIO). Complementa a revisão ([skill-pr-selectors-for-automation](../skill-pr-selectors-for-automation/SKILL.md)), que só reporta.

**Web:** `data-testid`. **React Native:** `testID`. Nomenclatura obrigatória: **`<feature>-<component>-<element>`** (kebab-case).

---

## Pré-requisito: multi-root

O frontend e o projeto de testes devem estar no **mesmo workspace** Cursor.

### Detectar roots

| Root | Sinais |
|------|--------|
| **Repo de testes (QA)** | `configs/wdio.*.conf.ts`, `pageobjects/`, `test/`, `AGENTS.md` de QA |
| **Repo frontend** | `package.json` com react / next / vue / react-native; pasta `src/` de UI |

No workspace típico: testes = este repo; frontend = ex. `qd-partners`.

Se houver mais de um candidato a frontend, usar o indicado pelo usuário ou o que contém os arquivos do escopo (diff/PR/paths).

**Editar:** arquivos de UI no frontend **e** `pageobjects/` no repo de testes.  
**Não editar por padrão:** specs (`test/`), configs wdio, commits/PRs.

---

## Workflow

Copiar e acompanhar:

```
Progresso:
- [ ] 1. Detectar roots (testes vs frontend)
- [ ] 2. Definir escopo de UI
- [ ] 3. Mapear Page Objects e seletores frágeis
- [ ] 4. Identificar elementos sem hook estável
- [ ] 5. Editar frontend (data-testid / testID)
- [ ] 6. Criar/atualizar Page Objects
- [ ] 7. Entregar resumo
```

### 1. Detectar roots

Identificar caminhos absolutos do repo QA e do frontend. Confirmar com sinais da tabela acima. Trabalhar diffs/`gh` no **repo frontend** para escopo de UI; Page Objects só no **repo QA**.

### 2. Definir escopo de UI

Ordem de preferência:

1. Arquivos, pastas ou diff fornecidos pelo usuário.
2. Relatório colado do `agt-qa-pr-selectors-reviewer` (bloqueadores = backlog).
3. PR no frontend: `gh pr diff` / `gh pr diff --name-only` (ou número de PR informado).
4. Fallback: `git diff main...HEAD` (ou branch base) **no cwd do frontend**.

Filtrar só UI (componentes, páginas, telas, estilos com markup). Ignorar APIs, stores puros, testes unitários do app, configs.

### 3. Mapear Page Objects e seletores existentes

No repo QA, localizar POs e specs do fluxo (`pageobjects/<Dominio>/`, `test/<dominio>/<fluxo>/`).

Registrar:

- Seletores já estáveis (`[data-testid]`, `name`, `id`, `aria/` estável) — **alinhar** novos hooks a esses nomes quando fizer sentido.
- Seletores **frágeis** (ver abaixo) — candidatos a rewrite após adicionar testid.

### 4. Identificar elementos sem hook estável

Mesmos critérios do reviewer / [frontend-qa-friendly](../skill-frontend-qa-friendly/SKILL.md):

- Inputs, botões (incl. só ícone), links, toggles, listas/itens, modais, loading/erro.

**Não** adicionar testid redundante se já houver hook estável **e** o PO o usa de forma saudável — exceto pedido explícito do usuário ou bloqueador do relatório.

### 5. Editar o frontend

| Plataforma | Atributo | Exemplo |
|------------|----------|---------|
| Web | `data-testid="feature-component-element"` | `data-testid="products-modal-submit"` |
| React Native | `testID="feature-component-element"` | `testID="orders-item-list-row"` |

Regras:

- Padrão **`<feature>-<component>-<element>`**; listas: prefixo + sufixo (`...-row-${id}`).
- Componentes reutilizáveis: prop opcional (`dataTestId` / `testID`) propagada ao DOM (ex.: StatusToggle no partners).
- Filhos derivados: `${dataTestId}-${suffix}` quando o pai já recebe o prop.
- Preservar estilo do projeto (aspas, tipagem, exports).
- Um identificador principal por elemento; não empilhar testid + aria só por hábito.
- Sem refactors de UI além do necessário para expor o hook.

### 6. Criar/atualizar Page Objects (obrigatório após os hooks)

Para **cada** testid/testID adicionado ou alterado no escopo:

1. Garantir getter/método no PO do domínio (`pageobjects/<Dominio>/` ou `components/`).
2. Preferir `$('[data-testid="..."]')` (web) ou helper/`~`/`testID` do projeto (RN / Appium).
3. Seguir [skill-qa-webdriverio-maintain-conventions](../skill-qa-webdriverio-maintain-conventions/SKILL.md): seletores em getters privados; ações públicas; **sem XPath**; sem `browser.$` em specs.

#### Candidatos a troca (seletores frágeis)

Reescrever no PO quando o elemento ganhou testid estável:

- Classes CSS / styled-components / hashes
- XPath (`//...`) — proibido no padrão QA
- Ordem/posição no DOM (`nth-child`, cadeias longas `div > div > span`)
- `button[type="submit"]` (ou similar) sem testid
- Texto i18n / `aria-label` traduzível como **único** seletor de elemento crítico

#### Manter

Seletores já estáveis (`[name="..."]` de form controlado, `#id` estável, `aria/` estável) se **não** houver testid novo para aquele elemento.

#### PO inexistente

Criar em `pageobjects/<Dominio>/` (e `components/` se for modal/widget), alinhado aos POs vizinhos. Não inventar specs nem fluxos de teste novos — só expor elementos cobertos pelos hooks.

### 7. Resumo de saída

Usar este formato:

```markdown
# data-testid / testID + Page Objects

**Frontend:** [path do root]
**Repo QA:** [path do root]
**Escopo:** [Web | React Native | Ambos]

## Hooks adicionados/alterados

| Elemento / arquivo UI | data-testid / testID |
|-----------------------|----------------------|
| ... | `feature-component-element` |

## Page Objects

| Arquivo PO | Mudança |
|------------|---------|
| `pageobjects/...` | criado / getter X: `antes` → `$('[data-testid="..."]')` |

## Seletores frágeis trocados

- `antes` → `depois` (`arquivo`)

## Follow-ups

- Specs com seletor inline frágil (não editados): ...
- Itens fora de escopo / não alterados: ...
```

---

## Relatório do reviewer como input

Se o usuário colar o relatório de `agt-qa-pr-selectors-reviewer`:

1. Tratar **Bloqueadores** (e Sugestões relevantes) como backlog.
2. Implementar hooks no frontend.
3. Sync dos Page Objects.
4. No resumo, referenciar quais itens do relatório foram resolvidos.

---

## Fora de escopo

- Reescrever specs (salvo pedido explícito).
- Commit, push ou PR.
- Refactors de UI além do hook.
- Citar Playwright (`getByTestId`, etc.) — sempre WebdriverIO / Appium.

---

## Referências

- [skill-pr-selectors-for-automation](../skill-pr-selectors-for-automation/SKILL.md) — critérios e nomenclatura
- [skill-frontend-qa-friendly](../skill-frontend-qa-friendly/SKILL.md) — markup acessível
- [skill-qa-webdriverio-maintain-conventions](../skill-qa-webdriverio-maintain-conventions/SKILL.md) — POM e seletores no repo QA
- [WebdriverIO — Selectors](https://webdriver.io/docs/selectors)
