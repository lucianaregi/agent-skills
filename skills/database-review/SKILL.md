---
name: database-review
description: Revisa decisões de persistência em bancos relacionais e migrations, cobrindo modelo físico, tipos, constraints, chaves estrangeiras, índices, migrations (compatibilidade, reversão, idempotência), concorrência, isolamento, menor privilégio, ambientes, ciclo de vida dos dados e estratégia de testes contra o banco. Use quando o usuário pedir revisão de schema, tabelas, índices, consultas ou migrations, ou quando um diff alterar migrations, mapeamentos de persistência ou SQL. Para o modelo conceitual, use domain-modeling; para injeção de SQL, security-review.
---

# Database Review

Apenas relata. Vale para bancos relacionais em geral; recursos específicos de um SGBD são avaliados no SGBD e na versão que o projeto usa.

## O que verificar

- **Modelo físico e tipos**: tipos adequados ao dado (precisão decimal para valores monetários, data e hora com fuso definido, tamanho e codificação de texto); nulabilidade coerente com o domínio; chaves primárias estáveis.
- **Integridade**: regras de integridade garantidas pelo banco, não só pela aplicação: chaves estrangeiras, unicidade e checks. O comportamento na exclusão (cascata, restrição, nulo) é intencional.
- **Índices**: suportam as consultas reais (filtros, junções, ordenação); sem índices redundantes; custo de escrita considerado.
- **Migrations**:
  - compatíveis com a versão da aplicação em execução durante o deploy; renomear ou remover em etapas (expandir e depois contrair);
  - operações que bloqueiam ou reescrevem tabelas grandes avaliadas antes;
  - protegidas contra reexecução quando o mecanismo de migrations não controla isso;
  - estratégia de reversão definida, seja migration de volta ou correção para frente, com a perda de dados explícita;
  - nenhuma migration vazia ou sem mudança real.
- **Concorrência e isolamento**: atualizações concorrentes do mesmo registro tratadas (controle otimista, bloqueio ou unicidade no banco); nível de isolamento adequado ao caso; transações curtas, sem chamadas externas dentro delas.
- **Menor privilégio**: o usuário da aplicação não é administrador e não tem permissão de alterar o schema quando as migrations rodam separadamente. Credenciais distintas por ambiente.
- **Ambientes**: o banco de produção fica isolado; dados reais não são copiados para outros ambientes sem anonimização.
- **Ciclo de vida dos dados**: retenção e exclusão alcançam dados derivados e cópias, como tabelas auxiliares, históricos e tabelas de auditoria, conforme a política do projeto.
- **Testes**:
  - testes automatizados não substituem a validação contra o banco real quando o comportamento depende dele (constraints, collation, tipos, isolamento, SQL específico, migrations). Nesses casos, validar contra o mesmo SGBD e versão, em contêiner ou ambiente real;
  - usar outro SGBD ou um banco em memória nos testes não é proibido: é um risco a avaliar quando o comportamento testado depende de características do banco real, e aceitável quando não depende.

## Severidade

- 🛑 **Bloqueante**: migration que perde dados ou quebra a versão em execução, integridade que só a aplicação garante em regra crítica, ou usuário da aplicação com privilégio administrativo.
- ⚠️ **Importante**: índice ausente para consulta frequente, concorrência não tratada, reversão indefinida ou comportamento dependente do banco sem validação contra o SGBD real.
- 💡 **Sugestão**: melhoria opcional.

## Formato da saída

Para cada achado: severidade, tabela, migration ou `arquivo:linha`, problema, impacto e correção recomendada. Omitir itens que não se aplicam. Sem achados, dizer isso explicitamente.

## Concluído quando

O schema, as migrations e o acesso a dados no escopo foram verificados e o relatório foi entregue.
