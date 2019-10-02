## MELI-Entrega23
Entrega Prácticos "Análisis y Curación de datos" e "Introducción al aprendizaje supervisado"

#### Ramiro CARO - Cohorte ALPHA
#### Marcelo LEON - Cohorte OMEGA
##### DiploDatos 2019 - FAMAF

Consideraciones:

- El dataset base se puede descagar de https://drivegoogle.com/file/d/1tNUKD1lf1z8C7LPpCruiDl6SKBAHb79v/view?ts=5cc45a8d 
- Las notebook buscan y generan los archivos csv en la carpeta _dataset_ 

#### -Previo

Se adjunta la docuemntación de la primer entrega sobre Análisis Exploratorio, en la misma se muestran las observaciones que se hicieron sobre los distintos atributos, y además un enfoque considerado para valores faltantes, que quedó solo como ejercicio ya que en el próximo práctico se respetó lo peidodo para esta situación.

_* MELI_Exploracion.ipynb_, notebook con análisis del dataset base.
_* MELI_Exploracion.docx_, informe exploratorio.

#### -Comentarios del trabajo:

El dataset base contiene las siguientes columnas:
* ITEM_ID: id unívoco de cada item publicado. (Ofuscado)
* SHP_WEIGHT: peso del paquete informado por el correo.
* SHP_LENGTH: largo del paquete informado por el correo.
* SHP_WIDTH: ancho del paquete informado por el correo.
* SHP_HEIGHT: altura del paquete informado por el correo.
* ATTRIBUTES: atributos como marca y modelo, entre otros, en formato json-lines
* CATALOG_PRODUCT_ID: id del catálogo (ofuscado).
* CONDITION: condición de venta (nuevo o usado).
* DOMAIN_ID: id de la categoría a la que pertenece la publicación.
* PRICE: precio en reales.
* SELLER_ID: id del vendedor (ofuscado).
* STATUS: estado de la publicación (activa, cerrada, pausada, etc.)
* TITLE: título de la publicación.

#### -Análisis y Curación de datos

_* MELI_AYC_A.ipynb_
En esta notebook revisamos las variables categóricas que nos interesa incluir en el modelo, con estas consideraciones:
- Las categorizaciones con valores nulos las vamos a asignar a un label "SIN_DATOS", ya que asumimos que es una situación que pueda darse al no ser valores obligatorios.
- Las categorizaciones con menos de 30 valores las consideramos sin valor estadístico, por lo tanto las agrupamos con el label "OTROS"

Categorías revisadas (nuevos atributos):

* DT_CAT_PROD, id del catálogo. El 88% de los envíos pertenecen a un solo catálogo, el siguiente caso en significancia son aquellos con pocos envíos (<30) agrupados en OTROS y representa el 10%. 
* DT_CONDITION, condición de venta. El 10% estaba sin info y fue a la categoría SIN_DATOS. Mas del 88% son Nuevos, llama la atención la poca cantidad de envíos de productos usados.
* DT_DOMAIN, categoría del producto. Está muy dispersa, las dos categorías mas siginificativas corresponden a los grupos SIN_DATOS 10% y OTROS 5%, el restante 85% se distribuye entre 1090 categorías.
* DT_SELLER, vendedor. La mayoría de los vendedores tiene menos de 30 envíos, por lo que los agrupamos en OTROS representando el 65%. 

Del análisis de los componentes de ATTRIBUTES extraemos estas columnas:
* DT_BRAND, marca. Cerca del 40% tiene poca relevancia (<30). El 20% de los envíos no tiene este dato.
* DT_MODEL, modelo. El 50% sin relevancia (<30) y 35% sin dato.

Sumamos como columna también:
* LEN_ATR, cantidad de atributos del JSON.

Además, siguiendo con lo solicitado, se eliminaron aquellos con STATUS 404 (78361 casos) y los que carecen de valor en SHP_HEIGHT, SHP_LENGTH, SHP_WEIGHT, SHP_WIDTH (125262 casos). También quitamos los envíos con precio > 30mil reales por consideralos no racionales (34 casos).


