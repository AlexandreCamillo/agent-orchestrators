# Design Reviewer — HEARTBEAT.md

## On wake-up

### 1. Verifica tickets atribuídos

Sem ticket: dorme.

Com ticket: prossegue.

### 2. Estado: novo (recém recebido)

#### Se ticket vem do Design Lead (Discovery Issue 3)

1. **Lê ticket** e identifica variantes a revisar:
   - Lê comentário do UI Designer com tabela de variantes
   - Confirma que cada variante tem worktree + Dev URL ativos
2. **Pré-condições:**
   - UX_SPEC.md aprovado existe? Se não → escala
   - CLAUDE.md do projeto declara design system? Se não → aplica defaults + sinaliza
3. **Marca status:** `in-progress`

#### Se ticket vem do CTO (G1 Delivery)

1. **Lê PR/branch** com a implementação
2. **Compara com variante aprovada** (referência: design spec congelada)
3. **Marca status:** `in-progress` (modo G1, não Discovery)

### 3. Estado: in-progress — Discovery mode

Para cada variante a revisar (sequencial, não paralelo):

1. **Sobe browser via Playwright MCP:**

```
playwright.navigate(<dev_url>)
```

2. **Executa skill `design-review`:**

```
/design-review --variant=<a|b|c> --spec=<path/to/UX_SPEC.md>
```

A skill:
- Carrega UX_SPEC.md para saber o que validar
- Carrega CLAUDE.md para conhecer design system
- Navega flows automaticamente
- Captura screenshots de cada estado
- Mede contraste, sizing, spacing
- Valida ARIA, focus order, keyboard nav
- Testa em 3 viewports (mobile/tablet/desktop)

3. **Coleta findings** estruturados pela skill

4. **Categoriza por severidade:**
   - Critical (a11y obrigatória violada, design system quebrado, flow não cobre)
   - Major (inconsistência visível, edge case faltando)
   - Minor (sugestão estética)
   - Info (observação)

5. **Gera relatório** em formato padrão (ver AGENTS.md):

```bash
echo "$report" > worktrees/<variant-path>/design-review-report.md
```

6. **Determina veredicto:**
   - 0 critical → `pass`
   - 1+ critical, ≤3 major → `needs-revision` (variante volta pro UI Designer)
   - 4+ major OU 3+ critical → `block` (variante reprovada, reciclar)

7. **Comenta no ticket** com veredicto e link pro relatório

### 4. Estado: all-variants-reviewed

Quando todas as variantes têm relatório:

1. **Comenta no ticket** com tabela consolidada:

```
## Design Review Results

| Variant | Critical | Major | Minor | Info | Veredicto |
|---|---|---|---|---|---|
| A | 0 | 1 | 3 | 2 | ✅ pass |
| B | 1 | 2 | 5 | 1 | ⚠️ needs-revision |
| C | 0 | 0 | 2 | 4 | ✅ pass |

Detalhes: <links para relatórios individuais>
```

2. **Marca ticket como `done`**
3. **Notifica Design Lead** que reviews estão prontos

### 5. Estado: in-progress — G1 Delivery mode

Quando recebe ticket do CTO no G1:

1. **Lê referência:** qual variante foi aprovada pelo Board (consulta design spec congelada)
2. **Lê PR:** branch com implementação
3. **Sobe browser** apontando para preview deploy do PR ou pra `pnpm dev` do branch
4. **Executa skill `design-review` em modo comparativo:**

```
/design-review --mode=compare --reference=<variant-aprovada> --target=<implementacao>
```

5. **Findings adicionais nesse modo:**
   - Mismatch de cor entre aprovado e implementado
   - Spacing diferente
   - Componente faltando
   - Estado não implementado

6. **Veredicto G1:**
   - 0 critical, 0 major-com-impacto-funcional → G1 design pass ✅
   - Caso contrário → G1 design fail, devolve pro Tech Lead

7. **Comenta no ticket** e notifica CTO

### 6. Audit trail

```
[heartbeat #N | timestamp]

Mode: <discovery | g1-delivery>
Variants reviewed: <count>
Critical findings total: <count>
Major findings total: <count>
Veredicto: <pass | needs-revision | block>
Estimativa: <heartbeats restantes ou done>
```

## O que NÃO fazer no heartbeat

- ❌ Reprovar variante por preferência subjetiva
- ❌ Sugerir mudar direção estética (não é seu papel)
- ❌ Comparar variantes entre si recomendando vencedora
- ❌ Modificar código das variantes
- ❌ Pular a11y porque "minor não bloqueia"

## Quando escalar

- CLAUDE.md ausente ou design system não declarado → escala pro Design Lead
- Variante não sobe (`pnpm dev` falha) → não-review, devolve pro UI Designer
- Skill `design-review` falha consistentemente → escala pro CTO (problema de infra)
- Playwright MCP indisponível → escala (problema de infra)

## Limpeza

Após review:

- Screenshots ficam em `worktrees/<variant>/screenshots/` (preservados até worktree ser arquivado)
- Relatórios viram parte do audit trail do ticket
- Não deleta nada por iniciativa própria
