## Aggregation

No MongoDb podemos utilizar o [Aggregation Framework](https://docs.mongodb.org/manual/core/aggregation-pipeline/) para agruparmos valores para alguma finalidade, por exemplo fazer uma query que retorne a média salarial dos seus funcionários.

Um agrupamento pode ser feito com um [operador aritimético](https://docs.mongodb.org/manual/reference/operator/aggregation-arithmetic/):
* Valor absoluto `$abs`
* Adição `$add`
* Arredondamento "para baixo" `$ceil`
* Arredondamento "para cima" `$floor`
* Truncar para inteiro `$trunc` sem arredondar  
* dentre outros

Com um [acumulador](https://docs.mongodb.org/manual/reference/operator/aggregation-group/):
* Soma `$sum`
* Média `$avg`
* Mínimo `$min`
* Máximo `$max`
* Desvio padrão `$stdDevPop`
* dentre outros

Com [strings](https://docs.mongodb.org/manual/reference/operator/aggregation-string/)
* Concatenação `$concat`
* Retornar parte de uma string `$substr`
* Converter para minúsculas `$toLower`
* Converter para maiúsculas `$toUpper`
* Comparar `$strcasecmp`

Com [arrays](https://docs.mongodb.org/manual/reference/operator/aggregation-array/):
* Junta arrays e retorna um novo com todos os elementos `$concatArrays`
* Retornar o número de elementos de um array `$size`
* Reparte um array, retirando um pedaço dele `$slice`
* dentre outros

Com [datas](https://docs.mongodb.org/manual/reference/operator/aggregation-date/):
* Dia do ano de uma data (de 1 a 366) `$dayOfYear`
* Número de milisegundos (de 0 a 999) `$millisecond`
* dentre outros

Vamos dar uma olhada na sintaxe geral do método **aggregate**:

### pipeline

```
db.collection.aggregate(pipeline, options)
```

Sendo estas abaixo todas as [instruções do pipeline](https://docs.mongodb.org/manual/reference/operator/aggregation-pipeline/):

```
[
 { $project: <RETORNA UM DOCUMENTO ADICIONANDO OU REMOVENDO CAMPOS PARA A STREAM DO PIPELINE> },
 { $match: <CONDIÇÃO DE FILTRO> },
 { $redact: <RETORNA O DOCUMENTO PARA A STREAM DO PIPELINE> },
 { $limit: <LIMITA A QUANTIDADE DE DOCUMENTOS A SEREM CONSIDERADOS> },
 { $skip: <PULA OS PRIMEIROS n DOCUMENTOS> },
 { $unwind <DESCONTROI UM ARRAY EM UM DOCUMENTO> },
 { $group: { _id: <KEY AGRUPADORA>, <OUTROS CAMPOS A SEREM AGRUPADOS> },
 { $sample: <AMOSTRA ALEATÓRIA> },
 { $sort: <ORDENAÇÃO A SER CONSIDERADA> },
 { $geoNear: <BUSCA UTILIZANDO LATITUDE E LONGITUDE> },
 { $lookup: <REALIZA UM 'left outer join' COM OUTRA COLEÇÃO DO MESMO BANCO DE DADOS> },
 { $out: <ESCREVE O RESULTADO EM UMA COLLECTION> },
 { $indexStats: <INDICA SE ALGUM ÍNDICE FOI UTILIZADO> }
]
```

Como o próprio nome `pipeline` indica, a **order** das declarações importa e modifica o resultado do aggregate. Então, se você quer por exemplo, ordenar o retorno, deixe o parâmetro `sort` por último, e não antes do `group`, entendeu?

Não vamos utilizar todas as opções disponíveis de uma só vez, e podemos fazer qualquer combinação delas, enquanto estamos construindo a nossa agregação, para atingirmos o resultado esperado.

### options

E os `options` (argumento opcional que não precisa ser enviado):

```
{
  explain: boolean,
  allowDiskUse: boolean,
  cursor: boolean,
  bypassDocumentValidation: boolean,
  readConcern: boolean
}
```

Basicamente, agregações mais simples podem ser feitas utilizando o método `.group()`, com uma sintaxe bem diferente, e um pouco mais manualmente, já que no group, recorremos a estratégia de map/reduce, em vez de termos operadores prontos que fazem o trabalho de média por exemplo para nós.

A agregação *pipeline* consiste em etapas, ou seja, cada estágio transforma os documentos a medida que passa através do pipeline. 

A melhor definição de [**Pipeline**](https://docs.mongodb.org/manual/core/aggregation-pipeline/#pipeline) é uma unidade de processamento de dados.

**exemplo:**
![](https://github.com/souzacristsf/MongoDb-ebook/blob/master/src/images/pipeline_mongodb.png)

Temos a seguinte leitura na imagem acima:

Obtemos os documentos da nossa coleção e em seguida temos as fases `stages` de cada um dos quais realiza a operação de cada entrada. Para cada fase temos uma modificação na nossa saída dos documentos. É **importante planejar** cada fase para que possamos realmente obter os documentos desejados.

Explicarei melhor com exemplos, logo em seguida e para entendermos sobre o que podemos ter em cada **stages** e facilitar nosso dia a dia. 

Como estou fazendo o curso [Be-mean](https://www.youtube.com/watch?v=leYxsEAL_yY), usarei o banco **be-mean** e a coleção **pokemons** para prosseguir com os exemplos. 
Então para uma melhor aprendizagem sugiro seguir esses passos abaixo:

- Importar o arquivo [json aqui](https://github.com/Webschool-io/MongoDb-ebook/blob/master/src/data/pokemons.json) para o banco be-mean. Puts, mas eu não sei como vou fazer para importar esses arquivos para o meu mongodb. Segue esse [vídeo](https://www.youtube.com/watch?v=1eHc8reT_Vk) em 2:03 minutos de video o professor [William Bruno]( https://github.com/wbruno ) pode te ajudar, é lógico, se você estiver com o mongodb instalado. Caso ainda não instalou siga essa [documentação](https://docs.mongodb.org/manual/tutorial/install-mongodb-on-ubuntu/) para instalar em seu S.O.


### Banco Relacional - ordem lógica de execução

Como estou vindo do banco relacional, primeiramente vale lembrar de como o banco de dados relacional trabalha em relação a ordem lógica de execução.

Mas o que tem a ver a ordem lógica de execução do banco relacional com `aggregation` do mongodb?

Muita calma nessa hora, o que eu quero mostrar é a ordem da estrutura no mongodb, onde dependendo do posicionamento dos operadores `$limit`, `$skip` e `$sort` o resultado pode não ser o esperado. 

**Ordem lógica de execução**

![](http://4.bp.blogspot.com/-1f9bRPl9YA8/UDTDQ8eJaFI/AAAAAAAAAGY/6G8iMVTJDtg/s1600/post4.png)

Na imagem acima, temos a ordem lógica de execução em um banco de dados SQL SERVER. Vale lembrar que esse é o modelo padrão da estrutura sql que utilizo normalmente no relacional. Temos a mesma estrutura em outros bancos como MySql, Postgres, DB2 e etc.


## Aggregation Operators
ou **`operadores de agregação`**

Iremos conhecer os operadores que podem fazer parte do método `aggregate`.

### $match 
Filtra os documentos que correspondem à condição especificada, para seguir no `stages` do pipeline seguinte. O **`$match`** nada mais é que o método **find()**.

**exemplo:**
```js
db.pokemons.aggregate([
        {$match: {speed: {$gte: 40}}}
])
```
Estamos utilizando o método **aggregate** e realizando um `find` com o operador `$match`, onde irá retornar da nossa coleção pokemons, todos pokemons onde o `speed` é maior ou igual 40.

### $project
Se quisermos informar os campos que queremos na nossa saida dos dados `output`. Vale ressaltar que estamos adicionando mais uma etapa na nossa `stages`.

**exemplo:**
```js
db.pokemons.aggregate([
        {$match: {speed: {$gte: 40}}},
        {$project: { 
           _id: 0,
           name: 1, 
           speed: 1	
        }}
])
```
Nesse caso o `_id` não será apresentado em nossa saída, apenas os nomes dos `pokemons` e `speed` utilizando o método de agregação.


### $limit
Limita o número de documentos passados ​​para a próxima etapa no pipeline.

**exemplo:**
```js
db.pokemons.aggregate([
        {$match: {speed: {$gte: 40}}},
        {$limit: 5},
        {$project: { 
           _id: 0,
           name: 1
        }}
])
```
Obtemos a quantidade de 5 documentos no processo de cada `stages` no pipeline. Apresentando na saída apenas os nomes dos pokemons.

Podemos perceber que podemos trocar a ordem de cada `stages`, como o exemplo abaixo:
```js
db.pokemons.aggregate([
        {$match: {speed: {$gte: 40}}},
        {$project: { 
           _id: 0,
           name: 1
        }},
        {$limit: 5}
])
```
Temos o mesmos documentos do exemplo anterior. Mas cuidado dependendo dos operadores utilizados os resultados não serão os mesmos.

### $sort
Ordena os documentos de acordo com a classificação de entrada. onde:
- 1 para especificar ordem crescente.
- -1 para especificar ordem decrescente.

**exemplo:**
```js
db.pokemons.aggregate([
        {$match: {speed: {$gte: 40}}},
        {$sort: {name: 1, speed: -1}},
        {$limit: 5},
        {$project: { 
           _id: 0,
           name: 1
        }}
])
```
Nesse caso estamos ordenando os documentos, onde `name` em ordem crescente e `speed` em ordem decrescente.

No exemplo abaixo iremos trocar o **$sort** no lugar do **$limit**. Podemos perceber que os documentos não são os mesmos. 

**ATENÇÃO!!!**, nem sempre os valores que queremos é conforme a ordem que aplicamos no método **aggregate**. Fiquem ligados na ordem de cada `stages`.

```js
db.pokemons.aggregate([
        {$match: {speed: {$gte: 40}}},
        {$limit: 5},
        {$sort: {name: 1, speed: -1}},
        {$project: { 
           _id: 0,
           name: 1
        }}
])
```

### $skip
Ignora o número especificado de documentos que passam para o `stages` e passa os documentos restantes para a próxima fase do `pipeline`. 

**exemplo:**
```js
db.pokemons.aggregate([
        {$match: {speed: {$gte: 40}}},
        {$sort: {name: 1, speed: -1}},
        {$skip: 5},
        {$limit: 5},
        {$project: { 
           _id: 0,
           name: 1
        }}
])
```
Adicionamos mais um operador em nossa `stages` para ignorar o número de documentos especificados no método `aggregate`. Nesse caso o **`$skip`** ignorou os 5 primeros documentos no processo do pipeline. Perceba que temos cinco `stages` para o filtro na nossa coleção pokemons, `$match === find()` , `$sort`, `$skip`, `$limit`, e `$project` . 


##$group
É o agrupamento de documentos por alguma expressão especificada e saída para a próxima fase de um documento para cada grupo distinto.

**exemplo:**
```js
db.pokemons.aggregate(
   [
      {
        $group : {
           _id : null,
           totalPokemons: {$sum: 1}
        }
      }
   ]
)
```
Apenas para exemplificar o uso do `$group` no método **aggregate**.

A imagem abaixo temos um melhor entendimento no que se refere ao método aggregate. 
![](https://docs.mongodb.org/manual/_images/aggregation-pipeline.png)


##Relacional vs. mongodb
Percebam que temos no relacional uma estrutura onde não podemos trocar de lugares conforme as `stages` da estrutura do mongodb.
Fora que no mongoDB podemos trabalhar com [Embedded Documents](https://docs.mongodb.org/v3.0/tutorial/model-embedded-one-to-many-relationships-between-documents/) de One-to-Many ou One-to-One. 
No banco relacional precisaríamos usar JOIN entre várias tabelas.

##Performace
Pessoal, em outro post falarei sobre a criação de [INDEX](https://docs.mongodb.org/manual/tutorial/create-an-index/) no mongodb, para obter uma melhor performance ao utilizar uma busca. 

É isso aí e até a próxima!!!!


##Referências
1 - [Documentação MongoDB](https://docs.mongodb.org/manual/meta/aggregation-quick-reference/)


