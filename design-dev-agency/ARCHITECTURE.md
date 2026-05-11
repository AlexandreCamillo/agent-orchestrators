# Dev Agency + Design Phase — Spec de Arquitetura

> Empresa autônoma sobre Paperclip, derivada do template Dev Agency com fase de Discovery (UX/UI) antecedendo Delivery (engenharia), integrada com Superpowers e design-review.

---

## 1. Visão geral

Esta arquitetura modifica o template **Dev Agency** padrão do Clipmart com quatro mudanças importantes:

1. **Fase de Discovery (Design) antes do Delivery (engenharia)** — UX e UI são definidos e aprovados pelo Board *antes* de qualquer escrita de código de produção
2. **Superpowers** integrado como metodologia interna de cada agente executor (TDD, brainstorming, systematic-debugging, verification-before-completion)
3. **design-review** integrado como skill do Design Reviewer e do CTO, usando Playwright MCP para validação visual real (não só análise estática)
4. **Roteamento de demandas por tipo** — feature, bugfix, hotfix e spike têm pipelines distintos, configurados na lógica do CEO

A Board (humano) opera como aprovador de gates estratégicos. Não escreve tickets pra agentes intermediários, não microgerencia.

---

## 2. Princípios

1. **Discovery e Delivery são fases distintas.** UX/UI é decidido e aprovado antes de engenharia começar. Evita retrabalho e brigas tardias entre código e design.
2. **Cada tipo de demanda tem seu pipeline.** Feature passa por discovery; bugfix vai direto pro CTO com debugging sistemático; hotfix vai expresso; spike entrega documento.
3. **Board governa, não executa.** Você aprova gates, não dirige agentes intermediários. Board fala com o CEO, ponto.
4. **Skills > Prompts.** Lógica complexa (TDD, design review, debugging) fica em skills versionáveis e composíveis — Superpowers, design-review, paperclipai/companies — não em prompts inflados dentro do AGENTS.md.
5. **Audit trail é não-negociável.** Toda decisão tem heartbeat ID, ticket associado, comentário com contexto. Nada acontece fora de um thread de ticket.
6. **Worktree isolation por padrão.** Variantes de design em paralelo, features em paralelo — sem conflito de merge, sem poluição de contexto.

---

## 2.1. Paperclip primitives (obrigatórias)

O template depende de três primitivas do Paperclip que previnem travamento de pipeline. Ignorar qualquer uma delas causa esteira parada silenciosamente.

### `blockedByIssueIds` (dependências first-class)

Sempre usar `blockedByIssueIds` ao criar issues dependentes, nunca mencionar dependências apenas em texto livre. Paperclip dispara `issue_blockers_resolved` automaticamente quando todos os blockers viram `done`, acordando o agente assignado.

```json
POST /api/companies/{companyId}/issues
{
  "title": "PR-3: UI Components",
  "blockedByIssueIds": ["<pr-2-issue-id>"],
  "status": "blocked",
  ...
}
```

### `executionPolicy` (review routing automático)

Issues de implementação que passam por G1 (code review do CTO) devem ter `executionPolicy` com stage de review. Quando o executor marca `in_review`, Paperclip reassigna automaticamente ao revisor e envia wake.

```json
PATCH /api/issues/{issueId}
{
  "executionPolicy": {
    "stages": [
      { "type": "review", "participant": "<cto-agent-id>", "label": "Code Review (G1)" }
    ]
  }
}
```

Sem `executionPolicy`, a issue fica em `in_review` assignada ao executor e nenhum revisor é notificado.

### Goals como tracking-only

Goals (umbrella issues) servem para agrupar sub-goals e issues. Não devem ter execução ativa (heartbeat de agente executor). Atribuir ao CEO ou ao Board user. Se um goal tiver execução ativa, Paperclip detecta "live execution disappeared" entre heartbeats e auto-bloqueia, criando loop de `blocked` → `in_progress` → `blocked`.

---

## 3. Org Chart

