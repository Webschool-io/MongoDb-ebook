##Index
---
Existe uma variedade de fatores para obtermos um bom desempenho para o nosso sistema. Desempenho de hardware como, cpu, disco e a memoria. Para obter impacto a latência e taxa de transferência na consulta de banco de dados, temos duas possibilidades que são:
 - Adicionar *índixes* a nossa coleção.
 - Distribuir a carga em vários servidores, implementando *sharding*.

##Create Index
---
O *index* é nosso aliado quando o assunto é desempenho na busca de dados em nossa *coleção*. Seja o *[MMAPv1](https://docs.mongodb.org/v3.0/core/mmapv1/)* ou *[wiredTiger](https://docs.mongodb.org/manual/core/wiredtiger/)*  mecanismo de armazenamento utilizado em *mongodb*, podemos obter uma grande melhoría na busca de dados ao utilizar *índex*.

Com operações de atualização e exclusão, onde você encontra o documento que deseja e, em seguida, executar uma escrita, temos a ajuda do índice quando realizamos a fase de consulta, mas não quando executamos a gravação.

Os próximos exemplos iremos utilizar o banco *restaurantes*. Segue o [link](https://raw.githubusercontent.com/Webschool-io/MongoDb-ebook/master/src/data/restaurantes.javascripton) para copiar os documentos e importar no seu banco de dados mongodb.

Iremos fazer uma consulta com o seguinte comando:
```javascript
db.restaurantes.find({cuisine: "Bakery"}).explain('executionStats');
```
Estou utilizando uma **versão mongodb anterior da 3.2.**
Na versão mongodb 3.2 o comando seria o seguinte:
```javascript
db.restaurantes.explain('executionStats').find({cuisine: "Bakery"});
```

Estamos fazendo um *find()* na coleção `restaurantes` onde a cuisine é `Bakery` e exibindo nossa `explain`, exibindo o plano de consulta.

```javascript
{
  "queryPlanner": {
    "plannerVersion": 1,
    "namespace": "be-mean.restaurantes",
    "indexFilterSet": false,
    "parsedQuery": {
      "cuisine": {
        "$eq": "Bakery"
      }
    },
    "winningPlan": {
      "stage": "COLLSCAN",
      "filter": {
        "cuisine": {
          "$eq": "Bakery"
        }
      },
      "direction": "forward"
    },
    "rejectedPlans": [ ]
  },
  "executionStats": {
    "executionSuccess": true,
    "nReturned": 691,
    "executionTimeMillis": 23,
    "totalKeysExamined": 0,
 "executionStats": {
        "executionSuccess": true,
        "nReturned": 691,
        "executionTimeMillis": 23,
        "totalKeysExamined": 0,
        "totalDocsExamined": 25359,
        "executionStages": {
        "stage": "COLLSCAN",
           "filter": {
             "cuisine": {
             "$eq": "Bakery"
         }
  },   "totalDocsExamined": 25359,
    "executionStages": {
      "stage": "COLLSCAN",
      "filter": {
        "cuisine": {
          "$eq": "Bakery"
        }
      },
      "nReturned": 691,
      "executionTimeMillisEstimate": 30,
      "works": 25361,
      "advanced": 691,
      "needTime": 24669,
      "needFetch": 0,
      "saveState": 198,
      "restoreState": 198,
      "isEOF": 1,
      "invalidates": 0,
      "direction": "forward",
      "docsExamined": 25359
    }
  },
  "serverInfo": {
    "host": "Souza",
    "port": 27017,
    "version": "3.0.8",
    "gitVersion": "83d8cc25e00e42856924d84e220fbe4a839e605d"
  },
  "ok": 1
}
```
Com o retorno, podemos perceber no plano de consulta as seguintes propriedade:

1. "COLLSCAN" - nossa consulta passou por todos os documentos da nossa coleção.
2. "parsedQuery" - informações da consulta, consulta analisada.
3. "Filter" - nosso filtro da busca.
4. "totalDocsExamined" - total de documentos analisados.
5. "totalKeysExamined" - total de Keys examinados, nesse caso quando temos índices aplicados.
6. "nReturned" - numero de documento retornado na consulta.

*Resumindo*. Temos em nossa coleção `restaurantes` um total de 25359 documentos. Ao exibir o plano de consulta, foi feito uma varredura em nossa coleção passando em cada documento e comparando se a `cuisine` é igual `Bakery`. Com isso foi retornado 691 documentos.

Podemos melhorar a busca aplicando um `índice` na propriedade que passamos por paramento, nesse caso `cuisine`.

Com o seguinte comando aplicamos um índex na propriedade cuisine, copie e cole no seu terminal.
```javascript
db.restaurantes.createIndex({cuisine: 1});
```
Na criação do índex passei o valor **1** e estou dizendo que quero **indexar** essa propriedade na ordem crescente e na se fosse **-1** seria ordem decrescente.

retorno: ![alt text][logo]
```javascript
{
  "createdCollectionAutomatically": false,
  "numIndexesBefore": 1,
  "numIndexesAfter": 2,
  "ok": 1
}
```
Por padrão no mongodb o **_id** já possue um índice, nesse caso temos dois índices aplicado na nossa coleção `restaurantes`.

Com o comando abaixo, vamos imprimir os index criado em nossa coleção.
```javascript
db.system.indexes.find()
{
  "v": 1,
  "key": {
    "_id": 1
  },
  "name": "_id_",
  "ns": "be-mean.restaurantes"
}
{
  "v": 1,
  "key": {
    "cuisine": 1
  },
  "name": "cuisine_1",
  "ns": "be-mean.restaurantes"
}
```
Temos as seguintes descrições para o `stage` quando executamos o método *explain*:

- **COLLSCAN**  para uma varredura coleção
- **IXSCAN** para chaves de índice de digitalização
- **FETCH** para recuperar documentos
- **SHARD_MERGE** para mesclar os resultados dos *shard*

Depois de criado o `índex`, vamos analisar a diferença executando a consulta abaixo
```javascript
db.restaurantes.explain('executionStats').find({cuisine: "Bakery"});
```
retorno: ![alt text][logo]
```javascript
{
  {nner": {
    "plannerVersion": 1,
    "namespace": "be-mean.restaurantes",
    "indexFilterSet": false,
    "parsedQuery": {
      "cuisine": {
        "$eq": "Bakery"
      }
    },
    "winningPlan": {
      "stage": "FETCH",
      "inputStage": {
        "stage": "IXSCAN",
        "keyPattern": {
          "cuisine": 1
        },
        "indexName": "cuisine_1",
        "isMultiKey": false,
        "direction": "forward",
        "indexBounds": {
          "cuisine": [
            "[\"Bakery\", \"Bakery\"]"
          ]
        }
      }
    },
    "rejectedPlans": [ ]
  },
  "executionStats": {
    "executionSuccess": true,
    "nReturned": 691,
    "executionTimeMillis": 1,
    "totalKeysExamined": 691,
    "totalDocsExamined": 691,
    "executionStages": {
      "stage": "FETCH",
      "nReturned": 691,
      "executionTimeMillisEstimate": 0,
      "works": 692,
      "advanced": 691,
      "needTime": 0,
      "needFetch": 0,
      "saveState": 5,
      "restoreState": 5,
      "isEOF": 1,
      "invalidates": 0,
      "docsExamined": 691,
      "alreadyHasObj": 0,
      "inputStage": {
        "stage": "IXSCAN",
        "nReturned": 691,
        "executionTimeMillisEstimate": 0,
        "works": 692,
        "advanced": 691,
        "needTime": 0,
        "needFetch": 0,
        "saveState": 5,
        "restoreState": 5,
        "isEOF": 1,
        "invalidates": 0,
        "keyPattern": {
          "cuisine": 1
        },
        "indexName": "cuisine_1",
        "isMultiKey": false,
        "direction": "forward",
        "indexBounds": {
          "cuisine": [
            "[\"Bakery\", \"Bakery\"]"
          ]
        },
        "keysExamined": 691,
        "dupsTested": 0,
        "dupsDropped": 0,
        "seenInvalidated": 0,
        "matchTested": 0
      }
    }
  },
  "serverInfo": {
    "host": "Souza",
    "port": 27017,
    "version": "3.0.8",
    "gitVersion": "83d8cc25e00e42856924d84e220fbe4a839e605d"
  },
  "ok": 1
}
```
Com o retorno, podemos analisar o seguinte:
Em `totalKeysExamined` tivemos 691 chaves examinadas, na consulta anterior o resultado era 0.
Foi feito uma procura pelo index criado, como mostra a propriedade `stage`, **IXSCAN**.
Podemos perceber que o tempo de execução da consulta `executionTimeMillis` melhorou em realação a consulta anterior.

**Pronto!!!**

O *index* é o nosso aliado quando o assunto é perfomance na busca de dados. Para mais informações procure a documentação do [mongodb](https://docs.mongodb.org/manual/tutorial/create-an-index/).

Próximo post vou falar sobre *Full text search*. Como funciona no mongodb uma simples procura por texto e também falar um pouco mais a respeito dos operadores $regex, $text, $search e a criação de ensureIndex() para a melhoria na procura das palavras. Clique [aqui](./full_text_search.md) para o próximo post.

##Referência
-[Documentação Mongodb](https://docs.mongodb.org/manual/indexes/)

[logo]: https://github.com/souzacristsf/MongoDb-ebook/blob/master/src/images/seta.png "Ebook MongoDB"