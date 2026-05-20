# SPEC: Pomodoro Foco Dev

> Uma SPEC descreve **o problema, quem sofre com ele, e como saberemos que foi resolvido**.
> Ela não descreve stack, arquitetura ou APIs — isso vive na Tech Spec, depois.

---

## 🎯 Problema

- **Qual dor existe hoje?** Dev/tech workers trabalham horas seguidas sem pausas estruturadas e chegam ao final do dia exaustos, com sensação de pouca entrega apesar do esforço.
- **Quem sente essa dor?** Daniel — desenvolvedor/tech worker que usa o celular como ferramenta de trabalho e nunca adotou o método Pomodoro de forma séria.
- **Como essa dor se manifesta?** Sessões de trabalho longas e contínuas, sem ritmo de pausa, geram fadiga progressiva. Sem um sistema de foco, é difícil medir e melhorar a produtividade real.
- **Por que resolver agora?** É a primeira tentativa séria de adotar a técnica — o app precisa remover toda fricção de adoção e ser simples o suficiente para criar o hábito.

---

## 🏁 Resultado Esperado

Como saberemos que funcionou?

- **Mudança no comportamento do usuário:** Daniel completa sessões de trabalho com pausas intercaladas, chegando ao final do dia com energia e percepção clara de entrega.
- **Mudança no negócio:** maior número de tarefas concluídas percebidas ao longo da semana.
- **Métrica-âncora:** **pomodoros concluídos por dia** — meta inicial de **4 pomodoros (≥ 2h de foco real)** por dia de trabalho.

> Referência de sucesso: após 30 dias, Daniel consegue manter a meta de 4 pomodoros/dia em pelo menos 4 de cada 5 dias úteis.

---

## 👤 Histórias de Usuário

**Como** dev que trabalha no celular entre reuniões e blocos de código,
**Quero** iniciar um timer Pomodoro com um toque,
**Para** não perder mais tempo configurando — o foco começa imediatamente.

**Como** usuário que costuma ignorar pausas,
**Quero** receber notificação sonora e vibração ao fim de cada sessão,
**Para** ser lembrado de parar mesmo quando estou absorto no trabalho.

**Como** usuário em contexto onde não posso pausar agora,
**Quero** poder adiar ou pular a pausa,
**Para** não quebrar meu fluxo quando uma situação urgente aparece.

**Como** dev que quer entender sua produtividade,
**Quero** ver um gráfico semanal de pomodoros concluídos,
**Para** saber se estou melhorando ou regredindo semana a semana.

**Como** usuário distraído que às vezes abandona o timer,
**Quero** que o app registre sessões parciais,
**Para** ter um histórico honesto mesmo nos dias difíceis.

---

## 🧩 Comportamentos Esperados

### Timer Pomodoro

- **O que permite:** iniciar, pausar e cancelar uma sessão de foco de 25 minutos (configurável).
- **Por que importa:** é o núcleo do método — sem timer confiável, não há Pomodoro.
- **Critérios de aceite:**

```gherkin
Cenário: Iniciar sessão de foco
  Dado que estou na tela principal do app
  Quando toco no botão "Iniciar"
  Então o timer começa a contar regressivamente a partir de 25 minutos
  E a tela exibe o tempo restante de forma proeminente

Cenário: Conclusão de sessão
  Dado que o timer está ativo
  Quando os 25 minutos se esgotam
  Então o app emite som de alerta e vibra
  E a sessão é registrada como "concluída" no histórico
  E o app pergunta se quero iniciar a pausa agora ou aguardar

Cenário: Sessão interrompida
  Dado que o timer está ativo
  Quando toco em "Cancelar" antes do fim
  Então o app registra a sessão como "parcial" com o tempo trabalhado
  E retorna à tela principal sem contar o pomodoro como concluído
```

---

### Gestão de Pausas (modo híbrido)

- **O que permite:** o app alterna automaticamente entre sessão e pausa, mas o usuário pode adiar ou pular a pausa.
- **Por que importa:** respeita o contexto real — às vezes a pausa precisa esperar.
- **Critérios de aceite:**

```gherkin
Cenário: Pausa automática ao fim de sessão
  Dado que uma sessão foi concluída
  Quando o usuário toca em "Iniciar pausa"
  Então o timer de pausa (5 min) começa automaticamente
  E a interface indica claramente que é momento de pausa

Cenário: Adiar pausa
  Dado que o app exibe a tela de pausa
  Quando o usuário toca em "Adiar"
  Então o app aguarda sem iniciar o timer
  E exibe opção de iniciar pausa quando o usuário quiser

Cenário: Pular pausa
  Dado que o app exibe a tela de pausa
  Quando o usuário toca em "Pular pausa"
  Então o app retorna à tela principal pronto para nova sessão
  E não penaliza o histórico pela pausa pulada

Cenário: Pausa longa (a cada 4 pomodoros)
  Dado que o usuário completou 4 pomodoros no ciclo atual
  Quando a sessão de trabalho termina
  Então o app sugere pausa longa de 15 minutos (configurável)
  E diferencia visualmente pausa curta de pausa longa
```

---

### Notificações e Sons

- **O que permite:** receber alertas sonoros e vibração ao fim de cada sessão e pausa, mesmo com o app em background.
- **Por que importa:** o usuário trabalha absorto — o alerta é o único mecanismo que garante a pausa.
- **Critérios de aceite:**

