## MELI-Entrega23
Entrega Prácticos "Análisis y Curación de datos" e "Introducción al aprendizaje supervisado"

#### Ramiro CARO - Cohorte ALPHA
#### Marcelo LEON - Cohorte OMEGA
##### DiploDatos 2019 - FAMAF

Consideraciones:

- El dataset base se puede descargar de https://drivegoogle.com/file/d/1tNUKD1lf1z8C7LPpCruiDl6SKBAHb79v/view?ts=5cc45a8d 
- Las notebooks buscan y generan los archivos csv en la carpeta _dataset_ 

#### -Previo

Se adjunta la documentación de la primera entrega sobre Análisis Exploratorio, en la misma se muestran las observaciones que se hicieron sobre los distintos atributos, y además un enfoque considerado para valores faltantes, que quedó solo como ejercicio ya que en el próximo práctico se respetó lo pedido para esta situación.

- _MELI_Exploracion.ipynb_, notebook con análisis del dataset base.
- _MELI_Exploracion.docx_, informe exploratorio.

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

- MELI_AYC_A.ipynb

En esta notebook revisamos las variables categóricas que nos interesa incluir en el modelo, con estas consideraciones:
* Las categorizaciones con valores nulos las vamos a asignar a un label "SIN_DATOS", ya que asumimos que es una situación que pueda darse al no ser valores obligatorios.
* Las categorizaciones con menos de 30 valores las consideramos sin valor estadístico, por lo tanto las agrupamos con el label "OTROS"

Categorías revisadas (nuevos atributos):

* DT_CAT_PROD, id del catálogo. El 88% de los envíos pertenecen a un solo catálogo, le siguen en significancia aquellos con pocos envíos (<30) agrupados en OTROS y representa el 10%. 
* DT_CONDITION, condición de venta. El 10% estaba sin info y fue a la categoría SIN_DATOS. Mas del 88% son Nuevos, llama la atención la poca cantidad de envíos de productos usados.
* DT_DOMAIN, categoría del producto. Está muy dispersa, las dos categorías más significativas corresponden a los grupos SIN_DATOS 10% y OTROS 5%, el restante 85% se distribuye entre 1090 categorías.
* DT_SELLER, vendedor. La mayoría de los vendedores tiene menos de 30 envíos, por lo que los agrupamos en OTROS representando el 65%. 

Del análisis de los componentes de ATTRIBUTES extraemos estas columnas:
* DT_BRAND, marca. Cerca del 40% tiene poca relevancia (<30). El 20% de los envíos no tiene este dato.
* DT_MODEL, modelo. El 50% sin relevancia (<30) y 35% sin dato.

Sumamos como columna también:
* LEN_ATR, cantidad de atributos del JSON.

Además, siguiendo con lo solicitado, se eliminaron aquellos con STATUS 404 (78361 casos) y los que carecen de valor en SHP_HEIGHT, SHP_LENGTH, SHP_WEIGHT, SHP_WIDTH (125262 casos). También quitamos los envíos con precio > 30mil reales por considerarlos no racionales (34 casos).

- MELI_AYC_B.ipynb

En esta notebook intentamos buscar una relación entre lo expuesto en el título de la publicación y la probabilidad de exceder el límite impuesto por el correo, ya que, tal lo expuesto en el análisis preliminar, habría palabras que tienen más peso en los productos que superan ese límite.

Como resultado sumamos la siguiente columna:
* SCORE, probabilidad de que el envío sea multado según lo expresado en el título.

- MELI_AYC_C.ipynb

En esta notebook completamos los valores faltantes de PRICE utilizando KNN. 

Como resultado sumamos la siguiente columna:
* R_PRICE, precio del producto enviado, con los casos nulos completados con el método solicitado.

#### -Introducción al aprendizaje supervisado

Consideraciones sobre el dataset final:

Descartamos estos atributos:
* ITEM_ID,  es la publicación, el modelo aplicaría a nuevas publicaciones ya que una publicación ya multada queda "marcada" 
* SHP_WEIGHT, no se conoce, es info del correo 
* SHP_LENGTH, idem
* SHP_WIDTH, idem
* SHP_HEIGHT, idem
* TITLE
* EXCEDIDO: creamos el atributo NO_MAQUINABLE como dato lógico

Atributos a incluir en el modelo:
* RV_PRICE
* LEN_ATR cantidad de atributos
* STATUS
* DT_CAT_PROD: ID Catalogo del Producto-Revisado
* DT_CONDITION: Condición de Venta -Revisado
* DT_DOMAIN: Categoría de la Publicación -Revisado
* DT_SELLER: ID Vendedor -Revisado
* DT_BRAND: Marca del Producto -Revisado
* DT_MODEL: Modelo del Producto -Revisado
* SCORE: probabilidad calculada sobre TITLE
* NO_MAQUINABLE: TRUE excede límite, caso contrario FALSE

Se probaron los siguientes modelos:
* SGDClassifier
* RandomForestClassifier

- MELI_INTRO_A.ipynb

En esta notebook ejecutamos ambos modelos con los parámetros por defecto y observamos de entrada valores muy altos de precisión y recall. Revisando los principales atributos considerados por el árbol de decisión, nos muestra:

SCORE 0.498368
RV_PRICE 0.092246
LEN_ATR 0.039542

SCORE es la columna que agregamos en función de aplicar un modelo sobre las palabras que se repiten en el título y su vínculo sobre la multa en el envío. Necesitamos verificar si el procedimiento empleado es correcto o estamos incorporando de alguna manera la variable objetivo dentro de los atributos de predicción.

- MELI_INTRO_B.ipynb

En esta notebook volvemos a ejecutar ambos modelos con los parámetros por defecto pero descartando el atributo SCORE.
Los valores de precisión y recall bajan considerablemente, los principales atributos considerados ahora serían:

RV_PRICE 0.306015
LEN_ATR 0.093905

En el práctico siguiente procedemos a optimizar estos modelos.



