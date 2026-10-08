## [ADR-03] Stack e arquitetura modular do Apto53-api

Neste documento:

1.  [Contexto](#contexto)
2.  [Opções](#opções)
3.  [Decisão](#decisão)
4.  [Consequências](#consequências)
5.  [Referências](#referências)
6.  [Versões](#versões)
7.  [Revisões](#revisões)

### Contexto

O backend do **Apto 53** (`Apto53-api`) precisa de uma stack e de uma organização de código alinhadas aos módulos já descritos na ERS (Auth, Catálogos, Apartamentos, Avaliações, Importação), ao [ADR-01](./ADR-01-browser-headless-importacao.md) (browser headless na importação por link) e ao [ADR-02](./ADR-02-repositorio-docs-fonte-verdade.md) (docs em repositório separado).

Sem padrão explícito, o risco é misturar scrape com persistência, espalhar checagem de papéis, ou adotar stack desproporcional ao uso pessoal (dois usuários, volume baixo). Também é necessário definir onde mora o **contexto de implementação** (paths/contratos): no repo da api, não como cópia da ERS no Docs.

### Opções

Foram consideradas as seguintes opções:

* **Camadas clássicas (routes → services → repositories):** organização por tipo técnico.
    * **Prós:** familiar; simples no início.
    * **Contras:** `services/` vira saco de regras; UC e módulos da ERS ficam espalhados; Import tende a misturar com Apartments.
* **Arquitetura modular por feature (vertical slice leve):** pastas por domínio do produto (`auth`, `catalogs`, `apartments`, `ratings`, `import`) + `shared/` fino.
    * **Prós:** espelha a ERS e o mapa mental do time; Import isolado; use cases por `UC##`; facilita testes colocalizados.
    * **Contras:** exige disciplina nas fronteiras entre módulos.
* **Hexagonal completo (ports & adapters em tudo):** domínio puro com adapters HTTP/Prisma/Playwright.
    * **Prós:** testabilidade alta do núcleo.
    * **Contras:** overhead de interfaces para MVP pessoal a dois.
* **Stack TypeScript + Fastify + Playwright + Postgres + Prisma:** API e scrape no mesmo ecossistema JS/TS.
    * **Prós:** Playwright de primeira classe; tipagem compartilhada com front futuro; Prisma adequado a Postgres; Fastify leve com `inject` para testes.
    * **Contras:** deploy precisa de runtime Node + Chromium.
* **Python + FastAPI + Playwright:** alternativa sólida para API + scrape.
    * **Prós:** FastAPI maduro; Playwright oficial.
    * **Contras:** duas linguagens se o front for React/TS.
* **Java + Spring (+ Selenium/Playwright externo):** stack enterprise.
    * **Prós:** tipagem e estrutura rígidas.
    * **Contras:** atrito com headless/SPA; overkill para o escopo; distância do front TS.

### Decisão

Adotar no **`Apto53-api`**:

1. **Stack:** TypeScript + Fastify + Playwright + PostgreSQL + Prisma (+ Vitest para testes — ver [ADR-04](./ADR-04-estrategia-testes-api.md)).
2. **Organização:** arquitetura **modular por feature**, com `shared/` restrito a HTTP/app factory, Prisma client, JWT e policies de membership/papel.
3. **Contexto de implementação vivo** em `Apto53-api/contexto_modulos.md` (template canônico em `Apto53-Docs/templates/contexto_modulos.md`). O Docs **não** mantém o mapa de paths do código.

**Árvore canônica:**

```text
Apto53-api/
  contexto_modulos.md
  src/
    modules/
      auth/
      catalogs/
      apartments/
      ratings/
      import/              # Playwright + parsers; NÃO persiste
    shared/
      http/                # buildApp, erros
      db/                  # Prisma client
      auth/                # JWT
      policies/            # membership / papel
  prisma/
  test/
    setup/
    helpers/
    e2e/
```

**Fluxo de request:** HTTP (Fastify) → authn (JWT) → authz (membership/papel no catálogo) → use case do módulo → Prisma e/ou Import → resposta.

**UC09:** `apartments` orquestra `from-url` (policy → Import → draft → persistência com `source=link`). Import só devolve `ApartmentDraft`.

**Boas práticas (padrão obrigatório):**

- Import não persiste e não expõe scrape genérico sem membership de criador/editor.
- Toda rota sob `/catalogs/:id/...` exige membership; escrita de apartamento = criador ou editor; gestão de membros/convites/exclusão de catálogo = só criador.
- Sem autenticação → `401`; autenticado sem papel → `403`.
- Nota: cada membro edita apenas a própria; média calculada.
- Campo ausente no scrape → `null` / `?` / `[]`; não inventar preço, m² ou IPTU.
- Queries de domínio sempre escopadas a `catalogId` (+ membership).
- Senha apenas em hash (Argon2/bcrypt); não logar senha/token (RNF12).
- Use cases nomeáveis por `UC##`; `shared/` sem regra de negócio de catálogo/apartamento.
- Mudança de comportamento de produto → `docs-delta` com IDs e atualização no Apto53-Docs (DCR-01).

**Data:** 07/10/2026

### Consequências

#### Benefícios

* Stack alinhada ao ADR-01 e a um front TS futuro.
* Módulos espelham requisitos; Import isolado reduz bugs de persistência indevida.
* Contexto vivo na api orienta IA/time sem duplicar a ERS.
* Boas práticas explícitas reduzem erros clássicos de authz e scrape.

#### Malefícios

* Deploy Node + Chromium mais pesado que HTTP puro.
* Disciplina de fronteira entre módulos é contínua.

#### Riscos

* `shared/` inchado — mitigar: só infra transversal; regra de negócio fica no módulo.
* Esquecer docs-delta — mitigar: template de PR e rules no `Apto53-api`.
* Contexto da api desatualizado — mitigar: rule Cursor `alwaysApply` no repo da api.

### Referências

* [ADR-01 — Browser headless](./ADR-01-browser-headless-importacao.md)
* [ADR-02 — Apto53-Docs fonte da verdade](./ADR-02-repositorio-docs-fonte-verdade.md)
* [ADR-04 — Estratégia de testes](./ADR-04-estrategia-testes-api.md)
* [ERS](../../especificacao_requisitos_software.md)
* [Rastreabilidade](../../rastreabilidade_requisitos.md)
* [Template contexto de módulos](../../../templates/contexto_modulos.md)
* [DCR-01 — Processo de PR](../DCR/DCR-01-processo-pull-request.md)

### Versões

| Versão | Data | Descrição | Responsável |
| :---- | :---- | :---- | :---- |
| 1.0 | 07/10/2026 | Versão inicial | Cauê Moraes |

### Revisões

| Data | Responsável |
| :---- | :---- |
| 07/10/2026 | Cauê Moraes |
