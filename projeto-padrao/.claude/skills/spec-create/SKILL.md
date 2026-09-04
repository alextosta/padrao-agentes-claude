---
name: spec-create
description: Cria as specs completas de uma feature (requirements → design → tasks) com aprovação humana entre cada documento
arguments:
  - name: feature
    description: Nome da feature em kebab-case (ex. cadastro-servidor, consulta-processo)
    required: true
---

Crie as specs da feature "$0" seguindo o fluxo SDD do projeto.

## Instruções

1. **Leia primeiro**, nesta ordem:
   - `docs/PRD.md`, `.claude/steering/product.md`, `.claude/steering/tech.md`, `.claude/steering/structure.md`
   - `docs/adr/`, `docs/modelo-de-dados.md`, `CHANGELOG.md`
   - `specs/` existentes, para manter consistência de formato e profundidade
   - `.claude/agents/spec-writer.md` — o formato e as regras de cada documento

2. Crie `specs/$0/requirements.md` no formato definido pelo `spec-writer`.
   - Status: `Draft (aguardando aprovação humana)`
   - Liste as **Perguntas em Aberto** que exigem decisão humana
   - **PARE. Apresente o resumo e espere aprovação explícita.**

3. Aprovado o requirements: crie `specs/$0/design.md`.
   - Modelo de dados com DDL Oracle, contratos de API, camadas, segurança e permissões
   - Decisão estrutural nova → proponha um ADR em `docs/adr/`
   - **PARE e espere aprovação.**

4. Aprovado o design: crie `specs/$0/tasks.md`.
   - Ordem: migration → backend (test-first no `service/`) → frontend → testes de ponta a ponta
   - Cada task com: o que, arquivos, critério de done, dependências, estimativa
   - **PARE e espere aprovação.**

5. Aprovado o tasks: informe que o próximo passo é `/criar-issues $0`.

## Regras

- Português brasileiro com acentuação correta.
- Nunca responda por conta própria uma Pergunta em Aberto — liste e espere a decisão.
- Nunca avance para o próximo documento sem aprovação explícita do humano.
- Nenhuma decisão de implementação no `requirements.md`.
- Numere tudo (RF-01, RNF-01, CA-01, PA-01) — é o que permite ao agente `qa` auditar depois.
- Se um documento de referência não existir, registre como Pergunta em Aberto em vez de inventar o conteúdo.
