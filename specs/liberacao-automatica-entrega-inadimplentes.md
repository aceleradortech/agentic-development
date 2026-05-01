# SPEC: Liberação Automática de Entrega para Alunos Inadimplentes

> Uma SPEC descreve **o problema, quem sofre com ele, e como saberemos que foi resolvido**.
> Ela não descreve stack, arquitetura ou APIs — isso vive na Tech Spec, depois.

---

## 🎯 Problema

- **Qual dor existe hoje?** O botão de entrega é bloqueado para alunos inadimplentes, exigindo que o embaixador de estoque solicite um desbloqueio manual a cada caso — criando gargalo operacional e atraso nas entregas.
- **Quem sente essa dor?** O embaixador de estoque, responsável por realizar as entregas físicas de materiais/kits aos alunos.
- **Como essa dor se manifesta?** O embaixador encontra o botão desabilitado, precisa acionar outra pessoa para desbloqueá-lo e aguardar — mesmo quando a regra de negócio já permitiria a entrega.
- **Por que resolver agora?** Urgente — há alunos inadimplentes acumulados aguardando entregas. O processo manual é o único caminho hoje e está gerando represamento.

---

## 🏁 Resultado Esperado

- **Mudança no comportamento do usuário:** O embaixador realiza a entrega para alunos inadimplentes (após 30 dias de matrícula) sem precisar solicitar desbloqueio manual.
- **Mudança no negócio:** Redução do volume de desbloqueios manuais solicitados por semana.
- **Métrica-âncora:** Número de desbloqueios manuais por semana — esperamos que chegue a zero para o caso coberto por esta feature (inadimplente com ≥ 30 dias de matrícula).

---

## 👤 Histórias de Usuário

**Como** embaixador de estoque
**Quero** que o botão de entrega seja liberado automaticamente após 30 dias da matrícula do aluno inadimplente
**Para** realizar a entrega sem depender de desbloqueio manual

**Como** embaixador de estoque
**Quero** visualizar que um aluno ainda está inadimplente mesmo quando o botão está liberado
**Para** ter ciência da situação financeira sem que isso impeça a entrega

**Como** área financeira / coordenador da unidade
**Quero** ser notificado quando uma entrega for realizada para um aluno inadimplente
**Para** tomar as ações de cobrança e acompanhamento cabíveis

---

## 🧩 Comportamentos Esperados

### 1. Verificação automática da elegibilidade para entrega

- **O que permite:** O sistema avalia, no momento em que o embaixador acessa o aluno, se o botão de entrega deve estar ativo ou bloqueado com base no status de inadimplência e no tempo de matrícula.
- **Por que importa:** Elimina a necessidade de intervenção humana para avaliar o prazo.

**Critérios de aceite:**

```gherkin
Cenário: Aluno inadimplente com menos de 30 dias de matrícula
  Dado que o embaixador acessa o perfil de um aluno inadimplente
  E a matrícula do aluno tem menos de 30 dias
  Quando o embaixador visualiza o botão de entrega
  Então o botão está desabilitado

Cenário: Aluno inadimplente com 30 ou mais dias de matrícula
  Dado que o embaixador acessa o perfil de um aluno inadimplente
  E a matrícula do aluno tem 30 dias ou mais
  Quando o embaixador visualiza o botão de entrega
  Então o botão está habilitado
  E um badge/tag de "inadimplente" é exibido visivelmente ao lado do botão
```

---

### 2. Liberação imediata ao regularizar a situação financeira

- **O que permite:** Quando o aluno quita a inadimplência, o botão é desbloqueado imediatamente — sem necessidade de aguardar os 30 dias.
- **Por que importa:** Incentiva o pagamento e não penaliza o aluno que regularizou.

**Critérios de aceite:**

```gherkin
Cenário: Aluno inadimplente que quita a dívida antes dos 30 dias
  Dado que o embaixador acessa o perfil de um aluno inadimplente
  E a matrícula do aluno tem menos de 30 dias
  E o aluno quitou todas as parcelas em atraso
  Quando o embaixador visualiza o botão de entrega
  Então o botão está habilitado
  E nenhum badge de inadimplência é exibido
```

---

### 3. Liberação restrita à próxima entrega do ciclo

