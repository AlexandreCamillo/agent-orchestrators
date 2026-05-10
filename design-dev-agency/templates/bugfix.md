# Template: Bugfix

> Use para defeito conhecido, comportamento errado em funcionalidade já existente. Pula Discovery, vai direto pro CTO com debugging sistemático.

---

**Tipo:** Issue
**Label:** `bug`
**Severidade:** [P0 | P1 | P2 | P3]
**Atribuído a:** CEO

---

## Severidade (referência)

- **P0** — Produção quebrada, perda de dados, brecha de segurança. Considere reabrir como `hotfix`.
- **P1** — Funcionalidade importante quebrada, sem workaround
- **P2** — Bug visível, com workaround ou impacto limitado
- **P3** — Nice-to-fix, cosmético, edge case raro

---

## Comportamento atual

<!-- Passos exatos pra reproduzir. Numere. -->

1.
2.
3.

## Comportamento esperado

<!-- O que deveria acontecer? -->



## Onde acontece

<!-- Tela, componente, endpoint, módulo. Quanto mais específico, mais rápido o debugging. -->

- **Local:**
- **Browser/dispositivo:** (se aplicável)
- **Conta/usuário:** (se reproduzível só com perfil específico)

## Quando começou

<!-- Se você sabe ou suspeita. Commit, deploy, mudança de dependência. -->

- **Última versão funcionando:**
- **Primeira versão com bug:**
- **Suspeita:**

## Logs / stacktrace (se aplicável)

```

```

## Screenshot / vídeo (opcional)

<!-- Anexa se for bug visual ou de fluxo. -->

---

### O que vai acontecer depois que eu submeter

1. CEO faz triagem, valida que tem repro steps + severidade
2. Delega direto pro CTO (pula Design Lead)
3. CTO atribui ao Tech Lead com instrução de usar **systematic-debugging** (4 fases) do Superpowers
4. Tech Lead investiga root cause antes de tocar em código
5. Developer escreve **teste de regressão** que reproduz o bug → faz passar (TDD)
6. Quality Gates G1 → G2 → G3 → G4

**Estimativa:** 1-3 dias de heartbeats

**Próximo input que precisarei de você:** apenas no G4 (aprovação de deploy)

---

### ⚠️ Se durante a investigação se descobrir que é P0 (produção quebrada)

CEO automaticamente reclassifica como `hotfix` e dispara o path expresso. Você é notificado imediatamente.
