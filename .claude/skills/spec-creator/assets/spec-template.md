# SPEC: [Nome da funcionalidade]

> Uma SPEC descreve **o problema, quem sofre com ele, e como saberemos que foi resolvido**.
> Ela não descreve stack, arquitetura ou APIs — isso vive na Tech Spec, depois.
> Se você está pensando em "como implementar", pare. Volte para o "o quê" e o "por quê".

---

## 🎯 Problema

- **Qual dor existe hoje?**
- **Quem sente essa dor?** (persona específica — não "o usuário")
- **Como essa dor se manifesta?** (comportamento atual, workaround, frustração)
- **Por que resolver agora?**

> Se não há dor clara, não há SPEC. Há ideia.

---

## 🏁 Resultado Esperado

Como saberemos que funcionou?

- **Mudança no comportamento do usuário:** [ex: completa a tarefa sem pedir ajuda]
- **Mudança no negócio:** [ex: reduz tickets de suporte em X%]
- **Métrica-âncora:** a UMA coisa que vamos olhar toda semana

> Resultado sem métrica é desejo. Seja específico o suficiente para saber quando parar.

---

## 👤 Histórias de Usuário

**Como** [persona concreta]
**Quero** [ação observável]
**Para** [benefício claro]

Cubra:
- ✅ **Fluxo feliz** — o caminho de 80% dos usuários
- ⚠️ **Casos de borda** — o que pode dar errado na jornada
- 🚫 **Falhas esperadas** — erro de rede, dado inválido, sem permissão

---

## 🧩 Comportamentos Esperados

Para cada funcionalidade, descreva **o que o usuário vê e faz** — não como funciona por dentro:

**[Nome da funcionalidade]**
- **O que ela permite:** uma frase, do ponto de vista do usuário
- **Por que importa:** conecte com o Resultado Esperado
- **Critérios de aceite** (observáveis, testáveis):
  - Dado [contexto], quando [ação], então [resultado visível]
  - ...

> Regra: se um critério não pode ser verificado olhando a tela ou o dado, reescreva.

---

## ✨ Experiência

- **Jornada principal:** passo a passo, do gatilho ao resultado
- **Estados da interface:** vazio, carregando, sucesso, erro, sem permissão
- **Princípios inegociáveis:** [ex: "nunca mais de 2 cliques para a ação principal"]
- **Acessibilidade:** o mínimo que precisa ser respeitado

> Os estados de erro merecem o mesmo cuidado que os de sucesso.

---

## 🧭 Premissas e Restrições de Negócio

Nada de stack aqui. Só o terreno em que o produto precisa se mover:

- **Quem precisa concordar** (jurídico, compliance, parceiros)
- **Regras de negócio não negociáveis** (ex: não pode cobrar antes de confirmar)
- **Prazos ou janelas críticas** (ex: precisa estar pronto antes da Black Friday)

> Decisões técnicas, integrações e performance ficam para a Tech Spec de cada task.

---

## 🚫 Fora de Escopo

Liste o que **não** entra nesta SPEC:

- Funcionalidades que parecem óbvias mas ficam para depois
- Casos de uso de nicho que não justificam o custo agora
- Integrações ou automações "que seriam legais ter"

> Uma boa SPEC tem "Fora de Escopo" tão rica quanto "Comportamentos Esperados".

---

## ✅ Pronto para virar plano?

- [ ] Problema descrito com persona e dor concreta
- [ ] Métrica-âncora definida
- [ ] Toda história tem critérios de aceite observáveis
- [ ] Estados de erro e borda mapeados
- [ ] Fora de escopo explícito
- [ ] Um engenheiro consegue começar a Tech Spec sem perguntar "o que é isso?"