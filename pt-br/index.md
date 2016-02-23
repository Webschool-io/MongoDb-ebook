##Index

Existe uma variedade de fatores para obtermos um bom desempenho para o nosso sistema. Desempenho de hardware como, cpu, disco e a memoria. Para obter impacto a latência e taxa de transferência na consulta de banco de dados, temos duas possibilidades que são:
 - Adicionar *índixes* a nossa coleção.
 - Distribuir a carga em vários servidores, implementando *sharding*.

Há dois tipos de mecanismos de armazenamento principais em mongodb, e um deles é chamado de *MMAPv1* e o outro *WiredTiger*.

##MMAPv1
---
Se destaca em cargas de trabalho com inserções de alto volume, leitura e atualizações in-loco. MMAPv1 é o mecanismo de armazenamento padrão no MongoDB 3.0 e todas as versões anteriores. Em uma máquina *Unix* se digitarmos o comando **man mmap**, teremos a seguinte descrição **mmap -- allocate memory, or map files or devices into memory".**Para o Mongodb salvar os documentos dentro de arquivos, ele inicialmente aloca um arquivo grande em disco. Então o mongodb faz a chamada de sistema `mmap` para o mapeamento de memoria virtual do mesmo tamanho do arquivo. O sistema operacional decide qual página estará na memória, nesse caso quando fazemos uma leitura a um documento e esse documento se encontra na página da memória o mesmo é retornado, caso contrário em seguida o sistema busca no disco e leva até a memória, antes que possamos ter acesso. Para mais informações sobre *MMAPv1* consulte a documentação [aqui](https://docs.mongodb.org/v3.0/core/mmapv1/#record-allocation-strategies).
**Power of 2 Sized Allocations**
*MongoDB 3.0* usa o poder de alocação de 2 tamanhos como a estratégia de alocação de registro padrão para **MMAPv1**. Com o poder da estratégia de alocação de 2 tamanhos, cada registro tem um tamanho em bytes que é uma potência de 2 (por exemplo, 32, 64, 128, 256, 512 ... 2MB). Para documentos com mais de 2 MB, a atribuição é arredondado para o múltiplo mais próximo de 2MB.

##WiredTiger
---
Por ser mais rápido, oferece algumas caracterisiticas interessantes e a primeira é a *simultaniedade a nivél de documento* de controle de concorrência para operações de gravação. Como resultado, vários clientes podem modificar diferentes documentos de uma coleção ao mesmo tempo, na verdade implementações livres de bloqueio. Outra caracteristica importante é a compressão. Por padrão, `WiredTiger` usa compressão para todas as coleções e índices. A compressão minimiza o uso do armazenamento à custa de CPU adicional. Portanto, se deseja iniciar o banco de dados com `wiredTiger`, usamos o seguinte comando:

- Antes de iniciar o wiredTiger, certifique-se de parar o processo mongod.
- Criamos um diretório `mkdir WT` para em seguinda apontar para o mesmo
- Em seguida iniciamos o mongod.

**exemplo:**
```javascript
mongod --dbpath WT --storageEngine wiredTiger
```

Depois de levantar o *mongod* com o comando acima, teremos o seguinte retorno:
```javascript
2016-02-22T13:10:46.009-0300 I CONTROL  [initandlisten] MongoDB starting : pid=7330 port=27017 dbpath=WT 64-bit host=Souza
2016-02-22T13:10:46.009-0300 I CONTROL  [initandlisten] db version v3.0.8
2016-02-22T13:10:46.010-0300 I CONTROL  [initandlisten] git version: 83d8cc25e00e42856924d84e220fbe4a839e605d
2016-02-22T13:10:46.010-0300 I CONTROL  [initandlisten] build info: Linux ip-10-5-158-147 3.2.0-36-virtual #57-Ubuntu SMP Tue Jan 8 22:04:49 UTC 2013 x86_64 BOOST_LIB_VERSION=1_49
2016-02-22T13:10:46.010-0300 I CONTROL  [initandlisten] allocator: tcmalloc
2016-02-22T13:10:46.010-0300 I CONTROL  [initandlisten] options: { storage: { dbPath: "WT", engine: "wiredTiger" } }
2016-02-22T13:10:46.053-0300 I STORAGE  [initandlisten] wiredtiger_open config: create,cache_size=1G,session_max=20000,eviction=(threads_max=4),statistics=(fast),log=(enabled=true,archive=true,path=journal,compressor=snappy),file_manager=(close_idle_time=100000),checkpoint=(wait=60,log_size=2GB),statistics_log=(wait=0),
2016-02-22T13:10:46.997-0300 I CONTROL  [initandlisten]
2016-02-22T13:10:46.997-0300 I CONTROL  [initandlisten] ** WARNING: /sys/kernel/mm/transparent_hugepage/enabled is 'always'.
2016-02-22T13:10:46.997-0300 I CONTROL  [initandlisten] **        We suggest setting it to 'never'
2016-02-22T13:10:46.998-0300 I CONTROL  [initandlisten]
2016-02-22T13:10:46.998-0300 I CONTROL  [initandlisten] ** WARNING: /sys/kernel/mm/transparent_hugepage/defrag is 'always'.
2016-02-22T13:10:46.998-0300 I CONTROL  [initandlisten] **        We suggest setting it to 'never'
2016-02-22T13:10:46.998-0300 I CONTROL  [initandlisten]
2016-02-22T13:10:47.424-0300 I NETWORK  [initandlisten] waiting for connections on port 27017
```
Estamos iniciando o *mongod* com `wiregTiger` na porta 27017.

