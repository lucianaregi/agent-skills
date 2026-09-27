---
name: dotnet-mcp-server
description: Orienta e revisa servidores MCP em .NET com o SDK oficial em C# e sua integração com ASP.NET Core, cobrindo pacotes, registro de ferramentas, autorização por ferramenta, identidade do usuário, hospedagem no host existente ou separado, modo sem estado e validação de host. Use quando o servidor MCP for em C# ou .NET. Complementa mcp-server, que traz os princípios do protocolo independentes de linguagem.
---

# .NET MCP Server

Orienta o desenho e revisa servidores existentes em .NET. Em revisão, apenas relata. Traz só o que é específico do SDK oficial em C#; os princípios do protocolo estão em `mcp-server`, se disponível. APIs e comportamento devem ser conferidos na versão do SDK usada pelo projeto.

Não incluir, por iniciativa própria, atribuição, assinatura, crédito ou identificação do agente, nem indicação de que o conteúdo foi gerado por IA. Só incluir se o usuário pedir ou uma regra explícita do projeto exigir.

## O que verificar

- **Pacote**: `ModelContextProtocol.AspNetCore` para servidor HTTP; `ModelContextProtocol` para hospedagem com DI, como servidor stdio; `ModelContextProtocol.Core` quando bastam as APIs de baixo nível.
- **Registro**: `AddMcpServer()` com o transporte (`WithStdioServerTransport()` ou `WithHttpTransport()`) e as ferramentas. Preferir registrar os tipos de ferramenta explicitamente a `WithToolsFromAssembly()`, que expõe tudo o que estiver marcado no assembly.
- **Ferramentas**: classes com `[McpServerToolType]` e métodos com `[McpServerTool]` e `[Description]` precisos. Os métodos recebem por DI os serviços dos casos de uso existentes, sem `DbContext` ou repositório usados diretamente para contornar essas camadas.
- **Autorização por ferramenta**: `AddAuthorizationFilters()` com `[Authorize]`, políticas ou papéis em cada ferramenta, e `[AllowAnonymous]` só onde for intencional. Ferramentas não autorizadas somem da listagem e retornam erro de acesso na chamada. A identidade chega à ferramenta como parâmetro `ClaimsPrincipal`, sem entrar no schema.
- **Host existente ou separado**: `MapMcp("/rota")` adiciona o endpoint a uma aplicação ASP.NET Core existente e usa o pipeline de autenticação dela. Para decidir entre compartilhar e separar, aplicar os critérios de `mcp-server`.
- **Modo sem estado**: `WithHttpTransport(o => o.Stateless = true)` permite várias instâncias sem afinidade quando o servidor não depende de recursos que exigem estado no SDK (ex.: requisições do servidor ao cliente, assinaturas). Conferir quais versões do protocolo a versão do SDK suporta e se isso corresponde aos clientes-alvo.
- **Validação de host**: `AllowedHosts` com os nomes exatos do ambiente (só loopback em servidor local), nunca `*`. Atrás de proxy reverso, validar no proxy ou com o middleware de cabeçalhos encaminhados. CORS não substitui essa validação e só entra com a política mais restritiva possível.

## Severidade

- 🛑 **Bloqueante**: ferramenta sem `[Authorize]` ou equivalente quando o servidor exige autenticação, `AllowedHosts` com `*` em servidor HTTP, ou ferramenta que executa SQL ou comandos arbitrários.
- ⚠️ **Importante**: `WithToolsFromAssembly()` expondo ferramentas não intencionais, ferramenta acessando dados sem passar pelos casos de uso, ou modo sem estado com recurso que exige estado.
- 💡 **Sugestão**: melhoria opcional.

## Formato da saída

Em revisão, para cada achado: severidade, `arquivo:linha`, problema, impacto e correção recomendada. Omitir itens que não se aplicam; sem achados, dizer isso explicitamente. Em orientação de desenho, apresentar a abordagem recomendada e as alternativas descartadas.

## Concluído quando

A revisão foi entregue no formato acima ou, em orientação de desenho, a abordagem recomendada foi apresentada com as decisões em aberto.
