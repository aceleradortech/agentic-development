# SPEC: AI Companion de Recomendação de Pizza

> Um consultor conversacional que ajuda o cliente indeciso a escolher o que pedir,
> com o papel de "garçom experiente da pizzaria". Não fecha o pedido — direciona para
> o sistema de pedidos online já existente.

---

## 🎯 Problema

- **Dor:** clientes indecisos diante do cardápio abandonam a jornada antes de pedir. A fricção não está no checkout — está na **escolha**. Muita opção, pouca confiança sobre o que vale a pena.
- **Persona:** cliente final da pizzaria navegando o cardápio online (não identificado, sem login).
- **Manifestação:** cliente entra, olha sabores, fica em dúvida, sai sem pedir — ou pede o mesmo de sempre sem explorar. Quem pediu algo ruim de primeira raramente volta.
- **Por que agora:** aposta estratégica. Sem incêndio de custo, queremos testar se IA move o ponteiro em **retenção** antes que a concorrência normalize esse tipo de experiência.

---

## 🏁 Resultado Esperado

Como saberemos que funcionou:

- **Mudança no comportamento do cliente:** cliente indeciso obtém recomendação confiável em poucos segundos e pede com confiança, sem perder tempo navegando cardápio.
- **Mudança no negócio:** cliente que usou o companion volta a pedir no mês seguinte — a recomendação foi boa o suficiente para gerar nova visita.
- **Métrica-âncora:** **taxa de recompra em 30 dias** entre clientes que interagiram com o companion, comparada ao baseline de clientes que pediram direto pelo sistema sem companion.

> ⚠️ **Premissa pendente:** como o companion é anônimo (sem login), ligar a sessão de conversa ao pedido que será feito depois no sistema existente é uma questão de atribuição. A forma de medir isso será decidida na Tech Spec (ex: código de referência no handoff, cookie de sessão, nada disso). Sem definir isso, a métrica fica insuficiente.

---

## 👤 Histórias de Usuário

### Fluxo feliz

**Como** cliente indeciso olhando o cardápio num sábado à noite
**Quero** descrever meu contexto ("somos 4 pessoas, uma não come carne") e receber uma sugestão pronta
**Para** fechar a escolha rápido e pedir com confiança de que vou gostar

### Casos de borda

**Como** cliente com restrição alimentar ("sou intolerante à lactose")
**Quero** que o companion só sugira coisas que respeitem isso
**Para** não correr risco de errar e perder a confiança na pizzaria

**Como** cliente que pergunta algo que não existe no cardápio ("vocês têm pizza de abacaxi?")
**Quero** receber uma resposta honesta
**Para** não me sentir enganado e considerar uma alternativa real

### Falhas esperadas

**Como** cliente que não consegue decidir mesmo depois de conversar
**Quero** que o companion me ofereça uma recomendação única e clara em algum momento
**Para** não ficar num loop infinito de perguntas

**Como** cliente com reclamação, dúvida complexa ou pedido especial
**Quero** conseguir falar com um humano
**Para** resolver o que o companion não resolve

---

## 🧩 Comportamentos Esperados

### Recomendação contextual (linguagem de garçom)
- **O que permite:** cliente descreve o contexto da refeição em uma conversa curta e recebe uma sugestão com justificativa em tom de garçom experiente.
- **Por que importa:** transforma indecisão em confiança. Uma boa primeira recomendação é o que gera a recompra.
- **Critérios de aceite:**
  - **Dado** que o cliente abre a página "Ajude-me a escolher", **quando** inicia a conversa, **então** o companion se apresenta com tom descontraído e faz no **máximo 3 perguntas** antes de sugerir algo.
  - **Dado** que o cliente respondeu às perguntas de contexto, **quando** o companion recomenda, **então** a sugestão vem com uma justificativa em linguagem de garçom (ex: "essa é nossa mais pedida na sexta à noite", "pra quem gosta de X costuma pedir Y").
  - **Dado** que o companion recomendou, **quando** o cliente pergunta "por que essa?", **então** a explicação cita atributos reais do item (sabor, tamanho, ocasião).

### Proteção contra alucinação
- **O que permite:** companion jamais sugere item, preço ou promoção que não exista no cardápio real da loja.
- **Por que importa:** recomendação que não existe quebra confiança permanentemente. Custa o cliente e custa recompra — justamente a métrica-âncora.
- **Critérios de aceite:**
  - **Dado** que o companion apresenta qualquer sugestão, **quando** o cliente lê, **então** o item citado existe no cardápio da loja naquele momento.
  - **Dado** que o cliente pede algo que não está no cardápio ("tem pizza de abacaxi?"), **quando** o companion responde, **então** ele informa honestamente que não há e sugere uma alternativa **real** em seguida.
  - **Dado** que o cliente pergunta preço ou promoção, **quando** o companion responde, **então** apenas informa o que consta no cardápio atual — **nunca** cria promoção, desconto ou combo inexistente.

### Respeito a restrições declaradas
- **O que permite:** cliente declara restrição alimentar e o companion nunca sugere algo incompatível durante aquela sessão.
- **Por que importa:** errar em restrição (lactose, glúten, vegetariano) é perda permanente de cliente — e possível risco à saúde.
- **Critérios de aceite:**
  - **Dado** que o cliente declara uma restrição na conversa ("sou vegetariano", "sem lactose"), **quando** o companion recomenda daquele ponto em diante, **então** todas as opções apresentadas respeitam a restrição.
  - **Dado** que o cardápio não tem nenhuma opção compatível com a restrição, **quando** o companion percebe isso, **então** avisa honestamente ao cliente ao invés de forçar uma sugestão inadequada.

