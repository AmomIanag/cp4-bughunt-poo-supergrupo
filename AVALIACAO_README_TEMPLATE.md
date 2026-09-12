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
| **Total de bugs corrigidos** | 5 / 12 |
| **Total de ajustes de Clean Code** | ___ / 6 |

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
| bug06 | | | | |
| bug07 | | | | |
| bug08 | | | | |
| bug09 | | | | |
| bug10 | | | | |
| bug11 | | | | |
| bug12 | | | | |

## Parte 2 — Ajustes de Clean Code

| # | Onde estava | Qual princípio/boas práticas era violado | O que eu mudei |
|---|---|---|---|
| clean01 | | | |
| clean02 | | | |
| clean03 | | | |
| clean04 | | | |
| clean05 | | | |
| clean06 | | | |

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
