# Plano: AI Companion de Recomendação de Pizza

> SPEC de referência: [`specs/ai-companion-recomendacao-pizza-teste.md`](../specs/ai-companion-recomendacao-pizza-teste.md)
> Este plano quebra a SPEC em **tasks independentes**, cada uma pronta para virar uma Tech Spec dedicada.
> Aqui **não** há stack, schema, arquitetura ou estimativa — apenas comportamento e sequência.

---

## 🎯 Resumo do escopo

- **Resultado-âncora** (da SPEC): taxa de recompra em 30 dias entre clientes que interagiram com o companion, comparada ao baseline de clientes que pediram direto pelo sistema sem companion.
- **MVP deste plano:** **T01 + T02 + T03 + T05** — cliente acessa a landing, conversa com o companion, recebe recomendação baseada no cardápio real e sai com um resumo textual para concluir o pedido no sistema existente.
- **Fases posteriores:** **T04** (respeito a restrições alimentares) e **T06** (atribuição da sessão do companion ao pedido futuro para destravar a métrica-âncora de recompra).

> ⚠️ **Risco consciente no MVP:** adiar T04 e T06 foi decisão explícita. T04 deixa um flanco aberto para risco de saúde/perda permanente de cliente caso o companion sugira algo incompatível com restrição. T06 deixa o lançamento sem prova quantitativa do resultado-âncora (recompra em 30 dias). Ambos precisam subir para fase 2 antes que o experimento seja avaliado como sucesso/fracasso.

---

## 🗺️ Mapa de tasks

| #   | Task                                                         | Cobre (SPEC)                                                                            | Depende de | Fase   | Status   |
| --- | ------------------------------------------------------------ | --------------------------------------------------------------------------------------- | ---------- | ------ | -------- |
| T01 | Landing "Ajude-me a escolher" com entrada de conversa        | Experiência/jornada 1–2, estados vazio/carregando/erro de sistema, acessibilidade mínima | —          | MVP    | pendente |
| T02 | Companion recomenda a partir de contexto (linguagem de garçom) | Comportamento "Recomendação contextual" (até 3 perguntas + justificativa + "por que essa?") | T01        | MVP    | pendente |
| T03 | Cardápio real como fonte de verdade (proteção contra alucinação) | Comportamento "Proteção contra alucinação" + premissa "Fonte de verdade do cardápio" | T01        | MVP    | pendente |
| T05 | Handoff com resumo em texto para o sistema de pedidos        | Comportamento "Handoff para sistema de pedidos"                                         | T02        | MVP    | pendente |
| T04 | Respeito a restrições alimentares durante a sessão           | Comportamento "Respeito a restrições declaradas" + estado "sem match"                   | T02, T03   | Fase 2 | pendente |
| T06 | Atribuição da sessão do companion ao pedido (métrica-âncora) | Premissa pendente "Atribuição da recompra"                                              | T05        | Fase 2 | pendente |

> Ordem sugerida do MVP: **T01 → T02 + T03 (paralelo) → T05**.
> Fase 2: T04 entra assim que T02 e T03 estiverem entregues; T06 entra após T05.

---

## 📋 Detalhamento por task

### T01 — Landing "Ajude-me a escolher" com entrada de conversa

- **Comportamento entregue:** cliente acessa uma página dedicada, é recebido pelo companion com tom descontraído e consegue iniciar uma conversa com feedback claro de carregamento e estados de erro.
- **Histórias/comportamentos cobertos na SPEC:**
  - Jornada principal, passos 1 e 2 ("cliente acessa a página Ajude-me a escolher" + "companion cumprimenta com tom descontraído e convida a conversar")
  - Estados da interface: **Vazio / primeira visita**, **Carregando resposta**, **Erro de sistema (cardápio indisponível)**
  - Acessibilidade (mínimo)
