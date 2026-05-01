# Plano: Liberação Automática de Entrega para Alunos Inadimplentes

> SPEC de referência: [`specs/liberacao-automatica-entrega-inadimplentes.md`](../specs/liberacao-automatica-entrega-inadimplentes.md)
> Este plano quebra a SPEC em **tasks independentes**, cada uma pronta para virar uma Tech Spec dedicada.
> Aqui **não** há stack, schema, arquitetura ou estimativa — apenas comportamento e sequência.

---

## 🎯 Resumo do escopo

- **Resultado-âncora** (da SPEC): Número de desbloqueios manuais por semana → zero para o caso: aluno inadimplente com ≥ 30 dias de matrícula.
- **MVP deste plano:** T01 + T02 + T03 — fecha o resultado-âncora: embaixador entrega sem desbloqueio manual, ciclo é controlado e financeiro/coordenador são notificados.
- **Fases posteriores:** T04 (liberação imediata ao quitar inadimplência) — represamento urgente é de inadimplentes acumulados; path de regularização pode aguardar.

---

## 🗺️ Mapa de tasks

| # | Task | Cobre (SPEC) | Depende de | Fase | Status |
|---|------|--------------|------------|------|--------|
| T01 | Liberação automática do botão de entrega com badge de inadimplência | Comportamento 1, Histórias 1 e 2 | — | MVP | pendente ⚠️ bloqueada por premissa |
| T02 | Restrição a uma entrega por ciclo (bloqueio automático pós-entrega) | Comportamento 3 | T01 | MVP | pendente |
| T03 | Notificação automática para financeiro e coordenador ao entregar | Comportamento 4, História 3 | T01 | MVP | pendente |
| T04 | Liberação imediata ao quitar inadimplência | Comportamento 2 | T01 | Fase 2 | pendente |

> Ordem de execução (equipe solo): **T01 → T02 → T03**

---

## 📋 Detalhamento por task

### T01 — Liberação automática do botão de entrega com badge de inadimplência

- **Comportamento entregue:** O embaixador visualiza o botão de entrega habilitado ou desabilitado com base no status de inadimplência e no tempo de matrícula, com badge visual "Inadimplente" exibido quando o botão está liberado automaticamente.
- **Histórias/comportamentos cobertos na SPEC:** História 1, História 2, Comportamento 1
- **Critérios de aceite:**
  - Dado que o embaixador acessa o perfil de um aluno inadimplente e a matrícula tem menos de 30 dias, quando visualiza o botão de entrega, então o botão está desabilitado.
  - Dado que o embaixador acessa o perfil de um aluno inadimplente e a matrícula tem 30 dias ou mais, quando visualiza o botão de entrega, então o botão está habilitado e um badge "Inadimplente" é exibido visivelmente.
  - O badge de inadimplência deve ser perceptível visualmente (não apenas por cor — exige texto ou ícone legível, conforme critério de acessibilidade da SPEC).
- **Depende de:** —
- **Premissas pendentes que podem bloquear:** ⚠️ **BLOQUEADORA** — comportamento indefinido para aluno inadimplente com ≥ 30 dias de matrícula e **nenhuma entrega prévia registrada**. Validar com área de negócio antes de iniciar a Tech Spec desta task.
- **Tech Spec:** pendente
- **Notas de negociação:** A SPEC explicita que o prazo de 30 dias é contado a partir da data de matrícula, não da primeira entrega.

---

### T02 — Restrição a uma entrega por ciclo (bloqueio automático pós-entrega)

- **Comportamento entregue:** Após o embaixador confirmar a entrega para um aluno inadimplente liberado automaticamente, o sistema retorna o botão ao estado bloqueado para as entregas subsequentes do ciclo atual, enquanto a inadimplência persistir.
- **Histórias/comportamentos cobertos na SPEC:** Comportamento 3
- **Critérios de aceite:**
  - Dado que o botão de entrega está habilitado por regra automática (30+ dias, inadimplente), quando o embaixador confirma a entrega da próxima unidade do ciclo, então a entrega é registrada com sucesso e o botão retorna ao estado bloqueado para entregas subsequentes do mesmo ciclo (se ainda inadimplente).
