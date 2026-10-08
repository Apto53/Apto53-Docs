## Especificação de Requisitos de Software

### 1. Introdução

Este documento registra os requisitos detalhados do Sistema **Apto 53** (Catálogos de Apartamentos para Aluguel), na forma de **descrição de casos de uso** do produto. A documentação canônica vive no repositório **Apto53-Docs**.

O sistema permite que usuários autenticados criem e compartilhem **catálogos** de apartamentos para alugar, com controle de acesso por papéis (**criador**, **editor** e **visualizador**). Apartamentos podem ser adicionados **manualmente** ou **a partir do link** de anúncios em portais imobiliários (QuintoAndar, Chaves na Mão, Imovelweb, Viva Real e Zap Imóveis), com posterior edição, avaliação individual e cálculo de nota média.

A rastreabilidade entre estes requisitos e demais artefatos está definida em [rastreabilidade_requisitos.md](./rastreabilidade_requisitos.md).

### 2. Classes de usuários

#### 2.1. Visitante

Usuário não autenticado. Pode criar conta e autenticar-se no sistema. Não possui acesso a catálogos nem a apartamentos.

#### 2.2. Criador

Usuário autenticado que criou um determinado catálogo e é o único dono desse catálogo. Pode gerenciar membros (convidar, alterar papéis entre editor e visualizador, remover), gerenciar apartamentos (adicionar por link ou manualmente, editar, remover), avaliar apartamentos e excluir o catálogo. Um mesmo usuário pode ser criador de um catálogo e editor ou visualizador de outros.

#### 2.3. Editor

Membro autenticado de um catálogo com permissão de escrita sobre apartamentos. Pode adicionar apartamentos (por link ou manualmente), editar e remover apartamentos, listar dados e registrar a própria nota. Não pode convidar, remover ou alterar papéis de membros.

#### 2.4. Visualizador

Membro autenticado de um catálogo com permissão apenas de leitura dos apartamentos e de seus dados, podendo registrar a própria nota. Não pode adicionar, editar ou remover apartamentos, nem gerenciar membros.

### 3. Definição de conceitos

#### 3.1. Glossário

**Usuário** - Pessoa cadastrada no sistema, identificada por nome, e-mail único e senha armazenada de forma segura (hash).

**Catálogo** - Conjunto de apartamentos com restrição de acesso. Possui um único criador (dono) e zero ou mais membros com papéis de editor ou visualizador.

**Membro** - Usuário associado a um catálogo mediante membership, com um papel (criador, editor ou visualizador).

**Papel** - Permissão do membro no catálogo: criador, editor ou visualizador.

**Convite** - Solicitação enviada pelo criador para um e-mail, atribuindo papel de editor ou visualizador, com token e prazo de validade, até aceitação ou expiração.

**Apartamento** - Registro de um imóvel de interesse para aluguel, pertencente a um catálogo, com atributos de negócio (condomínio, bairro, status, aluguel+condomínio, metros², quartos/banheiros, pet friendly, IPTU, link, características) e origem (`link` ou `manual`).

**Origem (source)** - Indica se o apartamento foi importado a partir de um link de anúncio (`link`) ou cadastrado manualmente (`manual`).

**Características** - Lista de atributos e diferenciais do imóvel (por exemplo: armários de cozinha, varanda, sol da manhã).

**Avaliação (nota)** - Pontuação individual atribuída por um membro a um apartamento do catálogo do qual participa.

**Nota média** - Média aritmética das notas atribuídas ao apartamento pelos membros que o avaliaram.

**Portal imobiliário** - Site externo de anúncios suportado para importação por link (QuintoAndar, Chaves na Mão, Imovelweb, Viva Real, Zap Imóveis).

#### 3.2. Modelo conceitual

```mermaid
classDiagram
  direction LR
  Usuario "1" --> "*" Membership : possui
  Catalogo "1" --> "*" Membership : possui
  Catalogo "1" --> "*" Apartamento : contem
  Catalogo "1" --> "*" Convite : emite
  Usuario "1" --> "*" Avaliacao : atribui
  Apartamento "1" --> "*" Avaliacao : recebe
  Usuario "1" --> "*" Catalogo : cria

  class Usuario {
    nome
    email
    senhaHash
  }
  class Catalogo {
    nome
  }
  class Membership {
    papel
  }
  class Convite {
    email
    papel
    token
    expiracao
  }
  class Apartamento {
    origem
    condominio
    bairro
    status
    aluguelCond
    metrosQuadrados
    quartosBanheiros
    petFriendly
    iptu
    link
    caracteristicas
  }
  class Avaliacao {
    nota
  }
```

