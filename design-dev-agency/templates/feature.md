# Template: Feature

> Use para nova funcionalidade ou expansão de funcionalidade existente. Dispara o pipeline completo (Discovery → Board approval → Delivery).
>
> Para mudanças triviais (<50 linhas, 1 tela, 0 lógica nova), adicione também a label `trivial` para path compactado.

---

**Tipo:** Goal
**Labels:** `feature` (+ opcional: `trivial`)
**Atribuído a:** CEO
**Prioridade:** [low | normal | high]

---

## Problema

<!-- Qual problema do usuário ou do negócio isso resolve? Foco no porquê, não no como. -->



## Critério de sucesso

<!-- Como saberemos que esta feature está pronta? Lista observável e testável. -->

- [ ]
- [ ]
- [ ]

## Constraints

<!-- O que limita a solução? Orçamento, prazo, stack obrigatória, integrações existentes, etc. -->

-

## Out of scope

<!-- O que explicitamente NÃO faz parte desta feature, mesmo que pareça relacionado. Ajuda a evitar scope creep. -->

-

## Inspirações / referências (opcional)

<!-- Links pra produtos, screenshots, artigos. Os agentes leem. -->

-

## Anexos (opcional)

<!-- Mockups iniciais, dados de pesquisa, conversas com usuários. -->

-

---

### O que vai acontecer depois que eu submeter

1. CEO faz triagem e cria estrutura de Goals (Discovery + Delivery)
2. Design Lead recebe Discovery → UX Architect produz UX_SPEC.md
3. UI Designer gera 3 variantes em paralelo (worktrees isolados)
4. Design Reviewer roda design-review (Playwright + screenshots) em cada
5. **★ Board Approval Gate ★** — você escolhe variante ou pede iteração
6. CTO recebe Delivery com design CONGELADO → Tech Lead → Developer
7. Quality Gates G1 (CTO) → G2 (QA) → G3 (CEO)
8. **★ Board Approval Gate ★ G4** — você aprova deploy

**Estimativa:** 3-7 dias de heartbeats (típico para heartbeats de 2-4h)

**Próximo input que precisarei de você:** escolha de variante após Discovery
