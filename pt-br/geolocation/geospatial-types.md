## Geolocation

Como sabemos, o MongoDB suporta os seguintes tipos de objetos GeoJSON:
* Point
* LineString
* Polygon
* MultiPoint
* MultiLineString
* MultiPolygon
* GeometryCollection

Apresentação do objeto que podemos armazenar.
```
{ type: "<GeoJSON type>" , coordinates: <coordinates> }
```
**Obs** : lembrando a ordem da coordenada no mongodb é (longitudade, latitude).

Falaremos de cada um na sequência paraum melhor entendimento. Então, prepare um cafézinho e vamos nessa. :)

#### Point
Vamos dizer que queremos armazenar um tipo GeoJSON `Point`, usaremos o seguinte documento de exemplo:

```
{
  "type": "Point",
  "coordinates": [
    -47.59917855262756,
    -15.938995401664664
  ]
}
```
Temos um GeoJSON que é o do tipo `Point`.      
Estou usando esse [site](http://geojson.io/#map=17/-15.93847/-47.59879) para pegar os tipo de objetos GeoJSON.

#### LineString
Um exemplo bem simples usando `LineString` é um trajeto que fazemos, por exemplo um percurso de casa para o trabalho ou uma caminhada na praia.

```
{ 
  "type": "LineString", 
  "coordinates": [ 
    [ 40, 5 ], 
    [ 41, 6 ],
    [ 42, 7 ],
    [ 43, 8 ]
  ] 
}
```

![](/src/images/linestring.png)    
Nessa imagem podemos perceber a marcação em azul, para um exemplo `LineString`.

### Polygon
Consistem de uma matriz de GeoJSON LinearRings coordenada matrizes. LineStrings ter pelo menos quatro pares de coordenadas e temos que especificar a mesma posição que o primeiro e o último coordenadas.

```
{
  "type": "Polygon",
  "coordinates": [
    [
      [
        -47.6002836227417,
        -15.940635696515947
      ],
      [
        -47.6002836227417,
        -15.937850282215209
      ],
      [
        -47.597107887268066,
        -15.937850282215209
      ],
      [
        -47.597107887268066,
        -15.940635696515947
      ],
      [
        -47.6002836227417,
        -15.940635696515947
      ]
    ]
  ]
}
```
![](/src/images/polygon.png)   
Exemplo de um `Polygon`. Podemos fazer uma consulta para verificar se um ponto está dentro de um polygon. 
Nos proximos conteudo vamos explorar melhor os tipos de GeoJSON.

### MultiPoint
GeoJSON MultiPoint uma lista de pontos.
```
{
  type: "MultiPoint",
  coordinates: [
     [ -73.9580, 40.8003 ],
     [ -73.9498, 40.7968 ],
     [ -73.9737, 40.7648 ],
     [ -73.9814, 40.7681 ]
  ]
}
```

![](/src/images/multistring.png)  
Exemplo de um `MultiPoint` e um `Polygon`.

### MultiLineString   
É uma matriz de pontos, onde temos o inicio e fim dos pontos.

```
{
  type: "MultiLineString",
  coordinates: [
     [ [ -73.96943, 40.78519 ], [ -73.96082, 40.78095 ] ],
     [ [ -73.96415, 40.79229 ], [ -73.95544, 40.78854 ] ],
     [ [ -73.97162, 40.78205 ], [ -73.96374, 40.77715 ] ],
     [ [ -73.97880, 40.77247 ], [ -73.97036, 40.76811 ] ]
  ]
}
```
![](/src/images/multilinestring.png) 
Exemplo de `MultiLineString`.

```
{
  "type": "GeometryCollection",
  "geometries": [
     {
       "type": "MultiPoint",
       "coordinates": [
          [ -73.9580, 40.8003 ],
          [ -73.9498, 40.7968 ],
          [ -73.9737, 40.7648 ],
          [ -73.9814, 40.7681 ]
       ]
     },
     {
       "type": "MultiLineString",
       "coordinates": [
          [ [ -73.96943, 40.78519 ], [ -73.96082, 40.78095 ] ],
          [ [ -73.96415, 40.79229 ], [ -73.95544, 40.78854 ] ],
          [ [ -73.97162, 40.78205 ], [ -73.96374, 40.77715 ] ],
          [ [ -73.97880, 40.77247 ], [ -73.97036, 40.76811 ] ]
       ]
     }
  ]
}
```
![](/src/images/multlinestring.png)
Exemplo de `Multipoint` e `MultiLineString` chamamos de tipos `GeometryCollection`.

### MultiPolygon
Para o tipo "MultiLineString", o "coordinates"membro deve ser uma matriz de LineString coordenar matrizes.

```
{
  type: "MultiPolygon",
  coordinates: [
     [ [ [ -73.958, 40.8003 ], [ -73.9498, 40.7968 ], [ -73.9737, 40.7648 ], [ -73.9814, 40.7681 ], [ -73.958, 40.8003 ] ] ],
     [ [ [ -73.958, 40.8003 ], [ -73.9498, 40.7968 ], [ -73.9737, 40.7648 ], [ -73.958, 40.8003 ] ] ]
  ]
}
```
![](/src/images/multipolygon.png)   
Exemplo lindo de `MultiPolygon` utilizando Geospatial.

### GeometryCollection
O exemplo a seguir armazena coordenadas de GeoJSON tipo `GeometryCollection`.
```
{
  type: "GeometryCollection",
  geometries: [
     {
       type: "MultiPoint",
       coordinates: [
          [ -73.9580, 40.8003 ],
          [ -73.9498, 40.7968 ],
          [ -73.9737, 40.7648 ],
          [ -73.9814, 40.7681 ]
       ]
     },
     {
       type: "MultiLineString",
       coordinates: [
          [ [ -73.96943, 40.78519 ], [ -73.96082, 40.78095 ] ],
          [ [ -73.96415, 40.79229 ], [ -73.95544, 40.78854 ] ],
          [ [ -73.97162, 40.78205 ], [ -73.96374, 40.77715 ] ],
          [ [ -73.97880, 40.77247 ], [ -73.97036, 40.76811 ] ]
       ]
     }
  ]
}
```

Aprendemos alguns tipos de objetos GeoJSON que podemos armazenar no **MongoDB**.

Lembrando que teremos para cada tipo de objetos GeoJSON um índice, seja ele `2dsphere` ou `2d`.

Nos proximos post veremos os operadores que precisamos para trabalhar com Geospatial. Teremos muita mão na massa e alguns exemplo para executar e aprendermos melhor.

Participe, você pode nos ajudar também mandando algumas correções nesse Ebook MongoDB de Ortografia ou escrevendo algo novo. :)

Então, é isso aí e até a próxima!!!!

Proxímos post [clique aqui](/pt-br/geolocation/geospatial-types.md).

##Referências  
1 - [Geospatial Types](https://docs.mongodb.com/manual/reference/geojson/#linestring)