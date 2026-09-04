# Catálogo de Agentes

Subagentes especializados do Claude Code. Cada arquivo `.md` desta pasta é um agente: o frontmatter
define nome, quando usar e quais ferramentas ele pode acionar; o corpo é o prompt de sistema dele.

## Por que subagentes

Um agente especializado tem **contexto próprio** e **escopo restrito**. Isso produz três ganhos práticos:

1. **Foco** — o revisor de banco não se distrai com CSS; o revisor de UI não opina sobre índice.
2. **Contexto limpo** — a auditoria roda numa janela separada e devolve só o relatório, sem poluir a sessão principal.
3. **Governança** — auditor não escreve código de produção. A separação entre *quem faz* e *quem confere* é a mesma que já existe no processo humano.

## Como invocar

```
> use o agente qa para auditar a feature cadastro-servidor
> rode o dba-oracle na migration V20260903__cria_tabela_processo.sql
```

O Claude Code também pode acionar um agente sozinho quando a descrição do frontmatter casa com a tarefa.

## Núcleo mínimo × complementares

| Agente | Categoria | Quando adotar |
|---|---|---|
| `spec-writer` | **Núcleo** | Desde o primeiro projeto. É o que impede código sem requisito. |
| `qa` | **Núcleo** | Desde o primeiro projeto. É o que impede requisito sem código. |
| `orquestrador` | Complementar | Quando as features passarem a ter mais de ~8 tasks. |
| `dba-oracle` | Complementar | Projetos com schema próprio e migrations frequentes. |
| `seguranca` | Complementar | Sistemas com dado pessoal, autenticação ou exposição externa. |
| `design-reviewer` | Complementar | Quando houver design system e front com telas complexas. |

Comece pelo núcleo. Um catálogo grande e não usado é pior que dois agentes usados de verdade.

## Regras comuns a todos os agentes

1. **Auditor não escreve código de produção.** `qa`, `dba-oracle`, `seguranca` e `design-reviewer` produzem relatório. A correção entra pelo fluxo normal, com issue.
2. **Toda constatação cita evidência**: `arquivo:linha`, nome de teste, número do requisito, artigo da norma.
3. **Não assuma.** Sem evidência encontrada, o item é reportado como ❌ ou ⚠️, nunca como ✅ presumido.
4. **Português brasileiro com acentuação** em todo texto produzido.
5. **Ao encontrar ambiguidade**, o agente lista como *Pergunta em Aberto* e para. Não decide pelo humano.

## Como adicionar um agente novo

1. Crie `.claude/agents/<nome>.md` com o frontmatter (`name`, `description`, `tools`).
2. Escreva o prompt na estrutura: **Papel → Quando invocar → O que ler antes → Processo → Formato do relatório → Regras**.
3. Restrinja `tools` ao mínimo necessário (auditor não precisa de `Write` além do relatório).
4. Registre o agente na tabela de `CLAUDE.md` §Agentes e, se ele for um portão do fluxo, no §Workflow de Feature.
