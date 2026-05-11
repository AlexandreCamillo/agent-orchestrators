# CLAUDE.md — Project Context

> Contexto compartilhado por TODOS os agentes (e Superpowers, design-review). Tudo aqui entra no system prompt automaticamente. Mantenha conciso — cada linha é custo recorrente em tokens.
>
> **Substitua os placeholders `<...>` pelo seu projeto antes de usar.**

---

## Project

- **Name:** `<project-name>`
- **Description:** `<one-liner do que o produto faz>`
- **Stage:** `<MVP | growth | mature>`
- **Domain:** `<saas | ecommerce | fintech | etc>`

## Stack

### Frontend
- **Framework:** React 18 + TypeScript
- **Bundler:** Vite
- **Styling:** Tailwind CSS (utility-first, sem CSS-in-JS)
- **Component primitives:** `<shadcn/ui | Radix | Headless UI | custom>`
- **State:** `<Zustand | Redux | TanStack Query | local apenas>`
- **Routing:** `<React Router | TanStack Router | Next.js>`
- **Forms:** `<React Hook Form + Zod>`

### Backend
- **Runtime:** Node.js 20+
- **Framework:** `<Hono | Express | Fastify | Next.js API>`
- **DB:** PostgreSQL `<14|15|16>`
- **ORM:** `<Drizzle | Prisma | Kysely>`
- **Auth:** `<Auth.js | Clerk | custom>`
- **Background jobs:** `<BullMQ | Inngest | Trigger.dev>`

### Infra
- **Deploy:** `<Railway | Fly.io | Vercel | AWS>`
- **Observability:** `<Sentry | Datadog | OpenTelemetry>`
- **CI:** GitHub Actions
- **Package manager:** pnpm 9+

---

## Design System

### Cores

```
Primary:    #2563EB (blue-600)        — actions, links, focus
Secondary:  #64748B (slate-500)       — supporting text
Success:    #16A34A (green-600)
Warning:    #CA8A04 (yellow-600)
Danger:     #DC2626 (red-600)

Background: #FFFFFF (light) / #0F172A (dark, slate-900)
Surface:    #F8FAFC (light, slate-50) / #1E293B (dark, slate-800)
Border:     #E2E8F0 (light, slate-200) / #334155 (dark, slate-700)

Text primary:   #0F172A (light) / #F1F5F9 (dark)
Text secondary: #475569 (light) / #94A3B8 (dark)
Text muted:     #94A3B8 (light) / #64748B (dark)
```

**Contraste:** garantir ≥ 4.5:1 (texto normal) e ≥ 3:1 (texto large) em ambos os temas.

### Typography

- **Font family:** Inter, sans-serif (system-ui fallback)
- **Mono:** JetBrains Mono, ui-monospace
- **Escala (rem):** 0.75 / 0.875 / 1 / 1.125 / 1.25 / 1.5 / 2 / 3
- **Line height:** 1.5 (body), 1.2 (headings)
- **Font weights:** 400 (normal), 500 (medium), 600 (semibold), 700 (bold)

### Spacing

Escala em múltiplos de 4: `4 / 8 / 12 / 16 / 24 / 32 / 48 / 64`. Não usar valores fora dessa escala sem justificativa.

### Border radius

```
sm: 4px   — inputs, small buttons
md: 8px   — cards, modals (default)
lg: 12px  — large surfaces
xl: 16px  — heros
full: 9999px — pills, avatars
```

### Shadows

```
sm: subtle, hover de cards
md: dropdowns, popovers
lg: modals, command palettes
```

Não usar shadows mais densas. Estética geral: clean, baixa decoração.

### Inspirações estéticas (referência para variantes)

- **Linear** (linear.app) — minimalismo, monocromático, denso
- **Vercel** (vercel.com) — neutros, tipografia forte
- **Stripe** (stripe.com) — bold gradients, large type, illustration
- **Notion** (notion.so) — playful, ícones, friendly

Quando UI Designer gerar 3 variantes, deve cobrir 3 espaços estéticos genuinamente distintos (não 3 versões do mesmo).

### Acessibilidade — não-negociável

- WCAG AA+ obrigatório
- Focus rings visíveis em todos os elementos interativos
- Tab order lógico
- ARIA labels em ícones-only buttons
- Estados de erro lidos por screen reader (`aria-live`)
- Headings em hierarquia (h1 → h2 → h3 sem pular)
- Contraste validado em ambos os temas

---

## Code conventions

### Estrutura de arquivos

```
src/
├── app/              # rotas / páginas
├── components/
│   ├── ui/           # primitivas (button, input, etc.)
│   └── <feature>/    # componentes específicos de feature
├── lib/              # utilities, helpers
├── hooks/            # React hooks
├── server/           # backend
│   ├── api/          # routes
│   ├── db/           # schema, migrations
│   └── services/     # business logic
└── types/            # types compartilhados
```

