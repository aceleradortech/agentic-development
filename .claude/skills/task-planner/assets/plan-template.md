# Plano: [Nome da funcionalidade]

> SPEC de referência: [`specs/<slug>.md`](../specs/<slug>.md)
> Este plano quebra a SPEC em **tasks independentes**, cada uma pronta para virar uma Tech Spec dedicada.
> Aqui **não** há stack, schema, arquitetura ou estimativa — apenas comportamento e sequência.

---

## 🎯 Resumo do escopo

- **Resultado-âncora** (da SPEC): [métrica ou mudança-chave]
- **MVP deste plano:** [quais tasks fecham o resultado-âncora]
- **Fases posteriores:** [o que foi adiado propositalmente]

---

## 🗺️ Mapa de tasks

| # | Task | Cobre (SPEC) | Depende de | Fase | Status |
|---|------|--------------|------------|------|--------|
| T01 | [Nome] | História 1 | — | MVP | pendente |
| T02 | [Nome] | História 2 | T01 | MVP | pendente |
| T03 | [Nome] | História 3 | — | MVP | pendente |
| T04 | [Nome] | História 4 | T01 | Fase 2 | pendente |

> Ordem sugerida: **T01 → T03 (paralelo com T01) → T02 → T04**

---

## 📋 Detalhamento por task

### T01 — [Nome da task]

- **Comportamento entregue:** (uma frase, do ponto de vista do usuário)
- **Histórias/comportamentos cobertos na SPEC:** H1, C2
- **Critérios de aceite** (referenciar/copiar da SPEC):
  - Dado [contexto], quando [ação], então [resultado]
- **Depende de:** —
- **Premissas pendentes que podem bloquear:** (se houver — ex: doc do parceiro)
- **Tech Spec:** pendente
- **Notas de negociação:** (se algo foi discutido e vale registrar)

### T02 — [Nome da task]

- **Comportamento entregue:** ...
- **Histórias/comportamentos cobertos na SPEC:** ...
- **Critérios de aceite:**
  - Dado ... quando ... então ...
- **Depende de:** T01
- **Premissas pendentes:** —
- **Tech Spec:** pendente

*(... repetir para cada task)*

---

## 🔗 Dependências externas

Itens de fora do repo que podem bloquear uma ou mais tasks:

- [ ] Documentação do parceiro X (Swagger / manual) — bloqueia T0?
- [ ] Aprovação jurídica de Y — bloqueia T0?
- [ ] Acesso ao ambiente Z — bloqueia T0?

---

## 🚫 Fora deste plano

Itens da SPEC **conscientemente adiados** para fase 2+:

- [funcionalidade A] — motivo: [não crítica para o resultado-âncora]
- [funcionalidade B] — motivo: [depende de premissa ainda não resolvida]

---

## ✅ Pronto para Tech Spec?

- [ ] Toda task cita pelo menos uma história/comportamento da SPEC
- [ ] Toda task cabe em uma Tech Spec (nenhuma "mega-task")
- [ ] Dependências estão explícitas e sem ciclo
- [ ] MVP identificado e fecha o resultado-âncora
- [ ] Zero detalhes técnicos de implementação (stack, schema, endpoint, infra)
- [ ] Fora de Escopo da SPEC respeitado
- [ ] Dependências externas bloqueadoras listadas
