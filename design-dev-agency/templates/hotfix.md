# Template: Hotfix

> Use APENAS para produção quebrada (P0). Path expresso: pula Design Lead, pula CTO brainstorm, vai direto pro Tech Lead. Quality gates reduzidos. Cria automaticamente follow-up de post-mortem.
>
> Se não é P0 com produção comprometida, use o template de **bugfix**.

---

**Tipo:** Issue
**Label:** `hotfix`
**Severidade:** P0 (obrigatório)
**Atribuído a:** CEO
**Prioridade:** Máxima

---

## Sintoma exato

<!-- O que está acontecendo AGORA em produção. Mensagem de erro, stacktrace, screenshot. -->

```

```

## Impacto

<!-- Quem está afetado e quanto. Quanto mais quantitativo, melhor pra priorização. -->

- **% de usuários afetados:**
- **Funcionalidade quebrada:**
- **Perda estimada (se aplicável):**
- **Desde quando:**

## Hipótese (opcional mas ajuda muito)

<!-- Se você suspeita do commit/deploy/mudança que causou, anota aqui. Acelera debugging. -->

-

## Workaround temporário disponível?

<!-- Existe alguma forma manual de mitigar enquanto o fix é deployado? -->

-

## Logs

```

```

---

### O que vai acontecer depois que eu submeter

1. CEO **notifica Board imediatamente** (não espera próximo heartbeat de health check)
2. CEO delega DIRETO pro Tech Lead com instrução: "fix mínimo, sem refactor, deploy imediato"
3. Tech Lead usa **systematic-debugging** em modo expresso (root cause primeiro)
4. Developer faz fix mínimo + 1 teste de regressão
5. **G1 reduzido** (smoke test apenas, sem coverage gate)
6. **★ Board Approval Gate (você) ★** — aprova deploy imediato
7. Deploy
8. **Follow-up automático criado** (label: `bug`, prioridade P1): "Post-mortem + fix definitivo"

**Estimativa:** 1 heartbeat (urgente, pode override schedule)

**Próximo input que precisarei de você:** aprovação de deploy o quanto antes

---

### ⚠️ Sobre o follow-up de post-mortem

O hotfix é por definição uma solução não-ideal. O CEO cria automaticamente uma issue de follow-up que vai:

- Investigar root cause em profundidade
- Implementar fix definitivo (com testes proper, sem atalhos)
- Documentar lessons learned
- Adicionar guardrails pra prevenir recorrência

Esse follow-up entra no path normal de bugfix e é importante NÃO ignorar — senão dívida técnica acumula.
