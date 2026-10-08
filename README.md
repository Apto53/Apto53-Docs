# Apto53-Docs

Documentação do software **Apto 53**: sistema para catálogos compartilhados de apartamentos (cadastro manual ou por link, notas por membro, papéis criador/editor/visualizador).

## Onde encontrar o quê

| Precisa de… | Caminho |
| ----------- | ------- |
| Casos de uso e RNFs (ERS) | [`DOCS/especificacao_requisitos_software.md`](DOCS/especificacao_requisitos_software.md) |
| Matriz requisito → API / Web / testes / ADR | [`DOCS/rastreabilidade_requisitos.md`](DOCS/rastreabilidade_requisitos.md) |
| Índice / ponte de contexto | [`DOCS/contexto_modulos.md`](DOCS/contexto_modulos.md) |
| Memória do início do projeto | [`DOCS/historico-contexto-inicial.md`](DOCS/historico-contexto-inicial.md) |
| Decisões arquiteturais (ADR) | [`DOCS/diario-decisoes/ADR/`](DOCS/diario-decisoes/ADR/) |
| Processos e workflows (DCR) | [`DOCS/diario-decisoes/DCR/`](DOCS/diario-decisoes/DCR/) |
| Templates (ERS, diário, contexto) | [`templates/`](templates/) |
| Acordos de escrita de docs (Cursor) | [`.cursor/rules/acordos-documentacao.mdc`](.cursor/rules/acordos-documentacao.mdc) |

### ADRs e DCRs registrados

| ID | Tema |
| -- | ---- |
| [ADR-01](DOCS/diario-decisoes/ADR/ADR-01-browser-headless-importacao.md) | Browser headless na importação por link |
| [ADR-02](DOCS/diario-decisoes/ADR/ADR-02-repositorio-docs-fonte-verdade.md) | Este repo como fonte da verdade (multi-repo) |
| [ADR-03](DOCS/diario-decisoes/ADR/ADR-03-stack-arquitetura-modular-api.md) | Stack e arquitetura modular da API |
| [ADR-04](DOCS/diario-decisoes/ADR/ADR-04-estrategia-testes-api.md) | Estratégia de testes da API |
| [DCR-01](DOCS/diario-decisoes/DCR/DCR-01-processo-pull-request.md) | Processo de pull request e docs-delta |

## Estrutura

```text
Apto53-Docs/
├── README.md                 ← você está aqui
├── DOCS/
│   ├── especificacao_requisitos_software.md
│   ├── rastreabilidade_requisitos.md
│   ├── contexto_modulos.md   ← ponte (não mapa de código)
│   ├── historico-contexto-inicial.md
│   └── diario-decisoes/
│       ├── ADR/
│       └── DCR/
├── templates/                ← esqueletos para novos artefatos
└── .cursor/rules/            ← acordos de documentação
```

## Como atualizar a documentação

1. **Mudança só de docs** (requisito/ADR antes ou sem código): PR neste repo — ver [DCR-01](DOCS/diario-decisoes/DCR/DCR-01-processo-pull-request.md).
2. **Mudança vinda do código**: o PR de código declara um `docs-delta` com IDs; este repo é atualizado em seguida (manual ou bridge futuro).

```markdown
## docs-delta
- requisitos: UC09, RNF08
- impacto: api   # api | web | ambos
- resumo: <o que mudou em uma frase>
- arquivos-docs: DOCS/rastreabilidade_requisitos.md, DOCS/especificacao_requisitos_software.md
```

Novos ADR/DCR: copiar [`templates/diario-decisoes.md`](templates/diario-decisoes.md) para `DOCS/diario-decisoes/ADR/` ou `DCR/`.

## Relação com outros repositórios

| Repositório | Papel |
| ----------- | ----- |
| **Apto53-Docs** (este) | Requisitos, rastreabilidade, ADR/DCR, templates |
| **Apto53-api** | Código da API + contexto de implementação (`contexto_modulos.md`) |
| **Apto53-web** | Código do frontend (quando existir) |

Mapa de paths/contratos de código **não** fica duplicado aqui; use a [ponte de contexto](DOCS/contexto_modulos.md) para saber onde procurar.
