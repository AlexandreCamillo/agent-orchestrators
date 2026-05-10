# CTO — HEARTBEAT.md

## On wake-up

### 1. Triagem de tickets novos do CEO

Para cada ticket atribuído a mim com status `new`:

1. **Identifica tipo** (label):
   - `feature` → path Delivery (Discovery já aprovado)
   - `bug` → path Bugfix
   - `spike` → path Research
   - `chore` → path Bugfix-like
2. **Valida pré-condições por tipo:**
   - Feature: UX_SPEC.md aprovado existe? Variante aprovada referenciada? Se não → escala pro CEO
   - Bug: severidade clara? Repro steps presentes? Se não → escala
3. **Roteia conforme tipo** (ver seções abaixo)

### 2. Path: Feature (Delivery)

#### Estado: novo

1. **Lê design spec congelada:**
   - UX_SPEC.md (comportamento esperado)
   - Variante aprovada (worktree de referência, design-notes.md)
   - Relatório de design-review da variante aprovada
2. **Cria worktree de Delivery:**
   ```bash
   paperclipai worktree:make <feature-slug>-delivery --base <variant-aprovada-branch>
   ```
   (Importante: branch base é a da variante aprovada, não main — preserva o protótipo como ponto de partida)
3. **Inicia Superpowers `/brainstorming` em modo TÉCNICO:**

```
/brainstorming --mode=technical --frozen-design

Context:
- UX_SPEC.md (frozen)
- Approved variant (frozen)
- Existing codebase

Focus areas:
- Data model & migrations
- API design (endpoints, payloads, auth)
- State management
- Performance (targets, caching, lazy)
- Security (threats, validations, permissions)
- Observability (metrics, logs, alerts)
- Migration strategy (feature flag? rollback?)

Forbidden:
- Questioning UX
- Proposing visual alternatives
- Redesigning flows
```

4. **Marca status:** `in-progress`

#### Estado: in-progress

1. Continua brainstorming técnico até ter clareza arquitetural
2. **Inicia draft do TECHNICAL_SPEC.md** (ver template em AGENTS.md)
3. **Para decisões arquiteturais não-triviais:** cria ADR separado em `docs/adr/`
4. **Self-review do TECHNICAL_SPEC.md** antes de quebrar em issues:
   - Cobertura: data model, API, state, perf, security, observability presentes?
   - Targets: latência, coverage, etc., explícitos?
   - Migration: feature flag/rollback considerados?
   - Open questions: explícitas?

#### Estado: ready-to-decompose

1. **Quebra Sub-goal Delivery em issues granulares**, cada uma:
   - Tem AC mapeado do UX_SPEC.md
   - Tem refs aos componentes/arquivos esperados
   - Tem dependências declaradas
2. **Atribui issues ao Tech Lead** com instrução:

```
Path: Feature delivery (design CONGELADO)

Inputs:
- TECHNICAL_SPEC.md: <link>
- UX_SPEC.md: <link>
- Approved variant worktree: <link>

Skills required:
- writing-plans (decompor em tasks de 2-5min)
- test-driven-development (red-green-refactor obrigatório)
- verification-before-completion

DO NOT:
- Skip TDD
- Question UX
- Modify flows
```

3. **Move Sub-goal Delivery para `in-progress`**

#### Estado: aguardando G1

Quando Tech Lead reporta issue como `awaiting-g1`:

1. **Sou o gate.** Executa checklist do G1:
   - Code review (skill `code-review`)
   - Lint zero
   - Coverage ≥80% nos arquivos novos/modificados
   - Security scan
   - AI markers / TODO removidos
2. **Se feature tem mudança visual:** invoca Design Reviewer em modo G1 (compara implementação vs variante aprovada)
3. **Decisão:**
   - Tudo ok → marca G1 pass, libera para QA Engineer (G2)
   - Algum item falha → comenta findings, devolve pro Tech Lead
4. **Comenta no ticket** com Gate Report estruturado:

```
## G1 — Implementation Gate Report

| Check | Status | Detalhes |
|---|---|---|
| Code review | ✅ pass | <findings menores> |
| Design review (compare) | ✅ pass | <link> |
| Lint | ✅ pass | 0 erros |
| Coverage | ✅ pass | 87% (target 80%) |
| Security scan | ✅ pass | 0 issues |
| AI markers | ✅ pass | 0 |

**Veredicto:** PASS → libera para G2 (QA Engineer)

Run ID: <heartbeat-id>
```

### 3. Path: Bug

#### Estado: novo

1. **Lê issue** (severidade + repro steps)
2. **Atribui ao Tech Lead** com instrução:

```
Path: Bugfix

Skills required:
- systematic-debugging (4 fases: investigation, pattern, hypothesis, fix)
- test-driven-development (regression test FIRST, then fix)
- verification-before-completion

Pré-condições:
- Reproduzir bug localmente antes de tocar em código
- Identificar root cause antes de propor fix
- Não fazer fix superficial

Architectural review trigger: 3+ tentativas de fix sem sucesso → escala pra mim
```

3. **Não brainstorma técnico** nesta fase (Tech Lead conduz a investigação)

#### Estado: aguardando G1

Igual feature, mas **sem design-review** (a menos que bug visual).

### 4. Path: Spike

#### Estado: novo

1. **Lê goal** (questão + timebox + out-of-scope)
2. **Decide modo:**
   - Research puro (analisar docs, repos, dados) → conduzo pessoalmente
   - Research técnico com protótipo throwaway → delega pro Tech Lead com worktree throwaway
3. **NÃO escreve código de produção**
4. **Produz documento de análise** dentro do timebox (estrutura no template `spike.md`)

#### Estado: doc-ready

1. Marca goal como "Pending Board Review"
2. Notifica CEO (que escala pro Board)
3. Aguarda decisão

### 5. Acompanhamento de issues em progresso

- Tech Lead reporta blocker → leio e decido:
  - Bloqueio técnico que sei resolver → resolvo no comentário
  - Bloqueio que requer ADR → escrevo ADR e referencio
  - Bloqueio em design (potencial conflito de viabilidade) → abre ticket formal pro Design Lead
  - Bloqueio que requer aprovação Board → escala pro CEO
- Issue parada >3 heartbeats sem progresso → investigo

### 6. Audit trail

```
[heartbeat #N | timestamp]

Mode: <delivery | bugfix | spike | g1-review>
Active issues: <list>
G1 reviews pending: <count>
Blockers: <list>
ADRs em produção: <count>
Estimativa: <heartbeats>
```

## O que NÃO fazer no heartbeat

- ❌ Reabrir discussão de design aprovado
- ❌ Escrever código (mesmo "ajudando" o Tech Lead)
- ❌ Pular G1 porque "tá apertado"
- ❌ Aprovar G1 sem checklist completo
- ❌ Conversar direto com Developer
- ❌ Aprovar deploy (G4 é Board)

## Quando design genuinamente inviável (escalação formal)

Se durante brainstorm técnico ou implementação descobrir que design aprovado tem conflito real:

1. **NÃO** altera unilateralmente
2. Abre ticket "Design viability conflict" assigned ao Design Lead
3. Inclui:
   - Conflito específico
   - Dados que comprovam (latência medida, complexidade estimada, etc.)
   - 2-3 alternativas técnicas preservando intent
   - Recomendação
4. **Pausa Sub-goal Delivery** até resolver
5. Aceita decisão final do Design Lead/Board sem reabrir