- **Usuario**: pessoa autenticável no sistema.
- **Catalogo**: coleção controlada de apartamentos.
- **Membership**: vínculo usuário–catálogo com papel.
- **Convite**: pendência de entrada no catálogo.
- **Apartamento**: imóvel catalogado.
- **Avaliacao**: nota de um membro a um apartamento.

### 4. Requisitos de Software

Nesta seção são descritos os casos de uso do produto. Na Seção 4.1 são descritos os requisitos funcionais (casos de uso). Na Seção 4.2 são descritos os requisitos não-funcionais.

#### 4.1. Requisitos funcionais

##### 4.1.1. Diagrama de casos de uso

```mermaid
flowchart LR
  visitante((Visitante))
  criador((Criador))
  editor((Editor))
  visualizador((Visualizador))

  visitante --> UC01
  visitante --> UC02

  criador --> UC03
  criador --> UC04
  criador --> UC05
  criador --> UC07
  criador --> UC08
  criador --> UC09
  criador --> UC10
  criador --> UC11
  criador --> UC12
  criador --> UC13
  criador --> UC14
  criador --> UC15

  editor --> UC04
  editor --> UC09
  editor --> UC10
  editor --> UC11
  editor --> UC12
  editor --> UC13
  editor --> UC14

  visualizador --> UC04
  visualizador --> UC11
  visualizador --> UC14

  UC06((UC06 Aceitar convite))
  criador -.-> UC06
  editor -.-> UC06
  visualizador -.-> UC06

  UC01[UC01 Cadastrar usuario]
  UC02[UC02 Autenticar usuario]
  UC03[UC03 Criar catalogo]
  UC04[UC04 Listar catalogos]
  UC05[UC05 Convidar membro]
  UC07[UC07 Alterar papel]
  UC08[UC08 Remover membro]
  UC09[UC09 Apt por link]
  UC10[UC10 Apt manual]
  UC11[UC11 Listar apts]
  UC12[UC12 Editar apt]
  UC13[UC13 Remover apt]
  UC14[UC14 Avaliar apt]
  UC15[UC15 Excluir catalogo]
```

Os atores **Criador**, **Editor** e **Visualizador** pressupõem usuário já autenticado (pós-condição de UC02). O papel aplica-se no contexto de um catálogo específico.

##### 4.1.2. Descrição detalhada dos casos de uso

| Dado                | Valor |
| ------------------- | ----- |
| Nome do caso de uso | Cadastrar usuário |
| Identificador       | UC01 |
| Pré-condições       | O visitante não possui conta com o e-mail informado. |
| Pós-condições       | Existe um usuário cadastrado com nome, e-mail e senha armazenada de forma segura; o visitante pode autenticar-se. |
| Fluxo principal     | 1. O visitante informa nome, e-mail e senha.<br>2. O sistema valida os dados informados.<br>3. O sistema registra o usuário com a senha armazenada de forma segura.<br>4. O sistema confirma o cadastro. |
| Fluxos alternativos | 2 - FA01 Dados inválidos:<br>2.1 O sistema informa que os dados não atendem às regras de validação.<br>2.2 Encerra o caso de uso.<br><br>2 - FA02 E-mail já cadastrado:<br>2.1 O sistema informa que o e-mail já está em uso.<br>2.2 Encerra o caso de uso. |

_Tabela — Descrição do Caso de Uso UC01_

| Dado                | Valor |
| ------------------- | ----- |
| Nome do caso de uso | Autenticar usuário |
| Identificador       | UC02 |
| Pré-condições       | Existe um usuário cadastrado com o e-mail informado. |
| Pós-condições       | O usuário está autenticado e pode acessar funcionalidades conforme seus papéis nos catálogos. |
| Fluxo principal     | 1. O visitante informa e-mail e senha.<br>2. O sistema valida as credenciais.<br>3. O sistema estabelece a sessão autenticada do usuário.<br>4. O sistema confirma a autenticação. |
| Fluxos alternativos | 2 - FA01 Credenciais inválidas:<br>2.1 O sistema informa que e-mail ou senha estão incorretos.<br>2.2 Encerra o caso de uso. |

_Tabela — Descrição do Caso de Uso UC02_

