## [ADR-02] Repositório Apto53-Docs como fonte da verdade (multi-repo)

Neste documento:

1.  [Contexto](#contexto)
2.  [Opções](#opções)
3.  [Decisão](#decisão)
4.  [Consequências](#consequências)
5.  [Referências](#referências)
6.  [Versões](#versões)
7.  [Revisões](#revisões)

### Contexto

O produto **Apto 53** terá backend e frontend em repositórios separados (`Apto53-api`, `Apto53-web`). A documentação (ERS, rastreabilidade, contexto de módulos, ADR/DCR) precisa permanecer consistente com o que for desenvolvido, **sem duplicar** a especificação completa em cada repo de código.

Sem uma fonte única, surgem cópias divergentes da ERS, perda de rastreabilidade por requisito (`UC##` / `RNF##`) e atrito ao atualizar docs a cada mudança. É necessário definir onde mora a documentação e como o código se relaciona a ela.

### Opções

Foram consideradas as seguintes opções:

* **Monorepo (api + web + docs no mesmo Git):** um PR altera código e docs juntos.
    * **Prós:** menor atrito de sincronismo; um único histórico.
    * **Contras:** acopla ciclo de vida de front, back e docs; foge da preferência por repositórios separados.
* **Cópia da ERS em cada repo de código:** cada repositório mantém sua própria pasta de requisitos.
    * **Prós:** docs “perto” do código.
    * **Contras:** duplicação; divergência rápida; manutenção dobrada.
* **Repositório exclusivo de documentação (Apto53-Docs) + IDs + docs-delta:** um repo só de docs; api/web apenas citam identificadores de requisito e atualizam o Docs via PR direto ou (futuro) automation bridge.
    * **Prós:** fonte única; sem cópia da ERS; rastreabilidade centralizada; permite atualizar docs sem código.
    * **Contras:** pode haver dois PRs (código + docs); exige disciplina de declarar IDs no delta.

### Decisão

Adotar o repositório **Apto53-Docs** como **única fonte da verdade** da documentação do Apto 53.

- Repos `Apto53-api` e `Apto53-web` **não** recebem cópia da ERS; carregam apenas ponte leve (rules/README apontando para este repo e citação de `UC##` / `RNF##` / `ADR-##` / `DCR-##`).
- Mudanças de comportamento exigem atualização neste repo, alinhada à matriz de rastreabilidade.
- O processo de Pull Request (atualização direta nos docs, via código + docs-delta, e bridge futuro) está definido no **DCR-01**.

**Data:** 05/10/2026

### Consequências

#### Benefícios

* Uma ERS e uma matriz de rastreabilidade, sem forks divergentes.
* Docs podem evoluir **antes** ou **independente** do código (PR só em Apto53-Docs).
* IDs estáveis ligam código ↔ requisito ↔ ADR/DCR.

#### Malefícios

* Trabalho de documentação fica em outro repositório (dois PRs quando há mudança de código, até existir bridge).
* Quem implementa precisa conhecer/consultar Apto53-Docs (ou o índice de IDs).

#### Riscos

* Esquecer de atualizar docs — mitigar com acordos Cursor neste repo, docs-delta obrigatório nos PRs de código e DCR-01.
* Bridge futuro mal configurado — no MVP a atualização **direta** neste repo é caminho oficial e suficiente.

### Referências

* [DCR-01 — Processo de pull request](../DCR/DCR-01-processo-pull-request.md)
* [Acordos de documentação](../../../.cursor/rules/acordos-documentacao.mdc)
* [Rastreabilidade de requisitos](../../rastreabilidade_requisitos.md)
* [Especificação de requisitos](../../especificacao_requisitos_software.md)

### Versões

| Versão | Data | Descrição | Responsável |
| :---- | :---- | :---- | :---- |
| 1.0 | 05/10/2026 | Versão inicial | Cauê Moraes |

### Revisões

| Data | Responsável |
| :---- | :---- |
| 05/10/2026 | Cauê Moraes |
