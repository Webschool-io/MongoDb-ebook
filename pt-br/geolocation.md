## Geolocation

Fala galera, quanto tempo. :)

Estou de volta, e agora para falar de algo novo e disseminar ainda mais conhecimento pra geral.

Vou ajudar a dar continuidade nesse grande Ebook sobre `Mongodb` e vou começar falando sobre nada mais nada menos que **Geospatial** (E a galera vai ao delirio). Isso mesmo, começarei aqui o ano de 2017 bem, então sente a bunda em qualquer lugar e vamos nessa.

Primeiramente, acredito que você já tenha o Mongodb instalado na sua maquina, caso não tenha instalado aproveita e [clique aqui](https://docs.mongodb.com/manual/), e baixe a nova versão. Estarei usando a versão MongoDB 3.2.

Usarei esse [site](http://geojson.io/#map=17/-15.93847/-47.59879) (Essa coordenada está no estado de Brasilia), como um guia para pegar as coordenadas que irei utilizar junto com o dados `Pokemons` que já temos de outros posts.

### Superfície 
Antes de armazenar seus dados de localização e consultas de escrita, você deve decidir o tipo de superfície a ser usada para executar cálculos. O tipo que você escolher afeta como você armazenar dados, que tipo de índice para construir, e a sintaxe de suas consultas.

#### Existe dois tipos de superfície no MongoDB:

*Spherical*    
Para calcular a geometria de mais de uma esfera semelhante à Terra, armazenar seus dados de localização em uma superfície esférica e usar `2dsphere índice`.

O modo de armazenamento de objetos GeoJSON de pares de coordenadas no MongoDB é ordem de eixo: `longitude`, `latitude`. Respeitem a sequencia para evitar possíveis falhas.

*Flat*    
Para calcular distâncias em um plano euclidiano (Na matemática, geometria euclidiana é a geometria, em duas e três dimensões, baseada nos postulados de Euclides de Alexandria), armazenar seus dados de localização como pares de coordenadas e usar um `2d` índice.

#### MongoDB suporta os seguintes objetos do GeoJSON:

* Point
* LineString
* Polygon
* MultiPoint
* MultiLineString
* MultiPolygon
* GeometryCollection
 
Podemos armazenar qualquer tipo de dados no mongodb, mas se quisermos fazer uma consulta `geospatial` precisamos usar coordenadas e criar `índice`. E para isso o mongodb suporte quatros tipos de índices para consulta geospatial:
* `2dsphere Indexes` : O índice suporta dados armazenados como GeoJSON com os pares de coordenadas (longitude, latitude). Calculando geometrias em uma esfera earth-like.
* `2d Indexes` : usamos coordenadas padrao no modelo (longitude, latitude) do mongodb. Como segue a indicação da documentação. O 2d índice suportar dados armazenados como pares de coordenadas e destina-se para uso em MongoDB 2.2 e versões anteriores. Esse índice é usado para consultar dados armazanados como pontos em um plano bidimensional.
* `geoHaystack Indexes` : Um índice haystack é um índice especial otimizado para retornar resultados mais pequenas áreas. Para consultas que usam geometria esférica, um 2dsphere índice é uma opção melhor do que um índice haystack.
* `2d Index Internals` : Fornece uma explicação mais aprofundada dos internos de índices geoespaciais. Este material não é necessário para o funcionamento normal, mas pode ser útil para a resolução de problemas e para melhor compreensão.

Nesse artigo, conhecemos os principais índices Geospatial no MongoDB e também os seguintes tipos de Objetos GeoJSON.

Não vou me estender muito, para não ficar desmotivador esse aprendizado.

Então, é isso aí e até a próxima!!!!

Proxímos post [clique aqui]().

##Referências
1 - [Documentação MongoDB Geospatial](https://docs.mongodb.com/manual/reference/operator/query-geospatial/)
2 - [Geospatial Indexes](https://docs.mongodb.com/manual/applications/geospatial-indexes/#geospatial-indexes)