# MLOps-Data-Engineer-Machine-Learning
El objetivo del proyecto es realizar un sistema de recomendacion de videojuegos a usuarios.

En este proyecto se lleva acabo un estudio basado en Machine Learning Operations (MLOps).Este estudio se divide en tres etapas principales : 

---

1. **Exploración y Transformación:** Se realizará un análisis exploratorio de los datos, incluyendo la exploración de distribuciones y detección de correlaciones y valores atípicos.

2. **Preparación de Datos:** Se prepararán los datos para comprender las relaciones entre las variables y construir modelos sobre ellos. También se crearán funciones para consultas a los datos, consumibles a través de una API.

3. **Modelado:** Se desarrollarán modelos de Machine Learning para entender relaciones y predecir correlaciones entre variables.

Los datos utilizados incluyen información sobre juegos en la plataforma Steam y la interacción de los usuarios con estos juegos.

---

# Desarrollo

### Exploración, Transformación y Carga (ETL)

A partir de los 3 dataset proporcionados (`steam_games, user_reviews y user_items`) referentes a la plataforma de Steam, en primera instancia se realizó el proceso de limpieza de los datos.

#### `steam_games`
- Se eliminaron filas completamente nulas y se procede a analizar cada columna.  
- Se da prioridad a ciertas columnas que se consideran relevantes para el desarrollo de la API, estas incluyen: `id, genres, price, release date y developer`.
- En la columna "id" se realiza la eliminación de valores nulos , duplicados y Cambio del tipo de dato a INT.
- En la columna "genres" se realiza el tratamiento de valores nulos. Se utiliza la información de la columna "tags" para rellenar los nulos en "genres" y los demas valores nulos son eliminados 
- En la columna "price" se identifican los valores unicos estableciendo en 0.0 los valores nulos y aquellos que corresponden a juegos con "tags" o género "Free to Play". También se ajustan a 0.0 los que tienen 
  valores como "Free," "Demo," "Play" e "Install".Se eliminan tambien los datos nulos y se redondea los valores a 2 decimales. 
- En la columna "release date" Se busca obtener únicamente el año de lanzamiento ya que presenta diferentes formatos de fecha , para ello se procesa cada tipo de formato de fecha para extraer el año.
  Los datos datos nulos y aquellos que no tienen un formato de fecha válido se eliminan.
- Por ultimo se verifica que no queden valores vacios en las columnas procesadas y se eliminan el resto de columnas no utilizadas.
- Se exporta los datos en un formato JSON comprimido en gzip.

#### `user_reviews`
- Se eliminan valores duplicados antes de comenzar la tranformacion .
- Se realiza la transformacion de datos separando todas las reviews y se genera una fila por cada review dentro de la columna "reviews", donde cada elemento contiene múltiples reviews del mismo usuario. Estas 
   se concatenan para facilitar su procesamiento.
- Se eliminaron filas con valores nulos,considerándolos como datos irrelevantes.
- Se crea la columna `"sentiment_analysis"` utilizando la librería nltk, que incluye la herramienta Sentiment Vader, un modelo entrenado para puntuar un texto como positivo, neutro o negativo.
- Por último se verifican las columnas procesadas y se elimina la columna "review" antes de exportar los datos en un formato JSON comprimido en gzip.

#### `user_items`  


