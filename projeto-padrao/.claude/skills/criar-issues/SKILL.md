---
name: criar-issues
description: Converte um tasks.md aprovado em issues no GitLab (issue-mãe + uma issue por task), com labels, dependências e critérios de done
arguments:
  - name: feature
    description: Nome da feature em specs/ (ex. cadastro-servidor)
    required: true
---

Converta `specs/$0/tasks.md` em issues no GitLab.

## Pré-condições (verificar antes de criar qualquer coisa)

1. `specs/$0/tasks.md` existe e está com **Status: Approved**. Se estiver `Draft`, pare e peça aprovação.
2. Não há Perguntas em Aberto pendentes em `requirements.md` ou `design.md`. Se houver, pare e liste.
3. O `glab` está autenticado e o projeto está vinculado:
   ```bash
   glab auth status
   glab repo view --output json | head -5
   ```
   Se não estiver, informe o comando (`glab auth login`) e pare — não tente contornar.

## Etapa 1 — Planejar as issues (sem criar nada ainda)

Leia o `tasks.md` e monte a tabela do que será criado:

| # | Título da issue | Tipo (label) | Depende de | Estimativa | Critério de done |
|---|-----------------|--------------|------------|-----------|------------------|

Convenções de título: `[{{SIGLA_FEATURE}}] {{verbo no infinitivo}} {{objeto}}` — ex.: `[CAD] Criar migration da tabela SERVIDOR`.

Labels sugeridas: `tipo::migration`, `tipo::backend`, `tipo::frontend`, `tipo::test`, `tipo::docs`,
`camada::api`, `camada::ui`, `camada::banco`, além da label da feature (`feature::$0`).

**Apresente a tabela ao humano e espere confirmação antes de criar.**

## Etapa 2 — Criar a issue-mãe

```bash
glab issue create \
  --title "[$0] {{Nome da feature}}" \
  --description "$(cat <<'DESC'
{{Resumo do requirements.md em 3-5 linhas}}

**Specs**: `specs/$0/`
- [requirements.md](specs/$0/requirements.md)
- [design.md](specs/$0/design.md)
- [tasks.md](specs/$0/tasks.md)

## Tarefas
(preenchido após a criação das issues filhas)
DESC
)" \
  --label "feature::$0,tipo::epico"
```

> **Épicos**: se a instância do GitLab tiver épicos disponíveis no tier em uso, crie um épico
> (`glab api ...` ou pela interface) e vincule as issues a ele. Caso contrário, o padrão é a
> **issue-mãe** acima com a lista de tarefas — funciona em qualquer tier. Confirme com a equipe
> qual dos dois é o padrão do órgão e registre a escolha em `CLAUDE.md`.

## Etapa 3 — Criar uma issue por task

Para cada task do `tasks.md`:

```bash
glab issue create \
  --title "[$0] {{título da task}}" \
  --description "$(cat <<'DESC'
**Task**: #{{n}} do `specs/$0/tasks.md`
**Issue-mãe**: #{{numero_da_mae}}

## O que
{{descrição da coluna "O que"}}

## Arquivos
{{lista de arquivos a criar/modificar}}

## Critério de done
{{critério objetivo — comando que passa, teste verde, tela funcionando}}

## Depende de
{{issues das quais esta depende, ou "—"}}

## Notas
{{armadilhas, referências, restrições do CLAUDE.md aplicáveis}}
DESC
)" \
  --label "feature::$0,tipo::{{tipo}}" \
  --milestone "{{milestone se houver}}"
```

Registre o número devolvido por cada criação — ele é necessário na etapa seguinte.

## Etapa 4 — Amarrar as dependências

1. Atualize a descrição da issue-mãe com a lista de tarefas em checklist, referenciando os números criados:
   ```
   - [ ] #124 Criar migration da tabela SERVIDOR
   - [ ] #125 Testes do ServidorService
   ```
2. Onde houver dependência entre tasks, registre-a explicitamente na descrição da issue filha
   (`Depende de #124`) e, se a instância suportar, relacione as issues.
3. Se o projeto usa quadro (board), mova a issue-mãe para a coluna inicial.

## Etapa 5 — Relatório

Informe ao humano:
- Issue-mãe criada (número e URL)
- Quantidade de issues filhas criadas, com número e título
- Dependências registradas
- Próximo passo sugerido: `/implementar $0`

## Regras

- **Nunca crie issues a partir de um `tasks.md` não aprovado.**
- **Nunca crie issues duplicadas**: antes de criar, verifique se já existem (`glab issue list --search "[$0]"`).
- Toda issue tem critério de done objetivo. "Fazer a tela" não é critério de done.
- Descrição em português brasileiro com acentuação.
- Se a criação de alguma issue falhar, pare, reporte o que já foi criado e não tente recriar em silêncio.
