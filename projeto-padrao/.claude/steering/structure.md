# Steering: Estrutura

Mapa permanente do repositório. Define onde cada coisa mora, de quem é a responsabilidade e o que
o agente **não** deve editar por conta própria.

---

## Árvore

```
{{projeto}}/
├── CLAUDE.md                       # Constituição — lida em toda sessão
├── CHANGELOG.md                    # Memória entre sessões
├── README.md                       # Onboarding humano
├── .gitlab-ci.yml                  # Pipeline
│
├── .claude/                        # Contexto permanente do agente
│   ├── settings.json               # Configuração compartilhada (versionada)
│   ├── settings.local.json         # Configuração pessoal (NÃO versionar)
│   ├── steering/
│   │   ├── product.md              # Visão de produto permanente
│   │   ├── tech.md                 # Restrições técnicas permanentes
│   │   └── structure.md            # Este documento
│   ├── agents/                     # Subagentes especializados
│   │   ├── README.md               # Catálogo e critérios de adoção
│   │   ├── spec-writer.md
│   │   ├── orquestrador.md
│   │   ├── dba-oracle.md
│   │   ├── seguranca.md
│   │   ├── qa.md
│   │   └── design-reviewer.md
│   └── skills/                     # Comandos de fluxo (/nome)
│       ├── spec-create/            # 3 documentos, com aprovação entre eles
│       ├── criar-issues/           # tasks.md → issues no GitLab
│       ├── implementar/            # execução em waves + portões
│       ├── spec-review/
│       ├── spec-status/
│       ├── bug-fix/
│       └── check-changelog/
│
├── docs/
│   ├── PRD.md                      # Requisitos de produto
│   ├── modelo-de-dados.md          # Schema, entidades, dados pessoais
│   ├── adr/                        # Architecture Decision Records
│   │   ├── README.md
│   │   ├── template.md
│   │   └── NNN-titulo.md
│   ├── qa-{{feature}}.md            # Relatórios do agente qa
│   ├── revisao-banco-{{escopo}}.md  # Relatórios do dba-oracle
│   ├── revisao-seguranca-*.md      # Relatórios do agente seguranca
│   └── design-review-*.md          # Relatórios do design-reviewer
│
├── specs/                          # Uma pasta por feature
│   └── {{feature}}/
│       ├── requirements.md
│       ├── design.md
│       └── tasks.md
│
├── backend/
│   ├── pom.xml                     # Fonte de verdade das dependências
│   ├── mvnw / mvnw.cmd             # Wrapper — sempre commitado
│   └── src/
│       ├── main/
│       │   ├── java/{{PACOTE_BASE}}/
│       │   │   ├── config/ controller/ dto/ domain/
│       │   │   ├── repository/ service/ mapper/
│       │   │   ├── exception/ integration/
│       │   └── resources/
│       │       ├── application.yml           # Configuração comum
│       │       ├── application-{dev,hml,prd}.yml
│       │       └── db/migration/             # MANDA NO SCHEMA
│       └── test/java/{{PACOTE_BASE}}/
│
└── frontend/
    ├── package.json / package-lock.json      # Lockfile sempre commitado
    └── src/app/
        ├── core/        # Singletons: interceptors, guards, serviços de API
        ├── shared/      # Componentes, pipes e diretivas reutilizáveis
        ├── features/    # Uma pasta por domínio, lazy-loaded
        └── layout/      # Casca da aplicação
```

---

## Responsabilidades

| Caminho | Responsabilidade | Regra |
|---------|------------------|-------|
| `db/migration/` | Define o schema | Migration aplicada é **imutável**. Corrija com uma nova |
| `service/` | Regra de negócio | Teste escrito antes. Código crítico |
| `controller/` | Entrada HTTP | Sem regra de negócio. Sem entidade JPA no contrato |
| `domain/` | Entidades JPA | Reflete o schema; não o define |
| `core/` (front) | Serviços globais | Importado uma vez; nunca em módulo de feature |
| `shared/` (front) | Reuso visual | Sem regra de negócio, sem chamada de API |
| `features/` (front) | Telas por domínio | Lazy-loaded; não importa de outra feature |
| `specs/` | O que será construído | Alterado pelo `spec-writer` com aprovação humana |
| `docs/adr/` | Por que foi decidido | ADR aprovado não se edita: cria-se outro que o supera |
| `CHANGELOG.md` | O que já aconteceu | Append-only, mais recente no topo |

---

## O que o agente NÃO edita sem autorização explícita

- `.gitlab-ci.yml` e qualquer configuração de deploy
- `application-hml.yml` e `application-prd.yml`
- Qualquer migration já aplicada em ambiente compartilhado
- ADRs existentes (cria-se um novo que supera o anterior)
- Arquivos de política do órgão (segurança, LGPD, acessibilidade)
- Configuração de branch protegida, aprovações de MR ou permissões do GitLab

---

## Arquivos que nunca entram no repositório

`.env`, `application-local.yml` com credenciais, chaves e certificados, dump de dados reais,
`node_modules/`, `target/`, `.claude/settings.local.json`.
