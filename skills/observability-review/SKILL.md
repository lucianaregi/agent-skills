---
name: observability-review
description: Revisa logs, métricas e traces no código, cobrindo níveis de log, logging estruturado, contexto de correlação, cardinalidade de métricas e vazamento de dados e conteúdo sensíveis na telemetria, inclusive em operações com modelos de IA e ferramentas. Use quando o usuário pedir revisão de logging ou observabilidade, ou quando um diff adicionar ou alterar logs, métricas, traces ou a configuração de instrumentação. Para outros riscos de segurança, use security-review.
---

# Observability Review

Apenas relata. Avaliar com base nas bibliotecas e na infraestrutura de telemetria que o projeto já usa; não recomendar ferramentas novas sem pedido.

## O que verificar

- **Níveis de log** (os nomes variam por biblioteca):
  - erro: falha que impede a operação e exige ação;
  - aviso: anomalia contornada (retry, fallback);
  - informação: evento relevante do ciclo de vida ou do negócio;
  - debug/trace: detalhe de diagnóstico, desligado por padrão em produção.

  Problemas comuns: erro esperado (ex.: validação do usuário) registrado como erro; log dentro de laço frequente; a mesma exceção registrada em várias camadas.
- **Logging estruturado**: se a biblioteca suportar, os valores vão em campos nomeados e não interpolados no texto da mensagem, para que possam ser filtrados e agregados.
- **Contexto**: logs de erro identificam o recurso afetado e registram a exceção com stack trace, não só a mensagem. Com chamadas entre serviços ou processamento assíncrono, um identificador de correlação (ex.: trace ID do OpenTelemetry) é propagado. Operações com modelos e ferramentas são correlacionadas pelo mesmo identificador, sem copiar conteúdo para os atributos.
- **Dados e conteúdo sensíveis**: nunca aparecem em logs, atributos de métricas ou spans:
  - credenciais, secrets, tokens de autenticação, senhas e links assinados;
  - dados pessoais (ex.: CPF), dados de cartão e de saúde;
  - conteúdo: documentos (ex.: currículos), áudio, transcrições, prompts, respostas de modelos e payloads de ferramentas (ex.: MCP).

  Atenção a objetos inteiros serializados no log e a mensagens de exceção que carregam o conteúdo processado.
- **Metadados em vez de conteúdo**: registrar identificadores, tipo, tamanho, duração, resultado, modelo e versão usados, contagem de tokens do modelo e custo. A contagem de tokens do modelo é telemetria legítima; tokens de autenticação nunca são registrados.
- **Captura automática por configuração**: bibliotecas de instrumentação (HTTP, clientes de modelos de IA, ferramentas) podem registrar corpos de requisição, prompts e respostas quando uma opção de configuração ou variável de ambiente está ligada. Verificar essa configuração e mantê-la desligada em produção, salvo decisão explícita com controle de acesso e retenção definidos. Exemplos de código da documentação às vezes a ligam.
- **Cardinalidade** (se o projeto coleta métricas): labels não usam valores únicos por requisição, como IDs de usuário, GUIDs, timestamps ou URLs com parâmetros.
- **Cobertura de métricas** (se o projeto coleta métricas): operações críticas novas expõem taxa de erro, latência e volume, seguindo o padrão existente.

## Severidade

- 🛑 **Bloqueante**: dado ou conteúdo sensível em telemetria, captura automática de conteúdo ligada em produção sem decisão explícita, ou cardinalidade sem limite em métricas.
- ⚠️ **Importante**: falha sem log ou sem contexto suficiente para diagnóstico, ou nível incorreto que gera ruído ou esconde erros.
- 💡 **Sugestão**: melhoria opcional.

## Formato da saída

Para cada achado: severidade, `arquivo:linha`, problema, impacto no diagnóstico ou na operação e correção recomendada. Omitir itens que não se aplicam. Sem achados, dizer isso explicitamente.

## Concluído quando

Todos os pontos de telemetria no escopo foram verificados e o relatório foi entregue.
