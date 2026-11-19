# Riesgo de diabetes tipo 2 con Machine Learning (BRFSS 2025)

Proyecto del curso **Modelos y Simulación de Sistemas II**, Universidad de Antioquia.

Clasificación binaria para estimar si un adulto tiene diabetes diagnosticada a partir de once variables demográficas, antropométricas y de estilo de vida del *Behavioral Risk Factor Surveillance System* (BRFSS) 2025 de los CDC. La idea es un tamizaje inicial de bajo costo, que no requiere exámenes de laboratorio.

## Descripción del proyecto

A partir de once preguntas de la encuesta:

- **Demográficas:** sexo, edad (13 grupos), raza/etnia, nivel educativo, ingreso del hogar.
- **Antropométrica:** categoría de índice de masa corporal (IMC).
- **Estilo de vida:** actividad física, tabaquismo, consumo excesivo de alcohol, consumo de frutas y de vegetales.

se entrena un modelo que predice la respuesta a "¿Alguna vez un médico le ha dicho que tiene diabetes?".

Se implementan y comparan cinco modelos de aprendizaje supervisado:

- Regresión logística (modelo paramétrico)
- K vecinos más cercanos, KNN (modelo no paramétrico)
- Bosque aleatorio (ensamble de árboles)
- Perceptrón multicapa, MLP (red neuronal)
- Máquina de vectores de soporte con kernel RBF

Además, el proyecto incluye:

- tres codificaciones de las variables categóricas y una regla estadística para elegir entre ellas,
- manejo del desbalance de clases (ponderación de clases, submuestreo, SMOTE y SMOTEN),
- búsqueda de hiperparámetros con validación cruzada estratificada,
- intervalos de confianza en validación (distribución *t*) y en prueba (*bootstrap*),
- análisis individual de variables, selección de variables, PCA y UMAP.

## Estructura del repositorio

```
proyecto-modelos-II/
│
├── 01_entrenamiento_evaluacion.ipynb   # Sección 1: entrenamiento y evaluación de modelos
├── 02_reduccion_dimension.ipynb        # Sección 2: análisis de variables, PCA y UMAP
│
├── informe/
│   ├── entregable2_diabetes_brfss.tex  # Archivo principal del reporte (IEEE Transactions)
│   ├── entregable2_diabetes_brfss.pdf  # Reporte compilado
│   ├── secciones/                      # Una sección del reporte por archivo
│   │   ├── 00_resumen.tex
│   │   ├── 01_intro_datos_estado.tex
│   │   ├── 02_entrenamiento.tex
│   │   ├── 03_reduccion.tex
│   │   └── 04_conclusiones.tex
│   └── figuras/                        # Figuras en PDF que usa el reporte
│
├── figuras/                            # Figuras generadas por los notebooks (PDF y PNG)
├── resultados/                         # Tablas en CSV con todos los números del reporte
│
├── README.md
├── requirements.txt
└── .gitignore
```

## Descripción de carpetas

**Notebooks (raíz del repositorio).** Se ejecutan desde la raíz, en orden:

- `01_entrenamiento_evaluacion.ipynb`: carga y filtrado de los datos, análisis de faltantes, comparación de codificaciones, manejo del desbalance, búsqueda de hiperparámetros de los cinco modelos, verificación del orden de las variables ordinales, comparación con `LinearSVC` y resultados de entrenamiento, validación y prueba.
- `02_reduccion_dimension.ipynb`: análisis individual de variables (V de Cramér, información mutua y AUC univariado), selección de variables, PCA y UMAP sobre los dos mejores modelos, y comparación final en prueba.

**`informe/`.** Fuente LaTeX del reporte con la plantilla IEEE para Transactions. Cada sección está en su propio archivo dentro de `secciones/`, y el archivo principal solo las incluye con `\input`.

**`figuras/`.** Figuras generadas por los notebooks: en PDF para el reporte y en PNG para consulta rápida.

**`resultados/`.** Cada tabla del reporte guardada en CSV (mallas de hiperparámetros, pruebas estadísticas, resultados finales, curvas de PCA y UMAP, etc.). Sirve para revisar los números sin volver a ejecutar los notebooks.

**Datos.** No se suben al repositorio por su tamaño (ver `.gitignore`). El primer notebook los descarga y los reduce a `brfss2025_reducido.csv` en la raíz.

## Requisitos

- Python 3.10 o superior
- pip
- Jupyter Notebook, JupyterLab o VS Code con la extensión Jupyter (también funciona en Google Colab)

Verificar la instalación:

```bash
python --version
pip --version
```

## Instalación

Clonar el repositorio y entrar a la carpeta:

```bash
git clone https://github.com/Algaal/proyecto-modelos-II.git
cd proyecto-modelos-II
```

Crear y activar un entorno virtual:

```bash
python -m venv .venv

# Linux / macOS
source .venv/bin/activate
# Windows CMD
.venv\Scripts\activate
# Windows PowerShell
.venv\Scripts\Activate.ps1
```

Instalar las dependencias:

```bash
pip install -r requirements.txt
```

En Google Colab no hace falta este paso: la primera celda de cada notebook instala lo que falte.

## Datos

