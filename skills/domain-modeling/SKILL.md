---
name: domain-modeling
description: Revisa a modelagem de domínio antes da persistência e da implementação, cobrindo conceitos, entidades e valores, invariantes, ownership, fronteiras de módulos, cardinalidade, estados e transições, origem e confirmação da informação, proveniência, ciclo de vida e exclusão de dados derivados. Use quando o usuário pedir para modelar ou revisar o domínio, definir entidades, estados ou fronteiras, ou antes de criar tabelas, projetos ou módulos para conceitos novos. Para o modelo físico e as migrations, use database-review.
---

# Domain Modeling

Analisa e propõe; não altera código. Trata do modelo conceitual: tabelas, índices e migrations ficam com `database-review`, se disponível.

## O que verificar

- **Conceitos e linguagem**: cada conceito tem nome e definição únicos. Um termo com significados diferentes em partes diferentes do sistema indica uma fronteira que precisa ser explícita.
- **Entidades e valores**: o que tem identidade e ciclo de vida próprio e o que é apenas valor, comparado pelo conteúdo.
- **Invariantes**: regras que sempre valem, quem é o único responsável por garanti-las e o que acontece quando uma operação as violaria.
- **Ownership e fronteiras de módulos**: qual módulo cria, altera e exclui cada dado. Outros módulos acessam por contrato, não pelo armazenamento do dono.
- **Cardinalidade e opcionalidade** das relações, conferidas contra os casos de uso reais, não contra o que "pode vir a existir".
- **Estados e transições**: estados válidos, transições permitidas, quem pode dispará-las, o que as dispara e quais são irreversíveis.
- **Origem e confirmação da informação**:
  - distinguir o fato confirmado da informação candidata (extraída, inferida ou sugerida) e da análise derivada;
  - identificar as evidências que sustentam cada informação;
  - definir como a informação candidata é confirmada ou promovida, por quem, e o que acontece quando é rejeitada ou quando a evidência muda.
- **Proveniência**: de onde veio cada dado (usuário, importação, processamento automático, terceiro), quando e por qual versão do processo, quando isso for necessário para auditar ou reprocessar.
- **Artefatos**: arquivos e conteúdo bruto (documentos, mídia) separados dos dados extraídos deles, com a relação entre os dois rastreável.
- **Ciclo de vida e exclusão**: por quanto tempo cada dado é mantido e, ao excluir um dado de origem, o que acontece com os derivados: extrações, análises, índices, caches e cópias em terceiros.
- **Materialização prematura**: um conceito não vira tabela, projeto, módulo, serviço ou infraestrutura antes de existir comportamento que o exija. Ele pode começar como valor ou estado dentro de outro conceito.

## Severidade

- 🛑 **Bloqueante**: invariante sem responsável, fronteira que leva dois módulos a alterarem o mesmo dado, ou exclusão que deixa dados derivados órfãos.
- ⚠️ **Importante**: conceito ambíguo, estado ou transição indefinido, proveniência ausente onde é necessária, ou materialização prematura.
- 💡 **Sugestão**: melhoria opcional.

## Formato da saída

- **Achados**: severidade, conceito ou `arquivo:linha`, problema, impacto e ajuste proposto. Omitir itens que não se aplicam; sem achados, dizer isso explicitamente.
- **Decisões em aberto**: pontos que só o usuário ou o negócio podem definir, marcando como bloqueante o que precisa ser decidido antes da implementação.

## Concluído quando

Os conceitos no escopo foram revisados e o usuário recebeu os achados e as decisões em aberto.
