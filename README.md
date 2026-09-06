# Food Delivery Time Prediction

## Orden de ejecución

Los notebooks están numerados y **deben ejecutarse en orden la primera vez**, porque el 02 genera
las particiones que consumen el 03, el 04 y el 05.

| Notebook | Criterios de la rúbrica | Requiere |
|---|---|---|
| `01_formulacion_hipotesis_y_datos.ipynb` | 1.1, 1.2, 2.1, 2.2, 3.1 | Nada |
| `02_preprocesamiento_y_particion.ipynb` | 3.2, 3.3 | Nada. **Genera las particiones** |
| `03_analisis_estadistico.ipynb` | 4.1, 4.2 | Notebook 02 |
| `04_modelos_y_metricas.ipynb` | 5.1, 5.2, 6.1 | Notebook 02 |
| `05_discusion_y_conclusiones.ipynb` | 6.2, 7.1, 7.2, 8.1, 8.2 | Notebooks 02 y 04 |

El notebook 01 es independiente: carga los datos desde la URL y puede correrse en cualquier
momento.

## Cómo usarlos en Google Colab

1. Entrar a [colab.research.google.com](https://colab.research.google.com)
2. `Archivo → Subir cuaderno` y elegir el notebook
3. `Entorno de ejecución → Ejecutar todo`

La primera celda monta Google Drive y crea la carpeta `MyDrive/food_delivery`, que es donde los
notebooks intercambian resultados y guardan los gráficos. Hay que autorizar el acceso la primera
vez.

Si el montaje falla o no se autoriza, la celda **corta con un error explícito** en lugar de
escribir en el disco temporal de Colab, que se borra al cerrar la sesión.

Fuera de Colab los notebooks funcionan igual: si no detectan Colab, usan la carpeta local
`./food_delivery` con la misma estructura.

## Datos

No hace falta descargar nada. Los notebooks leen el conjunto desde:

```
https://raw.githubusercontent.com/Vikranth3140/Food-Delivery-Time-Prediction/main/datasets/kaggle/train.csv
```

Son 7 MB, 45.593 pedidos y 20 columnas. Tras la limpieza del notebook 02 quedan 39.263 registros,
el 86 % del original.

La semilla está fijada en `RANDOM_STATE = 42` en la partición y en todos los modelos, de modo que
los resultados son reproducibles entre corridas.

## Tiempos aproximados

| Notebook | Duración |
|---|---|
| 01 | menos de 1 minuto |
| 02 | 1 a 2 minutos |
| 03 | 1 a 2 minutos |
| 04 | 2 a 4 minutos (entrena 5 modelos, el último con más de 1.500 variables) |
| 05 | 2 a 3 minutos (incluye el barrido de complejidad y el modelo de referencia) |

## Archivos que se generan

En `MyDrive/food_delivery/`:

```
food_delivery/
├── X_train.csv                    particiones de entrenamiento (notebook 02)
├── X_test.csv                     particiones de prueba (notebook 02)
├── y_train.csv                    variable objetivo de entrenamiento
├── y_test.csv                     variable objetivo de prueba
├── modelos_y_metricas.joblib      los cinco modelos entrenados (notebook 04)
├── tabla_metricas.csv             MSE, RMSE, MAE y R2 de train y test (notebook 04)
└── splits/                        los seis gráficos en PNG a 150 dpi
```

## Gráficos

Cada notebook guarda sus figuras en `splits/` además de mostrarlas en pantalla. Los nombres llevan
como prefijo el notebook que los produce:

| Archivo | Notebook | Contenido |
|---|---|---|
| `03_matriz_correlacion.png` | 03 | Correlaciones entre las variables numéricas |
| `03_varianza_por_variable.png` | 03 | Aporte individual de cada variable al tiempo de entrega |
| `03_dispersion_distancia_trafico.png` | 03 | Distancia contra tiempo, separado por nivel de tráfico |
| `03_distribuciones.png` | 03 | Histograma del objetivo y diagramas de caja por tráfico y clima |
| `04_comparacion_modelos.png` | 04 | R2 y RMSE de los cinco modelos en train y test |
| `04_prediccion_vs_real.png` | 04 | Predicción contra valor observado |
| `05_complejidad_y_sobreajuste.png` | 05 | Curvas de train y test frente al grado del polinomio |

## Modelos

Los cinco son lineales en sus parámetros. Lo que cambia entre ellos es qué variables recibe la
regresión.

| Modelo | Variables | Estimador |
|---|---|---|
| A. Lineal simple | 1 | `LinearRegression` |
| B. Lineal múltiple | 21 | `LinearRegression` |
| C. Polinómica grado 2 | 36 | `LinearRegression` |
| D. Interacciones completas | 231 | `Ridge(alpha=1.0)` |
| E. Splines con interacciones | 1.596 | `Ridge(alpha=1.0)` |

El notebook 05 ajusta además un `HistGradientBoostingRegressor` como referencia para medir cuánto
del error restante es atribuible a la familia lineal. No forma parte de los entregables del
experimento.
