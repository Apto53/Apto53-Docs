# Histórico e contexto inicial — Apto 53

Documento de memória do projeto: o que foi discutido e decidido antes (e durante) a criação deste repositório de docs. Serve para mudanças futuras sem depender do histórico de chat do Cursor (workspace antigo: `api_ape`).

**Produto:** Apto 53  
**Repo de docs (fonte da verdade):** `Apto53-Docs` (antes `api_ape`)  
**Repos futuros de código:** `Apto53-api`, `Apto53-web` (somente citam IDs: `UC##`, `RNF##`, `ADR-##`, `DCR-##`)

---

## 1. Problema original

Casal coletando manualmente dados de apartamentos para alugar (Campinas), a partir de portais:

- Quinto Andar
- Chaves na Mão
- Imovelweb
- Viva Real
- Zap Imóveis
- (OLX discutido; alinhar na ERS se entrar formalmente)

Dor: colar link e preencher tudo na mão. Desejo: sistema simples, uso simultâneo a dois; ao colar o link, a API preenche os campos; usuários só completam **notas** e **pontos positivos** / correções.

---

## 2. Modelo de dados do apartamento

Campos levantados para o catálogo:

| Campo | Origem típica |
|-------|----------------|
| Condomínio | API (link) ou manual |
| Bairro | API ou manual |
| Status | API (default Disponível) ou manual |
| Aluguel + Cond. (R$) | API ou manual (soma se vier separado) |
| Metros² | API ou manual |
| Qrts / Banh | API ou manual (ex.: `2+1`) |
| Pet Friendly | API ou manual (`Sim` / `Nao` / `?`) |
| IPTU (R$) | API ou manual (convenção mensal vs anual a documentar na implementação) |
| Nota por membro | Manual (não colunas fixas “Cauê/Jany”) |
| Nota média | Calculada |
| Link | URL do anúncio (obrigatória no fluxo por link; opcional no manual) |
| Características | API tenta; usuário complementa |
| `source` | `link` \| `manual` |

---

## 3. Autenticação, catálogo e papéis

- **Usuário:** nome, email, senha (hash Argon2/bcrypt).
- **Catálogo:** conjunto de apartamentos com acesso restrito.
- **Papéis:**
  - **Criador (dono):** apartamentos, convites, remover membros, alterar papéis, apagar catálogo.
  - **Editor:** adicionar/editar/remover apartamentos e visualizar.
  - **Visualizador:** só ler.
- Autorização na API em **toda** rota de catálogo/apartamento (membership + papel), não só no front.
- Notas: **uma nota por membro** do catálogo; média sobre quem avaliou.
- Cadastro de apartamento: **por link** (`from-url`) **ou manual** — só criador/editor.

Casos de uso: ver `DOCS/especificacao_requisitos_software.md` (UC01–UC15) e `DOCS/rastreabilidade_requisitos.md`.

---

## 4. Importação por link (pensamento + decisão)

Pipeline mental:

```text
URL → auth + papel → detectar portal → fetch → parser do portal
  → normalizar DTO Apartamento → salvar source=link (aceitar campos parciais)
```

- Um **adapter/parser por portal**, não scraper genérico.
- Campos faltando → `null` / `?` / `[]`; não falhar o cadastro inteiro (UC09 FA04).
- Opções técnicas avaliadas: HTTP+HTML, HTTP+JSON embutido, headless, SaaS de scrape, extensão no browser, LLM.
- **Decisão:** browser headless (Playwright ou equivalente) no próprio projeto — ver [ADR-01](diario-decisoes/ADR/ADR-01-browser-headless-importacao.md).
- LLM e SaaS ficam fora do núcleo (custo/complexidade para uso pessoal).

---

## 5. Documentação e processo

Artefatos canônicos neste repo:

| Artefato | Papel |
|----------|--------|
| `DOCS/especificacao_requisitos_software.md` | ERS / casos de uso |
| `DOCS/rastreabilidade_requisitos.md` | Matriz UC/RNF ↔ API/Web/ADR·DCR |
| `DOCS/contexto_modulos.md` | Ponte — mapa vivo está em `Apto53-api/contexto_modulos.md` |
| `templates/contexto_modulos.md` | Esqueleto genérico de contexto por módulo |
| `DOCS/diario-decisoes/ADR/` | Decisões arquiteturais |
| `DOCS/diario-decisoes/DCR/` | Processos / workflows / regras |
| `templates/` | ERS + diário (ADR/DCR) + contexto |

