## findAndModify

Lembrando da aula anterior quando falei que a busca com `findOne` retorna um cursor onde você deve iterar nele para buscar seus dados, hoje veremos a diferença dele para o `findAndModify`.


`FindAndModify()` modifica e retorna um único documento com no campo chave de seleção inseridos. Nessa busca o documento não retorna o conteúdo atualizado por padrão caso o campo chave não exista no banco de dados um novo registro é inserido e se o upsert seja definido como true.

Esquema do `findAndModify()`:

```

db.collection.findAndModify({
    query: <document>,
    sort: <document>,
    new: <boolean>,
    fields: <document>,
    upsert: <boolean>
})

```

Então como funciona ? 

    query:  Campo determinado para a busca, aquele documento especifico que queremos modificar.
    sort:  Ordena o resultado em forma crescente ou decrescente.
    fields : Especifica o conjunto de campos a serem mostrados.
    new: Indica  se o documento modificado será exibido.
    upsert:  Cria um novo documento, se o campo chave da busca não for encontrado.


### Query

```
var Query = {name: 'Squirtle'}

```
Isso significa que iremos pesquisar apenas os Pokemons com o `name` igual a `Squirtle`.

Esse nosso objeto de `Query` tem a mesma funcionalidade do tão conhecido `SELECT` dos bancos relacionais.

### Adicionando dados

Buscando os dados contido no documento pokemon.

```
db.pokemons.find();

{
  "_id": ObjectId("5880fbb190b829185531c387"),
  "name": "Bulbassauro",
  "description": "Chicote de trepadeira",
  "type": "grama",
  "attack": 49,
  "height": 0.4
}
{
  "_id": ObjectId("5880fbb190b829185531c388"),
  "name": "Charmander",
  "description": "Esse é o cão chupando manga de fofinho",
  "type": "fogo",
  "attack": 52,
  "height": 0.6
}
{
  "_id": ObjectId("5880fbb190b829185531c389"),
  "name": "Squirtle",
  "description": "Ejeta água que passarinho não bebe",
  "type": "água",
  "attack": 48,
  "height": 0.5
}
Fetched 3 record(s) in 2ms

```

"Gostaria de acrescentar + 20 no campo 'attack', como seria com o `findAndModify()`?"

```
var Query = {name: 'Squirtle'}

db.pokemons.findAndModify({
   query: Query,
   update: {$inc: {attack: 20}}
});

```

Resultado: 

```
db.pokemons.findAndModify({query: Query,update: {$inc: {attack: 20}} });
{
  "_id": ObjectId("5880fbb190b829185531c389"),
  "name": "Squirtle",
  "description": "Ejeta água que passarinho não bebe",
  "type": "água",
  "attack": 48,
  "height": 0.5
}

```
_Ouxe, agora não entendi foi nada._ :\

Calma, vamos explicar.

No campo  query estamos buscando pelo o `name` contido no documento no meu caso o `Squirtle`,  no campo update estou entrando com o JSON informando que quero implementar + 20 na `attack` usando $inc. 

Verificando se o documento foi alterado.

```

db.pokemons.findOne(Query);
{
  "_id": ObjectId("588100ad90b829185531c38f"),
  "name": "Squirtle",
  "description": "Ejeta água que passarinho não bebe",
  "type": "água",
  "attack": 68,
  "height": 0.5
}

```

