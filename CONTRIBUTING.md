# Contributing

Contribuições são bem-vindas — sejam correções no template existente, novos templates, ou melhorias na documentação.

## Tipos de contribuição

### 🐛 Correção / melhoria em template existente

1. Fork do repo
2. Branch a partir de `main`: `git checkout -b fix/<descrição-curta>`
3. Mantenha mudanças focadas (1 PR = 1 melhoria conceitual)
4. PR descrevendo o que mudou e por quê

### ✨ Novo template de company

Antes de abrir PR, abra uma **issue** descrevendo:

- **Nome** do template
- **Problema** que resolve (que tipo de empresa/uso ele endereça)
- **Diferencial** em relação aos templates existentes
- **Skeleton** dos agentes propostos

Após validação, siga a estrutura convencional:

```
<template-name>/
├── README.md
├── ARCHITECTURE.md
├── CLAUDE.md
├── agents/
│   └── <agent-name>/
│       ├── AGENTS.md
│       ├── SOUL.md
│       └── HEARTBEAT.md
└── templates/
    └── <issue-type>.md
```

### 📚 Documentação

Typos, clareza, exemplos: PRs diretos sem issue prévia.

## Convenções

### Linguagem

- Templates podem ser em português ou inglês
- README raiz, CONTRIBUTING, LICENSE em inglês para alcance maior
- Dentro de cada template, mantenha consistência interna

### Estrutura de arquivos do agente

Cada agente tem **exatamente 3 arquivos**:

- **AGENTS.md** — papel, responsabilidades, tools, skills (Superpowers + projeto), modelo, budget
- **SOUL.md** — personalidade, postura, valores, anti-personalidade
- **HEARTBEAT.md** — checklist de wake-up, transição de estados, audit trail

Não funda os arquivos. A separação ajuda agentes a carregarem o contexto certo no contexto certo.

### Markdown

- Use headers em hierarquia (h1 → h2 → h3, sem pular)
- Tables para informação tabular
- Code blocks com language hint (` ```bash`, ` ```javascript`, etc.)
- Links absolutos para repos externos, relativos para arquivos do próprio repo

### Commits

[Conventional Commits](https://www.conventionalcommits.org/):

```
feat(design-dev-agency): add UX Researcher agent
fix(design-dev-agency): correct G1 checklist
docs: improve root README
chore: update .gitignore
```

## Code of conduct

Seja respeitoso. Críticas atacam ideias, não pessoas. Discordância técnica é normal e bem-vinda — quando produtiva.

## Licença

Ao contribuir, você concorda que sua contribuição será licenciada sob a [MIT License](./LICENSE).
