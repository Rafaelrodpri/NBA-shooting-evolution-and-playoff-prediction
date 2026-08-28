# Analítica del Baloncesto Moderno NBA: Evolución de los Perfiles de Tiro y Predicción de Playoffs (2010–2024)

Este repositorio contiene el análisis estadístico desarrollado para mi Trabajo de Fin de Grado en Estadística en la Universidad de Salamanca (USAL).

El estudio analiza 14 temporadas de la NBA (2010–2024) para responder a dos cuestiones principales: cómo ha cambiado la distribución del tiro en la liga y hasta qué punto el rendimiento en temporada regular permite anticipar el avance de los equipos en los playoffs.

---

## Resumen y Objetivos del Proyecto

En la última década, el análisis de datos ha transformado la toma de decisiones en el baloncesto profesional. Frente a la narrativa habitual de que la "revolución del triple" sigue creciendo de forma indefinida, este trabajo examina el fenómeno con datos empíricos:

1. **Selección espacial del tiro:** Comprobar si el tiro de media distancia ha desaparecido, cómo se han redistribuido los roles en la pista y localizar el punto exacto de inflexión estructural en el juego mediante regresión segmentada.
2. **Predicción de playoffs:** Evaluar 9 algoritmos de aprendizaje supervisado para estimar qué ronda alcanza cada equipo clasificado, utilizando exclusivamente métricas de la fase regular y aplicando validación cruzada anidada para asegurar validez y precisión.

---

## Marco Metodológico

### 1. Perfiles de Tiro y Análisis No Supervisado
* **Conjunto de datos:** 5.669 registros individuales jugador-temporada clasificados en 5 zonas de lanzamiento (*Aro, Cerca del aro, Media distancia, Triple y Tiro libre*).
* **Reducción de dimensionalidad:** Análisis de Componentes Principales (PCA) que recoge el 73,95% de la varianza total en dos ejes (PC1: volumen de tiro exterior; PC2: perfil clásico de media distancia vs. enfoque moderno).
* **Agrupamiento (K-Means con $k=4$):** Segmentación en arquetipos de juego (*Tiradores equilibrados, Especialistas de media distancia, Pívots interiores y Especialistas en el triple*).
* **Detección de puntos de quiebre:** Ajuste de regresiones segmentadas (*piecewise regression*) junto con el test de Davies ($p < 0,001$). El análisis demuestra que el crecimiento del triple y la caída de la media distancia sufrieron un cambio de ritmo estructural en la temporada **2021-22**, estabilizándose en torno al 30% de los lanzamientos totales en lugar de mantener una progresión lineal indefinida.

### 2. Modelado Predictivo y Validación Cruzada Anidada
* **Datos de modelado:** 208 temporadas de equipos clasificados a playoffs entre 2010 y 2023.
* **Selección de características:** Control de multicolinealidad mediante el Factor de Inflación de la Varianza (VIF), complementado con Análisis Discriminante Lineal (LDA) y Regresión Logística Multinomial para aislar las 11 variables más informativas.
* **Formulación de la variable objetivo:** Tras comprobar mediante el test de Razón de Verosimilitud (LRT) que no se cumplía el supuesto de razones proporcionales ($p = 0,031$), se optó por un enfoque multiclase corregido con puntuación esperada de ronda.
* **Estrategia de validación:** Esquema de Validación Cruzada Anidada (*Nested Cross-Validation*) de $5 \times 3$ pliegues (3.150 iteraciones totales de entrenamiento y ajuste de hiperparámetros) para evitar el sobreajuste en una muestra pequeña y de alta varianza.
* **Métrica principal:** F1-Score Macro ($F1_{\text{macro}}$), garantizando que el modelo no se sesgue hacia las eliminaciones en primera ronda, que representan el 50% de las observaciones.

---

## Comparativa y Rendimiento de los Modelos

La siguiente tabla resume los resultados de la validación cruzada anidada en la muestra histórica:

| Modelo | Media $F1_{\text{macro}}$ | Mediana $F1_{\text{macro}}$ | ROC-AUC (OvR) | MAE |
| :--- | :---: | :---: | :---: | :---: |
| **Random Forest** | **0,4296** | **0,4039** | **0,7089** | **0,6961** |
| **LightGBM** | 0,4221 | 0,3806 | 0,6978 | 0,7494 |
| **XGBoost** | 0,4166 | 0,4205 | 0,7032 | 0,6528 |
| **AdaBoost** | 0,4155 | 0,4149 | 0,6812 | 0,6578 |
| **Regresión Logística**| 0,3844 | 0,3527 | 0,6837 | 0,7882 |
| **SVM (SVC)** | 0,3725 | 0,3812 | 0,6988 | 0,7837 |
| **Naive Bayes** | 0,3681 | 0,3458 | 0,6626 | 0,7836 |
| **Árbol de Decisión** | 0,3616 | 0,3143 | 0,6047 | 0,8210 |
| **K-Vecinos Cercanos (KNN)** | 0,3182 | 0,3078 | 0,6366 | 0,7597 |

### Observaciones sobre el Aprendizaje en Muestras Pequeñas
En una base tabular de 208 observaciones, los algoritmos complejos de *boosting* (XGBoost, LightGBM) mostraron cierta inestabilidad por sobreajuste. El enfoque de *bagging* mediante Random Forest ofreció el mejor compromiso entre sesgo y varianza, logrando la mayor capacidad discriminativa y el error medio más bajo.

### Evaluación en Temporada Real (Holdout 2023-24)
Para comprobar el comportamiento del modelo frente a una temporada completa no vista durante el entrenamiento:
* **Random Forest acertó la ronda exacta de 11 de los 16 equipos participantes**, obteniendo una precisión ponderada de **0,6875**.
* Como referencia, superó el consenso público del *Playoff Challenge* de ESPN (10 aciertos exactos y 0,5625 de precisión), a pesar de que las personas contaban con información de lesiones y dinámicas de final de temporada de las que el modelo no disponía.

---

## Tecnologías Utilizadas

* **Lenguajes:** Python 3.10+, R (vía RStudio para la extracción inicial y procesamiento de jugadas brutas).
* **Librerías de análisis y modelado:** `pandas`, `numpy`, `scipy`, `statsmodels`, `scikit-learn`, `piecewise-regression`, `xgboost`, `lightgbm`.
* **Visualización:** `matplotlib`, `seaborn`.

---

## Estructura del Repositorio

```text
├── data/
├── notebooks/
│   └──codigo.ipynb  <- Cuaderno con el flujo completo de análisis
├── docs/
│   └── Memoria_TFG_Rafael_Rodriguez.pdf <- Memoria académica completa del TFG
└── README.md
