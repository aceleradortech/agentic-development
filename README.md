# Agentic Development — Spec-Driven Development com Claude Code

Esqueleto de método para desenvolvimento orientado a especificações usando os 4 primitivos do Claude Code: **Rules, Commands, Skills e Agents**. Pensado para ser copiado/linkado em projetos reais.

---

## 🧭 O método em uma frase

> **Escreva antes de construir.** SPEC define o problema; Plano quebra em tasks entregáveis; Tech Spec detalha como; agente executa. Cada etapa é um contrato explícito — pular etapa é anti-padrão.

```
Briefing  →  /spec        →  specs/<slug>.md
SPEC      →  /plan        →  plans/<slug>.md
Task      →  /tech-spec   →  tech-specs/<slug>__<TASK_ID>.md
Tech Spec →  /code        →  invocação explícita do agente coder → código + PR
```

---

## 🧱 Os 4 primitivos presentes neste repo

| Primitivo | Onde vive | Natureza | Exemplos |
|---|---|---|---|
| **Rules** | `CLAUDE.md` + `rules/` | Declarativas — Claude lê e respeita | `rules/stack.md`, `rules/anti-patterns.md` |
| **Rules programáticas** | `.claude/settings.json` | Hooks — a **harness** executa em eventos | `SessionStart`, `PostToolUse` |
| **Commands** | `commands/*.md` | Ritual do processo — usuário dispara | `/spec`, `/plan`, `/tech-spec`, `/code` |
| **Skills** | `skills/<nome>/SKILL.md` | Conhecimento condicional sob demanda | `spec-creator`, `task-planner`, `tech-spec-creator` |
| **Agents** | `agents/*.md` | Execução isolada com contexto próprio | `react-coder`, `vue-coder`, `tester` |

---

## 📂 Estrutura do repo

```
.
├── CLAUDE.md                       # índice de Rules (carregado automaticamente)
├── rules/                          # convenções modulares (language, stack, patterns, ...)
├── .claude/
│   └── settings.json               # hooks (rules programáticas)
├── commands/                       # slash commands (/spec, /plan, /tech-spec, /code)
├── skills/                         # skills conversacionais (spec-creator, task-planner, tech-spec-creator)
├── agents/                         # agentes de execução (react-coder, vue-coder, tester)
├── briefings/                      # entrada do fluxo
├── specs/                          # saída de /spec
├── plans/                          # saída de /plan
└── tech-specs/                     # saída de /tech-spec
```

---

## ⚙️ Instalação

Este repo é um **skeleton**. Para usar em um projeto real, você tem três opções:

### Opção 1 — Copiar (mais simples, versiona junto do projeto)

```bash
cd <seu-projeto>
cp    <skeleton>/CLAUDE.md                 ./CLAUDE.md
cp -r <skeleton>/rules                     ./rules
cp -r <skeleton>/.claude                   ./.claude
cp -r <skeleton>/commands                  ./.claude/commands
cp -r <skeleton>/skills                    ./.claude/skills
cp -r <skeleton>/agents                    ./.claude/agents
mkdir -p briefings specs plans tech-specs
```

Depois, **edite os arquivos em `rules/`** para refletir as convenções reais do projeto (stack, auth, testes, padrões de código). **Nenhum método funciona bem com placeholders genéricos.**

### Opção 2 — Symlink (para desenvolver o método em paralelo)

Útil enquanto você ainda está evoluindo o método. Mudanças no skeleton se refletem em todos os projetos que linkam.

```bash
cd <seu-projeto>/.claude
ln -s <skeleton>/commands  commands
ln -s <skeleton>/skills    skills
ln -s <skeleton>/agents    agents

cd <seu-projeto>
ln -s <skeleton>/CLAUDE.md CLAUDE.md
ln -s <skeleton>/rules     rules
```

> **Atenção:** `settings.json` normalmente é específico por projeto (permissões, hooks locais). Prefira copiar, não linkar.

### Opção 3 — Instalar no nível do usuário

Para rodar o método em qualquer projeto da máquina sem instalação por repo:

```bash
mkdir -p ~/.claude
cp -r <skeleton>/commands  ~/.claude/commands
cp -r <skeleton>/skills    ~/.claude/skills
cp -r <skeleton>/agents    ~/.claude/agents
cp    <skeleton>/CLAUDE.md ~/.claude/CLAUDE.md
cp -r <skeleton>/rules     ~/.claude/rules
```

Neste modo, o projeto-alvo **ainda precisa** de um `CLAUDE.md` local para **sobrescrever ou complementar** com convenções específicas daquele repo.

### Pré-requisitos

- Claude Code instalado (`claude-code` CLI, desktop app, ou extensão IDE).
- Permissão aos MCPs declarados nos agentes (`tools:` no frontmatter) — em particular o `tester` precisa da **extensão Claude for Chrome** (ver seção "Agentes" abaixo).

---

## 🚀 Exemplo end-to-end

Imagine que você recebeu a demanda **"permitir que o usuário cancele um pedido"**. Veja como o fluxo se materializa:

### Passo 1 — Escrever o briefing

Você cria `briefings/cancel-order.md` com o essencial (pode ser 4 parágrafos — o bruto):

```markdown
Usuários pedem para cancelar pedidos antes do envio. Hoje precisam contatar
o suporte, que gasta ~10min por caso. Queremos permitir cancelamento self-service.
Restrição: só pedidos em status "em preparação" podem ser cancelados.
Precisamos capturar o motivo para entender padrões.
```

### Passo 2 — Rodar `/spec`

```
/spec briefings/cancel-order.md
```

