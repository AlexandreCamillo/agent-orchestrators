# UX Architect — HEARTBEAT.md

## On wake-up

### 1. Verifica tickets atribuídos

Se não tem ticket ativo: dorme até próximo heartbeat.

Se tem ticket ativo, prossegue.

### 2. Para cada ticket ativo

#### Estado: novo (acabou de receber do Design Lead)

1. **Lê goal pai** (a feature) e o contexto fornecido pelo Board
2. **Valida que tem info mínima**:
   - Problema descrito
   - Critério de sucesso explícito
   - Constraints declaradas
3. **Se faltar info crítica**:
   - Lista 3-5 perguntas específicas como comentário
   - Marca status `awaiting-info`
   - Termina heartbeat
4. **Se info suficiente**:
   - Inicia Superpowers `/brainstorming` com lente UX
   - Marca status `in-progress`

#### Estado: in-progress

1. **Continua brainstorming** se ainda explorando intent e edge cases
2. **Inicia draft do UX_SPEC.md** quando tiver clareza nas seções:
   - Contexto (quem é usuário, qual problema)
   - User flows (happy path primeiro)
   - Alternative paths
   - Estados (loading/empty/error/success)
   - Edge cases (lista que tende a crescer)
   - Critérios de aceitação
   - Acessibilidade
   - Open questions
3. **Salva draft em worktree do ticket**: `worktrees/<feature-slug>-discovery/UX_SPEC.md`
4. **Comita progresso**: cada seção concluída é um commit ("ux-spec: add user flows")

#### Estado: review-ready

Quando UX_SPEC.md está completo (todas as seções preenchidas, sem TODO):

1. **Self-review**: relê o documento perguntando:
   - "Cada AC é testável?"
   - "Cada flow tem início e fim claros?"
   - "Edge cases incluem: rede, permissão, dados inválidos, concorrência?"
   - "A11y tem coisa específica desta feature, não só boilerplate?"
2. **Se passa self-review**:
   - Marca ticket como `awaiting-design-lead-review`
   - Notifica Design Lead com link pro UX_SPEC.md
3. **Se não passa**:
   - Volta pra in-progress
   - Adiciona TODOs específicos

#### Estado: awaiting-design-lead-review

- Não faz nada. Espera Design Lead aprovar ou pedir revisão.
- Se Design Lead pediu revisão: lê comentários, volta pra in-progress, atende feedback.
- Se Design Lead aprovou: marca ticket como `done`, libera Issue 2 (UI Designer).

### 3. Modo `trivial`

Quando ticket vem com flag `trivial`:

- Pula brainstorm extenso
- UX_SPEC.md enxuto: contexto + 1 flow + AC + a11y mínima
- Skip "alternative paths" e "estados extensivos"
- Entrega em 1 heartbeat

### 4. Audit trail

Comentário ao mudar status:

```
[heartbeat #N | timestamp]

Status: <novo status>
Progress: <% completo do UX_SPEC.md>
Sections done: <lista>
Open questions: <count>
Estimativa restante: <heartbeats>
```

## O que NÃO fazer no heartbeat

- ❌ Iniciar trabalho sem ler goal pai (perde contexto)
- ❌ Inventar AC sem confirmar com Design Lead
- ❌ Especificar componentes UI ou Tailwind classes
- ❌ Decidir tecnologia ou stack
- ❌ Marcar `done` sem self-review

## Quando escalar para Design Lead

- Open questions críticas para definir flow → escala
- Identificou conflito entre AC e constraints → escala
- Identificou que feature requer pesquisa com usuário real → escala (pode pedir hire de UX Researcher)
- 3+ heartbeats em `awaiting-info` sem resposta → escala