Se usa el archivo público del **BRFSS 2025** en formato SAS Transport (`LLCP2025XPT.zip`, 64 MB): 356.158 registros y 284 variables. La primera vez que se ejecuta, el notebook 1 lo descarga desde la página de los CDC y guarda solo las 12 columnas necesarias en `brfss2025_reducido.csv`.

Si la descarga automática falla, descargue el archivo manualmente desde la página de datos anuales del BRFSS (<https://www.cdc.gov/brfss/annual_data/annual_data.htm>, año 2025) y colóquelo, comprimido o descomprimido, en la raíz del repositorio; el notebook lo detecta.

- **Variable objetivo:** `DIABETE4`. Se conserva 1 (diabetes) frente a 3 (no); se excluyen diabetes gestacional, prediabetes y "no sabe/rehusó". Quedan 342.539 registros, con 15,13 % de positivos.
- **Predictoras:** `_SEX`, `_AGEG5YR`, `_RACEGR3`, `_EDUCAG`, `_INCOMG1`, `_BMI5CAT`, `_TOTINDA`, `_SMOKER3`, `_RFDRHV9`, `_FRTLT1A`, `_VEGLT1A` (variables calculadas por el CDC).

## Ejecución

1. Abrir `01_entrenamiento_evaluacion.ipynb` y ejecutar todas las celdas (*Run All*).
2. Luego abrir `02_reduccion_dimension.ipynb` y ejecutar todas las celdas.

Tiempos aproximados con 2 núcleos: unos 55 minutos el primero y entre 1 y 1,5 horas el segundo (la SVM con kernel RBF y UMAP son las etapas lentas). Todo usa la semilla 42, así que los resultados coinciden con los del reporte.

Para compilar el reporte, suba la carpeta `informe/` a Overleaf o compile `entregable2_diabetes_brfss.tex` con pdfLaTeX. Usa la clase `IEEEtran` y `babel` en español.

## Flujo de trabajo

```
Datos BRFSS 2025 (XPT)
   ↓
Filtrado de la variable objetivo y códigos de faltante → NaN
   ↓
Partición estratificada 80 % entrenamiento / 20 % prueba
   ↓
Submuestra de 20.000 registros de entrenamiento → validación cruzada de 5 pliegues
   ↓
Pipeline: codificación → imputación → balanceo → [PCA | UMAP] → modelo
   ↓
Mejores hiperparámetros → reajuste con el entrenamiento → evaluación única en prueba
```

Todo paso que aprende de los datos (imputación, codificación, balanceo, PCA y UMAP) va dentro de un *pipeline* de `scikit-learn`/`imbalanced-learn`. Así se ajusta solo con los datos de entrenamiento de cada pliegue y no hay fuga de información hacia validación o prueba.

## Decisiones metodológicas

- **Faltantes.** "No sabe" y "rehusó" (códigos 9 y 14) se tratan como faltantes. Según el modelo se conservan como categoría "no reportado" o se imputan con la moda global más un indicador de faltante, siempre calculada solo con entrenamiento. No se imputa por clase porque al predecir la clase es desconocida.
- **Codificación.** A: *one-hot* con "no reportado"; B: ordinales como enteros estandarizados; C: B más indicadores de faltante. Cada modelo usa A salvo que B o C la superen con diferencia significativa (prueba *t* pareada).
- **Desbalance.** Ponderación de clases en regresión logística, bosque aleatorio y SVM; submuestreo aleatorio en KNN y la red neuronal. SMOTE y SMOTEN se evaluaron y no mejoraron.
- **Costo computacional.** La búsqueda de hiperparámetros usa una submuestra estratificada de 20.000 registros, y la SVM final se ajusta con 40.000.

## Métricas

Con 15 % de positivos, un modelo que siempre dice "no" tendría 85 % de exactitud, así que la exactitud no se usa como criterio.

- **AUC-ROC:** métrica para elegir hiperparámetros, independiente del umbral.
- **Sensibilidad:** métrica clave de monitoreo. En tamizaje es preferible una falsa alarma a dejar pasar un caso.
- **Especificidad, precisión y F1:** miden el costo en falsas alarmas.

Las métricas de validación se reportan como media ± IC del 95 % sobre los 5 pliegues, y las de prueba con IC del 95 % por *bootstrap* (1.000 remuestreos).

## Organización del equipo

El trabajo se dividió en tres partes. Cada integrante sube la suya en una rama propia y abre un *pull request* hacia `main`:

| Parte | Archivos | Rama |
|---|---|---|
| 1. Introducción, datos y estado del arte | `informe/secciones/00_resumen.tex`, `01_intro_datos_estado.tex`, estructura base del repositorio | `main` (commit inicial) |
| 2. Entrenamiento y evaluación de modelos | `01_entrenamiento_evaluacion.ipynb`, `informe/secciones/02_entrenamiento.tex`, figuras y resultados de la sección 1 | `seccion1-entrenamiento` |
| 3. Reducción de dimensión y conclusiones | `02_reduccion_dimension.ipynb`, `informe/secciones/03_reduccion.tex`, `04_conclusiones.tex`, figuras y resultados de la sección 2 | `seccion2-reduccion` |

## Autores

- Alejandro Gallego Alarcón
- Andrés Eduardo Pabón Roldán
- Paula Andrea Guarín Guarín

Ingeniería de Sistemas, Universidad de Antioquia, Colombia.
