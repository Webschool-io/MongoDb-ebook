# MongoDB

## Insert - InsertOne e InserMany

Com o novo lançamento do [MongoDb 3.2](https://docs.mongodb.org/manual/), vou atualizar aqui falando um pouco do `insertOne()` e `insertMany()` novos recursos disponivél na ferramenta, entre outros que possivélmente abordarei em outros posts. 

Estarei abordando direto o assunto me assegurando que conheçam o basico. Caso contrario segue a documentação detalhada do assunto [aqui](https://docs.mongodb.org/manual/reference/method/db.collection.insertOne/#definition) e o Curso [Be-Mean](https://www.youtube.com/watch?v=leYxsEAL_yY) gratuito.


##inserOne()

O `inserOne()` como o proprio nome ja diz insere um documento em uma coleção. O metodo tem a seguinte sintaxe:

```js
db.collection.insertOne(
   <document>,
   {
      writeConcern: <document> 
   }
)
```

- document -> Um documento para inserir na coleção.
- writeConcern -> Opcional. Podemos passar parametro ```{ writeConcern: { w : "majority", wtimeout : 100 } }; ``` para a gravação do documento, caso exceda o limite de tempo **wtimeout** é retornado um `exception thrown` como no exemplo abaixo e mesmo que a gravação é executada com sucesso: 

**exemplo**
```
WriteConcernError({
   "code" : 64,
   "errInfo" : {
      "wtimeout" : true
   },
   "errmsg" : "waiting for replication timed out"
})
```

###Exemplo insertOnde()

**exemplo**
```js
db.collection.insertOne( { item: "card", qty: 15 } );
```

A operação retorna o seguinte documento, é claro que se expecificar o `_id` será esse o seu retorno :

```js
{
   "acknowledged" : true,
   "insertedIds" : [
      ObjectId("562a94d381cb9f1cd6eb0e1a"),
   ]
}
```

###InsertMany()

Para o `insertMany()` iremos inserir alguns documentos utilizando insertOne() para prosseguir com a explicação.

Crie um dataBase **teste**, podemos criar utilizando o comando `use teste` ou  qualquer nome que desejar.

Para que confirme essa criação insirá os documentos abaixo, apenas copie e cole no seu terminal:

```js
db.mycollection.insertOne({ "_id" : "tt0084726", "title" : "Star Trek II: The Wrath of Khan", "year" : 1982, "type" : "movie" });
db.mycollection.insertOne({ "_id" : "tt0117731", "title" : "Star Trek: First Contact", "year" : 1996, "type" : "movie" });
db.mycollection.insertOne({ "_id" : "tt0796366", "title" : "Star Trek", "year" : 2009, "type" : "movie" });
db.mycollection.insertOne({ "_id" : "tt1408101", "title" : "Star Trek Into Darkness", "year" : 2013, "type" : "movie" });
``` 
**Pronto!!!**

Criamos nosso banco e inserimos alguns documentos, e com o seguinte comando para exibi-los `db.mycollection.find().pretty()`, ´pretty() apenas para deixar o resultado identado.


Caso haja a necessidade de inserir varios documentos ao mesmo tempo em nossa coleção. 
Poderiamos fazer o seguinte comando, copie e cole no seu terminal: 

```js
db.mycollection.insertMany(
    [
        {"_id" : "tt0084726", "title" : "Star Trek II: The Wrath of Khan", "year" : 1982, "type" : "movie"},
        {"_id" : "tt0117731", "title" : "Star Trek: First Contact", "year" : 1996, "type" : "movie"},
        {"_id" : "tt0081458", "title" : "The Scarlett O'Hara War", "year" : 1980, "type" : "movie"},
        {"_id" : "tt0084480", "title" : "San shi nian xi shuo cong tou", "year" : 1982, "type" : "movie"},
        {"_id" : "tt0084725", "title" : "O Estado das Coisas", "year" : 1982, "type" : "movie"},
        {"_id" : "tt0084755", "title" : "Taboo II", "year" : 1996, "type" : "movie"}
    ]
);
```

Obs: note que temos dois `_id` duplicado existente na nossa coleção ,sendo eles **"_id" : "tt0084726" e "_id" : "tt0117731"**.

Se você executou o comando, onde o **mongodbDB** age de forma ordenada é logico que você recebeu o seguinte erro de chave duplicada:


```js
BulkWriteError({
        "writeErrors" : [
                {
                        "index" : 0,
                        "code" : 11000,
                        "errmsg" : "E11000 duplicate key error index: teste.mycollection.$_id_ dup key: { : \"tt0084726\" }",
                        "op" : {
                                "_id" : "tt0084726",
                                "title" : "Star Trek II: The Wrath of Khan",
                                "year" : 1982,
                                "type" : "movie"
                        }
                }
        ],
        "writeConcernErrors" : [ ],
        "nInserted" : 0,
        "nUpserted" : 0,
        "nMatched" : 0,
        "nModified" : 0,
        "nRemoved" : 0,
        "upserted" : [ ]
})
```

Se eu tenho uma aplicação e sei que pode haver documentos duplicados e mesmo assim gostaria de continuar inserindo os outros documentos não duplicados.
Passariamos mais um argumento chamado `ordered`, copie e cole o seguinte comando:

```js
db.mycollection.insertMany(
    [
        {"_id" : "tt0084726", "title" : "Star Trek II: The Wrath of Khan", "year" : 1982, "type" : "movie"},
        {"_id" : "tt0117731", "title" : "Star Trek: First Contact", "year" : 1996, "type" : "movie"},
        {"_id" : "tt0081458", "title" : "The Scarlett O'Hara War", "year" : 1980, "type" : "movie"},
        {"_id" : "tt0084480", "title" : "San shi nian xi shuo cong tou", "year" : 1982, "type" : "movie"},
        {"_id" : "tt0084725", "title" : "O Estado das Coisas", "year" : 1982, "type" : "movie"},
        {"_id" : "tt0084755", "title" : "Taboo II", "year" : 1996, "type" : "movie"}
    ],
    {
        "ordered": false
    }
);
```

**Pronto!!!**

Acabamos de inserir os documento de forma não ordenada `{ "ordered": false }`, mesmo com valores duplicados o mongo continua inserindo os documentos.

Podemos perceber que temos um retorno de erro e mesmo assim inserimos os documentos:

```js
BulkWriteError({
        "writeErrors" : [
                {
                        "index" : 0,
                        "code" : 11000,
                        "errmsg" : "E11000 duplicate key error index: teste.mycollection.$_id_ dup key: { : \"tt0084726\" }",
                        "op" : {
                                "_id" : "tt0084726",
                                "title" : "Star Trek II: The Wrath of Khan",
                                "year" : 1982,
                                "type" : "movie"
                        }
                },
                {
                        "index" : 1,
                        "code" : 11000,
                        "errmsg" : "E11000 duplicate key error index: teste.mycollection.$_id_ dup key: { : \"tt0117731\" }",
                        "op" : {
                                "_id" : "tt0117731",
                                "title" : "Star Trek: First Contact",
                                "year" : 1996,
                                "type" : "movie"
                        }
                }
        ],
        "writeConcernErrors" : [ ],
        "nInserted" : 4,
        "nUpserted" : 0,
        "nMatched" : 0,
        "nModified" : 0,
        "nRemoved" : 0,
        "upserted" : [ ]
})
```

Com o seguinte comando podemos excluir a coleção criada nesse post `db.mycollection.drop()` ou  se você criou ontra coleção `db.nome_colecao.drop()`. 

Por enquanto é só!!!

## Referências

1 - [University MongoDB, Curso MongoDB e NodeJS](https://university.mongodb.com/courses); <br/>
2 - [Documentação MongoDB](https://docs.mongodb.org/manual/)



 
 
 


