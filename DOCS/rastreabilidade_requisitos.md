## Rastreabilidade de Requisitos

### 1. Objetivo

Este documento estabelece a rastreabilidade bidirecional entre os requisitos do Sistema **Apto 53**, descritos em [especificacao_requisitos_software.md](./especificacao_requisitos_software.md), e os demais artefatos do produto (código em `Apto53-api` / `Apto53-web`, ADRs/DCRs). A fonte da verdade documental é o repositório **Apto53-Docs** ([ADR-02](./diario-decisoes/ADR/ADR-02-repositorio-docs-fonte-verdade.md)). O processo de PR (atualização direta ou via código + docs-delta) está em [DCR-01](./diario-decisoes/DCR/DCR-01-processo-pull-request.md).

Repos de código **não** duplicam a ERS: preenchem as colunas da matriz citando os IDs (`UC##`, `RNF##`) no `docs-delta`. O mapa de implementação da API vive em `Apto53-api/contexto_modulos.md` ([ponte](./contexto_modulos.md)).

### 2. Inventário de requisitos

#### 2.1. Casos de uso (requisitos funcionais)

| ID | Nome | Atores principais |
| -- | ---- | ----------------- |
| UC01 | Cadastrar usuário | Visitante |
| UC02 | Autenticar usuário | Visitante |
| UC03 | Criar catálogo | Criador |
| UC04 | Listar catálogos do usuário | Criador, Editor, Visualizador |
| UC05 | Convidar membro ao catálogo | Criador |
| UC06 | Aceitar convite | Usuário convidado (torna-se Editor ou Visualizador) |
| UC07 | Alterar papel de membro | Criador |
| UC08 | Remover membro do catálogo | Criador |
| UC09 | Adicionar apartamento a partir de link | Criador, Editor |
| UC10 | Adicionar apartamento manualmente | Criador, Editor |
| UC11 | Listar apartamentos do catálogo | Criador, Editor, Visualizador |
| UC12 | Editar apartamento | Criador, Editor |
| UC13 | Remover apartamento | Criador, Editor |
| UC14 | Avaliar apartamento | Criador, Editor, Visualizador |
| UC15 | Excluir catálogo | Criador |

#### 2.2. Requisitos não-funcionais

| ID | Resumo |
| -- | ------ |
| RNF01 | Hash seguro de senha |
| RNF02 | Autenticação obrigatória em recursos protegidos |
| RNF03 | Autorização por membership e papel |
| RNF04 | Isolamento entre catálogos |
| RNF05 | Convite com token e expiração |
| RNF06 | Aceite de convite restrito ao e-mail convidado |
| RNF07 | HTTPS em produção |
| RNF08 | Rate limiting em login e importação por link |
| RNF09 | Origem `link` ou `manual` no apartamento |
| RNF10 | Portais suportados na importação |
| RNF11 | Cálculo de nota média |
| RNF12 | Sem vazamento de segredos em logs |

### 3. Matriz de rastreabilidade (requisito → artefatos)

Preencher as colunas à medida que os artefatos forem criados (via PR direto em Apto53-Docs ou docs-delta vindo da api/web — DCR-01). Convenção de testes: [ADR-04](./diario-decisoes/ADR/ADR-04-estrategia-testes-api.md). Organização da API: [ADR-03](./diario-decisoes/ADR/ADR-03-stack-arquitetura-modular-api.md).

