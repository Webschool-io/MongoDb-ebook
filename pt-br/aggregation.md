## Aggregation

Como o MongoDb não possui JOINS utilizamos uma ferramenta diferente para conseguirmos agregar valores para alguma finalidade, por exemplo fazer uma query que retorne a média salarial dos seus funcionários.

Em um banco relacional você usaria operações como:

- JOIN
- AVG
- SUM

No MongoDB usaremos o [Aggregation Framework](https://docs.mongodb.org/manual/core/aggregation-pipeline/), ele utiliza-se de operações de agregação que processam registros e retorna um resultado computado.


Primeiramente vale lembrar de como o banco relacional trabalha em relação a ordem lógica de execução.

E o que o banco relacional tem haver com aggregation do mongodb?

Muita calma nessa hora, o que eu quero mostrar é a flexibilidade que temos nessa ordem logica de execução


![](https://github.com/souzacristsf/MongoDb-ebook/blob/master/src/images/pipeline_mongodb.png)
![](http://4.bp.blogspot.com/-1f9bRPl9YA8/UDTDQ8eJaFI/AAAAAAAAAGY/6G8iMVTJDtg/s1600/post4.png)
![](https://docs.mongodb.org/manual/_images/aggregation-pipeline.png)

