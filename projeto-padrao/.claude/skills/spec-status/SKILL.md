---
name: spec-status
description: Lista o status de todas as features com specs no projeto e em que etapa cada uma está
---

Liste o status de todas as features com specs.

## Instruções

1. Liste os diretórios de `specs/`.
2. Para cada feature, leia o campo **Status** do cabeçalho de `requirements.md`, `design.md` e `tasks.md`.
3. Verifique se existem relatórios de portão em `docs/` (`qa-*.md`, `revisao-banco-*.md`, `revisao-seguranca-*.md`, `design-review-*.md`).
4. Apresente a tabela:

| Feature | Requirements | Design | Tasks | Issues criadas | Portões | Etapa atual |
|---------|--------------|--------|-------|----------------|---------|-------------|

Etapas:
- **Especificação** — requirements inexistente ou em Draft
- **Design** — requirements aprovado, design em Draft ou inexistente
- **Planejamento** — design aprovado, tasks em Draft ou inexistente
- **Pronta para implementar** — os três aprovados
- **Em implementação** — issues abertas em andamento
- **Em verificação** — implementação concluída, portões pendentes
- **Concluída** — QA aprovado e MR mergeado

5. Liste, ao final, todas as Perguntas em Aberto pendentes de qualquer feature — elas são bloqueios ativos.
