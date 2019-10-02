# MELI-Entrega23
Entrega Prácticos "Análisis y Curación de datos" e "Introducción al aprendizaje supervisado"

###Ramiro CARO - Cohorte ALPHA
###Marcelo LEON - Cohorte OMEGA
####DiploDatos 2019 - FAMAF

Notebook
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