- **Critérios de aceite** (referenciando a SPEC):
  - Dado que o cliente acessa a página "Ajude-me a escolher" pela primeira vez, quando a tela carrega, então ele vê uma saudação amigável com convite a conversar e exemplos curtos de como começar.
  - Dado que o cliente enviou uma mensagem, quando o companion está formulando a resposta, então há um indicador claro de que ele está "pensando" (sem silêncio).
  - Dado que o sistema de cardápio está indisponível, quando o cliente tenta conversar, então a interface mostra mensagem clara e convida a tentar depois ou falar com atendente.
  - Dado que o cliente usa apenas teclado ou leitor de tela, quando navega o chat, então consegue interagir com todas as ações e as mensagens do bot têm marcação semântica adequada.
- **Depende de:** —
- **Premissas pendentes que podem bloquear:** —
- **Tech Spec:** pendente
- **Notas de negociação:** decisão explícita de manter T01 separada de T02 para permitir validar a UX/shell antes de plugar o motor de recomendação.

---

### T02 — Companion recomenda a partir de contexto (linguagem de garçom)

- **Comportamento entregue:** o cliente descreve o contexto da refeição em uma conversa curta, o companion faz no máximo 3 perguntas e entrega uma recomendação com justificativa em tom de garçom experiente, inclusive respondendo "por que essa?" com atributos reais do item.
- **Histórias/comportamentos cobertos na SPEC:**
  - História do fluxo feliz ("descrever meu contexto e receber sugestão pronta")
  - Comportamento esperado: **Recomendação contextual (linguagem de garçom)** — todos os 3 critérios
  - Jornada principal, passos 3–5
  - Princípio inegociável: "Máximo 3 perguntas antes da primeira recomendação" + "tom descontraído, amigo da pizzaria"
  - Estado da interface: **Sucesso (recomendação)**
- **Critérios de aceite:**
  - Dado que o cliente abre a página "Ajude-me a escolher", quando inicia a conversa, então o companion se apresenta com tom descontraído e faz no máximo 3 perguntas antes de sugerir algo.
  - Dado que o cliente respondeu às perguntas de contexto, quando o companion recomenda, então a sugestão vem com uma justificativa em linguagem de garçom (ex: "essa é nossa mais pedida na sexta à noite", "pra quem gosta de X costuma pedir Y").
  - Dado que o companion recomendou, quando o cliente pergunta "por que essa?", então a explicação cita atributos reais do item (sabor, tamanho, ocasião).
  - Dado que o cliente pede outra sugestão ou faz perguntas extras, quando o companion responde, então mantém o tom amigável e coerente com o contexto já declarado na sessão.
- **Depende de:** T01
- **Premissas pendentes que podem bloquear:** guia de tom de voz / material de marca ("descontraído, amigo da pizzaria") — a SPEC registra que precisa ser calibrado antes da Tech Spec.
- **Tech Spec:** pendente
- **Notas de negociação:** —

---

### T03 — Cardápio real como fonte de verdade (proteção contra alucinação)

- **Comportamento entregue:** toda sugestão, preço ou promoção mencionada pelo companion corresponde ao cardápio real da loja no momento da conversa; quando o cliente pede algo que não existe, o companion é honesto e propõe alternativa real.
- **Histórias/comportamentos cobertos na SPEC:**
  - Comportamento esperado: **Proteção contra alucinação** — todos os 3 critérios
  - Premissa de negócio: **Fonte de verdade do cardápio** (vem do sistema de pedidos online existente)
  - Princípio inegociável: "Honestidade acima de venda: se não tem, não inventa"
- **Critérios de aceite:**
  - Dado que o companion apresenta qualquer sugestão, quando o cliente lê, então o item citado existe no cardápio da loja naquele momento.
  - Dado que o cliente pede algo que não está no cardápio ("tem pizza de abacaxi?"), quando o companion responde, então ele informa honestamente que não há e sugere uma alternativa real em seguida.
  - Dado que o cliente pergunta preço ou promoção, quando o companion responde, então apenas informa o que consta no cardápio atual — nunca cria promoção, desconto ou combo inexistente.
