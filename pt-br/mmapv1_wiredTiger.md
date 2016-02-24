##Mecanismos de armazenamento com MMAPv1 e WiredTiger

Há dois tipos de mecanismos de armazenamento principais em mongodb, e um deles é chamado de *MMAPv1* e o outro *WiredTiger*.

##MMAPv1
---
Se destaca em cargas de trabalho com inserções de alto volume, leitura e atualizações in-loco. MMAPv1 é o mecanismo de armazenamento padrão no MongoDB 3.0 e todas as versões anteriores. Em uma máquina *Unix* se digitarmos o comando **man mmap**, teremos a seguinte descrição **mmap -- allocate memory, or map files or devices into memory".** Para o Mongodb salvar os documentos dentro de arquivos, ele inicialmente aloca um arquivo grande em disco. Então o mongodb faz a chamada de sistema `mmap` para o mapeamento de memoria virtual do mesmo tamanho do arquivo. O sistema operacional decide qual página estará na memória, nesse caso quando fazemos uma leitura a um documento e esse documento se encontra na página da memória o mesmo é retornado, caso contrário em seguida o sistema busca no disco e leva até a memória, antes que possamos ter acesso. Para mais informações sobre *MMAPv1* consulte a documentação [aqui](https://docs.mongodb.org/v3.0/core/mmapv1/#record-allocation-strategies).
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

Depois de levantar o *mongod* com o comando acima, teremos o seguinte retorno: ![alt text][logo]
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
Por padrão conectamos no *mongo* na porta 27017, que ja está configurada para o `wiredTiger`.

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
1. ns - diz que a coleção `teste` pertence ao banco `test`.
2. capped - diz se a coleção é uma `capped collection`. Onde a coleção é do tamanho fixo.
3. wiredTiger - diz que é uma coleção `wiredTiger`.

Consulte a documentação sobre `wiredTiger` [aqui](https://docs.mongodb.org/manual/core/wiredtiger/#document-level-concurrency).

##Referência
-[Documentação Mongodb](https://docs.mongodb.org/manual/)

[logo]: https://github.com/souzacristsf/MongoDb-ebook/blob/master/src/images/seta.png "Ebook MongoDB"
