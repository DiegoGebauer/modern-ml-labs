# Laboratorio 01 — CatBoost en inspecciones sanitarias de Chicago

## Objetivo

Estimar la probabilidad de que una inspección sanitaria termine en `Fail` usando información disponible antes de conocer el resultado. El laboratorio compara el rendimiento predictivo y el preprocesamiento necesario para trabajar con variables categóricas.

## Datos y evaluación

La fuente es el dataset oficial [Food Inspections del City of Chicago Data Portal](https://data.cityofchicago.org/Health-Human-Services/Food-Inspections/4ijn-s7e5/about_data). Se consideran inspecciones completadas entre 2019 y 2025: `Fail` es la clase positiva; `Pass` y `Pass w/ Conditions` forman la clase negativa.

La división es temporal: **entrenamiento 2019–2023**, **validación 2024** y **test final 2025**. Tras elegir las configuraciones con la validación, los modelos se reentrenan con 2019–2024 y se evalúan una sola vez en 2025.

Se comparan cuatro alternativas con la misma información de entrada: **XGBoost**, **CatBoost**, **regresión logística** y una **tasa general** que asigna a todas las inspecciones la tasa de `Fail` del entrenamiento. XGBoost y regresión logística emplean *one-hot encoding* externo; CatBoost recibe las ocho variables originales y trata las categóricas de forma nativa.

## Resultados finales — test 2025

| Modelo | AP | ROC AUC | Precisión@10% | Lift@10% | Brier |
| --- | ---: | ---: | ---: | ---: | ---: |
| XGBoost | 0.2938 | 0.6296 | 0.3269 | 1.5482 | 0.1615 |
| CatBoost | 0.2912 | 0.6279 | 0.3257 | 1.5423 | 0.1623 |
| Regresión logística | 0.2852 | 0.6216 | 0.3182 | 1.5069 | 0.1631 |
| Tasa general | 0.2111 | 0.5000 | 0.2111 | 1.0000 | 0.1671 |

XGBoost obtuvo el mejor resultado en estas métricas, pero CatBoost quedó prácticamente empatado usando ocho variables originales y evitando el *one-hot encoding* externo de 367 columnas. CatBoost transforma las categorías internamente.

## Reproducción

1. Desde la raíz del repositorio, instala las dependencias con `python -m pip install -r requirements.txt`.
2. Descarga el CSV desde la fuente oficial y guárdalo como `experiments/01_catboost_food_inspections/data/raw/food_inspections.csv` (ruta `data/raw/food_inspections.csv` relativa a este laboratorio).
3. Entra en `experiments/01_catboost_food_inspections/`, abre `catboost_food_inspections.ipynb` con Jupyter y ejecuta las celdas en orden.

## Limitaciones

- Se evaluó un conjunto acotado de variables y configuraciones, sin búsqueda exhaustiva de hiperparámetros ni historial del establecimiento.
- Se supone que el tipo de inspección y los demás predictores están disponibles antes de conocer el resultado; esto no se validó operacionalmente.
- El dataset se actualiza periódicamente, por lo que una descarga posterior puede cambiar los resultados.
- Los tiempos provienen de una sola ejecución local en CPU; los resultados son predictivos, no causales.
