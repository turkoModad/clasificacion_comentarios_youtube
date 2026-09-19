# Análisis de Sentimiento en Comentarios de YouTube

Proyecto de clasificación de sentimientos (negativo, neutro, positivo) en comentarios de YouTube en español. El flujo abarca desde la descarga de los comentarios hasta la comparación de un modelo preentrenado contra uno fine-tuneado.

## Flujo del proyecto

### 1. Descarga del modelo base
`descargar_modelo_robertuito_local.ipynb`

Descarga el modelo `pysentimiento/robertuito-sentiment-analysis` desde Hugging Face y lo guarda localmente en `modelo_original/` junto con su tokenizador.

### 2. Descarga de comentarios de YouTube
`descargar_comentarios.ipynb`

Descarga los comentarios de los videos de un canal de YouTube en un rango de fechas definido. Requiere una API Key de YouTube Data API v3.

- Generar la API Key en [Google Cloud Console](https://console.cloud.google.com/) habilitando YouTube Data API v3.
- Guardarla en un archivo `.env` con la variable `YOUTUBE_TOKEN`.
- Configurar `CANAL_ID`, `FECHA_DESDE` y `FECHA_HASTA`.
- Genera un archivo CSV con separador `|` con los comentarios descargados.

### 3. Clasificación de los comentarios
Los comentarios descargados se clasifican manualmente o con ayuda de un modelo de IA como ChatGPT, etiquetando cada uno como `negativo`, `neutro` o `positivo`. El resultado se guarda como `clasificados.csv` con el formato `comentario|sentimiento`.

### 4. Entrenamiento del modelo
`entrenar_modelo.ipynb`

Fine-tuning del modelo `robertuito` sobre el dataset clasificado.

- Limpieza del dataset: eliminación de cabeceras, vacíos, duplicados y normalización de etiquetas.
- Split train/validation estratificado (80/20).
- Entrenamiento con `Trainer` de Hugging Face, pesos de clase balanceados y label smoothing.
- Early stopping y selección del mejor modelo por F1 macro.
- Guarda el modelo final en `mejor_modelo/`.

### 5. Comparación de modelos
`comparacion_final.ipynb`

Evalúa el modelo original (`modelo_original/`) y el fine-tuneado (`mejor_modelo/`) sobre un archivo de prueba (`predecir.csv`).

- Genera matrices de confusión y reportes de clasificación por modelo.
- Calcula mejora absoluta y relativa del modelo entrenado respecto al original.
- Exporta CSV con las predicciones y gráficos comparativos.

## Herramientas utilizadas

- **Python 3**
- **Hugging Face Transformers**: modelos y tokenizadores
- **Datasets**: manejo de datasets para entrenamiento
- **PyTorch**: backend de entrenamiento
- **scikit-learn**: métricas, split y pesos de clase
- **pandas** y **numpy**: manipulación de datos
- **matplotlib** y **seaborn**: visualización de matrices de confusión
- **Google API Python Client**: descarga de comentarios de YouTube
- **python-dotenv**: manejo de variables de entorno

## Requisitos

```bash
pip install torch transformers datasets scikit-learn pandas numpy matplotlib seaborn google-api-python-client python-dotenv

## Uso

1. Ejecutar `descargar_modelo_robertuito_local.ipynb` para obtener el modelo base.
2. Configurar `.env` con `YOUTUBE_TOKEN` y ejecutar `descargar_comentarios.ipynb`.
3. Clasificar los comentarios y guardarlos como `clasificados.csv`.
4. Ejecutar `entrenar_modelo.ipynb` para hacer fine-tuning.
5. Crear un archivo de pruebas clasificado llamado `predecir.csv`
5. Ejecutar `comparacion_final.ipynb` para comparar ambos modelos.