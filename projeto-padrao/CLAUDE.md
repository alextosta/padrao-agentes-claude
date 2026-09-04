# CLAUDE.md

Constituição do projeto **{{NOME_DO_PROJETO}}** para o Claude Code. Este arquivo é lido automaticamente no início de toda sessão — mantenha-o curto, imperativo e verdadeiro.

> **Como usar este template**: substitua todos os `{{PLACEHOLDERS}}`, apague as seções que não se aplicam
> e revise as regras "YOU MUST / NEVER" com a equipe. Uma regra que ninguém segue corrompe todas as outras.

---

## ⚡ Comandos Essenciais

### Backend (Spring Boot / Maven)
```bash
cd backend
./mvnw clean verify                 # compila + testes + checkstyle/spotless
./mvnw spring-boot:run -Dspring-boot.run.profiles=dev
./mvnw test -Dtest=NomeDaClasseTest # teste isolado
./mvnw spotless:apply               # formata o código
./mvnw dependency:tree              # investiga conflito de dependência
```

### Frontend (Angular)
```bash
cd frontend
npm ci                              # instala a partir do package-lock.json (nunca "npm install" na CI)
npm start                           # ng serve (http://localhost:4200)
npm run lint
npm run test -- --watch=false --browsers=ChromeHeadless
npm run build -- --configuration production
```

### Banco (Oracle / {{FERRAMENTA_MIGRATION}})
```bash
cd backend
./mvnw flyway:info                  # estado das migrations no ambiente atual
./mvnw flyway:migrate -Pdev         # aplica migrations no ambiente de desenvolvimento
./mvnw flyway:validate              # detecta migration alterada após aplicada (checksum)
```

### Docker / ambiente local
```bash
docker compose up -d                # sobe Oracle XE + dependências locais
docker compose logs -f oracle
```

---

## 🚨 YOU MUST (regras inegociáveis)

1. **YOU MUST** ler `docs/PRD.md` e `.claude/steering/tech.md` antes de tocar em código.
2. **YOU MUST** consultar `docs/adr/` para entender o porquê das decisões antes de propor mudança estrutural. Sem ADR que justifique, não se muda arquitetura.
3. **YOU MUST** criar specs em `specs/{{feature}}/{requirements,design,tasks}.md` antes de implementar qualquer feature (nível 3) e esperar aprovação humana em cada documento.
4. **YOU MUST** versionar toda mudança de schema como uma nova migration em `backend/src/main/resources/db/migration/`. Alteração feita direto no banco não existe.
5. **YOU MUST** usar DTOs nas bordas da API. Entidade JPA **nunca** é serializada em resposta HTTP nem aceita em request.
6. **YOU MUST** manter a lógica de negócio em `service/`. `controller/` só orquestra HTTP; `repository/` só acessa dados.
7. **YOU MUST** escrever teste antes da implementação para toda regra de negócio em `service/` (test-first).
8. **YOU MUST** validar toda entrada de API com Bean Validation (`@Valid`) e tratar erro de forma centralizada (`@RestControllerAdvice` + `ProblemDetail`).
9. **YOU MUST** obter secrets de variáveis de ambiente ou do cofre ({{COFRE_DE_SEGREDOS}}). Nenhuma credencial em `application*.yml`, código ou `.gitlab-ci.yml`.
10. **YOU MUST** criar/identificar a issue no GitLab antes de começar qualquer trabalho — fix, improvement ou feature. Sem issue = sem código.
11. **YOU MUST** referenciar a issue nos commits (`feat(cadastro): valida CPF — #123`) e fechar via `Closes #123` no Merge Request.
12. **YOU MUST** garantir pipeline verde (`build`, `test`, `lint`, `quality`) antes de marcar o MR como pronto para revisão.
13. **YOU MUST** atualizar `CHANGELOG.md` ao final de cada sessão de trabalho relevante — é a memória entre sessões do agente.
14. **YOU MUST** escrever todo texto visível ao usuário em português brasileiro **com acentuação correta**.
15. **YOU MUST** justificar em comentário qualquer `@SuppressWarnings`, `// eslint-disable` ou supressão de regra do Checkstyle/SonarQube.

---

## ⛔ NEVER

