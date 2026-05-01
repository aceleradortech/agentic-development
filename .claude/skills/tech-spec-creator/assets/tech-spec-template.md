# Tech Spec: [Nome da task] — `<TASK_ID>`

> **SPEC:** [`specs/<slug>.md`](../specs/<slug>.md)
> **Plano:** [`plans/<slug>.md`](../plans/<slug>.md) — task `<TASK_ID>`
> **Tech Specs irmãs:** [`tech-specs/<slug>__T0X.md`](./tech-specs/<slug>__T0X.md) (se houver)
> **Convenções aplicadas:** `CLAUDE.md` (projeto) + `~/.claude/CLAUDE.md` (usuário, quando aplicável)
>
> Este documento detalha **como** entregar a task. O **porquê** vive na SPEC; **o que** e **em que ordem**, no Plano.

---

## 🎯 Escopo desta task

- **Comportamento entregue** (do Plano): [uma frase do ponto de vista do usuário]
- **Histórias/critérios da SPEC cobertos:** H1, C3
- **Depende de:** — | T00
- **Dependências externas:** [doc de parceiro, credencial, etc. — ou "nenhuma"]

---

## 🧱 Arquitetura

- **Abordagem geral:** [2–4 frases descrevendo como a task se encaixa no código existente]
- **Módulos afetados:** [`src/features/X/`, `src/lib/Y.ts`, etc.]
- **Novos arquivos:** [lista curta com propósito de cada um]
- **Padrões reutilizados:** [cite arquivos do repo — ex: "segue padrão de `src/features/orders/ConfirmDialog.tsx`"]

> **Fonte das decisões:** `CLAUDE.md` (stack), padrão existente em [arquivo].

---

## 🔌 Contratos

> Comentários dentro dos snippets devem estar em inglês.

### API / Integração

```
POST /resource/:id/action
Request:
  { field: type }
Response 200:
  { status: 'value', updatedAt: ISO8601 }
Response 4xx:
  400 invalid_field | 404 resource_not_found | 409 conflict_state
```

### Eventos / mensagens (se aplicável)

```
ResourceActioned { resourceId, field, updatedAt }
```

### Interfaces internas (hooks, services, types)

```ts
// src/features/domain/action/types.ts
// shape used by the action flow
type ActionInput = { resourceId: string; field: string };
type ActionResult = { updatedAt: string };
```

---

## 🤝 Contratos compartilhados com tasks irmãs

> Preencha apenas se algum contrato desta task também é tocado por outra task do mesmo plano. Caso contrário, escreva "nenhum".

| Contrato | Tasks que tocam | Status | Fonte da definição |
|---|---|---|---|
| [nome do contrato] | T01 (esta), T0X | **herdado** | `tech-specs/<slug>__T0X.md` |
| [nome do contrato] | T01 (esta), T0Y | **proposto aqui** — T0Y herdará | esta Tech Spec |
| [nome do contrato] | T01 (esta), T0Z | **a definir conjuntamente** | bloqueio sinalizado |

> **Status possíveis:** `herdado` (já fixado em outra Tech Spec, esta respeita) · `proposto aqui` (esta define, irmãs herdarão) · `a definir conjuntamente` (decisão pendente entre tasks) · `mudança proposta` (esta task altera contrato já fixado em irmã — impacto descrito abaixo).

**Impacto em Tech Specs já escritas:** [descreva, ou "nenhum"]

---

## 🗂️ Modelo de dados

| Campo | Tipo | Obrigatório | Default | Observações |
|---|---|---|---|---|
| `field` | `string` | sim | — | [restrições] |
| `updatedAt` | `ISO8601` | sim | `now()` | preenchido pelo backend |

> **Validação:** [onde e como — ex: schema Zod em `action.schema.ts`]. Fonte: [padrão existente em arquivo].

---

## 🔗 Integrações externas

> Preencha apenas se a task depende de sistema de terceiro. Caso contrário, remova esta seção.

- **Parceiro:** [nome]
- **Documentação de referência:** [link Swagger/wiki/arquivo — obtido na SPEC]
- **Endpoint(s) usado(s):** [método + path]
- **Autenticação:** [OAuth2 bearer / API key / etc.]
- **Rate limits / retry:** [política]
- **Contrato mock para testes:** [arquivo de fixture]

---

## ⚖️ Trade-offs e alternativas descartadas

