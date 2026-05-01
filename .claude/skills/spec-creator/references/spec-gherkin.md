# SPEC: tom de stakeholder e uso de Gherkin

Toda SPEC é escrita em **português do Brasil (pt-BR)**. Termos técnicos consagrados podem permanecer em inglês quando naturais.

## Linguagem orientada a stakeholder

- Abra com **problema e valor** em linguagem simples. Evite codinomes internos nas primeiras seções, a menos que o público seja exclusivamente de engenharia.
- Decisões **técnicas** vivem na Tech Spec de cada task — a SPEC pode apenas referenciá-las ("ver Tech Spec para detalhes de API").
- Use **substantivos consistentes** para features, métricas e fluxos em todos os documentos do bundle (mesmos nomes que aparecem em README e resumos).

## Quando usar Gherkin

Use **Dado / Quando / Então** para:

- Critérios de aceite de uma **feature** ou mudança de comportamento
- Reprodução de **bug** e comportamento esperado após a correção
- Paridade ou cutover de **migração** quando cenários tornam o resultado mais claro

Pule Gherkin quando bullets de requisitos ficarem mais legíveis (investigações, avaliações simples).

## Formato

```gherkin
Cenário: Título curto em pt-BR
  Dado ...
  Quando ...
  Então ...
```

Agrupe cenários relacionados sob um título de **Funcionalidade** em prosa quando ajudar. Mantenha cenários **testáveis** e **atômicos**.

## Anti-padrões

- Duplicar esquemas técnicos completos dentro de cada cenário
- Passos vagos tipo "Então o sistema funciona" — nomeie sempre o **resultado observável**
- Misturar idiomas no corpo do Gherkin — mantenha o cenário em pt-BR
