# Checkpoint 4 — Bug Hunt StreamFIAP

> Copie este arquivo para a raiz do seu repositório com o nome **README.md**
> e preencha todas as seções.

## Identificação

**Grupo:** ___

| Integrante | RM | Turma |
|---|---|---|
|Amom Ianaguivara Brito | 565718| 2CCPH|
| Victor Chen| 565363|2CCPH |
| Fernando Antônio|562549 | 2CCPH|
|Vinícius Mello |565257 | 2CCPH|
|Gabriel Ramos |564074 | 2CCPH|

| Campo | |
|---|---|
| **Total de bugs corrigidos** | 12 / 12 |
| **Total de ajustes de Clean Code** | 6 / 6 |

---

## Parte 1 — Bugs encontrados

> Uma linha por bug, na ordem em que você os encontrou. Use a numeração dos seus
> commits (`fix: bug01 ...`). Preencha TODAS as colunas — metade da nota está aqui.

| # | Sintoma observado (o que fiz/vi) | Causa raiz (arquivo e linha aproximada) | Correção aplicada | Conceito da disciplina |
|---|---|---|---|---|
| bug01 | A busca do conteudo  que não existia nao dava return na mensagem de erro que era pra acontecer no contrato|O buscarPorId pegava Exception de forma genérica e ignorava a excecao ConteudoNaoEcontradoException, retornando o null |Removi o try/catch que nao precisavam, fazendo que o ConteudoNaoEncontradoException fosse tratado pelo GlobalException Handler|Tratamento de exceções / propagação de exceções / tratamento centralizado com @RestControllerAdvice. |
| bug02 |A busca por categoria não retornava corretamente os conteúdos cadastrados. |O controller comparava Strings usando ==, que compara referências de objetos, além de fazer a filtragem manualmente após buscar todos os conteúdos. | Substituí a lógica manual pelo método findByCategoria do ConteudoRepository.|Comparação de Strings em Java / Spring Data JPA / separação de responsabilidades. |
| bug03 |O usuário era criado sem o nome preenchido corretamente |O construtor usava "nome = nome", causando sombreamento do parâmetro e deixando o atributo da classe sem valor |Alterado para "this.nome = nome" |Construtores, atributos de instância e palavra-chave this |
| bug04 | Ao cadastrar um usuário, a API retornava erro 500 porque o banco tentava inserir NULL no campo id|O atributo id da entidade Usuario possuía apenas @Id e não tinha uma estratégia de geração automática, embora o cadastro criasse usuários sem informar um id | Foi adicionada a anotação @GeneratedValue(strategy = GenerationType.IDENTITY) ao campo id|JPA, chave primária e geração automática de identificadores |
| bug05 |A verificação de créditos podia considerar incorretamente se o usuário possuía saldo suficiente para realizar o aluguel |O método temCreditosSuficientes comparava os valores na ordem errada, verificando "preco >= creditos" em vez de verificar se os créditos do usuário eram maiores ou iguais ao preço |A condição foi alterada para "this.creditos >= preco" |Regra de negócio, operadores relacionais e encapsulamento |
| bug06 |Um conteúdo marcado como indisponível podia ser processado novamente para aluguel. | O método Usuario.alugar não verificava o atributo disponivel do conteúdo antes de executar as demais regras do aluguel|Foi adicionada uma validação que lança ConteudoIndisponivelException quando o conteúdo não está disponível |Regra de negócio, exceções customizadas e encapsulamento |
| bug07 |O preço promocional de filmes ficava 20% mais caro em vez de receber desconto. |O método aplicarPromocao multiplicava o preço por 1.2, acrescentando 20% ao valor. |O multiplicador foi alterado para 0.8, aplicando corretamente 20% de desconto |Interface, sobrescrita de método e regra de negócio. |
| bug08 |Ao cadastrar uma série, apenas o número de temporadas era salvo corretamente. Os campos herdados de Conteudo, como título, categoria, duração e classificação etária, ficavam vazios ou zerados, e o conteúdo era salvo como indisponível. |O construtor de Serie inicializava apenas o atributo numeroTemporadas e não chamava o construtor da classe pai Conteudo. Além disso, o ConteudoController não repassava o valor do campo disponivel para o construtor da série |O construtor de Serie foi alterado para receber todos os dados necessários e chamar super(...) para inicializar os atributos herdados. O ConteudoController também passou a repassar o campo disponivel |Herança, construtores, uso de super e inicialização de atributos herdados |
| bug09 |O preço de aluguel de séries não considerava a quantidade de temporadas. O sistema utilizava o preço padrão herdado de Conteudo |A classe Serie declarava o método calcularPrecoAluguel(double desconto), criando uma sobrecarga em vez de sobrescrever calcularPrecoAluguel() da classe Conteudo. |O parâmetro foi removido e o método passou a sobrescrever corretamente calcularPrecoAluguel(), com a anotação @Override. |Herança, polimorfismo, sobrescrita de métodos e diferença entre overload e override. |
| bug10 |Documentários eram cobrados com o preço padrão de R$ 9,90, mesmo que o contrato determine aluguel gratuito. |A classe Documentario não sobrescrevia o método calcularPrecoAluguel(), herdando o valor padrão definido em Conteudo. |Foi sobrescrito calcularPrecoAluguel() em Documentario para retornar 0.0. |Herança, polimorfismo e sobrescrita de métodos. |
| bug11 |Era possível cadastrar conteúdos com duração igual ou menor que zero, contrariando o contrato da API. |A classe Conteudo não validava o valor de duracaoMinutos antes de criar e persistir o objeto. |Foi adicionada uma validação no construtor de Conteudo que lança IllegalArgumentException quando duracaoMinutos <= 0. O GlobalExceptionHandler também passou a tratar essa exceção, retornando HTTP 400 com uma mensagem clara |Encapsulamento de regras de negócio, validação de dados e tratamento global de exceções. |
| bug12 |Ao tentar alugar um conteúdo com classificação indicativa superior à idade do usuário, a regra de negócio bloqueava o aluguel, mas a exceção não era tratada corretamente pela API. |A ClassificacaoIndicativaException era lançada em Usuario.alugar(), porém não existia um tratamento correspondente no GlobalExceptionHandler. |Foi adicionado um handler para ClassificacaoIndicativaException, retornando HTTP 403 e uma mensagem explicativa ao cliente. |Exceções personalizadas, tratamento global de erros e regras de negócio. |