- **Depende de:** T01
- **Premissas pendentes que podem bloquear:** acesso/contrato para consumir o cardápio do sistema de pedidos existente como fonte de verdade. A SPEC define **que exista** essa sincronização como requisito de negócio; o **como** é decisão da Tech Spec, mas sem acesso autorizado a task trava.
- **Tech Spec:** pendente
- **Notas de negociação:** pode ser desenvolvida em paralelo com T02 — T03 trata de disciplina factual, T02 trata de qualidade conversacional; juntas entregam o companion útil e confiável.

---

### T05 — Handoff com resumo em texto para o sistema de pedidos

- **Comportamento entregue:** quando o cliente decide, o companion encerra a conversa entregando um resumo textual claro (sabor(es), tamanho, observações) para o cliente levar ao sistema de pedidos existente, sem prometer prazo, preço final ou acompanhamento.
- **Histórias/comportamentos cobertos na SPEC:**
  - Comportamento esperado: **Handoff para sistema de pedidos** — ambos os critérios
  - Jornada principal, passo 6
  - Princípio inegociável: "Zero promessa de prazo de entrega"
  - Premissa de negócio: "Sem coleta de dados sensíveis"
- **Critérios de aceite:**
  - Dado que o cliente aceita a recomendação, quando confirma a escolha, então o companion apresenta um resumo em texto com sabor(es), tamanho e observações — informação suficiente para o cliente ir ao sistema e montar o pedido.
  - Dado que o cliente foi direcionado ao sistema de pedidos, quando a conversa termina, então o companion **não** promete prazo de entrega, **não** calcula preço final com taxas, **não** acompanha status.
  - Dado que o cliente está na conversa, quando o companion encerra, então ele não solicita CPF, e-mail, endereço ou dados de pagamento.
- **Depende de:** T02
- **Premissas pendentes que podem bloquear:** —
- **Tech Spec:** pendente
- **Notas de negociação:** o handoff é por texto (fora de escopo: pré-montagem de carrinho via link).

---

### T04 — Respeito a restrições alimentares durante a sessão

- **Comportamento entregue:** quando o cliente declara uma restrição alimentar na conversa, todas as sugestões seguintes respeitam a restrição; se o cardápio não tiver nenhuma opção compatível, o companion é honesto ao invés de forçar sugestão inadequada.
- **Histórias/comportamentos cobertos na SPEC:**
  - Comportamento esperado: **Respeito a restrições declaradas** — ambos os critérios
  - Estado da interface: **Sem match (restrição impossível de atender)**
- **Critérios de aceite:**
  - Dado que o cliente declara uma restrição na conversa ("sou vegetariano", "sem lactose"), quando o companion recomenda daquele ponto em diante, então todas as opções apresentadas respeitam a restrição.
  - Dado que o cardápio não tem nenhuma opção compatível com a restrição, quando o companion percebe isso, então avisa honestamente ao cliente ao invés de forçar uma sugestão inadequada.
- **Depende de:** T02, T03
- **Premissas pendentes que podem bloquear:** cardápio precisa expor atributos suficientes (ex: indicadores de vegetariano, sem lactose, sem glúten) para que a restrição seja aplicável de forma factual. A Tech Spec decide **como**; o **que** é requisito para a task fazer sentido.
- **Tech Spec:** pendente
- **Notas de negociação:** adiada para fase 2 por decisão explícita de corte de MVP. Registrado como risco no resumo do escopo — o MVP sobe ao ar sem essa proteção.

---

### T06 — Atribuição da sessão do companion ao pedido (destrava métrica-âncora)

