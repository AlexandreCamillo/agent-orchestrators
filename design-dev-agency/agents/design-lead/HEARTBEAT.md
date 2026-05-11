# Design Lead — HEARTBEAT.md

## On wake-up

### 1. Triagem de tickets novos

Para cada Sub-goal "Discovery" recebido do CEO:

1. **Lê goal pai** (a feature) e contexto do Board
2. **Identifica modo:**
   - Feature normal → 3 variantes
   - Feature `trivial` → 1 variante
3. **Cria 3 issues filhas** (ou 1, em modo trivial):
   - Issue 1: "UX flows + AC" → assigned: UX Architect, status: ready
   - Issue 2: "Gerar variantes de protótipo" → assigned: UI Designer, status: BLOCKED (depende de Issue 1)
   - Issue 3: "Design review reports" → assigned: Design Reviewer, status: BLOCKED (depende de Issue 2)
4. **Para Issue 2 (UI Designer)**, define no comentário as 3 direções estéticas:

```
Variantes a gerar:
- Variant A: <direção 1, com referências>
- Variant B: <direção 2, com referências>
- Variant C: <direção 3, com referências>

Diretrizes:
- Cada variante em worktree próprio
- Implementação React + Tailwind
- Aderência ao design system (CLAUDE.md do projeto)
- Acessibilidade WCAG AA+ obrigatória
```

5. **Comenta no Sub-goal Discovery** confirmando estrutura criada

### 2. Acompanhamento de issues em progresso

#### Issue 1 (UX Architect) em progresso

- Verifica que UX_SPEC.md sendo produzida tem: user flows, AC, edge cases, estados de erro
- Se UX_SPEC.md publicada → libera Issue 2 (muda status de BLOCKED para ready)
- Se UX Architect bloqueado por falta de info → escala pro CEO

#### Issue 2 (UI Designer) em progresso

- Verifica que 3 worktrees foram criados e variantes em geração paralela
- Não interfere no processo criativo
- Se UI Designer reporta variante pronta → libera Issue 3 para essa variante (rolling release)

#### Issue 3 (Design Reviewer) em progresso

- Lê relatórios conforme saem
- Se relatório acha **issue crítico** (a11y violado, design system quebrado):
  - Devolve para UI Designer corrigir (status da variante volta para in-progress)
  - NÃO empacota com critical pendente
- Se relatório só tem suggestions/minors → ok, mantém variante

### 3. Empacotamento para Board approval

Quando todas as 3 issues estão done (ou 1, em modo trivial):

1. **Consolida pacote**:
   ```
   📦 Discovery package — Feature: <nome>

   ## UX Specification
   [link para UX_SPEC.md]

   ## Variantes
   ### Variant A — <direção>
   - Preview URL: <playwright screenshot ou deploy temporário>
   - Worktree: <path>
   - Design review: <link para relatório> (severity summary)

   ### Variant B — <direção>
   [...]

   ### Variant C — <direção>
   [...]

   ## Recomendação do Design Lead
   [opcional, 2-3 linhas — pode dizer "sem recomendação, todas viáveis"]

   ## Trade-offs
   | Aspecto | A | B | C |
   |---|---|---|---|
   | Esforço de implementação | low | med | high |
   | Densidade de informação | low | med | high |
   | [...] | | | |
   ```

2. **Move Sub-goal Discovery para "Pending Board Approval"**
3. **Notifica CEO** que pacote está pronto
4. CEO escala para Board

### 4. Após Board approval

- Lê decisão do Board no comentário
- Se variante escolhida → marca como "design spec congelada", arquiva worktrees das outras 2
- Se Board pediu iteração → reabre Issue 2 com instruções específicas, novo ciclo
- Se Board rejeitou tudo → escala pro CEO ("Discovery precisa ser repensada")
- Se variante aprovada → executa Design System Update (passo 4.1) antes de liberar Delivery

### 4.1 Design System Update (pós-aprovação)

Obrigatório após toda aprovação de variante. Mesmo que o feature não introduza componentes visualmente novos, o designer audita o design system inteiro para identificar gaps.

1. **Lê variante aprovada** e UX_SPEC.md completo
2. **Compara cada componente visual** com o design system existente (design-system.md do projeto)
3. **Para cada componente novo ou alterado:**
   - Extrai tokens (cor, tipografia, spacing, radius, shadow, glass/blur, motion)
   - Documenta no design system com: nome de classe CSS, estados (default, hover, active, focus, disabled), uso recomendado
4. **Para componentes existentes no design system mas ausentes da variante:**
   - Verifica se são obsoletos ou apenas não aplicáveis ao feature atual
   - NÃO remove — apenas sinaliza para revisão futura se potencialmente obsoletos
5. **Commita design system atualizado** no mesmo branch da feature
6. **Posta diff summary** no comentário do issue: quais componentes/tokens foram adicionados, alterados, ou sinalizados
7. **Upload para Markup** (se conectado — passo 4.2)

### 4.2 Upload para Markup (se conectado)

Executar apenas se `MARKUP_URL` e `MARKUP_TOKEN` existirem como env vars. Se não existirem, pular silenciosamente.

1. **Verifica se projeto existe** no Markup para o projeto Paperclip atual
   - `GET https://${MARKUP_URL}/api/mockups` — procura por slug do projeto
   - Se não existe, cria estrutura básica
2. **Cria zip** com `index.html` do mockup aprovado
3. **Upload do mockup** na pasta "Feature Mockups":
   ```
   POST https://${MARKUP_URL}/api/mockups
   -F "name=<feature-name>"
   -F "slug=<feature-slug>"
   -F "build=@mockup.zip;type=application/zip"
   ```
4. **Upload do design system** (se atualizado) na pasta "Design System":
   - Converte design-system.md em HTML visual (com swatches de cor, exemplos de componentes)
   - Upload como mockup separado
5. **Posta URLs** publicadas no comentário do issue
6. **Se upload falhar:** loga erro, continua sem bloquear — Markup é complementar

### 5. Health check

- Issues paradas em BLOCKED >2 heartbeats? → investiga dependência
- Worktrees órfãos sem agente trabalhando? → limpa
- Design Reviewer rodou em todas as variantes ativas? → cobra se faltou

## O que reportar ao CEO

- Issue 1 done (UX_SPEC.md publicada)
- Issue 2 done (todas as variantes geradas)
- Issue 3 done (todos os reviews completos)
- Pacote pronto para Board
- Blockers que requerem decisão do Board (não micro-blockers internos da Discovery)