| Dado                | Valor |
| ------------------- | ----- |
| Nome do caso de uso | Criar catálogo |
| Identificador       | UC03 |
| Pré-condições       | O usuário está autenticado. |
| Pós-condições       | Existe um novo catálogo cujo criador é o usuário autenticado; o usuário é membro com papel criador. |
| Fluxo principal     | 1. O usuário informa o nome do catálogo.<br>2. O sistema valida o nome.<br>3. O sistema cria o catálogo e associa o usuário como criador.<br>4. O sistema confirma a criação e disponibiliza o catálogo ao usuário. |
| Fluxos alternativos | 2 - FA01 Nome inválido:<br>2.1 O sistema informa que o nome é inválido.<br>2.2 Encerra o caso de uso. |

_Tabela — Descrição do Caso de Uso UC03_

| Dado                | Valor |
| ------------------- | ----- |
| Nome do caso de uso | Listar catálogos do usuário |
| Identificador       | UC04 |
| Pré-condições       | O usuário está autenticado. |
| Pós-condições       | O sistema apresentou os catálogos em que o usuário é membro, com o respectivo papel. |
| Fluxo principal     | 1. O usuário solicita a listagem de seus catálogos.<br>2. O sistema identifica os catálogos em que o usuário possui membership.<br>3. O sistema apresenta a lista de catálogos com o papel do usuário em cada um. |
| Fluxos alternativos | 2 - FA01 Nenhum catálogo:<br>2.1 O sistema informa que o usuário não participa de nenhum catálogo.<br>2.2 Encerra o caso de uso. |

_Tabela — Descrição do Caso de Uso UC04_

| Dado                | Valor |
| ------------------- | ----- |
| Nome do caso de uso | Convidar membro ao catálogo |
| Identificador       | UC05 |
| Pré-condições       | O usuário está autenticado e é o criador do catálogo. |
| Pós-condições       | Existe um convite pendente para o e-mail informado, com papel editor ou visualizador, token e prazo de validade. |
| Fluxo principal     | 1. O criador informa o e-mail do convidado e o papel desejado (editor ou visualizador).<br>2. O sistema valida o e-mail e o papel.<br>3. O sistema cria o convite com token e expiração.<br>4. O sistema disponibiliza o convite para aceitação pelo convidado.<br>5. O sistema confirma o envio/registro do convite. |
| Fluxos alternativos | 2 - FA01 Papel inválido:<br>2.1 O sistema informa que apenas editor ou visualizador podem ser convidados.<br>2.2 Encerra o caso de uso.<br><br>2 - FA02 E-mail já é membro:<br>2.1 O sistema informa que o e-mail já possui membership no catálogo.<br>2.2 Encerra o caso de uso.<br><br>2 - FA03 Convite duplicado pendente:<br>2.1 O sistema informa que já existe convite pendente para aquele e-mail neste catálogo.<br>2.2 Encerra o caso de uso. |

_Tabela — Descrição do Caso de Uso UC05_

| Dado                | Valor |
| ------------------- | ----- |
| Nome do caso de uso | Aceitar convite |
| Identificador       | UC06 |
| Pré-condições       | Existe um convite pendente e não expirado; o usuário está autenticado com o mesmo e-mail do convite. |
| Pós-condições       | O usuário é membro do catálogo com o papel definido no convite; o convite deixa de estar pendente. |
| Fluxo principal     | 1. O usuário apresenta o token do convite.<br>2. O sistema valida o token, a expiração e a correspondência do e-mail autenticado.<br>3. O sistema cria a membership com o papel do convite.<br>4. O sistema marca o convite como aceito.<br>5. O sistema confirma a adesão ao catálogo. |
| Fluxos alternativos | 2 - FA01 Token inválido ou expirado:<br>2.1 O sistema informa que o convite é inválido ou expirou.<br>2.2 Encerra o caso de uso.<br><br>2 - FA02 E-mail divergente:<br>2.1 O sistema informa que o usuário autenticado não corresponde ao e-mail convidado.<br>2.2 Encerra o caso de uso. |

_Tabela — Descrição do Caso de Uso UC06_

