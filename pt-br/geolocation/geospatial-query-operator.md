
## Geospatial Query Operators

Os operadores de consulta Geospatial no MongoDB são:

#### Query Selectors
+ **$geoWithin**: Seleciona geometrias dentro de um delimitador geometria GeoJSON . Podemos criar índices 2dsphere e 2d índices para `$geoWithin`.
+ **$geoIntersects**: Seleciona geometrias que se cruzam com um GeoJSON geometria. Criamos índice 2dsphere 
parar `$geoIntersects`.
+ **$near**: Retorna objetos geoespaciais em proximidade com um ponto. Requer um índice geoespacial como: O 2dsphere e 2d índices para `$near`.
+ **$nearSphere**: Retorna objetos geoespaciais em proximidade a um ponto em uma esfera. Requer um índice geoespacial como: 2dsphere e 2d índices para `$nearSphere`.

#### Geometry Specifiers
+ **$geometry**: Especifica uma geometria em GeoJSON formato para operadores de consulta geoespaciais.
+ **$minDistance**: Especifica uma distância mínima para limitar os resultados de consultas $near e $nearSphere. Utilizando índice 2dsphere.
+ **$maxDistance**: Especifica uma distância máxima para limitar os resultados de consultas $near e $nearSphere. Utilizando os índices 2dsphere e 2d índices para $centerSphere .
+ **$center**: Especifica um círculo usando legado pares de coordenadas para $geoWithin consultas ao utilizar a geometria plana. Utilizando o índice 2d para `$center`.
+ **$centerSphere**: Especifica um círculo usando um legado pares de coordenadas ou GeoJSON formato para consulta $geoWithin ao usar geometria esférica. Utilizando índices 2dsphere e 2d índices para `$centerSphere`.
+ **$box**: Especifica uma caixa retangular usando legado pares de coordenadas para consultas $geoWithin. Utilizando índice 2d para `$box`.
+ **$polygon**: Especifica um polígono legado pares de coordenadas para consultas $geoWithin. Utilizando índice 2d para $center.

Alguns operadores que iremos trabalhar nos proximos conteúdos. Explicações retiradas da propria documentação mongodb.

Aprendemos alguns tipos de operadores para consultas Geospatial no **MongoDB** e os índices que cada um suporta como padrão e melhoria da performance de consultas.

Lembrando que teremos para cada tipo de `Query Operators` Geospatial um índice, seja ele `2dsphere` ou `2d`.

Nos proximos post veremos esses operadores de consultas. E Teremos verdade muita mão na massa e alguns exemplo para executar e aprendermos melhor.

Participe, você pode nos ajudar também mandando algumas correções nesse Ebook MongoDB de Ortografia ou escrevendo algo novo. :)

Então, é isso aí e até a próxima!!!!

Proxímos post [clique aqui](/pt-br/geolocation/geospatial-crud.md).

##Referências  
1 - [Geospatial Query Operators](https://docs.mongodb.com/manual/reference/operator/query-geospatial/)