- **Comportamento entregue:** o companion produz um sinal de atribuição no handoff que permite reconhecer, no sistema de pedidos existente, que um pedido veio de uma sessão do companion — tornando mensurável a recompra em 30 dias.
- **Histórias/comportamentos cobertos na SPEC:**
  - **Premissa pendente:** "Atribuição da recompra" (explícita na SPEC como questão aberta de Tech Spec, bloqueadora da métrica-âncora)
  - Suporte ao **Resultado esperado** (medir recompra em 30 dias)
- **Critérios de aceite:**
  - Dado que o cliente encerra a conversa no companion, quando recebe o resumo de handoff, então a sessão gera um identificador de atribuição entregável ao cliente junto ao handoff textual (forma exata — código de referência, cookie de sessão, outra — é decisão da Tech Spec).
  - Dado que o cliente conclui um pedido no sistema de pedidos existente dentro de uma janela definida, quando o time de análise consulta os dados, então é possível identificar que aquele pedido veio de uma sessão do companion.
  - Dado que a atribuição é produzida, quando aplicada, então respeita a premissa "sem coleta de dados sensíveis" (nada de CPF, e-mail, endereço, cartão).
- **Depende de:** T05
- **Premissas pendentes que podem bloquear:** definição conjunta com o time do sistema de pedidos existente sobre como reconhecer a atribuição no lado deles (campo livre no pedido, cookie compartilhado, parâmetro em URL de referência, etc.). Sem esse acordo, a task não sobe.
- **Tech Spec:** pendente
- **Notas de negociação:** adiada para fase 2 por decisão explícita. Registrado como risco: sem T06, o lançamento do MVP não consegue provar quantitativamente o resultado-âncora de recompra em 30 dias.

---

## 🔗 Dependências externas

Itens de fora do repo que podem bloquear uma ou mais tasks:

- [ ] **Acesso/contrato à API ou fonte do cardápio do sistema de pedidos existente** — bloqueia T03 (e, em cascata, T02 e T05 dependem de T03 ter entregue cardápio real).
- [ ] **Guia de tom de voz / material de marca ("descontraído, amigo da pizzaria")** — bloqueia T02 (a SPEC registra explicitamente que precisa ser fornecido antes da Tech Spec).
- [ ] **Acordo com o time do sistema de pedidos existente sobre mecânica de atribuição** — bloqueia T06 (fase 2).
- [ ] **Atributos de restrição alimentar disponíveis no cardápio** (vegetariano, sem lactose, sem glúten, etc.) — bloqueia T04 (fase 2).

---

## 🚫 Fora deste plano

### Adiados para fase 2 (conscientemente cortados do MVP)

- **T04 — Respeito a restrições alimentares** — motivo: decisão explícita de corte. Risco registrado (saúde/perda permanente de cliente).
- **T06 — Atribuição da sessão ao pedido** — motivo: decisão explícita de corte. Risco registrado (lançamento sem prova quantitativa do resultado-âncora).

### Fora de escopo permanente (vindo da SPEC)

- Companion para outras personas (atendente, operador, gerente, dono).
- Fechamento do pedido dentro da conversa (pagamento, carrinho, endereço).
- Pré-montagem de carrinho via link.
- Memória persistente, login, histórico do cliente.
- Pós-venda proativo.
- WhatsApp e chat dentro do sistema (canal MVP é landing dedicada).
- Tratamento de reclamações, status de entrega, prazo, acompanhamento de pedido.
- Recomendação baseada em histórico individual do cliente.
- Transferência para humano.

---

## ✅ Pronto para Tech Spec?

- [x] Toda task cita pelo menos uma história/comportamento da SPEC
- [x] Toda task cabe em uma Tech Spec (nenhuma "mega-task")
- [x] Dependências estão explícitas e sem ciclo
- [x] MVP identificado e fecha o resultado-âncora (com risco de T06 registrado)
- [x] Zero detalhes técnicos de implementação (stack, schema, endpoint, infra)
- [x] Fora de Escopo da SPEC respeitado
- [x] Dependências externas bloqueadoras listadas
