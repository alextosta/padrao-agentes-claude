# CHANGELOG

Memória entre sessões. Registra o que foi feito, o que foi decidido, **o que falhou** e o que se aprendeu.
Atualizado ao final de cada sessão de trabalho relevante (`/check-changelog`).

## Convenções

- Ordem cronológica **decrescente** — a entrada mais recente fica no topo.
- Uma seção por sessão: `## AAAA-MM-DD — breve descrição`.
- Marcadores:
  - `[Feito]` — concluído
  - `[Parcial]` — parcialmente feito; o resto está no backlog (dizer onde)
  - `[Falhou]` — abordagem tentada e abandonada, **com o motivo**
  - `[Decisão]` — decisão de arquitetura ou processo (se for estrutural, também vira ADR)
  - `[Lição]` — aprendizado que muda como agir da próxima vez
- 1 a 3 linhas por item; referencie a issue (`#123`).

> As entradas `[Falhou]` são as mais valiosas: elas impedem que a próxima sessão repita
> um caminho já descartado.

---

## AAAA-MM-DD — Configuração inicial do projeto

- [Feito] Estrutura padrão criada a partir do projeto-padrão da equipe.
- [Decisão] {{registre aqui as decisões iniciais — build, migrations, provedor de identidade}}.