| Requisito | Modelo / domínio | API | Web | Testes | ADR / DCR |
| --------- | ---------------- | --- | --- | ------ | --------- |
| UC01 | User | `POST /auth/register` (módulo auth) | | `uc01-*.unit\|contract` | [ADR-03](./diario-decisoes/ADR/ADR-03-stack-arquitetura-modular-api.md), [ADR-04](./diario-decisoes/ADR/ADR-04-estrategia-testes-api.md) |
| UC02 | User | `POST /auth/login`, `GET /auth/me` | | `uc02-*.unit\|contract` | [ADR-03](./diario-decisoes/ADR/ADR-03-stack-arquitetura-modular-api.md), [ADR-04](./diario-decisoes/ADR/ADR-04-estrategia-testes-api.md) |
| UC03 | Catalog, Membership | `POST /catalogs` | | `uc03-*.unit\|contract` | [ADR-03](./diario-decisoes/ADR/ADR-03-stack-arquitetura-modular-api.md), [ADR-04](./diario-decisoes/ADR/ADR-04-estrategia-testes-api.md) |
| UC04 | Catalog, Membership | `GET /catalogs` | | `uc04-*.unit\|contract` | [ADR-03](./diario-decisoes/ADR/ADR-03-stack-arquitetura-modular-api.md), [ADR-04](./diario-decisoes/ADR/ADR-04-estrategia-testes-api.md) |
| UC05 | Invite | `POST /catalogs/:id/invites` | | `uc05-*.unit\|contract` | [ADR-03](./diario-decisoes/ADR/ADR-03-stack-arquitetura-modular-api.md), [ADR-04](./diario-decisoes/ADR/ADR-04-estrategia-testes-api.md) |
| UC06 | Invite, Membership | `POST /invites/:token/accept` | | `uc06-*.unit\|contract` | [ADR-03](./diario-decisoes/ADR/ADR-03-stack-arquitetura-modular-api.md), [ADR-04](./diario-decisoes/ADR/ADR-04-estrategia-testes-api.md) |
| UC07 | Membership | `PATCH /catalogs/:id/members/:userId` | | `uc07-*.unit\|contract` | [ADR-03](./diario-decisoes/ADR/ADR-03-stack-arquitetura-modular-api.md), [ADR-04](./diario-decisoes/ADR/ADR-04-estrategia-testes-api.md) |
| UC08 | Membership | `DELETE /catalogs/:id/members/:userId` | | `uc08-*.unit\|contract` | [ADR-03](./diario-decisoes/ADR/ADR-03-stack-arquitetura-modular-api.md), [ADR-04](./diario-decisoes/ADR/ADR-04-estrategia-testes-api.md) |
| UC09 | Apartment, Import | `POST .../apartments/from-url` + import headless | | `uc09-*.unit\|contract`; fixtures import; E2E com Import mock | [ADR-01](./diario-decisoes/ADR/ADR-01-browser-headless-importacao.md), [ADR-03](./diario-decisoes/ADR/ADR-03-stack-arquitetura-modular-api.md), [ADR-04](./diario-decisoes/ADR/ADR-04-estrategia-testes-api.md) |
| UC10 | Apartment | `POST /catalogs/:id/apartments` | | `uc10-*.unit\|contract` | [ADR-03](./diario-decisoes/ADR/ADR-03-stack-arquitetura-modular-api.md), [ADR-04](./diario-decisoes/ADR/ADR-04-estrategia-testes-api.md) |
| UC11 | Apartment | `GET /catalogs/:id/apartments` | | `uc11-*.unit\|contract` | [ADR-03](./diario-decisoes/ADR/ADR-03-stack-arquitetura-modular-api.md), [ADR-04](./diario-decisoes/ADR/ADR-04-estrategia-testes-api.md) |
| UC12 | Apartment | `PATCH /catalogs/:id/apartments/:aptId` | | `uc12-*.unit\|contract` | [ADR-03](./diario-decisoes/ADR/ADR-03-stack-arquitetura-modular-api.md), [ADR-04](./diario-decisoes/ADR/ADR-04-estrategia-testes-api.md) |
| UC13 | Apartment | `DELETE /catalogs/:id/apartments/:aptId` | | `uc13-*.unit\|contract` | [ADR-03](./diario-decisoes/ADR/ADR-03-stack-arquitetura-modular-api.md), [ADR-04](./diario-decisoes/ADR/ADR-04-estrategia-testes-api.md) |
| UC14 | Rating | `PUT .../apartments/:aptId/rating` | | `uc14-*.unit\|contract` | [ADR-03](./diario-decisoes/ADR/ADR-03-stack-arquitetura-modular-api.md), [ADR-04](./diario-decisoes/ADR/ADR-04-estrategia-testes-api.md) |
| UC15 | Catalog | `DELETE /catalogs/:id` | | `uc15-*.unit\|contract` | [ADR-03](./diario-decisoes/ADR/ADR-03-stack-arquitetura-modular-api.md), [ADR-04](./diario-decisoes/ADR/ADR-04-estrategia-testes-api.md) |
| RNF01 | User.passwordHash | módulo auth | | unit hash | [ADR-03](./diario-decisoes/ADR/ADR-03-stack-arquitetura-modular-api.md) |
| RNF02 | | shared/auth JWT | | contract 401 | [ADR-03](./diario-decisoes/ADR/ADR-03-stack-arquitetura-modular-api.md), [ADR-04](./diario-decisoes/ADR/ADR-04-estrategia-testes-api.md) |
| RNF03 | Membership.role | shared/policies | | unit/contract 403 | [ADR-03](./diario-decisoes/ADR/ADR-03-stack-arquitetura-modular-api.md), [ADR-04](./diario-decisoes/ADR/ADR-04-estrategia-testes-api.md) |
| RNF04 | catalogId scope | policies + queries | | contract isolamento | [ADR-03](./diario-decisoes/ADR/ADR-03-stack-arquitetura-modular-api.md) |
| RNF05 | Invite | catalogs/invites | | `uc05` | [ADR-03](./diario-decisoes/ADR/ADR-03-stack-arquitetura-modular-api.md) |
| RNF06 | Invite.email | catalogs/invites | | `uc06` | [ADR-03](./diario-decisoes/ADR/ADR-03-stack-arquitetura-modular-api.md) |
| RNF07 | | deploy/ops | | | |
| RNF08 | | login + from-url | | contract/rate | [ADR-03](./diario-decisoes/ADR/ADR-03-stack-arquitetura-modular-api.md), [ADR-04](./diario-decisoes/ADR/ADR-04-estrategia-testes-api.md) |
| RNF09 | Apartment.source | apartments | | `uc09`/`uc10` | [ADR-03](./diario-decisoes/ADR/ADR-03-stack-arquitetura-modular-api.md) |
| RNF10 | Import parsers | modules/import | | fixtures unit | [ADR-01](./diario-decisoes/ADR/ADR-01-browser-headless-importacao.md), [ADR-03](./diario-decisoes/ADR/ADR-03-stack-arquitetura-modular-api.md) |
| RNF11 | Rating média | ratings | | `uc14` unit | [ADR-03](./diario-decisoes/ADR/ADR-03-stack-arquitetura-modular-api.md) |
| RNF12 | | logging | | | [ADR-03](./diario-decisoes/ADR/ADR-03-stack-arquitetura-modular-api.md) |

