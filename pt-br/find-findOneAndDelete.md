## findAndDelete

`findAndDelete()` deletar um único documento com no campo chave de seleção inseridos.



Esquema do `findAndDelete()`:

```

db.collection.findOneAndDelete(
   <filter>,
   {
     projection: <document>,
     sort: <document>,
     maxTimeMS: <number>,
   }
)

```

Então como funciona ? 

        Filter: Campo chave que você quer pesquisar no documento.
        Projection: Retornar todos os campos no documento.
        sort:  Ordena o resultado em forma crescente ou decrescente.
        maxTimeMS: Especifica um limite de tempo em milissegundos dentro do qual a operação deve concluir.


### Query

```
var Query = {name: 'Squirtle'}

```
Isso significa que iremos pesquisar apenas os Pokemons com o `name` igual a `Charmander`.

Esse nosso objeto de `Query` tem a mesma funcionalidade do tão conhecido `SELECT` dos bancos relacionais.

### Deletando dados

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
  "description": "ejeta cachaça gelada"
}
```

"Gostaria de deletar um documento cuja o `name`  seja `Charmander` com o  `findAndDelete()`?"


```
var Query = {name: 'Charmander'};
db.pokemons.findOneAndDelete(Query,{maxTimeMs: 2});
{
  "_id": ObjectId("588100ad90b829185531c38e"),
  "name": "Charmander",
  "description": "Esse é o cão chupando manga de fofinho",
  "type": "fogo",
  "attack": 52,
  "height": 0.6
}

```

_Então ele deleta e ainda  retorna o documento que foi excluido?_

Sim, pequeno gafanhoto.

