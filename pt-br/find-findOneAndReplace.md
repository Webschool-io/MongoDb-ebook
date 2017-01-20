## findOneAndReplace

`findOneAndReplace()` modifica e retorna um único documento com no campo chave de seleção inseridos.Nessa busca o documento retorna o conteúdo atualizado caso o campo chave não exista no banco de dados um novo registro é inserido e se o upsert seja definido como true.

Esquema do `findOneAndReplace()`:

```
db.collection.findOneAndReplace(
   <filter>,
   <replacement>,
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
    replacement: O campo que você quer alterar.
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
  "height": 1.5
}
Fetched 3 record(s) in 4ms

```

"Gostaria de alterar o campo `description` para *'ejeta cachaça gelada'*, como seria com o `findOneAndReplace()?`"

```

var Query = {name: 'Squirtle'};
var update = {description: 'ejeta cachaça gelada'};
db.pokemons.findOneAndReplace(
	Query,update,
   {
      maxTimeMS: 2,
      returnNewDocument: true
   }
)
{
  "_id": ObjectId("588100ad90b829185531c38f"),
  "description": "ejeta cachaça gelada"
}

```

_Doideira, mas como alterou sem o $set e outra, pq só retornou o campo `description`?_

Se liga, só na estrutura.

No campo  `Query` estamos buscando pelo o `name` contido no documento no meu caso o `Squirtle`,  no campo update estou entrando com o JSON informando que quero alterar para ` ejeta cachaça gelada`  `description`.

O Replace não usa operadores de modficação caso queira alterar algum documento só basta passar o objeto mas o dado que voçê quer modificar.

_Belezinha, mas só retornou um objeto sem os demais, pq ?_

Vamos lá, 

O Replace do inglês para o português significa substituir, então quando dou um `replace` estou modificando o documento e não só o campo.

Vamos buscar os documentos vendo o resultado do `findOneAndReplace()`.

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
  "description": "ejeta cachaça gelada"
}

```

***CUIDADO***

Quando for usar o `findOneAndReplace()` entenda que ele vai substituir todo o seu documento com o objetos que passou.

