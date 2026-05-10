# Template: Spike (Investigação)

> Use quando você não sabe ainda o que quer. Quer explorar viabilidade, fazer análise, entender trade-offs. Entrega é um documento, não código de produção.
>
> Após o spike, o resultado pode virar feature, bugfix, ou ser arquivado.

---

**Tipo:** Goal
**Label:** `spike`
**Timebox:** [obrigatório — ex: "2 dias de heartbeats"]
**Atribuído a:** CEO
**Prioridade:** [low | normal | high]

---

## Questão a responder

<!-- Uma pergunta clara que o spike deve responder. Evita "vamos ver o que tem". -->



## Por que isso importa agora

<!-- Contexto de negócio. Por que estamos gastando tempo investigando isso. -->



## O que quero saber

<!-- Lista específica de output esperado. -->

- [ ]
- [ ]
- [ ]

## O que NÃO quero (out of scope)

<!-- Importante pra spike não virar feature acidentalmente. -->

- [ ] Não migrar nada nesta fase
- [ ] Não adicionar dependências novas em produção
- [ ] Não criar PR de mudança de código

## Critério de "spike pronto"

<!-- Quando o documento está pronto pra eu revisar? -->



## Decisão a ser tomada após o spike

<!-- O que vou decidir com base no documento. Ajuda o agente a focar no que importa. -->

Exemplo: "Decidir se vamos migrar de SQLite pra Postgres este trimestre, ou adiar."

---

### O que vai acontecer depois que eu submeter

1. CEO faz triagem, valida que tem questão clara + timebox
2. Delega pro CTO em modo research:
   - **NÃO escreve código de produção**
   - Worktree throwaway permitido pra protótipos descartáveis
   - Foco: hipóteses + evidências + trade-offs + recomendação
3. CTO produz **documento de análise** dentro do timebox
4. CEO marca como "Pending Board Review"
5. **★ Você revisa o documento ★**:
   - Aprova recomendação → vira novo Goal de feature/bugfix
   - Pede mais profundidade → reabre o spike
   - Arquiva → encerra o spike

**Estimativa:** conforme timebox especificado

**Próximo input que precisarei de você:** revisão do documento quando entregue

---

### Estrutura típica do documento entregue pelo CTO

1. **Resumo executivo** (1 parágrafo)
2. **Questão investigada**
3. **Metodologia** (o que foi feito pra investigar)
4. **Findings** (dados, números, evidências)
5. **Trade-offs** (opções consideradas, prós/contras de cada)
6. **Recomendação** (com justificativa)
7. **Próximos passos sugeridos** (se Board aprovar)
8. **Riscos / unknowns** (o que ainda é incerto)