Decisões de processo / organização / técnicos:

| ID | Título | Arquivo |
| -- | ------ | ------- |
| ADR-01 | Browser headless na importação | [ADR-01](./diario-decisoes/ADR/ADR-01-browser-headless-importacao.md) |
| ADR-02 | Apto53-Docs como fonte da verdade | [ADR-02](./diario-decisoes/ADR/ADR-02-repositorio-docs-fonte-verdade.md) |
| ADR-03 | Stack e arquitetura modular do Apto53-api | [ADR-03](./diario-decisoes/ADR/ADR-03-stack-arquitetura-modular-api.md) |
| ADR-04 | Estratégia de testes do Apto53-api | [ADR-04](./diario-decisoes/ADR/ADR-04-estrategia-testes-api.md) |
| DCR-01 | Processo de pull request | [DCR-01](./diario-decisoes/DCR/DCR-01-processo-pull-request.md) |

### 4. Mapeamento preliminar requisito → endpoints previstos

Referência de planejamento (organização em [ADR-03](./diario-decisoes/ADR/ADR-03-stack-arquitetura-modular-api.md); não substitui a implementação em `Apto53-api`).

| Requisito | Endpoint previsto | Decisão / ADR |
| --------- | ----------------- | ------------- |
| UC01 | `POST /auth/register` | [ADR-03](./diario-decisoes/ADR/ADR-03-stack-arquitetura-modular-api.md) |
| UC02 | `POST /auth/login` | [ADR-03](./diario-decisoes/ADR/ADR-03-stack-arquitetura-modular-api.md) |
| UC03 | `POST /catalogs` | [ADR-03](./diario-decisoes/ADR/ADR-03-stack-arquitetura-modular-api.md) |
| UC04 | `GET /catalogs` | [ADR-03](./diario-decisoes/ADR/ADR-03-stack-arquitetura-modular-api.md) |
| UC05 | `POST /catalogs/:id/invites` | [ADR-03](./diario-decisoes/ADR/ADR-03-stack-arquitetura-modular-api.md) |
| UC06 | `POST /invites/:token/accept` | [ADR-03](./diario-decisoes/ADR/ADR-03-stack-arquitetura-modular-api.md) |
| UC07 | `PATCH /catalogs/:id/members/:userId` | [ADR-03](./diario-decisoes/ADR/ADR-03-stack-arquitetura-modular-api.md) |
| UC08 | `DELETE /catalogs/:id/members/:userId` | [ADR-03](./diario-decisoes/ADR/ADR-03-stack-arquitetura-modular-api.md) |
| UC09 | `POST /catalogs/:id/apartments/from-url` | [ADR-01](./diario-decisoes/ADR/ADR-01-browser-headless-importacao.md), [ADR-03](./diario-decisoes/ADR/ADR-03-stack-arquitetura-modular-api.md) |
| UC10 | `POST /catalogs/:id/apartments` | [ADR-03](./diario-decisoes/ADR/ADR-03-stack-arquitetura-modular-api.md) |
| UC11 | `GET /catalogs/:id/apartments` | [ADR-03](./diario-decisoes/ADR/ADR-03-stack-arquitetura-modular-api.md) |
| UC12 | `PATCH /catalogs/:id/apartments/:aptId` | [ADR-03](./diario-decisoes/ADR/ADR-03-stack-arquitetura-modular-api.md) |
| UC13 | `DELETE /catalogs/:id/apartments/:aptId` | [ADR-03](./diario-decisoes/ADR/ADR-03-stack-arquitetura-modular-api.md) |
| UC14 | `PUT /catalogs/:id/apartments/:aptId/rating` | [ADR-03](./diario-decisoes/ADR/ADR-03-stack-arquitetura-modular-api.md) |
| UC15 | `DELETE /catalogs/:id` | [ADR-03](./diario-decisoes/ADR/ADR-03-stack-arquitetura-modular-api.md) |
