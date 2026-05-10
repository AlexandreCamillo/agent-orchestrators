#  Agent Orchestrators

> Templates de **companies** para [Paperclip](https://github.com/paperclipai/paperclip) — a plataforma open-source de orquestração de agentes de IA.

Cada template é uma configuração completa de uma empresa autônoma: agentes, papéis, lógica de roteamento, templates de issue/goal, e integrações com skills externas (Superpowers, design-review, etc.).

---

## Templates disponíveis

### 🎨 [design-dev-agency](./design-dev-agency)

Agência de desenvolvimento com **fase de Discovery (UX/UI) antecedendo Delivery (engenharia)**. Integra:

- **9 agentes** — CEO, Design Lead, UX Architect, UI Designer, Design Reviewer, CTO, Tech Lead, Developer, QA Engineer
- **Superpowers** como metodologia interna (TDD, brainstorming, systematic-debugging, verification-before-completion)
- **design-review** (Playwright MCP) para validação visual real
- **Roteamento por tipo** — feature (path completo), bugfix, hotfix (expresso), spike

Adequado para times que querem **separação clara entre decisões de design e implementação**, com governance via Board approval gates.

---

## Como usar

Cada template tem README próprio explicando como aplicar. Geral:

1. **Clone este repo** ou baixe o template de interesse
2. **Adapte o `CLAUDE.md`** ao seu projeto (stack, design system, conventions)
3. **Configure o Paperclip** seguindo o checklist do template
4. **Importe os agentes** copiando os arquivos para a configuração do Paperclip company
5. **Smoke test** abrindo 1 ticket de cada tipo

## Estrutura de cada template

Convenção que todos seguem:

```
<template-name>/
├── README.md                # overview do template
├── ARCHITECTURE.md          # spec detalhada
├── CLAUDE.md                # contexto de projeto (design system, stack, conventions)
├── agents/
│   └── <agent-name>/
│       ├── AGENTS.md        # papel + responsabilidades + skills
│       ├── SOUL.md          # personalidade e postura
│       └── HEARTBEAT.md     # lógica de wake-up e transição de estados
└── templates/
    └── <issue-type>.md      # templates de issue/goal
```

## Contribuindo

Veja [CONTRIBUTING.md](./CONTRIBUTING.md).

Resumo: forks bem-vindos, novos templates idem. Mantenha a estrutura convencional acima.

## Referências

- [Paperclip](https://github.com/paperclipai/paperclip) — plataforma base
- [Clipmart](https://www.clipmart.ai/) — marketplace oficial de templates
- [paperclipai/companies](https://github.com/paperclipai/companies) — skills oficiais para companies
- [Superpowers](https://github.com/obra/superpowers) — plugin de metodologia
- [design-review](https://github.com/OneRedOak/claude-code-workflows/tree/main/design-review) — skill de design review com Playwright

## Licença

[MIT](./LICENSE).