```
                          BOARD (você)
                              │
                              ▼
                            CEO
                ┌─────────────┴─────────────┐
                ▼                           ▼
         Design Lead                       CTO
        ┌───────┴───────┐          ┌───────┴───────┐
        ▼       ▼       ▼          ▼               ▼
   UX Arch.  UI Des.  Design    Tech Lead      QA Engineer
                     Reviewer   ┌─────┴─────┐
                                ▼           ▼
                          Founding Eng.  Developer
```

**Discovery branch (Design Lead):** ativada apenas para features. Ignorada em bugfix/hotfix/spike.

**Delivery branch (CTO):** sempre ativa. Para features, recebe a design spec congelada. Para bugfix/hotfix/spike, recebe o ticket diretamente.

---

## 4. Pipelines por tipo de demanda

### 4.1 Feature (path completo)

```
Board abre goal "Feature X" com label `feature`
    │
    ▼
CEO triagem → cria sub-goals "Discovery" e "Delivery" (Delivery BLOCKED)
    │
    ▼
Design Lead → UX Architect
    │     Superpowers /brainstorming (lente UX)
    │     Saída: UX_SPEC.md (user flows, AC, edge cases)
    │
    ▼
Design Lead → UI Designer
    │     paperclipai worktree:make × 3 (variant-a, -b, -c)
    │     Spawn 3 subagents paralelos com estéticas distintas
    │     Cada um gera protótipo React + Tailwind navegável
    │
    ▼
Design Reviewer roda /design-review em cada variante
    │     Playwright MCP abre browser, screenshot, valida vs CLAUDE.md
    │     Output: 3 relatórios estruturados + screenshots
    │
    ▼
═══════════════════════════════════════════════════════════
  ★ BOARD APPROVAL GATE — Discovery ★
  Você escolhe variante (A/B/C) ou pede iteração
═══════════════════════════════════════════════════════════
    │ design aprovado vira "design spec congelada"
    ▼
Design Lead → Design System Update
    │     Audita variante aprovada vs design system existente
    │     Identifica componentes/tokens/padrões novos ou alterados
    │     Atualiza design system doc (design-system.md do projeto)
    │     Se Markup conectado: faz upload do mockup + design system
    │
    │ Sub-goal Delivery é desbloqueado
    ▼
CEO delega pro CTO (com UX_SPEC.md + variant escolhida + design system atualizado)
    │
    ▼
CTO → Tech Lead
    │     Superpowers /brainstorming (lente TÉCNICA, design CONGELADO)
    │     Saída: TECHNICAL_SPEC.md (data model, APIs, state, perf, security)
    │     CTO quebra em issues granulares
    │
    ▼
Tech Lead → Developer/Founding Engineer
    │     Superpowers writing-plans (tarefas de 2-5min)
    │     Superpowers test-driven-development (red-green-refactor)
    │     Superpowers verification-before-completion
    │
    ▼
G1 (CTO): code-review + design-review + lint + coverage ≥80% + security scan
    ▼
G2 (QA): test suite, pass rate ≥90%, zero criticals
    ▼
G3 (CEO): aderência ao goal, estimate vs actual
    ▼
═══════════════════════════════════════════════════════════
  ★ BOARD APPROVAL GATE — Delivery ★
  Você aprova deploy
═══════════════════════════════════════════════════════════
    ▼
Deploy
```

### 4.2 Bugfix (path médio)

```
Board abre issue "Bug: Y" com label `bug` + severidade
    │
    ▼
CEO triagem → pula Design Lead
    │
    ▼
CTO → Tech Lead (instrução: "use systematic-debugging, sem variantes de design")
    │     Superpowers systematic-debugging (4 fases: investigação, padrão,
    │     hipótese, implementação) — review arquitetural após 3 tentativas falhas
    │
    ▼
Tech Lead → Developer
    │     TDD: escreve teste que reproduz o bug → faz passar
    │     verification-before-completion
    │
    ▼
G1 → G2 → G3 → G4 (board)
```

### 4.3 Hotfix (path expresso)

