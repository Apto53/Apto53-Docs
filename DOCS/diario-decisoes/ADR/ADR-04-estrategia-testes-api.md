## [ADR-04] Estratégia de testes automatizados do Apto53-api

Neste documento:

1.  [Contexto](#contexto)
2.  [Opções](#opções)
3.  [Decisão](#decisão)
4.  [Consequências](#consequências)
5.  [Referências](#referências)
6.  [Versões](#versões)
7.  [Revisões](#revisões)

### Contexto

O `Apto53-api` deve manter conformidade com os casos de uso da ERS (`UC01`–`UC15`) e com autorização por catálogo/papel, sem tornar o CI frágil por dependência de portais imobiliários reais (anti-bot, layout instável, latência).

É necessário definir **quais tipos de teste existem**, **onde moram no repositório modular** ([ADR-03](./ADR-03-stack-arquitetura-modular-api.md)), **quando rodam** (PR e deploy) e o que fica **fora do gate**.

### Opções

Foram consideradas as seguintes opções:

* **Apenas E2E HTTP:** poucos testes de jornada cobrem tudo.
    * **Prós:** valida fluxo real.
    * **Contras:** lentos; falhas opacas; difícil amarrar a um UC isolado.
* **Apenas unitários:** regras isoladas com mocks.
    * **Prós:** rápidos.
    * **Contras:** não garantem contrato HTTP nem encadeamento entre UCs.
* **Pirâmide unitário + contrato + E2E API:** testes por UC na base; jornadas na ponta.
    * **Prós:** rastreável por `UC##`; estável; adequado a Fastify `inject` + Prisma.
    * **Contras:** exige convenção de pastas/nomes e Postgres de teste.
* **E2E com Playwright contra portais reais no PR:** máxima fidelidade do scrape.
    * **Prós:** pega regressão de HTML real.
    * **Contras:** flaky; lento; risco de bloqueio; inadequado como gate de merge.

### Decisão

Adotar a **pirâmide unitário + contrato + E2E API**, com runner **Vitest**, alinhada à arquitetura modular (ADR-03).

| Tipo | O que valida | Onde | Quando |
| ---- | ------------ | ---- | ------ |
| **Unitário por UC** | Regra pura / parser / policy | `src/modules/**/uc##-*.unit.spec.ts` | PR e deploy |
| **Contrato por UC/endpoint** | Status HTTP + shape JSON + erros | `src/modules/**/uc##-*.contract.spec.ts` (Fastify `inject`) | PR e deploy |
| **E2E API** | Jornada cross-módulo | `test/e2e/` | PR e deploy |
| **Scrape portal real** | HTML/API do site ao vivo | fora do gate (manual ou job opcional) | **não** bloqueia PR |

**Regras:**

- Todo UC documentado deve ter pelo menos um teste **unitário** ou de **contrato** com ID `uc##` no nome do arquivo.
- Contratos assertam status estáveis (`401` / `403` / `400` / `404`) e semântica de vazio (`null` vs `0`, `[]` vs omitido).
- E2E cobre histórias (ex.: register → catálogo → apt manual → nota; convite → papel; `from-url` com Import **mockado**).
- No CI de PR/deploy, `from-url` **não** abre Chromium contra QuintoAndar/Zap etc.: usar fake do Import ou fixtures em `src/modules/import/fixtures/`.
- Parsers de portal: testes unitários com fixtures HTML/JSON versionadas.
- Scripts previstos: `test:unit`, `test:contract`, `test:e2e` (e `test` agregando unit+contract; e2e pode ser job separado mais lento).
- Ambiente de integração/E2E: PostgreSQL de teste (Docker/compose); app via `buildApp()` do Fastify.

**Data:** 07/10/2026

### Consequências

#### Benefícios

* Rastreabilidade UC → arquivo de teste.
* CI previsível sem flaky de portal.
* Contratos protegem o futuro `Apto53-web` sem exigir E2E de front no MVP da api.

#### Malefícios

* Manter fixtures de portal quando o HTML mudar.
* Postgres de teste adiciona setup local/CI.

#### Riscos

* Confiar só em mock do Import e regressão silenciosa no portal — mitigar com fixtures e smoke manual/opcional fora do PR.
* E2E demais — mitigar: poucas jornadas críticas; detalhe fica em unit/contrato.

### Referências

* [ADR-03 — Stack e arquitetura modular](./ADR-03-stack-arquitetura-modular-api.md)
* [ADR-01 — Browser headless](./ADR-01-browser-headless-importacao.md)
* [ERS](../../especificacao_requisitos_software.md)
* [Rastreabilidade](../../rastreabilidade_requisitos.md)
* [Vitest](https://vitest.dev/)
* [Fastify inject](https://fastify.dev/docs/latest/Reference/Routes/#shorthand-declaration)

### Versões

| Versão | Data | Descrição | Responsável |
| :---- | :---- | :---- | :---- |
| 1.0 | 07/10/2026 | Versão inicial | Cauê Moraes |

### Revisões

| Data | Responsável |
| :---- | :---- |
| 07/10/2026 | Cauê Moraes |