```gherkin
Cenário: Notificação com app em background
  Dado que o timer está rodando e o app está em segundo plano
  Quando o tempo se esgota
  Então o dispositivo emite som e vibra
  E aparece notificação no sistema com resumo da sessão

Cenário: Som customizável
  Dado que estou nas configurações
  Quando seleciono um som de alerta
  Então posso ouvir preview e confirmar a escolha
  E nas próximas sessões o alerta usa o som escolhido

Cenário: Modo silencioso
  Dado que ativei "sem som" nas configurações
  Quando uma sessão termina
  Então apenas a vibração é acionada, sem som
```

---

### Histórico e Gráfico Semanal

- **O que permite:** visualizar um gráfico de barras semanal com pomodoros concluídos por dia, comparando com a meta.
- **Por que importa:** conecta diretamente à métrica-âncora — o usuário vê progresso semana a semana.
- **Critérios de aceite:**

```gherkin
Cenário: Visualizar semana atual
  Dado que estou na aba de histórico
  Quando acesso o gráfico semanal
  Então vejo barras para cada dia da semana
  E cada barra indica quantos pomodoros foram concluídos naquele dia
  E uma linha horizontal indica a meta diária (padrão: 4)

Cenário: Sessões parciais no histórico
  Dado que tive sessões interrompidas
  Quando acesso o histórico
  Então sessões parciais aparecem diferenciadas (ex: barra tracejada ou cor diferente)
  E o total de concluídas e parciais fica visível separadamente

Cenário: Sem dados ainda
  Dado que é meu primeiro dia usando o app
  Quando acesso o histórico
  Então vejo o gráfico vazio com mensagem motivadora de início
  E minha meta diária já está configurada (padrão: 4 pomodoros)
```

---

### Configurações

- **O que permite:** personalizar duração das sessões, pausas, sons e meta diária.
- **Por que importa:** o método Pomodoro varia por perfil — a configuração padrão funciona, mas deve ser ajustável.
- **Critérios de aceite:**

```gherkin
Cenário: Alterar duração da sessão
  Dado que estou nas configurações
  Quando altero a duração do pomodoro (mínimo: 5 min, máximo: 60 min)
  Então a próxima sessão usa a nova duração
  E a duração atual fica visível na tela principal

Cenário: Valores padrão ao instalar
  Dado que instalei o app pela primeira vez
  Quando abro o app
  Então as configurações padrão são: sessão 25min, pausa curta 5min, pausa longa 15min, meta 4 pomodoros/dia
```

---

## ✨ Experiência

**Jornada principal:**
1. Usuário abre o app → tela principal mostra timer zerado + contador de pomodoros do dia
2. Toca "Iniciar" → timer começa, tela fica limpa, mostrando só o tempo restante
3. Timer termina → alerta sonoro + vibração → app pergunta "Iniciar pausa ou adiar?"
4. Usuário faz pausa (automática ou manual) → após pausa, volta à tela principal
5. Ao final do dia → acessa histórico → vê gráfico com pomodoros concluídos vs. meta

**Estados da interface:**

| Estado | O que o usuário vê |
|---|---|
| Idle | Timer zerado, botão "Iniciar" em destaque, contador do dia (ex: 0/4) |
| Em sessão | Timer regressivo em destaque, botão "Cancelar" discreto |
| Fim de sessão | Feedback visual + som + opções: "Iniciar pausa" / "Adiar" |
| Em pausa | Timer de pausa, mensagem de incentivo ao descanso, opção "Pular" |
| Histórico | Gráfico de barras semanal, meta diária visível |
| Configurações | Sliders para duração + seletor de som + campo de meta |

**Princípios inegociáveis:**
- Interface minimalista: nunca mais de 2 elementos de ação visíveis ao mesmo tempo
- Sem cadastro obrigatório para começar a usar
- App funcional offline (dados locais no dispositivo)

**Acessibilidade:**
- Tamanhos de toque mínimos de 44pt para todos os controles
- Alto contraste entre timer e fundo
- Feedback tátil (vibração) além do visual e sonoro

---

## 🧭 Premissas e Restrições de Negócio

- O app é pessoal (não há compartilhamento de dados ou modo multi-usuário nesta versão).
- Dados de sessão ficam armazenados localmente no dispositivo — sem backend necessário para V1.
- A adoção da técnica Pomodoro clássica (25/5) é o padrão, mas customizável.
- Não há modelo de monetização definido para esta versão — foco total em adoção e hábito.

---

## 🚫 Fora de Escopo

- **Integração com ferramentas externas** (Jira, Linear, Notion, Todoist) — sincronização de tarefas fica para V2
- **Gerenciamento de tarefas dentro do app** — o app cronometa, não organiza backlog
- **Modo colaborativo ou de time** — pomodoros sincronizados com colegas
- **IA para sugestão de tarefas ou análise de padrões** — insights avançados ficam para V2
- **Widget de tela inicial** — timer acessível sem abrir o app
- **Sincronização em nuvem / multi-device** — V1 usa apenas armazenamento local
- **Relatórios exportáveis** (CSV, PDF) — análise avançada de histórico
- **Gamificação** (conquistas, badges, pontos) — foco na utilidade, não em mecânicas de jogo

---

## ✅ Pronto para virar plano?

- [x] Problema descrito com persona e dor concreta
- [x] Métrica-âncora definida (4 pomodoros/dia)
- [x] Toda história tem critérios de aceite observáveis
- [x] Estados de erro e borda mapeados (sessão parcial, pausa adiada, primeiro uso)
- [x] Fora de escopo explícito
- [x] Um engenheiro consegue começar a Tech Spec sem perguntar "o que é isso?"