```
Board abre issue "HOTFIX: Z" com label `hotfix`, severidade P0
    │
    ▼
CEO triagem → pula Design Lead, pula CTO brainstorm extenso
    │     Cria automaticamente issue follow-up "Post-mortem + fix definitivo"
    │
    ▼
Tech Lead direto (instrução: "fix mínimo, deploy imediato")
    │     systematic-debugging em modo expresso (root cause primeiro)
    │
    ▼
Developer fix mínimo + 1 teste de regressão
    │
    ▼
G1 (smoke test apenas, sem coverage gate)
    ▼
═══════════════════════════════════════════════════════════
  ★ BOARD APPROVAL GATE ★
═══════════════════════════════════════════════════════════
    ▼
Deploy
    │
    ▼
[G2/G3 acontecem post-deploy como follow-up]
[Issue de post-mortem entra no path normal de bugfix]
```

### 4.4 Spike (path aberto)

```
Board abre goal "Spike: W" com label `spike` + timebox
    │
    ▼
CEO triagem → delega CTO em modo research
    │     CTO NÃO escreve código de produção
    │     Pode escrever protótipos descartáveis em worktree throwaway
    │
    ▼
CTO produz documento de análise
    │     Hipóteses, evidências, trade-offs, recomendação
    │
    ▼
Board revisa documento
    │     Decide: vira feature/bugfix, arquiva, ou pede mais profundidade
```

---

## 5. Goal Hierarchy (estrutura two-phase)

Para features, a estrutura no Paperclip fica:

```
Goal: "Feature: <nome>"
├── Sub-goal: "Discovery: design aprovado pelo Board"
│   ├── Issue: UX flows + AC (UX Architect)
│   ├── Issue: 3 variantes de protótipo (UI Designer)
│   └── Issue: Design review reports (Design Reviewer)
│   [completion gate: Board approval da variante]
│
└── Sub-goal: "Delivery: feature shipada"  [BLOCKED até Discovery aprovar]
    ├── Issue: data model + migrations
    ├── Issue: API endpoints
    ├── Issue: frontend components (com variant aprovada)
    ├── Issue: integration tests
    └── Issue: deploy
    [completion gate: G1-G4]
```

Bugfix/hotfix/spike são **issues simples**, sem sub-goals.

---

## 6. Quality Gates

| Gate | Owner | Checks | Aplicável em |
|---|---|---|---|
| **G1 — Implementation** | CTO | code-review, design-review, lint, coverage ≥80%, security scan | feature, bugfix, hotfix(reduzido) |
| **G2 — QA** | QA Engineer | test suite, pass rate ≥90%, zero P0/P1 bugs | feature, bugfix |
| **G3 — CEO** | CEO | aderência ao goal, estimate vs actual, missing tasks | feature, bugfix |
| **G4 — Board** | Você | aprovação humana de deploy | feature, bugfix, hotfix |

Hotfix faz G1 (smoke test) + G4 apenas. G2/G3 acontecem post-deploy como follow-up.

---

## 7. Integração com Superpowers

Superpowers é instalado **globalmente** em cada Claude Code/Codex que roda agentes:

```bash
/plugin install superpowers@claude-plugins-official
```

Por agente, configurar quais skills ativam (no AGENTS.md ou via instrução de prompt):

| Agente | Skills ativas | Skills suprimidas |
|---|---|---|
| **CEO** | nenhuma — apenas roteamento | brainstorming, TDD, debugging |
| **Design Lead** | brainstorming (UX), frontend-design | TDD, debugging, technical brainstorming |
| **UX Architect** | brainstorming (UX) | TDD, technical brainstorming |
| **UI Designer** | frontend-design, brainstorming (aesthetic axis), using-git-worktrees, subagent-driven-development | TDD, systematic-debugging |
| **Design Reviewer** | frontend-design | todas as outras |
| **CTO** | brainstorming (técnica), writing-plans | UX brainstorming |
| **Tech Lead** | writing-plans, systematic-debugging, TDD, verification-before-completion | UX brainstorming |
| **Developer** | TDD, subagent-driven-development, verification-before-completion, using-git-worktrees | brainstorming, planning |
| **QA Engineer** | systematic-debugging, verification-before-completion | todas as outras |

**Skills × Agente (foco nas skills de execução estética e de worktree):**