- **Depende de:** T01
- **Premissas pendentes:** —
- **Tech Spec:** pendente

---

### T03 — Notificação automática para financeiro e coordenador ao entregar

- **Comportamento entregue:** Ao confirmar entrega de aluno inadimplente (via liberação automática), o sistema notifica em tempo real a área financeira e o coordenador da unidade com o nome do aluno e a data da entrega.
- **Histórias/comportamentos cobertos na SPEC:** Comportamento 4, História 3
- **Critérios de aceite:**
  - Dado que o embaixador realizou uma entrega para um aluno que estava inadimplente no momento, quando a entrega é confirmada com sucesso, então a área financeira recebe uma notificação com o nome do aluno e a data da entrega.
  - O coordenador da unidade do aluno também recebe a mesma notificação.
  - A notificação é disparada no momento da confirmação da entrega, não em batch.
- **Depende de:** T01
- **Premissas pendentes:** Canal de notificação (e-mail, push, in-app, etc.) não está definido na SPEC — deve ser decidido na Tech Spec desta task.
- **Tech Spec:** pendente

---

### T04 — Liberação imediata ao quitar inadimplência *(Fase 2)*

- **Comportamento entregue:** Quando o aluno quita todas as parcelas em atraso antes de completar 30 dias de matrícula, o botão de entrega é desbloqueado imediatamente, sem badge de inadimplência.
- **Histórias/comportamentos cobertos na SPEC:** Comportamento 2
- **Critérios de aceite:**
  - Dado que o embaixador acessa o perfil de um aluno inadimplente com menos de 30 dias de matrícula e o aluno quitou todas as parcelas em atraso, quando o embaixador visualiza o botão de entrega, então o botão está habilitado e nenhum badge de inadimplência é exibido.
- **Depende de:** T01
- **Premissas pendentes:** —
- **Tech Spec:** pendente
- **Notas de negociação:** Adiada para Fase 2 — o represamento urgente descrito na SPEC é de inadimplentes com 30+ dias acumulados, não de alunos que regularizaram a situação.

---

## 🔗 Dependências externas

- [ ] **Validação de negócio: comportamento para aluno inadimplente ≥ 30 dias sem entrega prévia** — bloqueia T01 (e por consequência T02, T03)
- [ ] **Definição do canal de notificação** (e-mail, push, in-app, webhook para financeiro/coordenador) — bloqueia T03

---

## 🚫 Fora deste plano

Itens da SPEC conscientemente adiados ou excluídos:

- **T04 (liberação imediata ao quitar)** → Fase 2. O path urgente é o de inadimplentes acumulados com 30+ dias, não o de quem regularizou.
- **Notificação ao próprio aluno** → fora de escopo da SPEC.
- **Automação de cobrança / recuperação de dívida** → fora de escopo da SPEC.
- **Relatórios ou dashboards** → fora de escopo da SPEC.
- **Remoção do fluxo de desbloqueio manual existente** → fora de escopo da SPEC; fluxo manual permanece em paralelo.
- **Bloqueios por motivos não financeiros** → fora de escopo da SPEC.

---

## ✅ Pronto para Tech Spec?

- [x] Toda task cita pelo menos uma história/comportamento da SPEC
- [x] Toda task cabe em uma Tech Spec (nenhuma "mega-task")
- [x] Dependências estão explícitas e sem ciclo
- [x] MVP identificado e fecha o resultado-âncora
- [x] Zero detalhes técnicos de implementação (stack, schema, endpoint, infra)
- [x] Fora de Escopo da SPEC respeitado
- [x] Dependências externas bloqueadoras listadas