## Parte 2 — Ajustes de Clean Code

| # | Onde estava | Qual princípio/boas práticas era violado | O que eu mudei |
|---|---|---|---|
| clean01 |Conteudo.java e ConteudoController.java. |Encapsulamento. O atributo duracaoMinutos estava público e era acessado diretamente pelo controller. |O atributo duracaoMinutos passou a ser private e os acessos externos foram substituídos pelo método getDuracaoMinutos(). |
| clean02 |Nos controllers que utilizavam @Autowired diretamente nos atributos. |As dependências eram injetadas diretamente nos campos, deixando-as menos explícitas e impedindo que fossem declaradas como final. |A injeção por atributo foi substituída por injeção via construtor. Os repositórios passaram a ser atributos final e o @Autowired deixou de ser necessário. |
| clean03 |ConteudoController.java. |Dead Code. Existia um método antigo de cálculo de desconto que não era utilizado por nenhuma parte da aplicação. |O método calcularDescontoAntigo() foi removido. |
| clean04 |ConteudoController.java. |Código comentado e TODO obsoleto. Havia um bloco de lógica de cupons comentado no arquivo, mesmo não fazendo parte do comportamento atual da aplicação. |O bloco de código comentado relacionado a cupons foi removido. |
| clean05 |Usuario.java. |Comentário incorreto e redundante. O comentário afirmava que o método adicionava créditos, enquanto a implementação realizava um débito. |O comentário incorreto foi removido, mantendo apenas o código e o nome do método, que já deixam clara a intenção da operação. |
| clean06 |Usuario.java, no método alugar(). |Separação de responsabilidades. O método responsável pela regra de aluguel também realizava impressão de recibo diretamente no console. |O bloco de System.out.println responsável pela impressão do recibo foi removido do método alugar(). |

---

## Parte 3 — Perguntas de reflexão

> Responda com suas palavras, 5 a 10 linhas cada, **usando o código real do projeto
> como exemplo**. Respostas genéricas de tutorial não pontuam.

### 1. Injeção de dependência (Aula 13)
Os controllers recebem os repositories via `@Autowired` (ex.: `ConteudoController`
usa `ConteudoRepository`). Explique por que o Spring precisa gerenciar esses objetos
em vez de criarmos com `new ConteudoRepository()`. O que exatamente o Spring faz ao
injetar um bean, e por que isso não funcionaria com um `new` comum?

### 2. JDBC vs Spring Data JPA (Aulas 12 e 13)
Na Aula 12 escrevemos um `ProdutoDAO` na mão com `Connection`, `PreparedStatement` e
`ResultSet`. Aqui o `ConteudoRepository` tem 2 linhas e faz CRUD completo. Compare as
duas abordagens: o que o Spring Data JPA automatiza, o que o JDBC/DAO ainda resolve
melhor, e como o `findByCategoria` consegue funcionar sem implementação.

### 3. Exceções checked vs unchecked (Aula 11)
A `ClassificacaoIndicativaException` estourava como um erro genérico do servidor,
sem mensagem útil para o cliente. Explique a diferença entre `extends Exception` e
`extends RuntimeException` no contexto desse bug, e como você fez a mensagem da
regra (classificação indicativa) chegar de forma clara ao cliente da API.

### 4. Sobrescrita vs sobrecarga (Aula 7)
Um dos bugs compilava sem nenhum erro: o método da `Serie` parecia sobrescrever
`calcularPrecoAluguel`, mas na verdade sobrecarregava. Explique a diferença entre
override e overload nesse caso e por que a anotação `@Override` teria impedido o bug.

### 5. Onde blindar o objeto? (Aulas 3, 4 e 13)
Vimos bugs de dados inválidos aceitos (duração negativa, créditos negativos, campos
nulos). Em quais lugares (construtor, setter, método do model) cada tipo de validação
deve ficar? Justifique usando os bugs que você encontrou e explique por que validar só
em um lugar não foi suficiente.

### 6. Abstração e interface (Aulas 8 e 9)
`Conteudo` é abstrata e `Promocionavel` é uma interface. Explique a diferença de
propósito entre as duas nesse projeto e o que mudaria no código se o Documentário
passasse a ter promoções — quais classes/linhas seriam tocadas e quais ficariam
intactas? O que isso diz sobre o design do sistema?

---

## Parte 4 — Espaço livre (opcional)

Alguma dificuldade, dúvida ou comentário sobre o checkpoint?

```

```
