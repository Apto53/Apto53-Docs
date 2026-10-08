## [`<ADR|DCR>`-XX] `<Título curto>`

> Template **geral** do diário de decisões (ADR e DCR) do produto **Apto 53** (repo **Apto53-Docs**).  
> Copiar para `DOCS/diario-decisoes/ADR/ADR-##-titulo-curto.md` **ou** `DOCS/diario-decisoes/DCR/DCR-##-titulo-curto.md`, conforme o tipo.  
> A estrutura das seções é a mesma; muda o prefixo, a pasta e o foco do conteúdo.

| Tipo | Pasta | Usar quando |
| ---- | ----- | ----------- |
| **ADR** | `DOCS/diario-decisoes/ADR/` | Decisão arquitetural significativa (stack, padrões, estratégias técnicas). |
| **DCR** | `DOCS/diario-decisoes/DCR/` | Modelagem de processo de negócio, workflow ou regras complexas (notação/declarativa em grafo quando couber no corpo da decisão). |

Neste documento:

1.  [Contexto](#contexto)
2.  [Opções](#opções)
3.  [Decisão](#decisão)
4.  [Consequências](#consequências)
5.  [Referências](#referências)
6.  [Versões](#versões)
7.  [Revisões](#revisões)

### Contexto

`<Descreva o problema ou a necessidade que motivou o registro. Inclua restrições relevantes (técnicas, de negócio, de prazo ou de equipe) e o que acontece se nada for decidido ou modelado.>`

`<Para ADR: foque no problema arquitetural. Para DCR: foque no processo, workflow ou regra complexa a organizar.>`

### Opções

Foram consideradas as seguintes opções:

* **`<Nome da opção A>`:** `<Descrição breve.>`
    * **Prós:** `<...>`
    * **Contras:** `<...>`
* **`<Nome da opção B>`:** `<Descrição breve.>`
    * **Prós:** `<...>`
    * **Contras:** `<...>`
* **`<Nome da opção C>`:** `<Descrição breve.>`
    * **Prós:** `<...>`
    * **Contras:** `<...>`

`<Em DCR, as opções podem ser notações, recortes de fluxo ou formas de modelar as regras; em ADR, costumam ser alternativas técnicas ou de desenho.>`

### Decisão

`<Declare a opção escolhida e o motivo da escolha em relação às demais. Seja explícito sobre o que passa a ser o padrão (ou o modelo oficial do fluxo/regra) no projeto a partir deste registro.>`

`<Se for DCR e houver diagrama/grafo, descreva-o aqui ou referencie o artefato anexo.`>

**Data:** `<DD/MM/AAAA>`

### Consequências

#### Benefícios

* `<Benefício 1>`
* `<Benefício 2>`

#### Malefícios

* `<Custo, complexidade ou limitação introduzida>`

#### Riscos

* `<Risco 1 e, se houver, como será mitigado>`

### Referências

* [`<Nome>`](`<URL ou caminho do artefato>`)
* [`<Nome>`](`<URL ou caminho do artefato>`)

### Versões

| Versão | Data | Descrição | Responsável |
| :---- | :---- | :---- | :---- |
| 1.0 | `<DD/MM/AAAA>` | Versão inicial | `<Nome>` |

### Revisões

| Data | Responsável |
| :---- | :---- |
| `<DD/MM/AAAA>` | `<Nome>` |
