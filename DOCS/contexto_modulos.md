# Contexto por módulo — ponte (Apto 53)

O **mapa de implementação** (paths, contratos de código, testes de referência) **não mora mais neste repositório**.

| Artefato | Onde |
| -------- | ---- |
| Contexto vivo da API (atualizar a cada feat) | Repositório **`Apto53-api`** → `contexto_modulos.md` |
| Template genérico (esqueleto) | [`templates/contexto_modulos.md`](../templates/contexto_modulos.md) |
| Requisitos (ERS) | [`especificacao_requisitos_software.md`](./especificacao_requisitos_software.md) |
| Rastreabilidade | [`rastreabilidade_requisitos.md`](./rastreabilidade_requisitos.md) |
| Stack e arquitetura modular | [ADR-03](./diario-decisoes/ADR/ADR-03-stack-arquitetura-modular-api.md) |
| Estratégia de testes | [ADR-04](./diario-decisoes/ADR/ADR-04-estrategia-testes-api.md) |
| Browser headless (importação) | [ADR-01](./diario-decisoes/ADR/ADR-01-browser-headless-importacao.md) |
| Docs como fonte da verdade | [ADR-02](./diario-decisoes/ADR/ADR-02-repositorio-docs-fonte-verdade.md) |
| Processo de PR | [DCR-01](./diario-decisoes/DCR/DCR-01-processo-pull-request.md) |

**Produto:** Apto 53. **Docs:** Apto53-Docs (requisitos e decisões). **Código:** Apto53-api / Apto53-web (sem cópia da ERS).

Ao implementar ou alterar a API: atualizar o contexto **no repo da api**. Se o comportamento de produto mudar: atualizar ERS/rastreabilidade/ADR aqui (docs-delta — DCR-01).
