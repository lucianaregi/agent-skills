---
name: ai-integration
description: Orienta o desenho e revisa integrações de aplicações com modelos de IA sem acoplamento desnecessário, cobrindo a solução mais simples que atende, abstrações e provedores substituíveis, capacidades dos modelos, saída estruturada, resiliência, custo, privacidade, testes com dublês, validação real, avaliação de qualidade quando modelo ou prompt mudam e rastreabilidade. Use quando o usuário for integrar, escolher ou revisar o uso de modelos de IA, provedores, gateways, frameworks de agentes ou orquestração. Para a escolha de pacotes em si, use dependency-review.
---

# AI Integration

Orienta o desenho e revisa integrações existentes. Em revisão, apenas relata. Quando o usuário pedir a implementação, estes critérios orientam o código.

Não incluir, por iniciativa própria, atribuição, assinatura, crédito ou identificação do agente, nem indicação de que o conteúdo foi gerado por IA. Só incluir se o usuário pedir ou uma regra explícita do projeto exigir.

## Desenho

- **Mais simples primeiro**: uma chamada ao modelo com instruções claras e saída estruturada resolve a maior parte dos casos. Se uma função comum resolve a tarefa, não usar modelo. Agentes, frameworks de agentes ou orquestração, gateways, bancos vetoriais e outras tecnologias especializadas entram só com requisito e benefício demonstrados, nunca por padrão.
- **Nativo e oficial antes de camada adicional**: preferir o recurso da plataforma e a abstração oficial do ecossistema, quando adequada, a camadas de terceiros. Para a escolha de pacotes, aplicar `dependency-review`, se disponível.
- **Domínio independente do provedor**: casos de uso dependem de uma interface própria ou da abstração do ecossistema. Tipos do SDK do fornecedor ficam na infraestrutura.
- **Provedores substituíveis**: provedor e modelo vêm de configuração. Trocar de provedor, ou usar mais de um, não altera o domínio.
- **Capacidades dos modelos**: modelos diferem em janela de contexto, saída estruturada, chamada de ferramentas, entrada de imagem ou áudio, idiomas, latência e custo. Verificar a capacidade antes de depender dela e definir o comportamento quando ela falta.
- **Gateway é infraestrutura**: roteamento, cotas, cache e fallback entre provedores. Só com necessidade concreta, e sem regra de negócio dentro dele.
- **Saída estruturada**: quando a resposta alimenta código, pedir um formato definido (schema) e validar o resultado. Tratar resposta inválida, incompleta ou recusa.
- **Resiliência e custo**: timeout, retry limitado a erros transitórios, respeito a limites de taxa, limite de tamanho da entrada, custo por operação conhecido e acompanhado, e comportamento definido quando o provedor falha.
- **Privacidade e segurança**: enviar ao provedor só o necessário e conhecer a retenção e o uso dos dados por ele. Injeção de instruções, saída não confiável e ferramentas ficam com `security-review`, se disponível.
- **Observabilidade**: registrar metadados, não conteúdo. Aplicar `observability-review`, se disponível.
- **Rastreabilidade**: quando o resultado precisa ser auditado ou reprocessado, registrar junto dele o modelo, a versão do modelo e a versão do prompt. Prompts versionados como código.

## Testes e avaliação

- **Dublês**: um substituto do modelo testa a integração com o restante do sistema (fluxo, tratamento de erro, validação da saída). Ele não diz nada sobre a qualidade das respostas.
- **Validação real**: o fluxo principal é verificado ao menos uma vez contra o provedor real, dentro ou fora da suíte padrão conforme custo e determinismo, com o resultado declarado.
- **Avaliação de qualidade**: ao trocar modelo, versão ou prompt, comparar os resultados num conjunto de casos representativos, com critérios definidos antes da troca. Uma mudança sem avaliação é um risco a declarar.

## Severidade

- 🛑 **Bloqueante**: saída do modelo usada sem validação onde afeta dados ou ações, ou dados enviados ao provedor além do necessário.
- ⚠️ **Importante**: domínio acoplado ao SDK do fornecedor, framework ou tecnologia sem requisito, ausência de timeout ou tratamento de falha, ou troca de modelo ou prompt sem avaliação.
- 💡 **Sugestão**: melhoria opcional.

## Formato da saída

Em revisão, para cada achado: severidade, `arquivo:linha` ou componente, problema, impacto e correção recomendada. Omitir itens que não se aplicam; sem achados, dizer isso explicitamente. Em orientação de desenho, apresentar a abordagem recomendada, as alternativas descartadas e o motivo.

## Concluído quando

A revisão foi entregue no formato acima ou, em orientação de desenho, a abordagem recomendada foi apresentada com as decisões em aberto.
