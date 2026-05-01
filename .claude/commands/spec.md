---
description: Cria uma SPEC de produto a partir de um briefing, via conversa guiada — sem detalhes técnicos de implementação.
argument-hint: "[caminho-do-briefing.md | ou cole o briefing diretamente]"
---

Você foi invocado via `/spec`. Inicie o fluxo de criação de SPEC.

**Argumento recebido:** $ARGUMENTS

## O que fazer

1. **Invoque imediatamente a skill `spec-creator`** via a tool `Skill`. Essa skill contém o protocolo completo (diagnóstico do briefing, rodadas de perguntas em lote via `AskUserQuestion`, escrita final em `specs/<slug>.md`).

2. **Entrada para a skill:**
   - Se `$ARGUMENTS` for um caminho de arquivo existente → leia o arquivo e trate o conteúdo como briefing.
   - Se `$ARGUMENTS` for texto cru → trate-o diretamente como briefing.
   - Se `$ARGUMENTS` estiver vazio → pergunte ao usuário onde está o briefing antes de qualquer outra coisa. **Não prossiga sem briefing.**

3. **Não escreva a SPEC diretamente aqui.** O comando `/spec` é apenas o gatilho — o trabalho real acontece dentro da skill `spec-creator`, que sabe como conduzir as rodadas de perguntas e preencher o template em `skills/spec-creator/assets/spec-template.md`.

## Lembretes rígidos

- **Resposta ao usuário sempre em pt-BR.**
- **Nunca desça a detalhes técnicos de implementação** (stack, schema, infra, APIs). Isso é responsabilidade da Tech Spec de cada task, em etapa posterior.
- **Não pule o diagnóstico inicial.** Mesmo com briefing completo, apresente ao usuário o mapeamento de seções ✅/⚠️/❌ antes de começar as perguntas.
- **Fim de jogo:** SPEC gravada em `specs/<slug>.md` + resumo curto + próximo passo sugerido (`/plan`).
