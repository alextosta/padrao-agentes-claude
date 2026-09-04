---
name: implementar
description: Executa uma feature especificada de ponta a ponta — plano em waves, implementação por task e os portões de qualidade (banco, segurança, design, QA) até o Merge Request
arguments:
  - name: feature
    description: Nome da feature em specs/ (ex. cadastro-servidor)
    required: true
  - name: wave
    description: "Retomar a partir de uma wave específica (ex. 3). Vazio = começa do início"
    required: false
---

Implemente a feature "$0" seguindo a orquestração completa do projeto.

## Pré-condições

1. `specs/$0/tasks.md` com **Status: Approved**.
2. Issues criadas no GitLab (`/criar-issues $0`). Se não houver, pare e peça para rodar antes — **sem issue não se escreve código**.
3. Branch dedicada criada a partir da branch base atualizada:
   ```bash
   git checkout {{BRANCH_BASE}} && git pull
   git checkout -b feature/$0
   ```

## Etapa 1 — Plano de execução

Invoque o agente `orquestrador` com `specs/$0/tasks.md` e `specs/$0/design.md`.
Ele devolve o plano em waves (o que é sequencial, o que é paralelo, o caminho crítico).

**Apresente o plano ao humano e espere o "pode ir" antes de executar.**

Se o argumento `$1` foi informado, retome a partir daquela wave e informe o que está sendo pulado.

## Etapa 2 — Executar wave a wave

Para cada wave:

1. **Marque as issues da wave como em andamento** no GitLab.
2. **Execute as tarefas**:
   - Wave sequencial: uma tarefa por vez.
   - Wave paralela: uma subtarefa por task, cada uma com contexto completo (o que fazer, arquivos, critério de done, restrições do `CLAUDE.md`).
3. **Regras de implementação que valem em toda tarefa**:
   - Migration antes de código de aplicação; **nunca** altere migration já aplicada.
   - Teste antes da implementação para regra de negócio em `service/`.
   - DTO nas bordas; entidade JPA nunca sai no controller.
   - Sem valor fixo que deveria ser configuração; sem credencial no código.
   - Texto visível ao usuário em português com acentuação.
4. **Valide antes de fechar a wave**:
   ```bash
   cd backend && ./mvnw clean verify
   cd ../frontend && npm run lint && npm run build
   ```
   Falhou? **Não avance.** Corrija dentro da wave. Se o mesmo erro persistir após duas tentativas,
   pare, registre no `CHANGELOG.md` e peça ajuda.
5. **Commit por escopo**, referenciando a issue:
   ```
   feat(cadastro): valida CPF do servidor — #125
   ```
6. Feche as issues concluídas da wave.
7. Reporte: "Wave N completa (X/X tarefas). Iniciando Wave N+1."

## Etapa 3 — Portões de qualidade (na ordem)

Nenhum portão é pulado. Cada um gera relatório; achados 🔴 bloqueantes são corrigidos **antes** do próximo.

| Ordem | Portão | Agente | Quando se aplica | Relatório |
|-------|--------|--------|------------------|-----------|
| 1 | **Banco** | `dba-oracle` | Feature criou/alterou schema ou consulta relevante — **antes** de aplicar a migration em ambiente compartilhado | `docs/revisao-banco-$0.md` |
| 2 | **Segurança** | `seguranca` | Feature toca autenticação, autorização, dado pessoal, upload ou integração externa | `docs/revisao-seguranca-$0.md` |
| 3 | **Design** | `design-reviewer` | Feature tem tela | `docs/design-review-$0.md` |
| 4 | **QA** | `qa` | **Sempre** — é o último portão antes do MR | `docs/qa-$0.md` |

Após cada portão: liste os achados por severidade, corrija os 🔴 (com commit referenciando a issue),
registre os 🟡 que ficarem para depois como issue nova, e só então avance.

## Etapa 4 — Fechamento

1. **CHANGELOG.md** — entrada da sessão, com o que foi feito, decisões tomadas e o que falhou pelo caminho.
2. **Merge Request**:
   ```bash
   glab mr create \
     --title "[$0] {{Nome da feature}}" \
     --description "$(cat <<'DESC'
## O que muda
{{resumo}}

## Specs
`specs/$0/`

## Portões
- Banco: {{✅/⚠️/N/A}} — `docs/revisao-banco-$0.md`
- Segurança: {{✅/⚠️/N/A}} — `docs/revisao-seguranca-$0.md`
- Design: {{✅/⚠️/N/A}} — `docs/design-review-$0.md`
- QA: {{✅/⚠️}} — `docs/qa-$0.md`

## Como testar
{{passo a passo}}

Closes #{{issue-mãe}}
DESC
)" \
     --remove-source-branch
   ```
3. Confirme que o pipeline do GitLab ficou verde.
4. Informe ao humano: issues fechadas, relatórios gerados, MR aberto e o que ficou como ressalva.

## Regras

- **Não pule portões**, mesmo com pressa. O portão pulado é exatamente o que volta como incidente.
- **Não faça merge.** Abrir o MR e parar — o merge é decisão humana.
- **Não aplique migration em ambiente compartilhado** por conta própria: escreva o `.sql`, passe pelo `dba-oracle`, e deixe o pipeline aplicar.
- **Não amplie o escopo.** Ideia boa fora da spec vira issue nova, não commit extra.
- Erro persistente após duas tentativas: pare, registre e escale.
