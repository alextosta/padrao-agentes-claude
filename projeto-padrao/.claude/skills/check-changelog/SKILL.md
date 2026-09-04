---
name: check-changelog
description: Revisa o histórico da conversa e registra no CHANGELOG.md o que foi feito, decidido, falhado e aprendido na sessão
---

Revise esta conversa e identifique o que deve ser registrado no `CHANGELOG.md`.

## Instruções

1. Leia `CHANGELOG.md` para entender o formato e a última entrada.

2. Percorra o histórico da sessão e identifique:
   - Tarefas concluídas (código escrito, migration criada, bug corrigido, configuração alterada)
   - Tentativas abandonadas — **e o motivo**
   - Decisões de arquitetura ou de processo
   - Lições aprendidas que mudam como a próxima sessão deve agir
   - Issues do GitLab mencionadas

3. Classifique cada item:
   - `[Feito]` — concluído
   - `[Parcial]` — parcialmente feito; o resto está no backlog (dizer onde)
   - `[Falhou]` — abordagem tentada e abandonada (**sempre com o porquê** — é a entrada mais valiosa)
   - `[Decisão]` — decisão de arquitetura ou processo
   - `[Lição]` — aprendizado relevante para sessões futuras

4. **Apresente o resumo ao humano antes de editar o arquivo.** Espere confirmação.

5. Após a confirmação, insira no topo do `CHANGELOG.md` (ordem cronológica decrescente):
   ```markdown
   ## AAAA-MM-DD — {{breve descrição da sessão}}

   - [Feito] ... (#123)
   - [Falhou] ... — motivo: ...
   - [Decisão] ...
   ```

## Regras

- Não invente. Registre apenas o que de fato aconteceu na conversa.
- Não duplique entradas existentes.
- 1 a 3 linhas por item. Detalhe técnico só quando for necessário para reproduzir ou evitar o erro.
- Decisão de arquitetura relevante não para no CHANGELOG: vira ADR em `docs/adr/`.
- Nada relevante na sessão? Diga isso — não force conteúdo.
