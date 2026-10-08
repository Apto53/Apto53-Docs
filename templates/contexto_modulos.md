# Contexto por módulo — [Nome do projeto / API]

Documento de orientação para o time e para a IA: o que cada módulo faz, onde o código mora,
contratos relevantes e como pedir mudanças com contexto.

**Fluxo ao alterar o sistema:** consultar este arquivo → criar seção se faltar → mudar código → atualizar este doc.
Se a mudança alterar comportamento de produto, atualizar também o repositório de requisitos (ERS / rastreabilidade / ADR) via docs-delta.

---

## N. [Nome do módulo]

**O que é:** Responsabilidade do módulo em 1–3 frases. Escopo e limites (o que não cabe aqui).

**Onde fica:**

- Entrada HTTP / handlers: `[paths]`
- Casos de uso / serviços / domínio: `[paths]`
- Persistência / repositórios / models: `[paths]`
- Autorização / policies / guards: `[paths]`
- Serialização / DTOs / response mappers: `[paths]`
- Jobs / workers / consumers (se houver): `[paths]`
- Rotas / endpoints: `[prefixo e ações principais]`

**Namespaces / pacotes:** `Dominio.`, `Dominio.Sub.`

**Dependências externas:** DBs, filas, caches, APIs de terceiros usadas por este módulo (só o essencial).

**Requisitos:** `UC##`, `RNF##` (IDs do repositório de docs — não copiar a ERS).

### [Feature ou endpoint relevante]

**O que é:** Comportamento esperado e intenção de negócio.

**Contrato:**

- Entrada: params / body / headers relevantes; defaults; o que “omitido” significa
- Saída: shape principal; campos novos; significado de vazio (`null` vs `[]` vs `{}`)
- Erros: códigos / formato quando falha de validação ou regra de negócio
- Idempotência / side effects (se importar)

**Regras que a implementação costuma errar:**

- Precedência entre filtros/flags
- Retrocompatibilidade (default seguro)
- Escopo multi-tenant / ownership
- Relação com endpoints vizinhos (este vs aquele)

**Onde fica:** handler → use case → repositório / adapter (paths ou símbolos).

**Testes de referência:** paths dos testes que travam o contrato (`uc##-*.unit.spec.ts`, `uc##-*.contract.spec.ts`, e2e).

**Exemplo de prompt:**

"No módulo de [X], [ação concreta] respeitando [contrato/regra], em [pacote/use case]."

---

## Regras transversais

- Camadas e fluxo de request (link para ADR de arquitetura, se houver)
- Convenções de nomenclatura e API
- Authn / Authz / multi-tenant
- Formato padrão de erro e paginação
- Estratégia de testes (unit / contrato / e2e)
- Onde atualizar docs de produto ao mudar comportamento

Consulte também: repositório de docs do produto (ERS, rastreabilidade, ADR/DCR).

---

## Como usar o esqueleto

| Nível | Quando usar |
| ----- | ----------- |
| `## Módulo` | Domínio estável (Users, Billing, Orders…) |
| `### Feature` | Só quando o contrato ou a regra de negócio não é óbvia pelo código |
| Bullet denso | Preferível a prosa longa |
| Prompt no fim | Frase pronta no estilo do projeto |

### Checklist mínimo por mudança

- Seção do módulo existe?
- “Onde fica” ainda aponta para os paths certos?
- Contrato de entrada/saída mudou?
- Tem regra de precedência/default que a IA/time erraria?
- Precisa de exemplo de prompt novo?
- Testes `uc##` unit/contrato (e e2e se jornada) atualizados?
- Se comportamento de produto mudou: docs-delta + ERS/rastreabilidade no repo de docs?

### O que não colocar

- Walkthrough linha a linha do código
- Lista completa de todos os arquivos
- Spec OpenAPI duplicada (linkar ou resumir só o que guia implementação)
- Detalhe que muda toda semana sem impacto de contrato
- Cópia integral da ERS (apenas IDs `UC##` / `RNF##`)