| skill | UI Designer | Design Lead | Design Reviewer | UX Architect |
|---|:-:|:-:|:-:|:-:|
| frontend-design | ✅ | ✅ | ✅ | — |
| brainstorming | ✅ | ✅ | — | ✅ |
| using-git-worktrees | ✅ | — | — | — |
| subagent-driven-development | ✅ | — | — | — |

- **UI Designer:** `frontend-design` é o core da execução estética — tipografia, paleta, motion, composição distinta por variante.
- **Design Lead:** usa `frontend-design` para avaliar e orientar direções estéticas durante briefing.
- **Design Reviewer:** usa `frontend-design` como referência para avaliar qualidade de execução visual das variantes.

**Como suprimir skill:** instrução explícita no AGENTS.md do agente, tipo: `"Skip /clarify and /brainstorming for UX. Design is FROZEN — you receive specs from CTO."` Superpowers respeita override no prompt.

---

## 8. Integração com design-review

**Componentes:**

1. **Subagent** especializado (`@agent-design-reviewer`) com prompt e ferramentas pré-configuradas
2. **Slash command** `/design-review` que analisa git diffs automaticamente
3. **CLAUDE.md memory** com design system + brand guidelines do projeto
4. **Playwright MCP server** para abrir browser real, navegar, screenshot

**Setup:**

```bash
# Playwright MCP
claude mcp add playwright npx @playwright/mcp@latest

# Skill design-review (de OneRedOak/claude-code-workflows ou paperclipai/companies)
git clone https://github.com/OneRedOak/claude-code-workflows
cp -r claude-code-workflows/design-review ~/.claude/skills/

# Design system no CLAUDE.md do projeto
echo "## Design System
- Cores primárias: ...
- Typography: ...
- Spacing: 4/8/16/32
- Acessibilidade: WCAG AA+
- Inspirações: Linear, Stripe, Vercel" >> CLAUDE.md
```

**Quando invocar:**

| Momento | Quem invoca | Output esperado |
|---|---|---|
| Após cada variante gerada pelo UI Designer | Design Reviewer | Relatório estruturado + screenshots, severidade por finding |
| No G1 do delivery (feature com mudança visual) | CTO | Verificação final que UI implementada == variante aprovada |
| Pós-bugfix em UI | Tech Lead | Garante que fix não quebrou outras telas |

**Critérios avaliados:** hierarquia visual, acessibilidade WCAG AA+, responsividade (mobile/tablet/desktop), estados de interação, consistência com design system declarado.

---

## 9. Modelos por agente

Otimização custo/capacidade. Ajuste conforme orçamento.

| Agente | Modelo recomendado | Justificativa |
|---|---|---|
| CEO | Opus 4.6 | Roteamento estratégico, aprovações, julgamento |
| Design Lead | Sonnet 4.6 | Coordenação, sem geração pesada |
| UX Architect | Sonnet 4.6 | Brainstorm conceitual |
| UI Designer | Sonnet 4.6 | Geração de código React (subagents paralelos) |
| Design Reviewer | Haiku 4.5 | Avalia contra checklist, não precisa raciocínio profundo |
| CTO | Opus 4.6 | Decisões arquiteturais, gate G1 |
| Tech Lead | Sonnet 4.6 | Planning + delegação |
| Developer | Sonnet 4.6 | Execução TDD, geração de código |
| QA Engineer | Sonnet 4.6 | Validação, geração de testes |

**Budget caps:** definir teto mensal por agente no Paperclip. <50% atingido = warning. 100% = pause automático. Sobrecusto pausa agentes e cancela work em fila.

---

## 10. Worktree Strategy

Paperclip nativamente provisiona worktrees isolados via `paperclipai worktree:make` — cada um com seu banco, secrets e git hooks.

**Uso por agente:**

- **UI Designer:** 3 worktrees paralelos (variant-a, -b, -c) por feature
- **Developer:** 1 worktree por issue/feature
- **CTO em modo spike:** 1 worktree throwaway (não merge na main)
- **Hotfix:** worktree dedicado ao branch de hotfix

**Naming convention sugerida:**

