---
name: github-actions-review
description: Revisa workflows de GitHub Actions, cobrindo permissões do GITHUB_TOKEN, secrets, actions fixadas por SHA e supply chain, injeção via expressões, pull_request × pull_request_target, cache, build e testes, service containers, artefatos, concorrência, environments, separação entre CI e deploy e a interação entre checks obrigatórios e filtros ou condições. Use quando o usuário pedir revisão de workflows ou de CI no GitHub, ou quando um diff alterar arquivos em .github/workflows ou actions próprias.
---

# GitHub Actions Review

Apenas relata. Específica de GitHub Actions: não altera workflows, rulesets nem configurações do repositório.

## O que verificar

- **Permissões do `GITHUB_TOKEN`**: bloco `permissions` explícito no workflow ou no job, com o mínimo necessário (ex.: `contents: read`), e escrita só nos jobs que precisam. Sem o bloco, o token recebe o padrão configurado no repositório ou na organização, que pode incluir escrita.
- **Secrets**: usados só nos jobs e passos que precisam; nunca impressos nem passados em argumentos de linha de comando. Secrets de deploy ficam em `environment` com regras de proteção. Em `pull_request` vindo de fork, os secrets não são passados e o `GITHUB_TOKEN` é somente leitura: um workflow que depende deles falha nesses PRs.
- **`pull_request_target` e `workflow_run`**: são privilegiados (podem ter token com escrita e acesso a secrets) e `pull_request_target` roda no contexto da branch padrão da base. Fazer checkout ou executar código do PR neles permite que um fork execute código com esses privilégios. Para build e testes do código do PR, usar `pull_request`.
- **Injeção via expressões**: `${{ }}` com texto controlado por terceiros (título e corpo do PR, nome de branch, mensagem de commit) interpolado em `run:` executa comandos. Passar o valor por variável de ambiente intermediária.
- **Actions de terceiros e supply chain**: fixadas pelo SHA completo do commit, com a versão em comentário, porque tags são mutáveis; origem confiável e em uso ativo; atualizações por processo revisado. No checkout, `persist-credentials: false` quando o job não precisa usar o token do Git depois.
- **Cache**: chave inclui o hash dos lockfiles; cache não substitui artefato de release; jobs privilegiados não restauram cache que código não confiável possa ter escrito.
- **Build e testes**: restore, build e test com os comandos do projeto e versões de runtime fixadas; falha de teste falha o job, sem `continue-on-error` indevido.
- **Service containers**: imagem com versão fixada e compatível com a usada em produção quando o teste depende do serviço; o job espera o serviço ficar saudável antes dos testes.
- **Artefatos**: publicados só quando algo os consome; retenção definida; sem secrets nem dados sensíveis.
- **Concorrência**: `concurrency` cancela execuções obsoletas do mesmo PR; deploys não são cancelados no meio.
- **CI e deploy**: deploy em job ou workflow separado, com `environment` e aprovação quando necessário. Workflows de PR não fazem deploy.
- **Checks obrigatórios** (rulesets ou proteção de branch):
  - o nome exigido corresponde ao nome do job como o GitHub o reporta;
  - um workflow pulado por filtro de caminho, de branch ou por mensagem de commit deixa o check obrigatório pendente e bloqueia o merge; um job pulado por condição `if:` reporta sucesso;
  - uma execução manual (`workflow_dispatch`) na branch não substitui o check pendente do PR;
  - com a exigência de branch atualizada ativa, o PR precisa estar em dia com a base.

## Severidade

- 🛑 **Bloqueante**: código não confiável executado com privilégios (`pull_request_target` ou `workflow_run` com checkout do PR, injeção via expressão), secret exposto, ou check obrigatório que nunca é reportado.
- ⚠️ **Importante**: permissões além do necessário, action de terceiros sem SHA, deploy acionável por PR ou teste que não falha o job.
- 💡 **Sugestão**: melhoria opcional.

## Formato da saída

Para cada achado: severidade, `arquivo:linha` do workflow, problema, impacto e correção recomendada. Omitir itens que não se aplicam. Sem achados, dizer isso explicitamente.

## Concluído quando

Os workflows no escopo foram verificados e o relatório foi entregue.
