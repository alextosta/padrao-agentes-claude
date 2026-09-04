# Architecture Decision Records (ADR)

Registro das decisões de arquitetura do projeto: **o que** foi decidido, **por quê**, e **o que foi descartado**.

## Para que serve

Meses depois, ninguém lembra por que se escolheu X em vez de Y — e a decisão é refeita do zero,
às vezes na direção errada. O ADR guarda o contexto da decisão, não apenas o resultado.

Para o agente, os ADRs são a fronteira: ele não propõe reverter uma decisão registrada sem
apresentar um ADR novo que a supere, com justificativa.

## Quando escrever um ADR

- Escolha de tecnologia, biblioteca ou padrão que afeta o projeto inteiro
- Decisão sobre modelo de dados que será cara de reverter
- Definição de contrato de integração com outro sistema
- Qualquer decisão que já foi discutida duas vezes

**Não** vira ADR: escolha local, nome de variável, detalhe de implementação de uma feature (isso é `design.md`).

## Regras

1. ADR é **imutável** depois de aceito. Mudou de ideia? Crie um novo com status `Substitui ADR-00X`,
   e marque o antigo como `Superado por ADR-00Y`.
2. Numeração sequencial: `001-titulo-em-kebab-case.md`.
3. Registre as alternativas **descartadas** e o motivo — é a parte mais útil no futuro.
4. Uma decisão por ADR.

## Índice

| # | Título | Status | Data |
|---|--------|--------|------|
| 001 | {{título}} | Aceito | AAAA-MM-DD |
