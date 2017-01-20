## findAndUpdate

`findOneAndUpdate()` modifica e retorna um único documento com no campo chave de seleção inseridos.Nessa busca o documento retorna o conteúdo atualizado caso o campo chave não exista no banco de dados um novo registro é inserido e se o upsert seja definido como true.


Esquema do `findOneAndUpdate()`:

```
db.collection.findOneAndUpdate(
   <filter>,
   <update>,
   {
     projection: <document>,
     sort: <document>,
     maxTimeMS: <number>,
     upsert: <boolean>,
     returnNewDocument: <boolean>
   }
)
```

Então como funciona ? 

    filter: Campo chave que você quer pesquisar no documento.
    update: Campos que você quer alterar.
    projection: Retornar todos os campos no documento.
    sort:  Ordena o resultado em forma crescente ou decrescente.
    maxTimeMS: Especifica um limite de tempo em milissegundos dentro do qual a operação deve concluir.
    returnNewDocument: Especifica se o retorno do documento como true (documento é atualizado) e false (documento não atualizado).


### Query

```
var Query = {name: 'Squirtle'}

```
Isso significa que iremos pesquisar apenas os Pokemons com o `name` igual a `Squirtle`.

Esse nosso objeto de `Query` tem a mesma funcionalidade do tão conhecido `SELECT` dos bancos relacionais.

### Alterando dados

Buscando os dados contido no documento pokemon.

```
db.pokemons.find();
{
  "_id": ObjectId("588100ad90b829185531c38d"),
  "name": "Bulbassauro",
  "description": "Chicote de trepadeira",
  "type": "grama",
  "attack": 49,
  "height": 0.4
}
{
  "_id": ObjectId("588100ad90b829185531c38e"),
  "name": "Charmander",
  "description": "Esse é o cão chupando manga de fofinho",
  "type": "fogo",
  "attack": 52,
  "height": 0.6
}
{
  "_id": ObjectId("588100ad90b829185531c38f"),
  "name": "Squirtle",
  "description": "Ejeta água que passarinho não bebe",
  "type": "água",
  "attack": 68,
  "height": 0.5
}
Fetched 3 record(s) in 3ms

```

"Gostaria de alterar o campo height para 1.5, como seria com o `findAndUpdate()?`"

```
var Query = {name: 'Squirtle'}
var update = {$set: {height: 1.5}};

db.pokemons.findOneAndUpdate(
	Query,update,{
	maxTimeMS: 2,
	returnNewDocument: true
	}
)
{
  "_id": ObjectId("588100ad90b829185531c38f"),
  "name": "Squirtle",
  "description": "Ejeta água que passarinho não bebe",
  "type": "água",
  "attack": 68,
  "height": 1.5
}

```

_Manolo, que loucura é essa ?_ 

Calma, vamos explicar.

No campo  `Query` estamos buscando pelo o `name` contido no documento no meu caso o `Squirtle`,  no campo update estou entrando com o JSON informando que quero alterar para `1.5` no `heigth` usando $set.

_E esse `maxTimeMS` ?_

Bom esse maxTimeMS  passo um number informando quanto tempo é para alterar o documento.

Exemplo: 'Tenho um documento que só pode ser alterado depois de 2 segundos após uma função que executo'.

_Onde está puro do gato do `findAndUpdate`?_

O campo `findAndUpdate` é diferente do `findAndModify` pois o mesmo retorna o documento atualizado ou não, basta indicar para `returnNewDocument` como true ou false

