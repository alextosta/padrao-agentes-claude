# Steering: Técnico

Restrições técnicas permanentes do projeto. Toda decisão de implementação respeita este documento.
Mudança aqui exige ADR em `docs/adr/`.

> **Template.** Ajuste as versões e ferramentas à realidade do órgão e apague o que não se aplica.

---

## Stack

### Backend
- **Linguagem**: Java {{VERSAO_JAVA}} (LTS)
- **Framework**: Spring Boot {{VERSAO_SPRING_BOOT}}
- **Build**: {{Maven|Gradle}} com wrapper versionado (`./mvnw`) — a versão do build não depende da máquina do dev
- **Persistência**: Spring Data JPA / Hibernate
- **Migrations**: {{Flyway|Liquibase}} — o schema é versionado no repositório
- **Validação**: Bean Validation (Jakarta)
- **Segurança**: Spring Security + {{PROVEDOR_IDENTIDADE}}
- **Documentação de API**: OpenAPI (springdoc)
- **Testes**: JUnit 5 + Mockito; integração com {{Testcontainers|banco de teste dedicado}}
- **Qualidade**: {{Checkstyle|Spotless|SonarQube}}

### Banco
- **SGBD**: Oracle {{VERSAO_ORACLE}}
- **Schema**: versionado exclusivamente em `backend/src/main/resources/db/migration/`
- **`ddl-auto`**: `validate` em todos os ambientes. Hibernate **valida**, não cria
- **Usuário da aplicação**: privilégios mínimos, sem DDL em produção

### Frontend
- **Framework**: Angular {{VERSAO_ANGULAR}}
- **Linguagem**: TypeScript em modo `strict`
- **Estilo**: {{Angular Material|design system do órgão|Tailwind}}
- **Estado**: {{signals|serviços com RxJS|NgRx}} — escolher um e manter
- **Testes**: {{Karma|Jest}} para unidade; {{Playwright|Cypress}} para ponta a ponta
- **Qualidade**: ESLint + Prettier

### Infraestrutura e entrega
- **Versionamento**: GitLab ({{URL_DA_INSTANCIA}})
- **CI/CD**: GitLab CI (`.gitlab-ci.yml`)
- **Empacotamento**: {{JAR executável|contêiner OCI}}
- **Ambientes**: desenvolvimento → homologação → produção
- **Segredos**: {{CI/CD variables protegidas|cofre}} — nunca no repositório
- **Observabilidade**: {{ferramenta de log|APM}}; Actuator com exposição restrita

---

## Decisões arquiteturais imutáveis

Não podem ser revertidas sem novo ADR e aprovação humana explícita.

1. **O schema é definido por migrations versionadas.** ADR-001.
2. **Arquitetura em camadas: `controller` → `service` → `repository`.** Regra de negócio vive em `service`. ADR-002.
3. **DTO nas bordas.** Entidade JPA não entra nem sai pela API. ADR-003.
4. **Autenticação centralizada em {{PROVEDOR_IDENTIDADE}}.** Sem base de senha própria. ADR-004.
5. **Erro de API no formato `ProblemDetail` (RFC 7807).** ADR-005.
6. **Versionamento de API na URL (`/api/v1/...`).** Quebra de contrato exige versão nova. ADR-006.

---

## Convenções

### Pacotes (backend)
```
{{PACOTE_BASE}}
├── config/          # configuração do Spring, segurança, OpenAPI
├── controller/      # entrada HTTP; sem regra de negócio
├── dto/             # request/response da API
├── domain/          # entidades JPA + enums do domínio
├── repository/      # Spring Data
├── service/         # regra de negócio (teste obrigatório)
├── mapper/          # conversão entidade ↔ DTO
├── exception/       # exceções de domínio + @RestControllerAdvice
└── integration/     # clientes de sistemas externos
```

### Nomenclatura
- **Java**: classes em `PascalCase`; métodos em `camelCase`; nomes em português quando forem do domínio (`ProcessoService`), em inglês quando forem técnicos (`PageRequest`). Escolha e mantenha — mistura sem critério é o que confunde.
- **Banco**: tabelas e colunas em `MAIÚSCULAS_COM_UNDERSCORE`; constraints nomeadas (`PK_`, `FK_`, `UK_`, `CK_`, `IX_`); nomes com no máximo 30 caracteres quando o banco for anterior ao 12.2.
- **Endpoints**: substantivo no plural, kebab-case (`/api/v1/processos-administrativos`).
- **Angular**: arquivos em kebab-case (`consulta-processo.component.ts`); componentes com sufixo de tipo.
- **Branches**: `feature/{{nome}}`, `fix/{{nome}}`, `chore/{{nome}}`.
- **Commits**: Conventional Commits com a issue — `feat(processo): permite anexar documento — #123`.

### Testes
- Toda regra de negócio em `service/` tem teste de unidade, escrito **antes** da implementação.
- Teste de integração cobre o caminho completo do endpoint crítico.
- Cobertura é indicador, não meta: teste sem `assert` significativo não conta.

---

## Limites conhecidos do ambiente

{{Registrar aqui o que é restrição real e não escolha: versão de JDK homologada pela infraestrutura,
ausência de Docker nas máquinas de desenvolvimento, proxy corporativo, janela de deploy,
indisponibilidade de banco de teste, tier do GitLab. O agente precisa saber disso para não
propor solução que não pode ser executada.}}
