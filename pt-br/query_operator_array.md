## [Query Operator Array](https://docs.mongodb.org/manual/reference/operator/query-array/)

Temos a nossa disposição os operadores `$all`, `$elemMatch` e `$size` para facilitar a buscas em array no MongoDb.

Explicarei melhor com exemplos, logo em seguida.

Como estou fazendo o curso [Be-mean](https://www.youtube.com/watch?v=leYxsEAL_yY), usarei o banco **be-mean** e a coleção **pokemons** para prosseguir com os exemplos. Então para uma melhor aprendizagem sugiro seguir esses passos abaixo:

- Importar o arquivo [json aqui](https://github.com/Webschool-io/MongoDb-ebook/blob/master/src/data/pokemons.json) para o banco be-mean. Puts, mas eu não sei como vou fazer para importar esses arquivos para o meu mongodb. Segue esse [vídeo](https://www.youtube.com/watch?v=1eHc8reT_Vk) em 2:03 minutos de video o professor [William Bruno]( https://github.com/wbruno ) pode te ajudar, é lógico, se você estiver com o mongodb instalado. Caso ainda não instalou siga essa [documentação](https://docs.mongodb.org/manual/tutorial/install-mongodb-on-ubuntu/) para instalar em seu S.O.

Primeiramente iremos fazer uma consulta por tipo de dados.

###$types
retorna os documentos de acordo com a especificação do tipo indicado.

**exemplo:**
```js
db.pokemons.find( { 
	_id: { $type : 7 } 
} ).pretty();
```
Retornamos todos os documentos, onde o `_id` é do tipo **Object id**.

Podemos retornar também todos os documentos onde o campo `types` é do tipo **string** ou um **array** com pelo menos um texto inserido.

**exemplo:**
```js
db.pokemons.find( { 
	types: { $type : 2 } 
} ).pretty();
```

**ou** se você quer listar documentos em que o campo `types` é uma matriz, podemos usar o operador `$were`.
db.pokemons.find({ 
	$where : "Array.isArray(this.types)" 
} ).count();


###$all    
Retorna todos os documentos onde o valor de um campo é uma matriz que contém um ou todos os elementos especificados. 

**exemplo:**
```js
db.pokemons.find( { 
	types: { $all: [  "normal", "flying" ] } 
} ).pretty()
```
É retornado todos os documentos que possuem o `types` **normal** ou **flying**. 


Os próximos exemplos iremos utilizar o banco `restaurantes`. Segue o **[link](https://raw.githubusercontent.com/Webschool-io/MongoDb-ebook/master/src/data/restaurantes.json)** para copiar os documentos e importar no seu banco de dados mongodb. Siga os passos para a importação, conforme a orientação no começo desse post. 

###$elemMatch
Retorna os documentos que contêm um campo de matriz com pelo menos um elemento que coincide com todos os critérios de consulta especificadas.

**exemplo:**
```js
db.restaurantes.find({
	grades:{
		$elemMatch:{
			grade: "A",
			score: {$gte: 23}
			}
	}
})
```
É retornado todos os documentos que possuem os valores especificados no método `find`.

Poderiamos ter realizado essa simples consulta, talvez teriamos os mesmos resultado. 
```js
db.restaurantes.find({
	grades:{
		grade: "A",
		score: {$gte: 23}
	}
})
```
Aí que você se engana. Nesse exemplo estamos dizendo o seguinte. Retornar todos os documentos onde a `grade` é "A" e `score` é maior que 23. 
Não podemos esquecer que estamos fazendo uma busca em **Array**, nesse caso se apenas um elemento do array for diferente dos argumentos da `query` não teremos retorno.

O **$elemMatch** retorna os documentos, se no caso um elemento for verdadeiro, ou seja, se um elemento entre cinco satisfazer o nosso critério de busca, esse documento será retornado.


###$size
Retorna todos os documentos na coleção, onde o número de elementos é especificados pelo argumento.

**exemplo:**
```js
db.restaurantes.find( { 
	grades: { $size: 2 } 
} ).pretty();
```
Nesse caso, foi retornado todos os documentos da coleção, onde na `grades` possui dois elementos no **array**.

É isso aí e até a próxima!!!!

##Referências
1 - [Documentação MongoDB](https://docs.mongodb.org/manual/reference/operator/query-array/)












