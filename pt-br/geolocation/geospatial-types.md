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

