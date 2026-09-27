---
name: security-review
description: Analisa código e configuração em busca de riscos de segurança concretos e exploráveis, como secrets expostos, injeção, falhas de autenticação e autorização, exposição de dados, envio de dados a terceiros e configurações inseguras, incluindo, quando aplicável, sistemas que usam modelos de IA ou expõem ferramentas a agentes. Use quando o usuário pedir revisão ou auditoria de segurança, ou quando uma mudança tocar autenticação, autorização, entrada externa, secrets, dados sensíveis, integrações com IA ou ferramentas para agentes. Para vulnerabilidades em pacotes de terceiros, use dependency-review.
---

# Security Review

Apenas relata. Não corrige sem pedido explícito.

## Princípios

- Reportar riscos com caminho de exploração plausível no código real: de onde vem a entrada, por onde passa e onde é usada.
- Não reportar riscos teóricos sem ligação com o código. Se a exploração depender de algo que não é visível no repositório (proxy, gateway, configuração de ambiente), declarar a premissa.

## O que verificar

- **Secrets**: credenciais, chaves privadas, tokens ou connection strings em código, em configuração versionada ou no histórico do Git. Um secret commitado continua no histórico mesmo depois de removido do arquivo, então a recomendação é **rotacionar a credencial**, não apenas apagá-la. Com vários provedores ou ambientes, uma credencial por provedor e por ambiente, com o menor escopo possível.
- **Isolamento entre ambientes**: credenciais, dados e serviços de produção inacessíveis a partir de desenvolvimento, testes e CI. Dados reais de produção não são usados fora dela sem anonimização.
- **Injeção**: consultas montadas por concatenação em vez de parâmetros; entrada externa chegando a comandos de shell, caminhos de arquivo (path traversal), templates, desserialização ou URLs requisitadas pelo servidor (SSRF).
- **XSS**: saída sem escape adequado ao contexto (HTML, atributo, JavaScript, URL). A defesa principal é o escape automático do mecanismo de templates; sanitização só é necessária quando HTML fornecido pelo usuário é permitido.
- **Autenticação e autorização**: toda operação protegida exige autenticação. O acesso por ID verifica se o recurso pertence ao usuário (IDOR). Papéis e escopos são verificados no servidor, não apenas na interface.
- **Exposição de dados**: dados sensíveis em logs, respostas ou mensagens de erro, incluindo prompts, respostas de modelos e conteúdo de arquivos; stack traces ou detalhes internos devolvidos ao cliente em produção. Para a telemetria em detalhe, aplicar `observability-review`, se disponível.
- **Dados enviados a terceiros** (APIs externas, provedores de IA, serviços de análise): enviar só o necessário para a operação, sem dados pessoais que ela não exige. A retenção e o uso dos dados pelo terceiro precisam ser conhecidos; como costumam estar fora do repositório (contrato, configuração da conta), declará-los como premissa.
- **Arquivos e mídia sensíveis** (documentos, áudio, transcrições, imagens): armazenamento não público; acesso verificado por usuário; links assinados com expiração curta e escopo mínimo; a exclusão acompanha a do dado de origem.
- **CORS**: origem da requisição refletida sem lista de permissões junto com `Access-Control-Allow-Credentials: true`. (`*` com credenciais já é bloqueado pelos navegadores.)
- **Transporte e sessão**: TLS na comunicação remota. Cookies de sessão com `Secure`, `HttpOnly` e `SameSite` adequados. Proteção contra CSRF quando a autenticação usa cookies.

### Se o sistema usa modelos de IA ou expõe ferramentas a agentes

- **Injeção de instruções (prompt injection)**: conteúdo não confiável, como entrada do usuário, documentos, páginas, e-mails e resultados de ferramentas, chega ao modelo misturado às instruções. O impacto de um desvio deve ser limitado pelas permissões e validações do sistema, não apenas pelo texto do prompt.
- **Saída do modelo como entrada não confiável**: a resposta do modelo é validada antes de chegar a consultas, comandos, HTML, caminhos de arquivo, URLs ou chamadas de ferramenta. Saída estruturada é validada contra o schema esperado.
- **Envenenamento de ferramentas (tool poisoning)**: descrições, anotações e metadados de ferramentas são lidos pelo modelo como instruções. Ferramentas ou servidores de terceiros (ex.: servidores MCP) não confiáveis, ou que mudam sem revisão, podem induzir ações indevidas: usar fontes confiáveis, fixar versões e revisar mudanças.
- **Exposição e autorização de ferramentas**: expor só as ferramentas necessárias; autorizar cada ferramenta ou capacidade conforme o usuário em nome de quem o agente age; ações destrutivas ou irreversíveis exigem confirmação. As credenciais usadas pelas ferramentas têm o menor privilégio, e o modelo nunca recebe secrets.

## Severidade

- 🛑 **Bloqueante**: vulnerabilidade explorável ou secret exposto. Impede o merge.
- ⚠️ **Importante**: risco real que exige condições adicionais para exploração, ou defesa em profundidade ausente em área sensível.
- 💡 **Sugestão**: endurecimento opcional.

## Formato da saída

Para cada achado:

1. Severidade e `arquivo:linha`.
2. Vulnerabilidade (ex.: SQL Injection, secret exposto).
3. Cenário de exploração: o que um atacante faz e o que obtém.
4. Correção recomendada.

Omitir itens que não se aplicam. Sem achados, dizer isso explicitamente e indicar o que foi verificado.

## Concluído quando

Todas as áreas aplicáveis foram verificadas e o relatório foi entregue no formato acima.
