# Agent Skills

Coleção pública de **skills reutilizáveis** para agentes de IA voltados ao desenvolvimento de software, no formato aberto [Agent Skills](https://agentskills.io).

## 🎯 Propósito

Este repositório reúne diretrizes, roteiros e checklists para orientar assistentes de código e agentes autônomos, como Claude Code, OpenAI Codex, GitHub Copilot, Cursor e Google Antigravity.

As skills seguem estes princípios:

- **Independência**: sem acoplamento a empresas, IDEs, plataformas de CI/CD proprietárias ou infraestrutura pré-assumida.
- **Objetividade**: critérios claros e orientados a resultado, sem overengineering.
- **Foco em qualidade**: prioridade para código correto, testável, legível e seguro.
- **Documentação em pt-BR**: instruções em português do Brasil, com termos técnicos e comandos em inglês quando apropriado.

---

## 📂 Estrutura

Cada skill fica em um diretório próprio, cujo nome é igual ao campo `name` do frontmatter:

```text
skills/
└── <nome-da-skill>/
    └── SKILL.md
```

O `SKILL.md` começa com um frontmatter YAML contendo `name` e `description`, seguido das instruções em Markdown. O conteúdo varia conforme a skill e inclui seções como propósito, princípios, roteiro ou checklist e o que evitar.

---

## 🧰 Skills Disponíveis

| Skill | Propósito |
| :--- | :--- |
| [`api-review`](skills/api-review/SKILL.md) | Revisar contratos HTTP/REST, status codes, validação de payload e retrocompatibilidade. |
| [`bug-fix`](skills/bug-fix/SKILL.md) | Investigar a causa raiz, reproduzir o erro com teste automatizado e corrigir com teste de regressão. |
| [`commit`](skills/commit/SKILL.md) | Validar alterações de forma proporcional à mudança e criar commits na convenção do repositório (padrão: Conventional Commits em pt-BR). |
| [`dependency-review`](skills/dependency-review/SKILL.md) | Avaliar necessidade, manutenção, licença e segurança antes de adicionar, atualizar ou remover dependências. |
| [`dotnet-code-review`](skills/dotnet-code-review/SKILL.md) | Revisar código C#/.NET com foco em bugs, async/await, injeção de dependência, nullability, recursos e performance. |
| [`observability-review`](skills/observability-review/SKILL.md) | Verificar logs estruturados, níveis de log, correlation IDs, cardinalidade e vazamento de dados sensíveis. |
| [`pr-review`](skills/pr-review/SKILL.md) | Revisar pull requests de forma holística: regressões, escopo, cobertura de testes e severidade dos achados. |
| [`readme`](skills/readme/SKILL.md) | Criar e manter READMEs fiéis ao código real, com pré-requisitos e comandos exatos de execução. |
| [`refactoring`](skills/refactoring/SKILL.md) | Refatorar código preservando o comportamento externo, com validação por testes. |
| [`scope-check`](skills/scope-check/SKILL.md) | Identificar e conter alterações fora do escopo da tarefa (*scope creep*). |
| [`security-review`](skills/security-review/SKILL.md) | Identificar riscos de segurança concretos: secrets no código, injeções, IDOR e configurações inseguras. |
| [`task-planning`](skills/task-planning/SKILL.md) | Planejar tarefas técnicas antes de implementar: escopo, arquivos afetados, riscos e validação. |
| [`technical-documentation`](skills/technical-documentation/SKILL.md) | Elaborar documentação técnica e ADRs fiéis ao código, declarando o que não pôde ser verificado. |
| [`testing`](skills/testing/SKILL.md) | Criar e revisar testes focados em comportamento observável (AAA), evitando excesso de mocks e fragilidade. |

---

## 🚀 Como Reutilizar em Outros Projetos

Para usar uma skill, copie o diretório dela (por exemplo, `skills/commit/`) para um dos diretórios que a ferramenta lê. Skills **de projeto** ficam dentro do repositório de destino e podem ser versionadas com ele; skills **pessoais** ficam no diretório do usuário e valem para todos os projetos da máquina.

| Ferramenta | Skills de projeto | Skills pessoais |
| :--- | :--- | :--- |
| Claude Code | `.claude/skills/` | `~/.claude/skills/` |
| Claude Desktop e claude.ai (chat) | — | Upload de `.zip` em **Customize > Skills** |
| OpenAI Codex | `.agents/skills/` | `~/.agents/skills/` |
| GitHub Copilot | `.github/skills/`, `.claude/skills/`, `.agents/skills/` | `~/.copilot/skills/`, `~/.agents/skills/` |
| Cursor | `.cursor/skills/`, `.agents/skills/` | `~/.cursor/skills/`, `~/.agents/skills/` |
| Google Antigravity (IDE) | `.agents/skills/` | `~/.gemini/config/skills/` |
| Antigravity CLI | `.agents/skills/` | `~/.gemini/antigravity-cli/skills/` |

> 💡 **Interoperabilidade**: `.agents/skills/` na raiz do projeto é lido por Codex, Copilot, Cursor e Antigravity. **O Claude Code não lê esse diretório**; para ele, use `.claude/skills/`, que o Copilot também reconhece. No escopo pessoal, `~/.agents/skills/` é compartilhado por Codex, Copilot e Cursor.

Observações por ferramenta:

- **Claude Code**: vale para a CLI, as extensões de IDE e a aba *Code* do Claude Desktop. As skills são carregadas automaticamente quando relevantes ou invocadas com `/<nome-da-skill>`.
- **Claude Desktop e claude.ai (chat)**: não há descoberta a partir de pastas locais. Compacte o diretório da skill em `.zip` e envie pela interface; o recurso exige que a execução de código esteja habilitada.
- **OpenAI Codex**: procura `.agents/skills/` em cada diretório entre o diretório de trabalho atual e a raiz do repositório. Na CLI, uma skill pode ser selecionada explicitamente com `$`.
- **Cursor**: por compatibilidade, também lê `.claude/skills/`, `.codex/skills/`, `~/.claude/skills/` e `~/.codex/skills/`.

### Exemplo

Para disponibilizar a skill `commit` em outro projeto, no diretório interoperável:

```bash
git clone https://github.com/lucianaregi/agent-skills.git
mkdir -p meu-projeto/.agents/skills
cp -r agent-skills/skills/commit meu-projeto/.agents/skills/
```

Para o Claude Code, substitua `.agents/skills` por `.claude/skills`.

### Referências

Os caminhos acima foram conferidos na documentação oficial em setembro de 2026 e podem mudar entre versões:

- [Claude Code: Skills](https://code.claude.com/docs/en/skills)
- [Claude: usando skills no app](https://support.claude.com/en/articles/12512180-using-skills-in-claude)
- [OpenAI Codex: Skills](https://developers.openai.com/codex/skills)
- [GitHub Copilot: About agent skills](https://docs.github.com/en/copilot/concepts/agents/about-agent-skills)
- [Cursor: Skills](https://cursor.com/docs/context/skills)
- [Google Antigravity: Skills](https://antigravity.google/docs/skills)

---

## 🤝 Princípios de Contribuição

Ao adicionar novas skills a este repositório:

1. Crie o diretório `skills/<nome-da-skill>/` com um `SKILL.md` cujo `name` seja idêntico ao nome do diretório (letras minúsculas, números e hífens).
2. Escreva as instruções em **pt-BR**, mantendo termos técnicos em inglês quando for o uso corrente.
3. Mantenha a skill **agnóstica** de projeto, empresa ou ferramenta.
4. Prefira diretrizes **pragmáticas**, objetivas e verificáveis.
5. Adicione a skill à tabela [Skills Disponíveis](#-skills-disponíveis).

---

## 📄 Licença

Distribuído sob a licença MIT. Consulte o arquivo [LICENSE](LICENSE).
