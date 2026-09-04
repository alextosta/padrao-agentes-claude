---
name: spec-review
description: Revisa as specs de uma feature e aponta lacunas de rastreabilidade entre requisitos, design e tasks
arguments:
  - name: feature
    description: Nome da feature em specs/ (ex. cadastro-servidor)
    required: true
---

Revise as specs da feature "$0".

## Instruções

1. Leia `specs/$0/requirements.md`, `design.md` e `tasks.md`.

2. Para cada documento, apresente:
   - **Status**: Draft / Approved / inexistente
   - **Resumo**: 2-3 frases sobre o escopo
   - **Contagens**: quantos RF, RNF, CA, tasks
   - **Perguntas em Aberto**: respondidas × pendentes
   - **Riscos** identificados

3. Faça a análise cruzada:
   - Todo RF tem tratamento no `design.md`?
   - Toda decisão do `design.md` tem task correspondente?
   - Todo CA tem uma task de teste que o cobre?
   - O `tasks.md` respeita a ordem obrigatória (migration → backend → frontend → testes)?
   - Há task no `tasks.md` que não corresponde a nenhum requisito? (escopo crescido)
   - Requisitos não-funcionais têm métrica verificável ou são frases vagas?

4. Liste as lacunas encontradas, cada uma com severidade (🔴 bloqueia a aprovação | 🟡 corrigir antes de implementar | 🟢 melhoria).

5. Se algum documento não existir, informe em que etapa do fluxo a feature está parada.

## Regras

- Não edite as specs — aponte. A correção é decisão do autor com o humano.
- Não aprove por conta própria: o veredito de aprovação é humano.