Agora vamos conectar no `mongo` e realizar um `insert`.

```javascript
mongo
MongoDB shell version: 3.0.8
connecting to: test
Mongo-Hacker 0.0.9
Server has startup warnings:
2016-02-22T13:10:46.997-0300 I CONTROL  [initandlisten]
2016-02-22T13:10:46.997-0300 I CONTROL  [initandlisten] ** WARNING: /sys/kernel/mm/transparent_hugepage/enabled is 'always'.
2016-02-22T13:10:46.997-0300 I CONTROL  [initandlisten] **        We suggest setting it to 'never'
2016-02-22T13:10:46.998-0300 I CONTROL  [initandlisten]
2016-02-22T13:10:46.998-0300 I CONTROL  [initandlisten] ** WARNING: /sys/kernel/mm/transparent_hugepage/defrag is 'always'.
2016-02-22T13:10:46.998-0300 I CONTROL  [initandlisten] **        We suggest setting it to 'never'
2016-02-22T13:10:46.998-0300 I CONTROL  [initandlisten]
```
Por padrão quando conectamos no *mongo* na porta 27017, que ja está configurada para o `wiredTiger`.

Agora vamos realizar o `insert`, com o comando:
```javascript
db.teste.insert({name: "WiredTiger"});


Inserted 1 record(s) in 542ms
WriteResult({
  "nInserted": 1
})
```
Inserimos um documento baseado em `wiredTiger` o mecanismo de armazenamento do *mongodb*.

Para saber se nossa coleção é baseada em wiredTiger, fazemos o seguinte comando.
```javascript
db.teste.stats();
```
Conferindo o retorno do comando, teremos:
```javascript
{
  "ns": "test.teste",
  "count": 1,
  "size": 43,
  "avgObjavascriptize": 43,
  "storageSize": 16384,
  "capped": false,
  "wiredTiger": {
    "metadata": {
      "formatVersion": 1
    },
 }
}
```
Com o resultado do método *stats()* e analisando o retorno, temos algumas propriedade.
- ns -> diz que a coleção `teste` pertence ao banco `test`.
- capped -> diz se a coleção é uma `capped collection`. Onde a coleção é do tamanho fixo.
- wiredTiger -> diz que é uma coleção `wiredTiger`.

Consulte a documentação sobre `wiredTiger` [aqui]((https://docs.mongodb.org/manual/core/wiredtiger/#document-level-concurrency)).

##Create Index
---
O *index* é nosso aliado quando o assunto é desempenho na busca de dados em nossa *coleção*. Seja o `MMAPv1` ou `wiredTiger` o mecanismo de armazenamento utilizado em *mongodb*, podemos obter uma grande melhoría na busca de dados ao utilizar *índex*.

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

1. "COLLSCAN" - nossa consulta passou por todos os dumentos da nossa coleção.
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

retorno:
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
retorno:
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

Próximo post vou falar sobre *Full text search*. Como funciona no mongodb uma simples procura por texto e também falar um pouco mais a respeito dos operadores $regex, $text, $search e a criação de ensureIndex() para a melhoria na procura das palavras. Clique [aqui](./pt-br/full_text_search.md) para o próximo post.

##Referência
-[Documentação Mongodb](https://docs.mongodb.org/manual/indexes/)

[logo]: https://github.com/adam-p/markdown-here/blob/master/src/common/images/icon19-button.png