```
worktrees/
├── feature-<slug>-discovery-variant-a/
├── feature-<slug>-discovery-variant-b/
├── feature-<slug>-discovery-variant-c/
├── feature-<slug>-delivery/
├── bugfix-<slug>/
├── hotfix-<slug>/
└── spike-<slug>/
```

---

## 11. Board Approval Gates

Pontos de approval **obrigatório** humano (sem auto-approve possível):

1. **Discovery Gate** — após UI Designer entregar 3 variantes + Design Reviewer publicar relatórios. Você escolhe variante ou pede iteração.
2. **G4 Delivery** — antes de deploy em produção (feature, bugfix, hotfix).
3. **Hire Approval** — se algum agente propuser contratar novo agente (default Paperclip).
4. **Budget Override** — se agente bater hard cap de budget e CEO pedir override.

Approvals adicionais opcionais (configuráveis):

- Spike conclusion (decidir se vira feature)
- Architecture decisions (ADRs propostas pelo CTO)

---

## 12. Considerações operacionais

### 12.1 Custo
- Discovery phase multiplica tokens (3 variantes em paralelo + design-review com Playwright)
- Mitigação: Sonnet/Haiku onde possível, budget cap por agente, label `trivial` pula discovery
- Estimativa: feature completa = 3-5× custo de feature direto-pro-código no Dev Agency padrão

### 12.2 Tempo de ciclo
- Feature completa: 2-5 heartbeats de cada agente envolvido (típico: 1-2 dias se heartbeat = 2-4h)
- Bugfix: 1-3 heartbeats
- Hotfix: 1 heartbeat (urgente, pode override schedule)
- Spike: timeboxed pelo Board

### 12.3 Quando NÃO usar este pipeline completo
- **Hotfix:** path expresso, pula tudo
- **Mudança trivial** (<50 linhas, 1 tela, 0 lógica nova): use label `trivial` → pula discovery
- **One-off scripts:** abre como spike e roda manualmente
- **Refatoração interna sem mudança de UX:** pula discovery, vai como bugfix técnico

### 12.4 Anti-patterns
- ❌ Board falando direto com Tech Lead ou Developer (quebra cadeia de comando)
- ❌ Implementação prescrita no ticket original ("use useState pra X") — sufoca brainstorm
- ❌ Múltiplos tipos no mesmo ticket ("fix bug Y e adiciona feature Z")
- ❌ Feature sem AC explícito → CEO devolve pedindo critério
- ❌ Hotfix sem follow-up de post-mortem → acumula dívida

