# Dev Agency + Design Phase — Spec Bundle

Bundle completo para configurar uma empresa autônoma sobre Paperclip com:

- Fase de Discovery (UX/UI) antecedendo Delivery (engenharia)
- Integração com Superpowers como metodologia interna dos agentes
- Integração com design-review (Playwright MCP) para validação visual
- Roteamento de demandas por tipo (feature, bugfix, hotfix, spike)

## Estrutura completa

```
dev-agency-spec/
├── README.md                    # este arquivo
├── ARCHITECTURE.md              # spec principal de arquitetura (14 seções)
├── CLAUDE.md                    # design system + stack + convenções do projeto
├── agents/
│   ├── ceo/
│   │   ├── AGENTS.md            # papel + skills config
│   │   ├── SOUL.md              # personalidade executiva
│   │   └── HEARTBEAT.md         # lógica de roteamento detalhada
│   ├── design-lead/
│   │   ├── AGENTS.md
│   │   ├── SOUL.md
│   │   └── HEARTBEAT.md
│   ├── ux-architect/
│   │   ├── AGENTS.md
│   │   ├── SOUL.md
│   │   └── HEARTBEAT.md
│   ├── ui-designer/
│   │   ├── AGENTS.md
│   │   ├── SOUL.md
│   │   └── HEARTBEAT.md
│   ├── design-reviewer/
│   │   ├── AGENTS.md
│   │   ├── SOUL.md
│   │   └── HEARTBEAT.md
│   ├── cto/
│   │   ├── AGENTS.md            # com restrição "design CONGELADO"
│   │   ├── SOUL.md
│   │   └── HEARTBEAT.md
│   ├── tech-lead/
│   │   ├── AGENTS.md
│   │   ├── SOUL.md
│   │   └── HEARTBEAT.md
│   ├── developer/
│   │   ├── AGENTS.md            # também serve para Founding Engineer
│   │   ├── SOUL.md
│   │   └── HEARTBEAT.md
│   └── qa-engineer/
│       ├── AGENTS.md
│       ├── SOUL.md
│       └── HEARTBEAT.md
└── templates/
    ├── feature.md               # template: nova feature (path completo)
    ├── bugfix.md                # template: bug em funcionalidade existente
    ├── hotfix.md                # template: produção quebrada (P0)
    └── spike.md                 # template: investigação/research
```

**Total:** 34 arquivos · 9 agentes mapeados · 4 templates de issue/goal

## Skills externas necessárias (não bundled no Paperclip)

Este template depende de **2 skills externas** que precisam ser importadas antes de contratar os agentes:

| Skill | Fonte | Usado por |
|---|---|---|
| **Superpowers** | [`obra/superpowers`](https://github.com/obra/superpowers) | CTO, Tech Lead, Developer, QA Engineer |
| **frontend-design** | [`anthropics/claude-code` — plugins/frontend-design](https://github.com/anthropics/claude-code/tree/main/plugins/frontend-design/skills/frontend-design) | UI Designer, Design Lead, Design Reviewer |

Importar via API do Paperclip:

```bash
# Superpowers
curl -X POST "$PAPERCLIP_API_URL/api/companies/$PAPERCLIP_COMPANY_ID/skills/import" \
  -H "Authorization: Bearer $PAPERCLIP_API_KEY" \
  -H "Content-Type: application/json" \
  -d '{"source": "https://github.com/obra/superpowers"}'

# frontend-design
curl -X POST "$PAPERCLIP_API_URL/api/companies/$PAPERCLIP_COMPANY_ID/skills/import" \
  -H "Authorization: Bearer $PAPERCLIP_API_KEY" \
  -H "Content-Type: application/json" \
  -d '{"source": "https://github.com/anthropics/claude-code/tree/main/plugins/frontend-design/skills/frontend-design"}'
```

Sem essas skills importadas, os AGENTS.md dos agentes vão referenciar skills inexistentes — silenciosamente ignoradas no runtime, sem aviso de erro.

---

## Por onde começar

1. **Lê `ARCHITECTURE.md`** — entende a arquitetura completa, pipelines, gates, integrações
2. **Adapta `CLAUDE.md`** — substitui placeholders pelo seu projeto (stack, design system, conventions)
3. **Configura o Paperclip** — segue checklist da seção 13 do ARCHITECTURE
4. **Importa cada agente** — copia `agents/<role>/{AGENTS,SOUL,HEARTBEAT}.md` para o Paperclip company
5. **Cria templates de issue/goal** — usa os 4 arquivos de `templates/` como base no Paperclip
6. **Smoke test** — abre 1 ticket de cada tipo e verifica roteamento correto

## Anatomia de cada agente

Cada agente tem 3 arquivos com responsabilidades claras:

| Arquivo | Conteúdo | Tamanho típico |
|---|---|---|
| `AGENTS.md` | Papel, responsabilidades, tools, skills (Superpowers + projeto), modelo, budget | médio |
| `SOUL.md` | Personalidade, postura, comunicação, valores, anti-personalidade | médio |
| `HEARTBEAT.md` | Checklist executado a cada wake-up, lógica de transição de estados, audit trail | longo |

## Modelos por agente

| Agente | Modelo | % budget sugerido |
|---|---|---|
| CEO | Opus 4.6 | 15% |
| Design Lead | Sonnet 4.6 | 8% |
| UX Architect | Sonnet 4.6 | 8% |
| UI Designer | Sonnet 4.6 | **18%** (subagents paralelos) |
| Design Reviewer | Haiku 4.5 | 5% |
| CTO | Opus 4.6 | 12% |
| Tech Lead | Sonnet 4.6 | 12% |
| Developer | Sonnet 4.6 | **22%** (mais consumidor) |
| QA Engineer | Sonnet 4.6 | 8% |

## Versionamento

Recomendação: este bundle vive em git, junto com a configuração do Paperclip company. Toda mudança em pipeline ou roteamento passa por commit, então você tem histórico de "como a empresa operou ao longo do tempo".

## Stack assumida

- Paperclip ≥ v0.3.1
- Node.js 20+, pnpm 9.15+
- Claude Code (ou Codex/Cursor) com plugin marketplace
- Superpowers: `obra/superpowers` (oficial)
- design-review: `OneRedOak/claude-code-workflows/design-review`
- Playwright MCP: `@playwright/mcp`
- Skills do `paperclipai/companies/fullstack-forge`

## O que ainda fica fora deste bundle

Esta spec define **arquitetura organizacional e workflow**. Fica fora (você precisa decidir/criar):

- ADRs específicos do seu projeto (vão para `docs/adr/`)
- Skills customizadas do seu domínio
- Test fixtures e seed data
- Configuração de CI/CD (GitHub Actions, etc.)
- Configuração de observabilidade (Sentry, Datadog)
- Brand guidelines (cores específicas, logo, voice & tone)
- LGPD compliance específica do seu produto

## Iteração

Esta é v1. Esperado mudar conforme você descobre o que funciona no seu contexto. Áreas mais prováveis de iteração:

- **Modelos por agente:** começar mais conservador (mais Sonnet, menos Opus) e escalar onde julgamento ruim apareça
- **Heartbeat intervals:** pode ser que CEO precise de 15min em vez de 30min, ou Developer precise de 30min em vez de 1h
- **Severidade de hotfix:** afinar critérios depois de ver alguns casos reais
- **Skills suprimidas por agente:** ajustar conforme observa over-engineering ou under-thinking
