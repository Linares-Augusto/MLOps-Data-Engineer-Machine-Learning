# MLOps-Data-Engineer-Machine-Learning
El objetivo del proyecto es realizar un sistema de recomendacion de videojuegos a usuarios.

En este proyecto se lleva acabo un estudio basado en Machine Learning Operations (MLOps).Este estudio se divide en tres etapas principales : 

---

1. **Exploración y Transformación:** Se realizará un análisis exploratorio de los datos, incluyendo la exploración de distribuciones y detección de correlaciones y valores atípicos.

2. **Preparación de Datos:** Se prepararán los datos para comprender las relaciones entre las variables y construir modelos sobre ellos. También se crearán funciones para consultas a los datos, consumibles a través de una API.

3. **Modelado:** Se desarrollarán modelos de Machine Learning para entender relaciones y predecir correlaciones entre variables.

Los datos utilizados incluyen información sobre juegos en la plataforma Steam y la interacción de los usuarios con estos juegos.

---
## Índice
1. [ETL](#etl)
2. [Desarrollo API](#api)
3. [Análisis exploratorio de los datos](#eda)
4. [Modelo de Aprendizaje Automático](#ml)
5. [Video](#video)
6. [Contacto](#contacto)
---
## <a name="etl">Exploración, Transformación y Carga (ETL)</a>

A partir de los 3 dataset proporcionados (`steam_games, user_reviews y user_items`) referentes a la plataforma de Steam, en primera instancia se realizó el proceso de limpieza de los datos.

#### `steam_games` [(ETL_Steam_Games.ipynb)](https://github.com/Linares-Augusto/MLOps-Data-Engineer-Machine-Learning/blob/main/ETL-EDA/ETL_Steam_Games.ipynb)
- Se eliminaron filas completamente nulas y se procede a analizar cada columna.  
- Se da prioridad a ciertas columnas que se consideran relevantes para el desarrollo de la API, estas incluyen: `id, genres, price, release date y developer`.
- En la columna **"id"** se realiza la eliminación de valores nulos , duplicados y Cambio del tipo de dato a INT.
- En la columna **"genres"** se realiza el tratamiento de valores nulos. Se utiliza la información de la columna "tags" para rellenar los nulos en "genres" y los demas valores nulos son eliminados 
- En la columna **"price"** se identifican los valores unicos estableciendo en 0.0 los valores nulos y aquellos que corresponden a juegos con "tags" o género "Free to Play". También se ajustan a 0.0 los que tienen 
  valores como "Free," "Demo," "Play" e "Install".Se eliminan tambien los datos nulos y se redondea los valores a 2 decimales. 
- En la columna "release date" Se busca obtener únicamente el año de lanzamiento ya que presenta diferentes formatos de fecha , para ello se procesa cada tipo de formato de fecha para extraer el año.
  Los datos datos nulos y aquellos que no tienen un formato de fecha válido se eliminan.
- Por ultimo se verifica que no queden valores vacios en las columnas procesadas y se eliminan el resto de columnas no utilizadas.
- Se exporta los datos en un formato JSON comprimido en gzip.

#### `user_reviews` [(ETL_user_reviews.ipynb)](https://github.com/Linares-Augusto/MLOps-Data-Engineer-Machine-Learning/blob/main/ETL-EDA/ETL_User_Reviews.ipynb)
- Se eliminan valores duplicados antes de comenzar la tranformacion .
- Se realiza la transformacion de datos separando todas las reviews y se genera una fila por cada review dentro de la columna "reviews", donde cada elemento contiene múltiples reviews del mismo usuario. Estas 
   se concatenan para facilitar su procesamiento.
- Se eliminaron filas con valores nulos,considerándolos como datos irrelevantes.
- Se crea la columna `"sentiment_analysis"` utilizando la librería nltk, que incluye la herramienta Sentiment Vader, un modelo entrenado para puntuar un texto como positivo, neutro o negativo.
- El análisis produce un diccionario de la forma {'neg': valor, 'neu': valor, 'pos': valor, 'compound': valor}. El valor "compound" representa la puntuación total de la oración compuesta, con un rango entre -1 y 1.
- Se utiliza la puntuación "compound" para categorizar la review como negativa (si x < -0.5), neutra (-0.5 >= x <= 0.5) o positiva (si x > 0.5). Luego se redondea a un valor entero (-1, 0, 1).
- Además, se suma 1 a ese valor para obtener la referencia pedida en el ejercicio (malo 0, neutro 1, positivo 2).
- Por último se verifican las columnas procesadas y se elimina la columna "review" antes de exportar los datos en un formato JSON comprimido en gzip.

#### `user_items` [(ETL_user_items.ipynb)](https://github.com/Linares-Augusto/MLOps-Data-Engineer-Machine-Learning/blob/main/ETL-EDA/ETL_users_items_ETL.ipynb) 
- En este cuaderno Jupyter, se lleva a cabo el procesamiento de datos para el conjunto de datos de los items que posee cada usuario.
- Este proceso de ETL es el más breve, ya que se centra en eliminar columnas innecesarias para el desarrollo de la API. Los datos procesados se exportan en un archivo CSV comprimido en formato gzip.

## <a name="eda">Análisis exploratorio de los datos [(EDA_Datasets.ipynb)](https://github.com/Linares-Augusto/MLOps-Data-Engineer-Machine-Learning/blob/main/ETL-EDA/EDA_Datasets.ipynb)</a>

### Top 5 juego con mas horas jugadas (playtime_forever)
![](img/Top_5_mas_jugados.png)
- Podemos observar los 5 juegos con mas horas jugadas por todos los usuarios.
### Analisis de price
![](img/Analisis_Price.png)
- Podemos observar que la mayoria de los juegos son gratis, y hay algunos outliers que decido mantenerlos.
### Analisis de generos
![](img/Popularidad_generos_videojuego.png)

![](img/Top_5_mas_jugados.png)

### Cantidad de juegos lanzados por año
![](img/Cantidad_juegos_por_anio.png)

### Analisis de recommend

![](img/cantidad_recomendaciones.png)

![](img/top_5_mas_recomendado.png)

### Analisis de sentiment_analysis

![](img/Cantidad_sentiment.png)

![](img/top_5_mayor_sentiment.png)
## <a name="ml">Modelo de Aprendizaje Automático [(ML_Opcion_2.ipynb)]()</a>

En el notbook, se presenta la creación de un modelo de aprendizaje automático utilizando el conjunto de datos previamente explorado en el Análisis Exploratorio de Datos (EDA).

Para este modelo, se ha elegido utilizar el algoritmo SVD (Singular Value Decomposition) de la biblioteca Surprise. El objetivo es predecir las calificaciones de los usuarios para los elementos del conjunto de datos para hacer un sistema de recomendacion usuario-item.

El proceso consta de los siguientes pasos:

1. **Creación de las Calificaciones:** Para entrenar el modelo, se genera una `calificación` a partir de la suma de las columnas `recommend` (0 o 1) y `sentiment_analysis` (0, 1, 2). Estas calificaciones se escalan en una clasificación que varía de 0 a 3.
2. **Selección de Características:** Se seleccionan las características relevantes para el modelo, incluyendo `user_id`, `item_name` y la `calificación` creada en el paso anterior.
3. **Entrenamiento del Modelo:** El conjunto de datos se divide en un 70% para entrenamiento y un 30% para pruebas. Luego, se crea el modelo SVD, se entrena con los datos de entrenamiento y se evalúa su rendimiento utilizando la métrica RMSE (Error Cuadrático Medio de la Raíz). El valor obtenido indica la precisión del modelo, obteniendo un valor aceptable para su utilizacion.
4. **Optimización de Hiperparámetros:** Se ajustan los hiperparámetros del modelo para mejorar su rendimiento. Esto se aplica en la creación del modelo, y el proceso de entrenamiento se repite con los nuevos hiperparámetros.
Finalmente, el modelo entrenado se guarda en formato Pickle para poder ser utilizado en la API.

### Función "recomendacion_usuario"

La función **`"recomendacion_usuario"`** recibe como parámetro el nombre de un usuario (str) y devuelve una lista de los 5 juegos recomendados para ese usuario.

El proceso de recomendación se realiza de la siguiente manera:

1. Se verifica si el usuario existe en el conjunto de datos. Si no se encuentra, la función retorna `{'Error': 'El usuario no existe'}`.
2. Si el usuario existe, se filtran los juegos que ya posee el usuario y se eliminan de la lista de juegos disponibles para que no se le recomienden juegos que ya tiene.
3. A continuación, importamos el modelo de recomendación previamente entrenado, que se encuentra en formato pickle.
4. Se genera una clasificación para el usuario con respecto a todos los juegos disponibles en el conjunto de datos. El modelo utiliza esta clasificación para predecir qué juegos podrían gustarle al usuario.
5. Finalmente, la función selecciona y retorna los 5 juegos con la mayor probabilidad de que le gusten al usuario, basándose en las predicciones del modelo.
   Ejemplo de retorno: `{
   'Juegos recomendados para maplemage': [
   "King Arthur's Gold",
  'Everlasting Summer',
  'Call of Juarez Gunslinger',
  'The Wolf Among Us',
  'Dust: An Elysian Tail'
]
}`