_* MELI_AYC_B.ipynb_
En esta notebook intentamos buscar una relación entre lo expuesto en el título de la publicación y la probabilidad de exceder el límite impuesto por el correo, ya que, tal lo expuesto en el análisis preliminar, habría palabras que tienen mas peso en los productos que superan ese límite.
Como resultado sumamos la siguiente columna:
* SCORE, probabiliadad de que el envío sea multado según lo expresado en el título.

_* MELI_AYC_C.ipynb_



Medir las distribuciones de las variables como histogramas, realizar normalizaciones e identificar outliers con los métodos vistos en clase. Hacer análisis de estos outliers y considerar si sería correcto o no eliminarlos del dataset. Sugerencia: Identificar outliers de las columnas `SHP_WEIGHT` y `SHP_VOLUME`, donde `SHP_VOLUME` se define como el producto de las dimensiones.

Presentación: 
MovieLens.ipynb
Carpetas
./data/ ::Contiene los dataset que se utilizaron en el análisis (MovieLens 20M Dataset).
-movies.csv, lista de películas
-ratings.csv, películas vistas por usuarios y calificación asignada
IMPORTANTE: Descargar de https://grouplens.org/datasets/movielens/ debido a que los quité por el peso de los archivos.
Resumen
Se cuenta con un dataset de más de 27mil películas, estrenadas entre 1891 y 2015. Pertenecen a diversos géneros predominando el DRAMA y en menor medida la COMEDIA.
Se tiene acceso, además, a 20 millones de calificaciones de usuarios que vieron estas películas. Son algo menos de 140mil usuarios, que en promedio vieron 144 pelis cada uno.
La calificación media por Peli es 3.1.
Por una cuestión de performance se consideran SOLO películas que hayan obtenido un Rating promedio de 4 en adelante. El dataset se reduce a algo más de 1700 películas con 3 millones de calificaciones de 135mil usuarios.
Se aplica el algoritmo APRIORI para identificar reglas en este subconjunto:
Nuestros TICKET serán cada USUARIO y los ITEMS las películas que rankeó
Contamos con 135mil usuarios/ticket de donde extraeremos asociaciones entre películas con estos criterios:
-Ambas películas deben haber sido vistas, en conjunto, por al menos en el 0.01% de los usuarios (1300 aprox), podría ser menos pero implica mucho tiempo de proceso. (Soporte)
-La peli recomendada debe haber sido vista por el 50% de los usuarios que vieron la otra peli, como mínimo. (Confianza)
Resultados, se identificaron:
7394 reglas de asociación.
231 Películas diferentes como precedente, asociadas a 32 Pelis en promedio.
-Las 5 pelis que mas se repiten como precedente son:
-Wild Bunch, The (1969)
-Stalag 17 (1953)
-Man Who Would Be King, The (1975)
-Conversation, The (1974)
-In the Heat of the Night (1967)
121 Películas diferentes como consecuente, asociadas a 61 Pelis precedentes en promedio.
-Las 5 pelis que mas se repiten como consecuente son:
-Pulp Fiction (1994)
-Silence of the Lambs, The (1991)
-Shawshank Redemption, The (1994)
-Star Wars: Episode IV - A New Hope (1977)
-Forrest Gump (1994)
El menor lift es 1.11, por lo que precedente y consecuente tienen mayor probabilidad de ocurrir juntos que de manera aislada.
Se observó que las 4 pelis con lift más alto están asociadas en ida y vuelta:
-lift 23.013182 Laputa: Castle in the Sky -> Nausicaä of the Valley of the Wind
-lift 23.013182 Nausicaä of the Valley of the Wind -> Laputa: Castle in the Sky
-lift 30.229950 Jean de Florette (1986) -> Manon of the Spring (Manon des sources) (1986)
-lift 30.229950 Manon of the Spring (Manon des sources) (1986) -> Jean de Florette (1986)
En la notebook se detalla todo el procedimiento realizado.