### Naming

- **Files:** kebab-case (`user-profile.tsx`)
- **Components:** PascalCase (`UserProfile`)
- **Functions/vars:** camelCase
- **Constants:** UPPER_SNAKE_CASE
- **Types/Interfaces:** PascalCase, prefer `type` over `interface` exceto quando precisar de `extends`

### Imports

- Absolute imports via `@/` (ex: `import { Button } from '@/components/ui/button'`)
- Order: node_modules → @/lib → @/components → relativos
- Sem `import * as`

### Commits (Conventional Commits)

```
feat:     nova feature
fix:      bug fix
refactor: mudança que não altera comportamento
test:     adiciona/altera testes
docs:     mudança em documentação
chore:    build, deps, configs
```

### TypeScript

- `strict: true` no tsconfig
- Sem `any` em código de produção (usar `unknown` se necessário)
- Sem `// @ts-ignore` sem comentário justificando

### Tests

- **Unit:** Vitest, ao lado do arquivo (`foo.ts` → `foo.test.ts`)
- **Integration:** Vitest, em `__tests__/integration/`
- **E2E:** Playwright, em `__tests__/e2e/`
- **Coverage target:** 80% em código novo
- **AC do UX_SPEC.md:** cada AC tem 1+ teste correspondente

---

## Performance targets

- **API latency:** p50 < 100ms, p95 < 300ms, p99 < 1s
- **First Contentful Paint:** < 1.5s em 3G
- **Time to Interactive:** < 3s
- **Bundle size:** main < 200KB gzip

---

## Security baseline

- Sem secrets hardcoded (use env vars + secrets manager)
- Validação server-side em todos os endpoints (Zod)
- Auth required por default (opt-out explícito para endpoints públicos)
- Rate limiting em endpoints de write
- HTTPS em produção (sem exceção)
- LGPD: dados pessoais com consent, deletion endpoint disponível

---

## Observability

- Errors → Sentry com source maps
- Métricas key → Datadog/Prometheus
- Logs estruturados (JSON) com correlation ID
- Alertas: error rate > 1%, latency p95 > target, payment failure rate > 0.5%

---

## Stack proibida (sem aprovação do CTO)

- jQuery
- Moment.js (use date-fns ou Temporal)
- Lodash inteiro (importar funções específicas)
- CSS-in-JS runtime (use Tailwind ou CSS modules)
- Redux clássico (use Zustand ou TanStack Query)

---

## Skills do projeto disponíveis

Skills instaladas em `~/.claude/skills/` ou referenciadas via `paperclipai/companies/fullstack-forge`:

- **Superpowers** (obra/superpowers) — TDD, debugging, planning, verification
- **design-review** (OneRedOak) — Playwright + a11y + design system check
- **frontend-design** (Anthropic) — anti-AI-slop em UI generation
- **architecture-decision** — ADRs estruturados
- **code-review** — review checklist
- **bug-report** — estrutura de bug report
- **gate-check** — validação de gates G1/G2

Cada agente tem subset destas ativas (ver `agents/*/AGENTS.md`).

---

## Markup Integration (optional)

When a Markup review server is connected, mockups and design system renders are automatically published for Board review.

### Environment variables

| Variable | Required | Description |
|---|---|---|
| `MARKUP_URL` | No | Markup server hostname (e.g. `markup.example.com`) |
| `MARKUP_TOKEN` | No | Bearer token for Markup API authentication |

Both must be present for the integration to activate. If either is missing, all Markup logic is silently skipped.

### How it works

- After design approval, Design Lead uploads the approved mockup and updated design system to Markup
- Mockups are organized by project and folder (Design System / Feature Mockups)
- Upload format: zip file containing `index.html`, sent as multipart POST to `/api/mockups`
- URLs are posted on the issue thread for Board review

---

## Design System

The project's design system is maintained in a dedicated doc (e.g. `design-system.md`) that serves as the single source of truth for tokens, components, and patterns.

Design Lead is the steward: after every approved design variant, the design system is audited and updated before Delivery begins. This applies even when a feature doesn't introduce visually new components — gaps are proactively identified.

Developers reference the design system doc during implementation. CTO and Design Reviewer validate against it during G1.

---

## Project-specific conventions

<!-- Adicione abaixo regras específicas do seu projeto que não cabem nos blocos acima. Exemplos: -->

- All API responses follow `{ data, error, meta }` envelope
- Money values stored as integer cents (BRL), never float
- Timezone: armazenar UTC, exibir America/Sao_Paulo
- Locale: pt-BR para usuário final, en para logs e código

---

*Versionar este arquivo com o resto do projeto. Quando mudar design system ou stack, atualizar aqui ANTES de fazer mudança no código.*
