# Padrão de agentes Claude Code — Spring Boot + Angular + Oracle + GitLab

Este pacote é a estrutura de agentes, skills e contexto permanente para o Claude Code, pronta para
ser encaixada no **PROJETO-PADRAO** da Secretaria de Tecnologia da Informação.

A pasta `projeto-padrao/` contém exatamente o que vai para a raiz do template. Este arquivo é só o guia.

---

## O que vai para o PROJETO-PADRAO

```
projeto-padrao/
├── CLAUDE.md                   # Constituição: regras, workflow, fluxo ponta a ponta
├── CHANGELOG.md                # Memória entre sessões (com convenções)
├── .claude/
│   ├── settings.json           # Permissões compartilhadas (allow/ask/deny)
│   ├── agents/                 # 6 subagentes + catálogo (README.md)
│   ├── skills/                 # 7 comandos de fluxo (/spec-create, /criar-issues, /implementar, ...)
│   └── steering/               # product.md, tech.md, structure.md
└── docs/adr/                   # README + template de ADR
```

O `.claude/commands/` e as skills `normas-dados` / `projeto-padrao` que a STI já criou **convivem**
com isso sem conflito — `commands/` e `skills/` são mecanismos equivalentes; sugiro migrar os
comandos para `skills/` com o tempo, para ter um lugar só.

---

## Os três pilares (e por que os três)

| Pilar | Onde | Responde a |
|-------|------|-----------|
| **Constituição** | `CLAUDE.md` | "O que é proibido, o que é obrigatório, qual o fluxo?" |
| **Contexto permanente** | `.claude/steering/` | "Para quem construímos, com que stack, onde fica cada coisa?" |
| **Papéis** | `.claude/agents/` + `.claude/skills/` | "Quem faz o quê, e em que ordem?" |

### Sobre a pasta `steering` — vale mandar?

**Sim, e é boa prática.** A razão é de higiene do contexto:

- `CLAUDE.md` deve ser **curto e imperativo** — é lido em toda sessão e cada linha compete por atenção.
- O steering guarda o que é **permanente mas extenso**: visão de produto, stack, mapa do repositório.
  Sem ele, tudo isso migra para o `CLAUDE.md`, que incha, e as regras importantes se perdem no meio.
- O steering muda raramente e de forma consciente. O `CHANGELOG.md` muda toda sessão. Separar os dois
  evita que o agente confunda "estado atual" com "regra permanente".

Para um **template de vários projetos**, o steering vai assim:
- `tech.md` e `structure.md` — quase prontos, porque a stack é padronizada pela STI. Só ajustar versões.
- `product.md` — **template com placeholders**, preenchido por projeto. É o único que muda de verdade.

O que **não** mandei: nosso `design.md`/`design-memory.md` de steering, porque são decisões visuais de
um produto específico. Se a STI tiver design system institucional, ele entra ali.

---

## O fluxo ponta a ponta (o que mais vale preservar)

```
/spec-create   →  requirements → design → tasks   (aprovação humana a cada documento)
/criar-issues  →  issue-mãe + issues por task no GitLab
/implementar   →  orquestrador monta waves → implementa → portões (dba → segurança → design → qa) → MR
```

Cada seta para e espera o humano. O agente abre o MR; o merge é humano.

---

## Adaptações em relação à origem

| Origem | Aqui | Motivo |
|--------|------|--------|
| Linear | GitLab Issues via `glab` | Ferramenta da STI |
| PR (GitHub) | Merge Request | Idem |
| Agente `cto` | Agente `orquestrador` | Nome descritivo; função idêntica |
| Agente de compliance de domínio | Removido | Específico do produto de origem |
| Agente `data-scientist` | Removido | Não há pipeline de ML no padrão |
| — | Agente `dba-oracle` | Oracle tem armadilhas próprias (FK sem índice, `VARCHAR2` em bytes, `allocationSize`, migration imutável) |
| — | Agente `seguranca` | Órgão público: OWASP + LGPD como portão, sem ser específico de domínio |
| Agentes sem frontmatter | Frontmatter `name/description/tools` | Assim o Claude Code os reconhece como subagentes de verdade e restringe ferramentas |

---

## O que a STI precisa preencher (placeholders `{{...}}`)

```bash
grep -rn "{{" projeto-padrao/ | grep -v "{{feature}}" | cut -d: -f1 | sort | uniq -c
```

Principais decisões a fechar antes de distribuir:

| Placeholder | Decisão |
|-------------|---------|
| `{{Maven\|Gradle}}` | Os comandos do `CLAUDE.md` assumem Maven (`./mvnw`). Trocar se for Gradle |
| `{{Flyway\|Liquibase}}` | Comandos assumem Flyway. Se for Liquibase, ajustar `CLAUDE.md` e `dba-oracle.md` |
| `{{PROVEDOR_IDENTIDADE}}` | Keycloak, gov.br, AD/LDAP… |
| `{{COFRE_DE_SEGREDOS}}` | CI/CD variables, Vault… |
| `{{PADRAO_ACESSIBILIDADE}}` | eMAG / WCAG 2.1 AA |
| `{{FERRAMENTA_E2E}}` | Playwright ou Cypress |
| `{{BRANCH_BASE}}` | `develop` ou `main` |
| Épico × issue-mãe | Épico exige tier Premium/Ultimate do GitLab; a skill `criar-issues` usa issue-mãe como padrão |

---

## Pré-requisitos na máquina do desenvolvedor

- Claude Code instalado e autenticado
- `glab` (GitLab CLI) instalado e autenticado na instância (`glab auth login --hostname <host>`)
- Wrapper do build (`./mvnw`) e Node na versão do projeto
- `settings.local.json` **fora do Git** (preferências pessoais; o `settings.json` versionado é o da equipe)

---

## Recomendação de adoção

1. **Semana 1**: `CLAUDE.md` + steering + `spec-writer` + `qa`. Só isso. Rodar uma feature real.
2. **Semana 2–3**: `criar-issues` + `implementar` + `orquestrador`. A cadeia ponta a ponta.
3. **Depois**: `dba-oracle` e `seguranca` como portões obrigatórios; `design-reviewer` quando houver design system.

Um catálogo grande e não usado é pior que dois agentes usados de verdade.