- **O que permite:** A liberação automática cobre apenas a **próxima entrega pendente do ciclo atual** — não todas as entregas futuras.
- **Por que importa:** Evita liberar indefinidamente um aluno que continua inadimplente sem revisão.

**Critérios de aceite:**

```gherkin
Cenário: Embaixador realiza a entrega liberada automaticamente
  Dado que o botão de entrega está habilitado por regra automática (30+ dias, inadimplente)
  Quando o embaixador confirma a entrega da próxima unidade do ciclo
  Então a entrega é registrada com sucesso
  E o botão retorna ao estado bloqueado para entregas subsequentes do mesmo ciclo (se ainda inadimplente)
```

---

### 4. Notificação para área financeira e coordenador da unidade

- **O que permite:** A cada entrega realizada para um aluno inadimplente (via liberação automática), área financeira e coordenador da unidade recebem uma notificação.
- **Por que importa:** Mantém as partes responsáveis informadas para ação de cobrança e acompanhamento.

**Critérios de aceite:**

```gherkin
Cenário: Entrega realizada para aluno inadimplente com botão liberado automaticamente
  Dado que o embaixador realizou uma entrega para um aluno que estava inadimplente no momento
  Quando a entrega é confirmada com sucesso
  Então a área financeira recebe uma notificação com o nome do aluno e data da entrega
  E o coordenador da unidade do aluno também recebe a mesma notificação
```

---

## ✨ Experiência

**Jornada principal:**
1. Embaixador abre o perfil ou fila de entregas do aluno inadimplente
2. O sistema verifica: inadimplente? → sim. Dias desde a matrícula ≥ 30? → sim
3. Botão de entrega exibido como **ativo**, com badge visual "Inadimplente"
4. Embaixador realiza a entrega normalmente
5. Sistema registra e dispara notificação para financeiro e coordenador

**Estados da interface:**
| Estado | Situação | Botão | Indicador |
|---|---|---|---|
| Bloqueado | Inadimplente, < 30 dias de matrícula | Desabilitado | Mensagem de prazo |
| Liberado automático | Inadimplente, ≥ 30 dias | Habilitado | Badge "Inadimplente" |
| Normal | Adimplente (ou regularizou) | Habilitado | Sem badge |

**Princípios inegociáveis:**
- O badge de inadimplência nunca pode ser ignorado visualmente — é informação crítica para o embaixador
- A notificação deve ser disparada no momento da confirmação da entrega, não em batch

**Acessibilidade:**
- O badge de inadimplência deve comunicar o estado apenas por cor e não precisa de texto ou ícone legível

---

## 🧭 Premissas e Restrições de Negócio

- **Inadimplência** é definida pelo sistema financeiro como: aluno com pelo menos uma parcela vencida e não paga.
- O prazo de **30 dias é contado a partir da data de matrícula** (não da primeira entrega, conforme alinhamento durante a SPEC).
- A liberação cobre **apenas a próxima entrega do ciclo atual** — não entregas subsequentes nem outros ciclos.
- O fluxo de **desbloqueio manual pelo gestor continua existindo** em paralelo e não é removido por esta feature.
- **Premissa pendente:** Comportamento não definido para aluno com ≥ 30 dias de matrícula e **nenhuma entrega registrada** anteriormente. Isso deve ser validado com a área de negócio antes do início da Tech Spec.
- **Urgência:** Há casos acumulados aguardando entrega — prioridade alta no backlog.

---

## 🚫 Fora de Escopo

- Regras de bloqueio por motivos não financeiros (disciplinar, documental, etc.) — não são afetadas por esta feature
- Notificação ao próprio aluno inadimplente sobre a liberação ou realização da entrega
- Qualquer automação no fluxo de cobrança ou recuperação de dívida do financeiro
- Remoção do fluxo de desbloqueio manual existente — ele permanece como está
- Relatórios ou dashboards sobre entregas para inadimplentes

---

## ✅ Pronto para virar plano?

- [x] Problema descrito com persona e dor concreta
- [x] Métrica-âncora definida
- [x] Toda história tem critérios de aceite observáveis
- [x] Estados de erro e borda mapeados
- [ ] **Premissa pendente:** comportamento para aluno sem entrega prévia após 30 dias — validar antes de iniciar a Tech Spec
- [x] Fora de escopo explícito
- [x] Um engenheiro consegue começar a Tech Spec sem perguntar "o que é isso?"
