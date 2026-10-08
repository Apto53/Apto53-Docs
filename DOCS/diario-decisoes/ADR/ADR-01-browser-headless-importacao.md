## [ADR-01] Browser headless para importação de apartamentos por link

Neste documento:

1.  [Contexto](#contexto)
2.  [Opções](#opções)
3.  [Decisão](#decisão)
4.  [Consequências](#consequências)
5.  [Referências](#referências)
6.  [Versões](#versões)
7.  [Revisões](#revisões)

### Contexto

O Sistema **Apto 53** permite cadastrar apartamentos a partir do link de anúncios em portais imobiliários (UC09 — `POST /catalogs/:id/apartments/from-url`). Os portais relevantes (QuintoAndar, Chaves na Mão, Imovelweb, Viva Real, Zap Imóveis) são predominantemente aplicações dinâmicas: o conteúdo útil do anúncio costuma depender de JavaScript e pode haver proteções anti-bot.

É necessário escolher **como a API obtém o conteúdo da página** antes de mapear os dados para o modelo de Apartamento. O projeto é **pessoal, de baixo volume** (links colados um a um) e deve priorizar **baixo custo** e simplicidade operacional. Sem uma estratégia explícita, cada tentativa de importação tende a ser ad hoc, frágil ou cara demais para o escopo.

### Opções

Foram consideradas as seguintes opções:

* **HTTP simples + parse de HTML (scraping leve):** requisição HTTP e extração via seletores no HTML bruto.
    * **Prós:** infraestrutura leve; sem custo de API externa; fácil de hospedar.
    * **Contras:** frequentemente retorna HTML incompleto em SPAs; seletores quebram com mudanças de layout; anti-bot bloqueia com facilidade.
* **HTTP + JSON embutido / APIs internas do portal:** inspecionar estado embutido na página ou endpoints XHR usados pelo front do portal.
    * **Prós:** dados mais estruturados quando existem; mais rápido que browser; custo baixo.
    * **Contras:** contratos não oficiais e instáveis; varia por portal; ainda pode exigir cookies/headers especiais.
* **Extração assistida por LLM:** obter HTML ou texto da página e pedir a um modelo para preencher os campos do apartamento.
    * **Prós:** menos dependência de seletores por portal; flexível a mudanças de layout.
    * **Contras:** custo recorrente por importação; latência; risco de alucinação (preço/m² incorretos); ainda precisa de uma forma de obter o conteúdo da página.
* **Serviço comercial de scraping / browser na nuvem:** terceirizar fetch anti-bot (ScrapingBee, Browserless, etc.).
    * **Prós:** maior resiliência a bloqueios; menos operação de browser própria.
    * **Contras:** custo mensal ou por request; dependência externa desnecessária para uso pessoal de baixo volume.
* **Browser headless (ex.: Playwright/Puppeteer) na própria API ou worker:** abrir a URL, aguardar o render e extrair do DOM (ou do estado da página) com parser por portal.
    * **Prós:** boa taxa de sucesso em páginas dinâmicas; sem custo por request de LLM/serviço; controle total; adequado a volume baixo.
    * **Contras:** mais RAM/CPU que HTTP puro; tempo de resposta maior; parsers por portal ainda são necessários e quebram quando o HTML muda.

### Decisão

Adotar **browser headless** (biblioteca a definir na stack, tipicamente Playwright) como estratégia padrão de obtenção de conteúdo para o fluxo de importação por link.

A escolha prioriza **confiabilidade em SPAs** e **baixo custo recorrente** para um projeto pessoal simples. LLM fica descartada no núcleo por custo e risco de dados incorretos; scraping HTTP puro fica como possível otimização pontual por portal **somente se** comprovadamente suficiente, sem substituir o padrão headless. Serviços comerciais de scrape ficam fora do escopo inicial.

Cada portal continua com **parser/adapter próprio** que normaliza para o DTO de Apartamento; o browser apenas garante a obtenção do conteúdo renderizado. Persistência e autorização permanecem no módulo de Apartamentos (`source=link`).

**Data:** 05/10/2026

### Consequências

#### Benefícios

* Alinhamento com UC09 e com portais que dependem de JavaScript.
* Sem custo variável por importação (LLM/SaaS); adequado a uso pessoal e esporádico.
* Separação clara: fetch (browser) vs normalização (parser por domínio).

#### Malefícios

* Deploy exige ambiente capaz de rodar browser (mais memória/CPU).
* Latência do `from-url` maior que um `fetch` simples.
* Manutenção contínua dos parsers quando os portais alterarem o DOM.

#### Riscos

* Bloqueio anti-bot mesmo com headless — mitigar com rate limit (RNF08), user-agent/comportamento moderado e falha explícita (UC09 FA03), sem inventar dados.
* Complexidade de hospedagem — mitigar no MVP rodando localmente ou em VPS pequena; avaliar worker/fila só se a API principal ficar pesada.
* Regressão de seletores — mitigar com fixtures de teste por portal e um adapter isolado por domínio.

### Referências

* [UC09 — Adicionar apartamento a partir de link](../../especificacao_requisitos_software.md)
* [Contexto de módulos (ponte)](../../contexto_modulos.md) — mapa vivo em `Apto53-api/contexto_modulos.md`
* [ADR-03 — Stack e arquitetura modular](./ADR-03-stack-arquitetura-modular-api.md)
* [RNF08, RNF09, RNF10 — rastreabilidade](../../rastreabilidade_requisitos.md)
* [Playwright](https://playwright.dev/)

### Versões

| Versão | Data | Descrição | Responsável |
| :---- | :---- | :---- | :---- |
| 1.0 | 05/10/2026 | Versão inicial | Cauê Moraes |

### Revisões

| Data | Responsável |
| :---- | :---- |
| 05/10/2026 | Cauê Moraes |