1. **NEVER** commite `.env`, `application-local.yml` com credenciais, chaves, certificados ou dumps de dados reais.
2. **NEVER** altere uma migration já aplicada em qualquer ambiente. Corrija com uma **nova** migration (o checksum quebra o pipeline e trava o deploy).
3. **NEVER** execute DDL manualmente em homologação ou produção. O caminho é sempre migration versionada.
4. **NEVER** use `SELECT *` ou concatene string em SQL/JPQL. Sempre bind variables / parâmetros nomeados (injeção de SQL + hard parse no Oracle).
5. **NEVER** exponha dado pessoal em log, mensagem de erro ou URL (LGPD). Nem CPF, nem e-mail, nem token.
6. **NEVER** deixe `spring.jpa.hibernate.ddl-auto` diferente de `validate` (ou `none`) em qualquer ambiente.
7. **NEVER** use `any` no TypeScript nem desligue `strict` no `tsconfig.json`.
8. **NEVER** faça `subscribe` sem estratégia de cancelamento em componente Angular (`takeUntilDestroyed`, `async` pipe).
9. **NEVER** faça commit direto na branch protegida (`main`/`master`/`develop`). Sempre branch + Merge Request.
10. **NEVER** suba dependência nova sem verificar licença e CVEs conhecidas ({{POLITICA_DE_DEPENDENCIAS}}).
11. **NEVER** invente nome de tabela, coluna ou endpoint. Confirme no schema real (`docs/modelo-de-dados.md` ou via migration) antes de escrever a query.

---

## 🧭 Workflow por Nível de Complexidade

Nem toda demanda precisa de spec completa. Calibre o processo pelo tamanho.
**Todo trabalho — sem exceção — tem issue no GitLab**, mesmo que seja de duas linhas.

| Nível | Nome | Tempo estimado | Issue | Branch | Merge Request |
|---|---|---|---|---|---|
| 1 | **Quick fix** | < 30 min | Issue simples (título + 1 linha de contexto) | Sim | Sim (revisão rápida) |
| 2 | **Improvement** | 30 min – 2h | Issue com descrição + critério de done | Sim | Sim |
| 3 | **Feature** | > 2h | Épico + issues filhas geradas do `tasks.md` | Sim | Sim |

### Checkpoints obrigatórios (todos os níveis)

**Antes de começar** — criar/identificar a issue, mover para *Doing*, anotar o número (`#123`).
**Durante** — commits referenciam `#123`; para features, cada task do `tasks.md` vira uma issue filha fechada conforme concluída.
**Ao finalizar** — `Closes #123` no corpo do MR; pipeline verde; revisão humana aprovada antes do merge.

### Workflow de Feature (nível 3)

Não pular etapas. Aprovação humana em cada portão.

1. **Specs** — `requirements.md` → `design.md` → `tasks.md`, aguardando aprovação a cada documento. Agente: `spec-writer`.
2. **Issues** — criar épico + uma issue por task antes de implementar.
3. **Plano de execução** — agente `orquestrador` transforma o `tasks.md` em waves (o que é sequencial, o que roda em paralelo).
4. **Migration primeiro** — se a feature mexe em schema, a migration vem antes do código de aplicação. Gate: agente `dba-oracle` revisa antes de aplicar.
5. **Backend antes do frontend** — endpoint + teste passando antes de tocar em Angular.
6. **Test-first em `service/`** — regra de negócio tem teste escrito antes da implementação.
7. **Revisão de segurança** — agente `seguranca` após a implementação do backend, antes do MR. Obrigatória quando a feature toca autenticação, autorização, dado pessoal, upload de arquivo ou integração externa.
8. **Pipeline verde** — `./mvnw verify` e `npm run lint && npm run build` locais antes de abrir o MR.
9. **Design review** — agente `design-reviewer` após o frontend pronto: screenshots multi-viewport, acessibilidade, aderência ao design system.
10. **CHANGELOG.md** — entrada antes de considerar a tarefa concluída.
11. **QA** — agente `qa` cruza specs × código × testes e gera relatório de gaps antes de fechar o épico.

---

## 🔄 Fluxo Ponta a Ponta (orquestração)

Uma feature percorre a mesma cadeia, sempre. Cada seta é um **portão com aprovação humana** — o agente para e espera.

```
/spec-create <feature>
   requirements.md  ──▶ (aprovação humana)
   design.md        ──▶ (aprovação humana)
   tasks.md         ──▶ (aprovação humana)
        │
/criar-issues <feature>
   issue-mãe + uma issue por task no GitLab, com dependências e critério de done
        │
/implementar <feature>
   orquestrador monta as waves ──▶ (aprovação do plano)
        │
   Wave 1..N  ── implementação, teste e pipeline verde a cada wave
        │
   Portões, nesta ordem, sem pular:
     1. dba-oracle       (se mexeu no schema — antes de aplicar a migration)
     2. seguranca        (se toca auth, dado pessoal, upload ou integração)
     3. design-reviewer  (se tem tela)
     4. qa               (sempre — último portão)
        │
   CHANGELOG.md ──▶ Merge Request ──▶ (revisão e merge humanos)
```