### Handoff para o sistema de pedidos
- **O que permite:** decidida a escolha, o companion entrega um resumo em texto claro para o cliente levar ao sistema de pedidos existente.
- **Por que importa:** MVP foca em **recomendação**, não em fechamento. Pagamento, endereço e checkout continuam no sistema já validado.
- **Critérios de aceite:**
  - **Dado** que o cliente aceita a recomendação, **quando** confirma a escolha, **então** o companion apresenta um **resumo em texto** com sabor(es), tamanho e observações — informação suficiente para o cliente ir ao sistema e montar o pedido.
  - **Dado** que o cliente foi direcionado ao sistema de pedidos, **quando** a conversa termina, **então** o companion **não** promete prazo de entrega, **não** calcula preço final com taxas, **não** acompanha status.

### Escalação para humano
- **O que permite:** em qualquer momento o cliente pode ser encaminhado para atendimento humano.
- **Por que importa:** reclamação, pedido especial ou frustração não pode ficar presa no bot.
- **Critérios de aceite:**
  - **Dado** que o cliente pede explicitamente atendimento humano ("quero falar com alguém", "cadê um atendente?"), **quando** o companion detecta, **então** encaminha imediatamente para o canal humano definido.
  - **Dado** que o cliente insiste em algo que o companion não consegue resolver após tentativas razoáveis, **quando** o companion detecta o loop, **então** oferece proativamente atendimento humano antes de insistir mais.

> Observação: o **modo exato** de escalação (link, transferência de canal, e-mail) fica para a Tech Spec — a SPEC apenas exige que exista saída para humano.

---

## ✨ Experiência

### Jornada principal
1. Cliente acessa a página **"Ajude-me a escolher"** (landing dedicada).
2. Companion cumprimenta com tom descontraído e convida a conversar.
3. Faz até 3 perguntas de contexto (ocasião, número de pessoas, preferências).
4. Apresenta **1-3 opções** com justificativa em linguagem de garçom.
5. Cliente escolhe, faz perguntas extras ou pede outra sugestão.
6. Ao fechar, companion entrega **resumo em texto** e direciona ao sistema de pedidos existente.

### Estados da interface
- **Vazio / primeira visita:** saudação amigável com convite a conversar, exemplos curtos de como começar.
- **Carregando resposta:** indicador claro de que o companion está "pensando" (não segundos em silêncio).
- **Sucesso (recomendação):** resposta com justificativa + próxima ação clara.
- **Sem match (restrição impossível de atender):** mensagem honesta + sugestão de falar com humano.
- **Erro de sistema (cardápio indisponível):** mensagem clara e convite para tentar depois ou falar com atendente.

### Princípios inegociáveis
- Máximo **3 perguntas** antes da primeira recomendação.
- Tom **descontraído, amigo da pizzaria** — não corporativo, não robotizado.
- **Honestidade acima de venda:** se não tem, não inventa.
- **Zero promessa de prazo de entrega** — isso é domínio do sistema de pedidos.

### Acessibilidade (mínimo)
- Chat navegável por teclado.
- Contraste e tamanho de texto legíveis.
- Compatível com leitor de tela (mensagens do bot com marcação semântica adequada).

---

## 🧭 Premissas e Restrições de Negócio

- **Fonte de verdade do cardápio:** precisa vir do sistema de pedidos online existente. Cardápio desatualizado = recomendação ruim = métrica-âncora quebrada. **Como** essa sincronização acontece é decisão da Tech Spec, **que exista** é requisito de negócio.
- **Atribuição da recompra — premissa pendente:** para medir a métrica-âncora, precisamos ligar uma sessão anônima do companion a um pedido futuro no sistema existente. Forma de medir é questão aberta para a Tech Spec (ex: código de referência, cookie, nada). Sem isso, a métrica fica cega.
- **Branding do tom de voz:** "descontraído, amigo da pizzaria" precisa ser calibrado com material de marca da empresa (guia de tom, exemplos) — a fornecer antes da Tech Spec.
- **Sem promessa de prazo:** o companion não fala sobre ETA de entrega em nenhuma hipótese.
- **Sem coleta de dados sensíveis:** MVP é anônimo; companion não pede CPF, e-mail, endereço, cartão.

---

## 🚫 Fora de Escopo

- **Companion para outras personas** (atendente, operador, gerente, dono). Esta SPEC é apenas **cliente final**.
- **Fechamento do pedido dentro da conversa:** pagamento, carrinho integrado, confirmação de endereço — tudo isso continua no sistema de pedidos existente.
- **Pré-montagem de carrinho via link:** MVP é handoff por texto. Envio de link com carrinho pronto fica para fase 2.
- **Memória persistente / login / histórico do cliente:** companion é anônimo por sessão. Nada de "da última vez você pediu X".
- **Pós-venda proativo:** companion não manda mensagem "faz tempo que você não pede". Só reage quando o cliente inicia.
- **WhatsApp e chat dentro do sistema:** canal MVP é landing dedicada. Outros canais ficam para depois.
- **Tratamento de reclamações, status de entrega, prazo, acompanhamento de pedido:** qualquer coisa pós-pedido fica fora — companion só atua no momento da escolha.
- **Recomendação baseada em histórico individual do cliente:** sem identificação, sem histórico; no máximo, contexto agregado da loja (mais pedido na sexta, combos populares).

---

## ✅ Pronto para virar plano?

- [x] Problema descrito com persona e dor concreta
- [x] Métrica-âncora definida (recompra em 30 dias) — **com premissa de atribuição pendente registrada**
- [x] Toda história tem critérios de aceite observáveis (Gherkin)
- [x] Estados de erro e borda mapeados (fora do cardápio, loop, restrição impossível, escalação humana)
- [x] Fora de escopo explícito e rico
- [x] Um engenheiro consegue começar a Tech Spec sem perguntar "o que é isso?"