**Decisão:** [ex: usar TanStack Query em vez de fetch direto]
- **Alternativa descartada:** [fetch direto no componente]
- **Motivo:** [cache, retry, invalidation já padronizados no repo]
- **Fonte:** [`CLAUDE.md` / padrão em X.tsx]

---

## ⚠️ Riscos e mitigações

| Risco | Impacto | Mitigação |
|---|---|---|
| [ex: operação concorrente (dupla chamada)] | dados inconsistentes | idempotência via `resourceId` + disable do botão no frontend |
| [ex: ação irreversível por engano] | reversão cara | dialog de confirmação |

---

## 🧪 Plano de testes

- **Unit:**
  - `useActionMutation` — sucesso, erro 409, erro de rede
  - `action.schema.ts` — validação de campos (vazio, inválido, limite)
- **Integration:**
  - `<ActionDialog />` — abre, confirma, fecha em sucesso, mostra erro em falha
- **E2E (se aplicável):**
  - [cenário ponta a ponta, geralmente coberto pelo agente `tester`]

> **Framework/padrão:** [ex: Vitest + Testing Library]. Fonte: padrão existente em `tests/`.

### Cobertura história × teste

> Para cada história/critério listado em "🎯 Escopo", aponte ao menos um teste que a cobre. Buracos aqui são bloqueio.

| História/critério | Teste(s) que cobre(m) | Nível |
|---|---|---|
| H1 — [descrição] | [nome do teste] | unit / integration / e2e |
| C3 — [descrição] | [nome do teste] | unit / integration / e2e |

---

## 🪜 Plano de implementação

> Cada passo deve ser **revertível isoladamente** — pode ser 1 commit ou um conjunto coeso de até 3 commits relacionados. Coesão > contagem.

1. Criar schema de validação (+ unit)
2. Criar hook de mutação (+ unit)
3. Criar componente principal (+ integration)
4. Integrar componente no ponto de entrada
5. Cobrir cenários de erro
6. Revisar acessibilidade (foco, escape, aria-label)

---

## 📚 Convenções aplicadas (de `CLAUDE.md`)

> Liste apenas as convenções do `CLAUDE.md` que impactam esta task. Não copie o `CLAUDE.md` inteiro.

- Stack: [ex: React + TypeScript + Vite]
- Estilo: [ex: Tailwind CSS, sem CSS-in-JS]
- Auth: [ex: `AuthProvider` + hook `useAuthedFetch`]
- Testes: [ex: Vitest + Testing Library]
- Idioma: código (e comentários em snippets) em inglês, resposta ao humano em pt-BR

> Se o projeto **não** tiver `CLAUDE.md`, esta seção registra as convenções combinadas durante a conversa.

---

## 📦 Dependências verificadas

> Saída do Explore obrigatório de dependências. Liste apenas as libs relevantes ao domínio desta task.

| Lib | Versão instalada | Uso nesta task |
|---|---|---|
| [nome] | [versão] | [propósito] |

**Libs novas introduzidas:** nenhuma. _(Se houver, justifique aqui: motivo, alternativa descartada, fonte.)_

---

## ✅ Pronto para codar?

- [ ] Arquitetura descrita com módulos e novos arquivos nomeados
- [ ] Decisões técnicas citam fonte (`CLAUDE.md`, arquivo do repo, lib instalada, ou "nova: motivo")
- [ ] Nenhuma pergunta foi feita ao usuário sobre algo que `CLAUDE.md`/repo já decidem
- [ ] Contratos (API/eventos/interfaces) com forma final
- [ ] Contratos compartilhados com tasks irmãs estão consistentes (herdados, propostos, ou impacto sinalizado)
- [ ] Modelo de dados com tipos, obrigatoriedade e defaults
- [ ] **Cobertura cruzada:** todas as histórias/critérios em "🎯 Escopo" têm teste correspondente na tabela
- [ ] Trade-offs não-triviais têm alternativa descartada
- [ ] Riscos conhecidos listados com mitigação
- [ ] Plano de testes cobre happy path + pelo menos 2 erros
- [ ] Sequência de implementação descreve passos revertíveis isoladamente
- [ ] Nenhuma lib nova introduzida (ou, se sim, com justificativa explícita e Explore de dependências citado)
- [ ] Convenções de `CLAUDE.md` citadas e respeitadas
- [ ] Draft foi revisado pelo usuário no chat antes da gravação