Regra Cursor sempre aplicada: `.cursor/rules/acordos-documentacao.mdc`.

**ADR vs DCR:**

- **ADR** — por que (decisão arquitetural significativa).
- **DCR** — como processos/workflows/regras se relacionam (notação/documentação de processo).

Template único: `templates/diario-decisoes.md` → gravar em `ADR/ADR-##-...` ou `DCR/DCR-##-...`.

Decisões já registradas:

- [ADR-01](diario-decisoes/ADR/ADR-01-browser-headless-importacao.md) — browser headless na importação.
- [ADR-02](diario-decisoes/ADR/ADR-02-repositorio-docs-fonte-verdade.md) — repo só de docs como fonte da verdade.
- [ADR-03](diario-decisoes/ADR/ADR-03-stack-arquitetura-modular-api.md) — stack TS/Fastify/Playwright/Postgres/Prisma + arquitetura modular.
- [ADR-04](diario-decisoes/ADR/ADR-04-estrategia-testes-api.md) — unit/contrato por UC; E2E API no PR/deploy.
- [DCR-01](diario-decisoes/DCR/DCR-01-processo-pull-request.md) — processo de PR (atualização direta nos docs **ou** via código + `docs-delta`).

---

## 6. Multi-repo e rastreabilidade (sem copiar a ERS)

Três repositórios previstos:

| Repo | Conteúdo |
|------|----------|
| `Apto53-Docs` | ERS, rastreabilidade, ADR/DCR, templates (sem mapa vivo de código) |
| `Apto53-api` | Backend + `contexto_modulos.md` vivo; cita IDs; sem cópia da ERS |
| `Apto53-web` | Frontend — idem (contexto próprio quando existir) |

Conformidade:

1. Mudança de comportamento declara `UC##` / `RNF##` (ou cria requisito no mesmo pacote de docs).
2. Atualiza linha na matriz de rastreabilidade (colunas API e/ou Web).
3. Na api: atualiza `contexto_modulos.md` quando paths/contratos mudam.
4. Opcional futuro: **automation bridge** (PR de código com `docs-delta` abre PR no docs).

Contrato `docs-delta` (no PR de código):

```markdown
## docs-delta
- requisitos: UC09, RNF08
- impacto: api # api | web | ambos
- resumo: <o que mudou em uma frase>
- arquivos-docs: DOCS/rastreabilidade_requisitos.md, DOCS/especificacao_requisitos_software.md
```

API/Web **não** precisam entender o requisito sozinhas: quem muda o código declara o ID; o docs atualiza o texto e a matriz.

---

## 7. Rename do workspace

- Pasta/projeto: `api_ape` → **`Apto53-Docs`**.
- Histórico de chat do Cursor pode continuar associado ao path antigo; este arquivo preserva o contexto de produto e processo.
- Para chats novos: abrir a pasta `/home/caue/Dev_Projects/Apto53-Docs`.

---

## 8. O que ainda não está fechado (para o futuro)

- Implementação dos UC01–UC15 no `Apto53-api` (esqueleto/scaffold já previstos).
- Convenção IPTU (mensal vs anual) na implementação.
- OLX na lista formal de portais (RNF), se for entrar.
- UX import: salvar direto vs preview+confirma.
- Automation bridge e repo `Apto53-web`.
- Fluxo de convite detalhado (link por email vs código).
- CI GitHub Actions completo na api.

**Já decidido:** stack + arquitetura modular (ADR-03), testes (ADR-04), contexto vivo na api + template no Docs.

---

## 9. Como usar este arquivo

- Antes de mudar comportamento: ler ERS + rastreabilidade; no código, o `contexto_modulos.md` da api; se a decisão for estrutural, ADR/DCR.
- Se a dúvida for “por que decidimos X no começo?”, consultar este histórico e o ADR/DCR correspondente.
- Não duplicar a ERS nos repos de código — só IDs e link para `Apto53-Docs`.
