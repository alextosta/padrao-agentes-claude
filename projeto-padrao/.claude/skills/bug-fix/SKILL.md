---
name: bug-fix
description: Fluxo estruturado de correção de bug (Reportar, Analisar, Corrigir, Verificar)
arguments:
  - name: descricao
    description: Descrição do bug, ou o número da issue no GitLab
    required: true
---

Corrija o bug: "$0".

## Pré-condição

Issue no GitLab existe e está em andamento. Se não existe, crie antes:
```bash
glab issue create --title "{{título}}" --description "{{comportamento esperado × observado}}" --label "tipo::bug"
```

## 1. Reportar

- Comportamento **esperado** × comportamento **observado**
- Passos de reprodução (e se você conseguiu reproduzir)
- Ambiente onde ocorre (dev / homologação / produção) e desde quando
- Módulo e camada afetados

Não conseguiu reproduzir? **Pare aqui** e peça o dado que falta. Correção às cegas gera regressão.

## 2. Analisar

- Leia os arquivos do caminho da requisição (controller → service → repository → SQL)
- Identifique a **causa raiz**, não o sintoma. Se a correção é um `if` de proteção, provavelmente você achou o sintoma.
- Liste os arquivos que precisarão mudar
- Verifique se existia teste que deveria ter capturado isso — a **ausência desse teste também é um achado**
- Verifique no `CHANGELOG.md` se esse problema já apareceu antes

## 3. Corrigir

- **Escreva primeiro o teste que falha** reproduzindo o bug; depois faça a correção passar
- Correção mínima: nada de refatoração oportunista junto (isso vira issue própria)
- Se o bug é de dados, a correção do dado é uma migration versionada — nunca `UPDATE` manual em produção
- Rode:
  ```bash
  cd backend && ./mvnw clean verify
  cd ../frontend && npm run lint && npm run test -- --watch=false
  ```

## 4. Verificar

- O teste novo falha sem a correção e passa com ela? (verifique de fato — reverta mentalmente ou por `git stash`)
- Toda a suíte passa (sem regressão)?
- O comportamento observado sumiu pelos passos de reprodução originais?
- Commit: `fix(escopo): descreve o efeito para o usuário — #{{issue}}`

## 5. Reportar de volta

Resuma: causa raiz, arquivos alterados, teste adicionado, e por que o bug passou despercebido.
Se a causa raiz revelar um problema mais amplo, abra issue separada — não a resolva neste commit.

## Regras

- Nenhuma mudança além do necessário para a correção.
- Nenhuma feature junto com o fix.
- Bug em regra de negócio: confirme o comportamento correto com o humano **antes** de alterar. Regra de negócio "óbvia" costuma ter exceção prevista em norma.
