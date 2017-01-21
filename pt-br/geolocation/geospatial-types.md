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
Exemplo de um `Polygon`.

### MultiPoint
### MultiLineString
### MultiPolygon
### GeometryCollection






##Referências  
1 - [Geospatial Types](https://docs.mongodb.com/manual/reference/geojson/#linestring)