O Claude carrega a skill `spec-creator`, faz o diagnóstico do briefing, conduz 3–4 rodadas curtas via `AskUserQuestion` (persona exata, métrica-âncora, casos de borda, fora de escopo) e grava `specs/cancel-order.md`.

Você ganha um documento focado em **problema + usuário + comportamentos esperados** — sem uma linha de stack ou arquitetura.

### Passo 3 — Rodar `/plan`

```
/plan specs/cancel-order.md
```

Skill `task-planner` entra em ação. Lê a SPEC, **pode despachar subagentes `Explore`** para checar se algo parecido já existe no repo, propõe uma quebra inicial em tasks:

| # | Task | Cobre | Depende | Fase |
|---|------|-------|---------|------|
| T01 | Botão "Cancelar" + dialog de confirmação com motivo | H1, H2 | — | MVP |
| T02 | Listagem de cancelamentos (admin) | H3 | T01 | Fase 2 |
| T03 | Relatório agregado de motivos | H4 | T01 | Fase 2 |

Após negociar com você, grava `plans/cancel-order.md` com MVP = T01.

### Passo 4 — Rodar `/tech-spec`

```
/tech-spec plans/cancel-order.md#T01
```

Skill `tech-spec-creator` lê `CLAUDE.md` + SPEC + plano + task, **despacha múltiplos subagentes `Explore` em paralelo** para mapear padrões do repo (auth, fetching, forms, testes), apresenta uma **proposta de abordagem**, negocia contratos de API, schema, trade-offs, e grava `tech-specs/cancel-order__T01.md`.

O documento tem **arquitetura + contratos + modelo de dados + plano de testes + sequência de implementação em 6 passos**, cada decisão citando a fonte (`CLAUDE.md`, arquivo X do repo, lib Y).

### Passo 5 — Rodar `/code`

```
/code tech-specs/cancel-order__T01.md
```

O comando valida o checklist **"Pronto para codar?"**, identifica o agente correto a partir de `rules/stack.md` (React → `react-coder`), mostra um resumo explícito:

```
Prestes a invocar:
  Agente:    react-coder
  Tech Spec: tech-specs/cancel-order__T01.md
  Plano:     1. cancel.schema.ts  2. hook  3. dialog  4. integrar  5. erro 409  6. a11y

Confirmar invocação?
```

Você confirma. O agente roda em **contexto isolado**, executa a Tech Spec passo a passo, produz o PR.

### Repita para T02, T03... quando forem para a fila

---

## 🤖 Agentes disponíveis

| Agente | Modelo | Propósito |
|---|---|---|
| `react-coder` | sonnet | Escrever, refatorar e revisar código React |
| `vue-coder` | sonnet | Escrever, refatorar e revisar código Vue 3 |
| `tester` | sonnet | Testes manuais de UI, validação de interações e console |

Todos respondem em **pt-BR** e mantêm código + termos técnicos em inglês.

### Agente `tester` — pré-requisito especial

Depende do **Claude in Chrome MCP** (extensão de browser que controla uma instância real do Chrome). Sem a extensão instalada e autorizada, o agente não funciona.

**Como instalar:**

1. Instale a extensão **Claude for Chrome** na Chrome Web Store.
2. Faça login com a mesma conta do Claude Code.
3. Abra o Chrome **antes** de invocar o agente.
4. Conceda permissão quando o agente tentar usar as tools `mcp__Claude_in_Chrome__*` pela primeira vez.

Os coders (`react-coder`, `vue-coder`) não exigem MCP especial — usam apenas tools nativas (`Read`, `Edit`, `Write`, `Grep`, `Glob`, `Bash`, `WebFetch`).

---

## 📐 Convenções adotadas

Convenções detalhadas vivem em [rules/](./rules/). Resumo:

- **Idioma:** resposta em pt-BR; código, identificadores e termos técnicos em inglês ([rules/language.md](rules/language.md))
- **Stack placeholders:** preencher para seu projeto ([rules/stack.md](rules/stack.md))
- **Feature-first:** organização por domínio de negócio, não por layer ([rules/architecture.md](rules/architecture.md))
- **Modelo padrão dos agentes:** `sonnet` — evita custo desnecessário de `opus` em execução
- **Tools declaradas explicitamente** no frontmatter de cada agente — nenhum herda todas
- **Escopo restrito:** cada agente tem seção "What NOT to do"
- **Hard stops numéricos:** limites concretos em vez de "use julgamento"

---

## 🧪 Rodando o método

Depois de instalar no seu projeto:

```
/spec briefings/<sua-feature>.md
```

E siga a trilha. Cada comando sinaliza o próximo no fechamento.

Para ver as mensagens da harness (hooks em `.claude/settings.json`), observe:

- Abertura de sessão exibe o lembrete do método.
- Após `Write`/`Edit`, aparece o aviso sobre consistência com `rules/`.

Essas mensagens são **rules programáticas** em ação — a harness as executa, não o Claude.

---

## 🎯 Para onde evoluir

Áreas naturais para estender:

- **Exemplos populados** em `briefings/`, `specs/`, `plans/`, `tech-specs/` (um fluxo completo congelado como referência).
- **Mais agentes coders** (Python backend, Go, Swift, etc.) em `agents/`.
- **Hooks reais** em `.claude/settings.json` — rodar `prettier --check` em `PreToolUse` de `Write|Edit`, por exemplo.
- **Skill de pós-código** (`pr-reviewer`?) para fechar o loop da Tech Spec → PR → revisão.

O método não é dogma. É um esqueleto para você moldar ao seu time.
