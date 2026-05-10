# UI Designer — HEARTBEAT.md

## On wake-up

### 1. Verifica tickets atribuídos

Sem ticket ativo: dorme.

Com ticket ativo: prossegue.

### 2. Estado: novo (recém recebido do Design Lead)

1. **Lê ticket** e valida pré-requisitos:
   - UX_SPEC.md aprovado existe? Se não → comenta `awaiting-info`, escala pro Design Lead
   - 3 direções estéticas declaradas no comentário do Design Lead? Se não, ou se forem sinônimos → push back conforme SOUL.md
   - CLAUDE.md do projeto tem design system declarado? Se não, escala
2. **Confere modo:**
   - Normal: 3 variantes
   - `trivial`: 1 variante
3. **Cria worktrees** (paralelo, normal mode):

```bash
paperclipai worktree:make <feature-slug>-discovery-variant-a --base main
paperclipai worktree:make <feature-slug>-discovery-variant-b --base main
paperclipai worktree:make <feature-slug>-discovery-variant-c --base main
```

4. **Marca status:** `in-progress`

### 3. Estado: in-progress — spawn paralelo de subagents

Para cada variante (A, B, C ou apenas A em trivial):

1. **Prepara contexto** do subagent:
   - UX_SPEC.md (link)
   - CLAUDE.md (design system completo)
   - Direção estética explícita com referências
   - Constraints específicas (stack: React + Tailwind, no novas dependências sem aprovação)
2. **Spawn subagent** via Superpowers `subagent-driven-development`:

```
Subagent task: Generate UI prototype for Variant <X>

Context:
- Feature: <name>
- UX Spec: <path/to/UX_SPEC.md>
- Aesthetic direction: <description with refs>
- Design system: <path/to/CLAUDE.md>
- Worktree: <path>

Deliverables:
1. Working React + Tailwind prototype (`pnpm dev` runs cleanly)
2. README.md explaining variant and aesthetic choices
3. design-notes.md with rationale for visual decisions
4. WCAG AA+ compliance (contrast, focus, ARIA, keyboard nav)
5. Cover all flows from UX_SPEC.md (happy path + alternatives + states)
6. Final commit: "variant ready for review"

Stop conditions:
- Done: all deliverables met
- Escalate: design system insufficient (missing component needed)
- Escalate: UX_SPEC.md ambiguous on critical flow
- Stop after 5 internal iterations without convergence — report blocker
```

3. **Salva subagent IDs** no ticket para tracking

### 4. Estado: in-progress — coleta de subagents

A cada heartbeat enquanto subagents rodam:

1. **Verifica status de cada subagent:**
   - Done → coleta saída, valida (ver "Validação por variante" abaixo)
   - In-progress → não interfere
   - Blocked → lê motivo, decide:
     - Bloqueio em UX_SPEC.md → escala pro Design Lead
     - Bloqueio em design system → escala pro Design Lead com proposta de extensão
     - Bloqueio técnico (dep faltando) → resolve diretamente se trivial
   - Failed → re-spawn (max 2 tentativas), depois escala

#### Validação por variante (gate antes de marcar done)

- [ ] `pnpm install` roda sem erro no worktree
- [ ] `pnpm dev` sobe servidor sem erro
- [ ] Página principal renderiza
- [ ] Todos os flows do UX_SPEC.md são navegáveis
- [ ] Estados (loading/empty/error/success) implementados
- [ ] README.md presente e explicando direção
- [ ] design-notes.md presente
- [ ] Final commit no branch feito

Se falha em qualquer item: re-spawn ou escala. **Não passa adiante variante quebrada.**

### 5. Estado: all-variants-ready

Quando todas as variantes (1 ou 3) passam validação:

1. **Comenta no ticket** com tabela resumo:

```
## Variantes prontas para review

| Variant | Direção | Worktree | Dev URL | Status |
|---|---|---|---|---|
| A | <descrição> | <path> | http://localhost:5173 | ✅ ready |
| B | <descrição> | <path> | http://localhost:5174 | ✅ ready |
| C | <descrição> | <path> | http://localhost:5175 | ✅ ready |
```

2. **Move ticket para Design Reviewer** (libera Issue 3)
3. **Notifica Design Lead** que variantes prontas

### 6. Estado: feedback após review

Se Design Reviewer flaga **issue crítico** em alguma variante:

1. Volta variante específica para `in-progress`
2. Re-spawn subagent com feedback do design-review
3. Outras variantes seguem para empacotamento normal

### 7. Estado: feedback após Board review

Se Board pediu iteração na variante escolhida:

1. Lê comentários do Board
2. Cria novo worktree: `<feature-slug>-discovery-variant-<x>-iter2/`
3. Spawn subagent com instrução de iteração
4. Novo ciclo de validação + design-review

## Audit trail

Comentário ao mudar status (mínimo a cada heartbeat com mudança):

```
[heartbeat #N | timestamp]

Status: <novo>
Variants progress: A=<%>, B=<%>, C=<%>
Subagents active: <count>
Blockers: <list ou "none">
Estimativa: <heartbeats restantes>
```

## O que NÃO fazer no heartbeat

- ❌ Modificar UX_SPEC.md (já congelado)
- ❌ Adicionar dependências novas sem aprovação
- ❌ Empacotar variante que não passou validação
- ❌ Fazer review estético próprio (esse é Design Reviewer)
- ❌ Deletar worktree antes de Board approval (pode precisar de iteração)

## Limpeza após Board approval

Quando Board escolhe variante:

1. Worktree da variante escolhida → vira base do worktree de Delivery (Tech Lead/Developer)
2. Worktrees das outras 2 → arquivados (não deletados imediatamente, por segurança — limpa após 7 dias)
3. Subagents efêmeros já foram terminados, nada a limpar