| Dado                | Valor |
| ------------------- | ----- |
| Nome do caso de uso | Alterar papel de membro |
| Identificador       | UC07 |
| Pré-condições       | O usuário está autenticado e é o criador do catálogo; o membro-alvo é editor ou visualizador do mesmo catálogo. |
| Pós-condições       | O papel do membro-alvo no catálogo foi alterado entre editor e visualizador. |
| Fluxo principal     | 1. O criador seleciona o membro e informa o novo papel (editor ou visualizador).<br>2. O sistema valida que o membro-alvo não é o criador e que o novo papel é permitido.<br>3. O sistema atualiza o papel do membro.<br>4. O sistema confirma a alteração. |
| Fluxos alternativos | 2 - FA01 Tentativa de alterar o criador:<br>2.1 O sistema informa que o papel de criador não pode ser alterado por este fluxo.<br>2.2 Encerra o caso de uso.<br><br>2 - FA02 Membro inexistente no catálogo:<br>2.1 O sistema informa que o usuário informado não é membro do catálogo.<br>2.2 Encerra o caso de uso. |

_Tabela — Descrição do Caso de Uso UC07_

| Dado                | Valor |
| ------------------- | ----- |
| Nome do caso de uso | Remover membro do catálogo |
| Identificador       | UC08 |
| Pré-condições       | O usuário está autenticado e é o criador do catálogo; o membro-alvo é editor ou visualizador do mesmo catálogo. |
| Pós-condições       | O membro-alvo deixa de ter acesso ao catálogo. |
| Fluxo principal     | 1. O criador solicita a remoção de um membro.<br>2. O sistema valida que o membro-alvo não é o criador.<br>3. O sistema remove a membership do membro-alvo.<br>4. O sistema confirma a remoção. |
| Fluxos alternativos | 2 - FA01 Tentativa de remover o criador:<br>2.1 O sistema informa que o criador não pode ser removido por este fluxo.<br>2.2 Encerra o caso de uso.<br><br>2 - FA02 Membro inexistente:<br>2.1 O sistema informa que o usuário não é membro do catálogo.<br>2.2 Encerra o caso de uso. |

_Tabela — Descrição do Caso de Uso UC08_

| Dado                | Valor |
| ------------------- | ----- |
| Nome do caso de uso | Adicionar apartamento a partir de link |
| Identificador       | UC09 |
| Pré-condições       | O usuário está autenticado e é criador ou editor do catálogo. |
| Pós-condições       | Existe um apartamento no catálogo com origem `link`, campos preenchidos a partir do anúncio quando disponíveis, e link registrado. |
| Fluxo principal     | 1. O usuário informa o link do anúncio e o catálogo de destino.<br>2. O sistema verifica que o usuário é criador ou editor do catálogo.<br>3. O sistema identifica o portal imobiliário a partir do link.<br>4. O sistema obtém os dados do anúncio e normaliza os atributos do apartamento (condomínio, bairro, status, aluguel+condomínio, metros², quartos/banheiros, pet friendly, IPTU, características, quando disponíveis).<br>5. O sistema registra o apartamento no catálogo com origem `link`.<br>6. O sistema apresenta o apartamento registrado. |
| Fluxos alternativos | 2 - FA01 Sem permissão:<br>2.1 O sistema informa que o usuário não tem permissão para adicionar apartamentos neste catálogo.<br>2.2 Encerra o caso de uso.<br><br>3 - FA02 Portal não suportado:<br>3.1 O sistema informa que o link não pertence a um portal suportado.<br>3.2 Encerra o caso de uso.<br><br>4 - FA03 Falha na obtenção dos dados:<br>4.1 O sistema informa que não foi possível obter os dados do anúncio.<br>4.2 Encerra o caso de uso.<br><br>4 - FA04 Dados parciais:<br>4.1 O sistema registra o apartamento com os campos obtidos e indica os campos não preenchidos.<br>4.2 Continua no passo 6 do fluxo principal. |

_Tabela — Descrição do Caso de Uso UC09_

| Dado                | Valor |
| ------------------- | ----- |
| Nome do caso de uso | Adicionar apartamento manualmente |
| Identificador       | UC10 |
| Pré-condições       | O usuário está autenticado e é criador ou editor do catálogo. |
| Pós-condições       | Existe um apartamento no catálogo com origem `manual` e com os atributos informados pelo usuário. |
| Fluxo principal     | 1. O usuário informa o catálogo de destino e os atributos do apartamento que desejar preencher (condomínio, bairro, status, aluguel+condomínio, metros², quartos/banheiros, pet friendly, IPTU, link opcional, características).<br>2. O sistema verifica que o usuário é criador ou editor do catálogo.<br>3. O sistema valida os atributos informados.<br>4. O sistema registra o apartamento no catálogo com origem `manual`.<br>5. O sistema apresenta o apartamento registrado. |
| Fluxos alternativos | 2 - FA01 Sem permissão:<br>2.1 O sistema informa que o usuário não tem permissão para adicionar apartamentos neste catálogo.<br>2.2 Encerra o caso de uso.<br><br>3 - FA02 Dados inválidos:<br>3.1 O sistema informa quais atributos são inválidos.<br>3.2 Encerra o caso de uso. |