Comandos de apoio: `/spec-review <feature>`, `/spec-status`, `/bug-fix <descrição>`, `/check-changelog`.

**Regras da cadeia**
- Nenhuma etapa começa antes de a anterior estar aprovada.
- Achado 🔴 em qualquer portão é corrigido antes do portão seguinte.
- O agente abre o MR; **quem faz merge é humano**.
- Ideia boa fora da spec vira issue nova — nunca commit extra na feature em andamento.

---

## 🤖 Agentes disponíveis

Definidos em `.claude/agents/`. Ver `.claude/agents/README.md` para o catálogo completo e quando invocar cada um.

| Agente | Papel | Escreve código? |
|---|---|---|
| `spec-writer` | Redige specs (requirements → design → tasks) | Não (só specs) |
| `orquestrador` | Converte `tasks.md` em plano de execução com waves paralelas | Não |
| `dba-oracle` | Revisa schema, migrations, índices e SQL/JPA | Não (audita) |
| `seguranca` | Audita segurança da aplicação e conformidade com a LGPD | Não (audita) |
| `qa` | Verifica implementação × specs e reporta gaps | Não (audita) |
| `design-reviewer` | Audita UI, acessibilidade e aderência ao design system | Não (audita) |

**Regra**: agentes auditores nunca escrevem código de produção. Eles produzem relatório; a correção é feita pelo fluxo normal, com issue.

## 🧩 Skills (comandos de fluxo)

Definidas em `.claude/skills/`. Invocadas como `/nome`.

| Comando | O que faz |
|---|---|
| `/spec-create <feature>` | Cria os três documentos de spec, parando para aprovação a cada um |
| `/criar-issues <feature>` | Converte o `tasks.md` aprovado em issues no GitLab |
| `/implementar <feature>` | Executa a feature em waves e roda os portões até o Merge Request |
| `/spec-review <feature>` | Revisa as specs e aponta lacunas de rastreabilidade |
| `/spec-status` | Mostra em que etapa está cada feature do projeto |
| `/bug-fix <descrição>` | Fluxo de correção: reportar → analisar → corrigir → verificar |
| `/check-changelog` | Registra no CHANGELOG.md o que a sessão produziu, decidiu e aprendeu |

---

## 🗂️ Estrutura do Repositório

Ver `.claude/steering/structure.md` para o mapa completo. Pontos rápidos:

- **`backend/src/main/resources/db/migration/`** manda no schema. JPA **valida**, não define.
- **`backend/src/main/java/{{PACOTE_BASE}}/service/`** é onde mora a regra de negócio. Código crítico, com teste obrigatório.
- **`frontend/src/app/core/`** = singletons (interceptors, guards, serviços de API). **`shared/`** = componentes reutilizáveis. **`features/`** = telas por domínio, lazy-loaded.
- **`docs/adr/`** guarda o porquê das decisões. **`specs/`** guarda o que será construído. **`CHANGELOG.md`** guarda o que já aconteceu.

---

## 📚 Documentos a Consultar (em ordem)

1. `docs/PRD.md` — o quê e para quem
2. `.claude/steering/product.md` — visão de produto permanente
3. `.claude/steering/tech.md` — restrições técnicas permanentes
4. `.claude/steering/structure.md` — mapa do repositório
5. `docs/adr/` — por quê (decisões de arquitetura)
6. `docs/modelo-de-dados.md` — schema, entidades e relacionamentos
7. `CHANGELOG.md` — o que já foi feito, o que falhou, lições aprendidas

---

## 🤝 Como Lidar com Incertezas

- **Em dúvida sobre regra de negócio?** Pergunte ao humano. Nunca chute — regra de negócio errada em sistema público vira processo.
- **Em dúvida sobre o schema?** Leia as migrations. Não deduza pelo nome da entidade.
- **Em dúvida se algo está no escopo?** Releia a seção "Fora de Escopo" do `requirements.md` da feature.
- **Em dúvida sobre arquitetura?** Leia o ADR relevante. Se não houver, **pare e proponha um ADR novo** antes de implementar.
- **Erro persistente após 2 tentativas?** Pare. Registre no `CHANGELOG.md` o que falhou e por quê. Peça ajuda. Não insista em loop.

---

**Última atualização**: {{DATA}} | **Responsável**: {{EQUIPE}}
