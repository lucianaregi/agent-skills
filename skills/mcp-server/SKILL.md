---
name: mcp-server
description: Orienta o desenho e revisa servidores MCP (Model Context Protocol), cobrindo tools, resources e prompts, transporte e hosting, estado entre chamadas, autenticação, autorização por ferramenta, exposição mínima, reutilização dos casos de uso existentes, testes, observabilidade e versões do protocolo. Use quando o usuário for criar, expor funcionalidades por meio de ou revisar um servidor MCP. Para riscos de segurança de IA em geral, use security-review.
---

# MCP Server

Orienta o desenho e revisa servidores existentes. Em revisão, apenas relata. Os detalhes do protocolo mudam entre revisões: conferir a especificação na versão que o servidor e seus clientes usam.

Não incluir, por iniciativa própria, atribuição, assinatura, crédito ou identificação do agente, nem indicação de que o conteúdo foi gerado por IA. Só incluir se o usuário pedir ou uma regra explícita do projeto exigir.

## O que verificar

- **Primitiva adequada**: *tools* são funções que o modelo decide executar; *resources* são contexto e dados para o usuário ou o modelo; *prompts* são modelos de mensagem escolhidos pelo usuário. Leitura que a aplicação deve controlar tende a ser resource, não tool.
- **Exposição mínima**: só as ferramentas que o caso de uso exige, com leitura e escrita separadas e parâmetros tipados e restritos por schema. Nenhum parâmetro livre que vire consulta, comando ou caminho de arquivo.
- **Casos de uso existentes**: ferramentas chamam os casos de uso ou serviços da aplicação, que já aplicam validação, autorização e regras. Não acessar o banco diretamente quando isso contorna essas fronteiras, e nunca executar SQL ou comandos arbitrários vindos do modelo.
- **Descrições são instruções**: nome, descrição e anotações de uma ferramenta orientam o modelo. Mantê-los precisos, sem instruções ocultas, e revisar mudanças neles como mudanças de comportamento. Para envenenamento de ferramentas e injeção de instruções, aplicar `security-review`, se disponível.
- **Transporte**: stdio para servidor local iniciado pelo cliente; Streamable HTTP para servidor remoto. O transporte HTTP+SSE antigo está depreciado. Em HTTP, o cabeçalho `Origin` é validado (proteção contra DNS rebinding), servidores locais escutam só em localhost e toda conexão é autenticada.
- **Estado entre chamadas**: na revisão atual do protocolo não há sessão no nível do protocolo. Estado entre chamadas usa um identificador explícito emitido pelo servidor, aleatório, vinculado ao usuário autenticado e nunca tratado como autenticação. Revisões anteriores tinham sessão e exigiam afinidade entre instâncias: considerar as versões usadas pelos clientes.
- **Autenticação e autorização**: o servidor só aceita tokens emitidos para ele (audience validada) e não repassa o token recebido a APIs downstream. Cada ferramenta ou capacidade é autorizada conforme o usuário representado, com escopos mínimos.
- **Host compartilhado ou separado**: compartilhar o host da aplicação quando o servidor MCP é outra interface para os mesmos casos de uso, com o mesmo modelo de autenticação, ciclo de deploy e necessidade de escala. Separar quando houver requisito próprio de escala, isolamento de falhas ou de segurança, ciclo de deploy ou autenticação. Não separar por padrão.
- **Versões do protocolo**: as versões suportadas atendem os clientes-alvo, e a compatibilidade com revisões anteriores é decidida explicitamente. Nomes e schemas de ferramentas são contrato: mudá-los quebra clientes.
- **Testes**: os casos de uso continuam testados como já são. A camada MCP tem testes de listagem, schemas, autorização por ferramenta e erros, e é verificada com um cliente MCP real antes de publicar.
- **Observabilidade**: registrar ferramenta chamada, duração, resultado e usuário, sem payloads. Aplicar `observability-review`, se disponível.

## Severidade

- 🛑 **Bloqueante**: ferramenta que executa SQL ou comandos arbitrários, ferramenta sem autorização, token aceito sem validar a audience ou repassado downstream, ou servidor HTTP sem validação de `Origin` ou sem autenticação.
- ⚠️ **Importante**: ferramentas além do necessário, acesso direto ao banco contornando os casos de uso, estado tratado como autenticação, ou host separado sem requisito.
- 💡 **Sugestão**: melhoria opcional.

## Formato da saída

Em revisão, para cada achado: severidade, ferramenta ou `arquivo:linha`, problema, impacto e correção recomendada. Omitir itens que não se aplicam; sem achados, dizer isso explicitamente. Em orientação de desenho, apresentar a abordagem recomendada, as alternativas descartadas e o motivo.

## Concluído quando

A revisão foi entregue no formato acima ou, em orientação de desenho, a abordagem recomendada foi apresentada com as decisões em aberto.