_Tabela — Descrição do Caso de Uso UC10_

| Dado                | Valor |
| ------------------- | ----- |
| Nome do caso de uso | Listar apartamentos do catálogo |
| Identificador       | UC11 |
| Pré-condições       | O usuário está autenticado e é membro do catálogo (criador, editor ou visualizador). |
| Pós-condições       | O sistema apresentou os apartamentos do catálogo e seus dados, incluindo nota média quando houver avaliações. |
| Fluxo principal     | 1. O usuário solicita a listagem de apartamentos de um catálogo.<br>2. O sistema verifica que o usuário é membro do catálogo.<br>3. O sistema obtém os apartamentos do catálogo.<br>4. O sistema calcula a nota média de cada apartamento a partir das avaliações existentes.<br>5. O sistema apresenta os apartamentos com seus atributos e notas. |
| Fluxos alternativos | 2 - FA01 Sem permissão:<br>2.1 O sistema informa que o usuário não tem acesso ao catálogo.<br>2.2 Encerra o caso de uso.<br><br>3 - FA02 Catálogo vazio:<br>3.1 O sistema informa que não há apartamentos no catálogo.<br>3.2 Encerra o caso de uso. |

_Tabela — Descrição do Caso de Uso UC11_

| Dado                | Valor |
| ------------------- | ----- |
| Nome do caso de uso | Editar apartamento |
| Identificador       | UC12 |
| Pré-condições       | O usuário está autenticado e é criador ou editor do catálogo; o apartamento pertence a esse catálogo. |
| Pós-condições       | Os atributos do apartamento foram atualizados conforme os dados informados. |
| Fluxo principal     | 1. O usuário informa o apartamento e os atributos a atualizar.<br>2. O sistema verifica que o usuário é criador ou editor do catálogo do apartamento.<br>3. O sistema valida os novos atributos.<br>4. O sistema atualiza o apartamento.<br>5. O sistema confirma a atualização. |
| Fluxos alternativos | 2 - FA01 Sem permissão:<br>2.1 O sistema informa que o usuário não pode editar apartamentos neste catálogo.<br>2.2 Encerra o caso de uso.<br><br>2 - FA02 Apartamento inexistente:<br>2.1 O sistema informa que o apartamento não foi encontrado no catálogo.<br>2.2 Encerra o caso de uso.<br><br>3 - FA03 Dados inválidos:<br>3.1 O sistema informa quais atributos são inválidos.<br>3.2 Encerra o caso de uso. |

_Tabela — Descrição do Caso de Uso UC12_

| Dado                | Valor |
| ------------------- | ----- |
| Nome do caso de uso | Remover apartamento |
| Identificador       | UC13 |
| Pré-condições       | O usuário está autenticado e é criador ou editor do catálogo; o apartamento pertence a esse catálogo. |
| Pós-condições       | O apartamento e suas avaliações deixam de pertencer ao catálogo. |
| Fluxo principal     | 1. O usuário solicita a remoção de um apartamento do catálogo.<br>2. O sistema verifica que o usuário é criador ou editor do catálogo.<br>3. O sistema remove o apartamento e suas avaliações associadas.<br>4. O sistema confirma a remoção. |
| Fluxos alternativos | 2 - FA01 Sem permissão:<br>2.1 O sistema informa que o usuário não pode remover apartamentos neste catálogo.<br>2.2 Encerra o caso de uso.<br><br>2 - FA02 Apartamento inexistente:<br>2.1 O sistema informa que o apartamento não foi encontrado no catálogo.<br>2.2 Encerra o caso de uso. |

_Tabela — Descrição do Caso de Uso UC13_

