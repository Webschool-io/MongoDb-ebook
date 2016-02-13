## Aggregation

Como o MongoDb não possui JOINS utilizamos uma ferramenta diferente para conseguirmos agregar valores para alguma finalidade, por exemplo fazer uma query que retorne a média salarial dos seus funcionários.

Em um banco relacional você usaria operações como:

- JOIN
- AVG
- SUM

No MongoDB usaremos o [Aggregation Framework](https://docs.mongodb.org/manual/core/aggregation-pipeline/), ele utiliza-se de operações de agregação que processam registros e retorna um resultado computado.

###Pipeline    
A agregação pipeline consiste em etapas, ou seja, cada estágio transforma os documentos a medida que passa através do pipeline. 

A melhor definição de [**Pipeline**](https://docs.mongodb.org/manual/core/aggregation-pipeline/#pipeline) é uma unidade de processamento de dados.

**exemplo:**
![](https://github.com/souzacristsf/MongoDb-ebook/blob/master/src/images/pipeline_mongodb.png)

Temos a seguinte leitura na imagem acima:

Obtemos os documentos da nossa coleção e em seguida temos as fases `stages` de cada um dos quais realiza a operação de cada entrada. Para cada fase temos uma modificação na nossa saida dos documentos. É **importante planejar** cada fase para que possamos realmente obter os documentos desejados.

Explicarei melhor com exemplos mais adiante para entendermos sobre o que podemos ter em cada stages e facilitar nosso dia a dia. 

Como estou fazendo o curso [Be-mean](https://www.youtube.com/watch?v=leYxsEAL_yY), estarei usando o banco be-mean e a coleção pokemons para prosseguir com os exemplos. 
Então para uma melhor aprendizagem aconselho seguir esses passos abaixo:

- Importar o arquivo [json aqui](https://github.com/Webschool-io/MongoDb-ebook/blob/master/src/data/pokemons.json) para o banco be-mean. Puts, mas eu não sei como vou fazer para importar esses arquivos para o meu mongodb. Segue esse [video](https://www.youtube.com/watch?v=1eHc8reT_Vk) em 2:03 minutos de video o Professor [William Bruno]( https://github.com/wbruno ) pode te ajudar, é logico que tenha o mongodb instalado. Caso ainda não instalou siga essa [documentação](https://docs.mongodb.org/manual/tutorial/install-mongodb-on-ubuntu/) para instalar em seu S.O.


###Banco Relacional - ordem lógica de execução

Como estou vindo do banco relacional, primeiramente vale lembrar de como o banco de dados relacional trabalha em relação a ordem lógica de execução.

Mas o que tem haver a ordem lógica de execução do banco relacional com `aggregation` do mongodb?

Muita calma nessa hora, o que eu quero mostrar é a ordem da estrutura no mongodb, onde dependendo do posicionamento dos operadores `$limit`, `$skip` e `$sort` o resultado pode não ser o esperado. 

**Ordem lógica de execução**
![](http://4.bp.blogspot.com/-1f9bRPl9YA8/UDTDQ8eJaFI/AAAAAAAAAGY/6G8iMVTJDtg/s1600/post4.png)

Na imagem acima, temos a ordem lógica de execução em um banco de dados SQL SERVER. Vale lembrar que esse é o modelo padrao da estrutura sql que utilizo normalmente no relacional. Pode ser que seja a mesma estrutura em outros bancos como MySql, Postgres, DB2 e etc.


##Aggregation Operators
**ou operadores de agregação**

Iremos conhecer os operadores que podem fazer parte do método `aggregate`.

###$match 
Filtra os documentos que correspondem à condição especificada, para seguir no `stages` do pipeline seguinte. Nada mais é que o método **find()**.

**exemplo:**
```js
db.pokemons.aggregate([
        {$match: {speed: {$gte: 40}}}
])
```
Estamos utilizando o método **aggregate** e realizando um `find` com o operador $match, onde irá retornar da nossa coleção pokemons, todos pokemons onde o speed e maior ou igual 40.

###$project
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
Nesse caso o `_id` não será apresentado em nossa saida, apenas os nomes dos `pokemons` e `speed` utilizando o método de agregação.


###$limit
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
Obtemos a quantidade de 5 documentos no processo de cada `stages` no pipeline. Apresentando na saida apenas os nomes dos pokemons.

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
Temos o mesmos documentos do exemplo anterior. Mas cuidado dependendo os operadores utilizado os resultados não será o mesmo.

###$sort
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

No exemplo abaixo iremos trocar o **$sort** no lugar do **$limit**. Podemos perceber que os documentos não é o mesmo. 

**ATENÇÂO!!!** nem sempre os valores que queremos e conforme a ordem que aplicamos no metodo **aggregate**. Fiquem ligados na ordem de cada `stages`.

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

###$skip
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
Adicionamos mais um operador em nossa `stages` para ignorar o numero de documentos especificados no método `aggregate`. Nesse caso ele **`$skip`** ignorou os 5 primeros documentos no processo do pipeline. Perceba que temos cinco `stages` para o filtro na nossa coleção pokemons, `$match (find())` , `$sort`, `$skip`, `$limit`, e `$project` . 


##$group
É o agrupamendo de documentos por alguma expressão especificada e saídas para a próxima fase de um documento para cada grupo distinto.

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

A imagem abaixo temos um melhor entendimento no que se refere ao metodo aggregate. 
![](https://docs.mongodb.org/manual/_images/aggregation-pipeline.png)


##Relacional vs. mongodb
Percebam que temos no relacional uma estrutura onde não podemos trocar de lugares conforme as `stages` da estrutura do mongodb.
Fora que no mongoDB podemos trabalhar com [Embedded Documents](https://docs.mongodb.org/v3.0/tutorial/model-embedded-one-to-many-relationships-between-documents/) de One-to-Many ou One-to-One. 
No banco relacional precisariamos usar JOIN entre varias tabelas.


##Referências
1 - [Documentação MongoDB](https://docs.mongodb.org/manual/meta/aggregation-quick-reference/)












