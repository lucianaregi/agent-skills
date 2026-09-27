---
name: dotnet-ai-integration
description: Orienta e revisa integrações com modelos de IA em aplicações .NET usando as abstrações oficiais do ecossistema (Microsoft.Extensions.AI), cobrindo registro e pipeline do cliente, telemetria sem conteúdo sensível, invocação automática de funções, dublês de teste, avaliação e critérios para adotar Semantic Kernel ou Microsoft Agent Framework. Use quando a integração com IA for em C# ou .NET. Complementa ai-integration, que traz os princípios independentes de stack.
---

# .NET AI Integration

Orienta o desenho e revisa integrações existentes em .NET. Em revisão, apenas relata. Traz só o que é específico de .NET; os princípios de desenho, testes e avaliação estão em `ai-integration`, se disponível. Nomes de pacotes e APIs devem ser conferidos na versão usada pelo projeto.

Não incluir, por iniciativa própria, atribuição, assinatura, crédito ou identificação do agente, nem indicação de que o conteúdo foi gerado por IA. Só incluir se o usuário pedir ou uma regra explícita do projeto exigir.

## O que verificar

- **Abstração oficial**: casos de uso dependem de `IChatClient` e `IEmbeddingGenerator` (Microsoft.Extensions.AI), não de tipos do SDK de um fornecedor. Bibliotecas referenciam `Microsoft.Extensions.AI.Abstractions`; aplicações referenciam `Microsoft.Extensions.AI` e o pacote do provedor que implementa as abstrações.
- **Registro e pipeline**: o cliente é registrado por DI (`AddChatClient`) e as funcionalidades transversais (cache, telemetria, limite de taxa, invocação de funções) entram como middleware do `ChatClientBuilder` ou `DelegatingChatClient`, não no código de domínio. A ordem dos middlewares altera o comportamento.
- **Telemetria sem conteúdo**: em `UseOpenTelemetry`, `EnableSensitiveData` fica desligado em produção. O padrão é `false`, mas a variável de ambiente `OTEL_INSTRUMENTATION_GENAI_CAPTURE_MESSAGE_CONTENT=true` o liga, e exemplos da documentação o definem como `true`. `UseLogging` pode registrar requisições e respostas: conferir o nível de log em produção.
- **Invocação automática de funções**: com `UseFunctionInvocation`, o modelo decide quais funções chamar. Expor só as funções necessárias e aplicar nelas a mesma autorização do caso de uso.
- **APIs experimentais**: recursos marcados como experimentais (ex.: geração de imagem, redução e roteamento de conversa) só entram com decisão explícita, porque podem mudar.
- **Testes**: o dublê é uma implementação simples de `IChatClient` com respostas controladas, sem mock do SDK do provedor. Para avaliar qualidade, as bibliotecas `Microsoft.Extensions.AI.Evaluation` rodam com o framework de testes do projeto; os avaliadores de qualidade usam um modelo e têm custo.
- **Semantic Kernel e Microsoft Agent Framework**: o Agent Framework é o sucessor de Semantic Kernel e AutoGen. Não adotar Semantic Kernel em código novo, e só adotar o Agent Framework quando houver requisito de agente ou de workflow que uma chamada ao modelo não atende. A própria documentação recomenda: se uma função resolve a tarefa, use a função. Conferir o estado de estabilidade do pacote para .NET antes de adotá-lo.

## Severidade

- 🛑 **Bloqueante**: `EnableSensitiveData` ligado em produção sem decisão explícita, ou função exposta ao modelo sem a autorização do caso de uso.
- ⚠️ **Importante**: domínio dependente de tipos do SDK do fornecedor, framework de agentes sem requisito, ou API experimental sem decisão.
- 💡 **Sugestão**: melhoria opcional.

## Formato da saída

Em revisão, para cada achado: severidade, `arquivo:linha`, problema, impacto e correção recomendada. Omitir itens que não se aplicam; sem achados, dizer isso explicitamente. Em orientação de desenho, apresentar a abordagem recomendada e as alternativas descartadas.

## Concluído quando

A revisão foi entregue no formato acima ou, em orientação de desenho, a abordagem recomendada foi apresentada com as decisões em aberto.