| Dado                | Valor |
| ------------------- | ----- |
| Nome do caso de uso | Avaliar apartamento |
| Identificador       | UC14 |
| Pré-condições       | O usuário está autenticado e é membro do catálogo (criador, editor ou visualizador); o apartamento pertence a esse catálogo. |
| Pós-condições       | Existe a nota do usuário para o apartamento; a nota média do apartamento reflete as avaliações dos membros. |
| Fluxo principal     | 1. O usuário informa o apartamento e a respectiva nota.<br>2. O sistema verifica que o usuário é membro do catálogo do apartamento.<br>3. O sistema valida a nota.<br>4. O sistema registra ou atualiza a avaliação do usuário para aquele apartamento.<br>5. O sistema recalcula a nota média do apartamento.<br>6. O sistema confirma a avaliação e apresenta a nota média atualizada. |
| Fluxos alternativos | 2 - FA01 Sem permissão:<br>2.1 O sistema informa que o usuário não tem acesso ao catálogo.<br>2.2 Encerra o caso de uso.<br><br>3 - FA02 Nota inválida:<br>3.1 O sistema informa que a nota está fora do intervalo permitido.<br>3.2 Encerra o caso de uso. |

_Tabela — Descrição do Caso de Uso UC14_

| Dado                | Valor |
| ------------------- | ----- |
| Nome do caso de uso | Excluir catálogo |
| Identificador       | UC15 |
| Pré-condições       | O usuário está autenticado e é o criador do catálogo. |
| Pós-condições       | O catálogo, seus memberships, convites pendentes, apartamentos e avaliações associados deixam de existir. |
| Fluxo principal     | 1. O criador solicita a exclusão do catálogo.<br>2. O sistema verifica que o usuário é o criador.<br>3. O sistema remove apartamentos, avaliações, convites, memberships e o catálogo.<br>4. O sistema confirma a exclusão. |
| Fluxos alternativos | 2 - FA01 Sem permissão:<br>2.1 O sistema informa que apenas o criador pode excluir o catálogo.<br>2.2 Encerra o caso de uso. |

_Tabela — Descrição do Caso de Uso UC15_

##### 4.1.3. Matriz resumida de permissões por catálogo

| Ação | Criador | Editor | Visualizador |
| ---- | ------- | ------ | ------------ |
| Listar apartamentos e dados | Sim | Sim | Sim |
| Adicionar apartamento por link | Sim | Sim | Não |
| Adicionar apartamento manualmente | Sim | Sim | Não |
| Editar apartamento | Sim | Sim | Não |
| Remover apartamento | Sim | Sim | Não |
| Avaliar apartamento (própria nota) | Sim | Sim | Sim |
| Convidar membro (editor/visualizador) | Sim | Não | Não |
| Alterar papel de membro | Sim | Não | Não |
| Remover membro | Sim | Não | Não |
| Excluir catálogo | Sim | Não | Não |

#### 4.2. Requisitos não-funcionais

**RNF01** - O sistema deve armazenar senhas exclusivamente na forma de hash seguro (Argon2 ou bcrypt), nunca em texto puro.

**RNF02** - O sistema deve exigir autenticação válida antes de permitir acesso a catálogos, membros, convites, apartamentos e avaliações.

**RNF03** - O sistema deve autorizar cada operação de catálogo e apartamento com base no membership e no papel do usuário autenticado naquele catálogo.

**RNF04** - O sistema deve impedir que um usuário acesse ou modifique dados de um catálogo do qual não seja membro.

**RNF05** - O sistema deve associar a cada convite um token opaco e uma data de expiração.

**RNF06** - O sistema deve permitir aceitar um convite somente quando o e-mail do usuário autenticado coincidir com o e-mail do convite e o convite estiver válido.

**RNF07** - O sistema deve comunicar-se com clientes em produção exclusivamente por meio de HTTPS.

**RNF08** - O sistema deve aplicar limitação de taxa (rate limiting) nas operações de autenticação e de importação de apartamento por link.

**RNF09** - O sistema deve registrar apartamentos importados por link com origem `link` e apartamentos cadastrados pelo usuário com origem `manual`.

**RNF10** - O sistema deve suportar importação por link a partir dos portais QuintoAndar, Chaves na Mão, Imovelweb, Viva Real e Zap Imóveis.

**RNF11** - O sistema deve calcular a nota média de um apartamento como a média aritmética das notas atribuídas pelos membros que o avaliaram.

**RNF12** - O sistema não deve registrar em logs senhas, hashes de senha ou tokens de convite em texto puro.

### 5. Rastreabilidade de requisitos

A rastreabilidade bidirecional entre os requisitos aqui descritos e os demais artefatos do sistema está definida em [rastreabilidade_requisitos.md](./rastreabilidade_requisitos.md). Todos os elementos rastreados, incluindo os requisitos, utilizam seus identificadores únicos como referência no documento de rastreabilidade.