### 12.5 Riscos conhecidos
- **False success** (Paperclip issue #1997): agente marca done sem entregar. Mitigação: `verification-before-completion` do Superpowers + G2 do QA.
- **Design Engineer redesigning** durante Delivery: mitigação via instrução explícita "design CONGELADO" no Tech Lead/CTO.
- **Skills com falhas de segurança** (estudo Snyk ToxicSkills): só instalar skills de fontes auditadas (Anthropic oficial, OneRedOak, paperclipai/companies, obra/superpowers).

---

## 13. Setup Checklist

### 13.1 Infraestrutura
- [ ] Paperclip instalado: `npx paperclipai onboard --yes`
- [ ] Node.js 20+ e pnpm 9.15+
- [ ] PostgreSQL embedded ou external configurado
- [ ] (Produção) VPS rodando 24/7 pra heartbeats

### 13.2 Agent runtimes
- [ ] Claude Code instalado em cada container/máquina de agente
- [ ] Superpowers instalado: `/plugin install superpowers@claude-plugins-official`
- [ ] Playwright MCP: `claude mcp add playwright npx @playwright/mcp@latest`

### 13.3 Skills do projeto
- [ ] design-review skill clonada de OneRedOak/claude-code-workflows
- [ ] Skills do paperclipai/companies importadas (architecture-decision, code-review, design-system, gate-check, prototype, etc.)

### 13.4 Configuração Paperclip
- [ ] Company criada via interactive setup
- [ ] CEO contratado e org chart construído (manual ou via prompt "monte um time")
- [ ] Modelos atribuídos por agente (ver tabela seção 9)
- [ ] Budget caps configurados por agente
- [ ] Heartbeat intervals definidos (CEO 30min, executores 2-4h)

### 13.5 Configuração de cada agente
- [ ] AGENTS.md com role + responsabilidades + skills supressas
- [ ] SOUL.md com personalidade/postura
- [ ] HEARTBEAT.md com checklist de wake-up (ver `agents/ceo/HEARTBEAT.md` para CEO)
- [ ] memory/ inicializado

### 13.6 CLAUDE.md do projeto
- [ ] Design system declarado (cores, typography, spacing, a11y)
- [ ] Inspirações (Linear, Stripe, etc.)
- [ ] Stack técnica
- [ ] Convenções de código

### 13.7 Templates de issue/goal
- [ ] Template `feature` configurado (ver `templates/feature.md`)
- [ ] Template `bugfix` configurado (ver `templates/bugfix.md`)
- [ ] Template `hotfix` configurado (ver `templates/hotfix.md`)
- [ ] Template `spike` configurado (ver `templates/spike.md`)

### 13.8 Approval gates
- [ ] Discovery Gate: policy "issues type=design-variant-selection require Board approval"
- [ ] G4 Delivery: policy "issues type=deploy require Board approval"
- [ ] Hire approval: default Paperclip (não desabilitar)
- [ ] Budget override: requires Board

### 13.9 Validação
- [ ] Smoke test: abrir 1 ticket de cada tipo, verificar roteamento correto
- [ ] Audit log: confirmar que toda decisão tem heartbeat ID
- [ ] Worktree isolation: confirmar que variantes em paralelo não conflitam
- [ ] Budget enforcement: simular hit em cap, confirmar pause

---

## 14. Markup Integration

Markup é um servidor de review para mockups HTML. Quando um projeto Paperclip tem conexão com um Markup server, o orquestrador publica mockups e design system automaticamente para review pelo Board.

### Detecção de conexão

O orquestrador verifica se existem as env vars `MARKUP_URL` e `MARKUP_TOKEN`. Se ambas estiverem presentes, a integração está ativa. Se não, toda lógica de upload é silenciosamente ignorada.

### Estrutura no Markup

Cada projeto Paperclip tem um projeto correspondente no Markup, organizado em pastas:

```
Projeto: <project-name> (slug: <project-slug>)
├── Design System/
│   └── design-system (HTML render do design system doc)
└── Feature Mockups/
    ├── <feature-slug>-variant-a
    ├── <feature-slug>-variant-b
    └── ...
```

### Upload format

Markup aceita mockups como zip files via multipart POST:

```bash
zip mockup.zip index.html
curl -X POST "https://${MARKUP_URL}/api/mockups" \
  -H "Authorization: Bearer ${MARKUP_TOKEN}" \
  -F "name=<display-name>" \
  -F "slug=<url-slug>" \
  -F "build=@mockup.zip;type=application/zip"
```

### Quem faz upload

| Momento | Agente | O que sobe |
|---|---|---|
| Após gerar variantes | UI Designer | Cada variante como mockup separado na pasta "Feature Mockups" |
| Após design system update | Design Lead | Design system atualizado na pasta "Design System" |
| Após Board escolher variante | Design Lead | Variante final re-publicada com tag de aprovação |

### Fallback

Se a API de projetos/pastas não estiver disponível no Markup server, upload acontece na raiz (sem organização em pastas). Se o upload falhar, o agente loga o erro e continua — Markup é complementar, não bloqueante.

---

## 15. Referências

- **Paperclip docs:** https://github.com/paperclipai/paperclip
- **Clipmart templates:** https://www.clipmart.ai/
- **Superpowers:** https://github.com/obra/superpowers
- **design-review:** https://github.com/OneRedOak/claude-code-workflows/tree/main/design-review
- **paperclipai/companies (skills):** https://github.com/paperclipai/companies
- **Anthropic skills oficiais:** https://github.com/anthropics/skills

---

*Esta spec é viva. Atualize conforme aprende o que funciona no seu contexto. Versionar em git junto com a configuração do Paperclip.*
