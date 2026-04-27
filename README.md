# GESTION-DE-PROYECTOS
CLV PARA VENTAS EN LINEA

> Cálculo del \*\*Customer Lifetime Value (CLV)\*\* mediante un pipeline completo que incluye limpieza de datos, análisis exploratorio (EDA), segmentación \*\*RFM\*\* y modelos probabilísticos \*\*BG/NBD + Gamma-Gamma\*\*, aplicados al dataset \*Online Retail\* de ventas en línea.

!\[Python](https://img.shields.io/badge/Python-3.8%2B-blue)
!\[pandas](https://img.shields.io/badge/pandas-✓-green)
!\[lifetimes](https://img.shields.io/badge/lifetimes-✓-orange)
!\[sklearn](https://img.shields.io/badge/scikit--learn-✓-red)
!\[Jupyter](https://img.shields.io/badge/Jupyter-Notebook-orange)
[!\[Open In Colab](https://colab.research.google.com/assets/colab-badge.svg)](https://colab.research.google.com/github/andrearoman1/GESTION-DE-PROYECTOS/blob/main/Untitled0.ipynb)

\---

## 📋 Tabla de Contenidos

1. [Descripción del Proyecto](#-descripción-del-proyecto)
2. [Contexto Empresarial](#-contexto-empresarial)
3. [Requisitos e Instalación](#-requisitos-e-instalación)
4. [Configuración Inicial](#-configuración-inicial)
5. [Parámetros Modificables](#-parámetros-modificables)
6. [Ejemplos de Uso](#-ejemplos-de-uso)
7. [Estructura del Proyecto](#-estructura-del-proyecto)
8. [Pipeline del Análisis](#-pipeline-del-análisis)
9. [Resultados Esperados](#-resultados-esperados)
10. [Tecnologías Utilizadas](#-tecnologías-utilizadas)
11. [Autor](#-autor)

\---

## 📌 Descripción del Proyecto

Este proyecto implementa un pipeline completo de **Customer Lifetime Value (CLV)** — el valor económico total que un cliente genera a lo largo de su relación con una empresa — aplicado a datos reales de un negocio de ventas en línea (*Online Retail* dataset).

El pipeline cubre cinco etapas principales:

1. **Limpieza y preparación de datos** — tratamiento de nulos, valores negativos, outliers y creación de columnas derivadas como `Revenue` y `Revenue Total`.
2. **Análisis Exploratorio (EDA)** — estadísticas descriptivas y visualización de distribuciones con boxplots.
3. **Segmentación RFM** — clasificación de clientes en Champions, Loyal Customers, Potential Loyalists y At Risk.
4. **Modelos probabilísticos BG/NBD + Gamma-Gamma** — predicción del número de compras futuras y del gasto promedio esperado por cliente.
5. **Evaluación del modelo** — comparación entre CLV real y CLV predicho usando métricas MAE, RMSE y R², incluyendo análisis del Top 20% de clientes más valiosos.

\---

## 🏢 Contexto Empresarial

En el comercio electrónico, adquirir un nuevo cliente puede costar entre **5 y 7 veces más** que retener uno existente. Conocer el CLV permite a las empresas de retail en línea tomar decisiones estratégicas como:

* Identificar a los **Champions** (clientes de mayor valor) y enfocar campañas de fidelización exclusivas.
* Detectar clientes **At Risk** antes de que abandonen, para activar estrategias de reactivación.
* Optimizar el **presupuesto de marketing** concentrándolo en los segmentos con mayor retorno esperado.
* Proyectar **ingresos futuros** con base en el comportamiento histórico de compra.
* Personalizar ofertas según el valor de vida proyectado de cada segmento.

En este proyecto, el modelo identifica que el **Top 20% de clientes predichos por CLV concentra la mayor proporción del ingreso real**, lo que valida la utilidad del modelo para la toma de decisiones basada en datos.

\---

## ⚙️ Requisitos e Instalación

### Requisitos previos

* Python **3.8 o superior**
* pip (gestor de paquetes de Python)
* Jupyter Notebook o Google Colab
* Archivo `Online Retail.xlsx` (dataset de entrada)

### Paso 1 — Clonar el repositorio

```bash
git clone https://github.com/andrearoman1/GESTION-DE-PROYECTOS.git
cd GESTION-DE-PROYECTOS
```

### Paso 2 — (Opcional) Crear un entorno virtual

```bash
python -m venv venv
source venv/bin/activate        # En Mac/Linux
venv\\Scripts\\activate           # En Windows
```

### Paso 3 — Instalar dependencias

```bash
pip install pandas numpy matplotlib seaborn scikit-learn lifetimes openpyxl jupyter
```

O instalar cada librería individualmente:

```bash
pip install pandas
pip install numpy
pip install matplotlib
pip install seaborn
pip install scikit-learn
pip install lifetimes
pip install openpyxl     # necesario para leer archivos .xlsx
pip install jupyter
```

### Paso 4 — Colocar el dataset

Coloca el archivo `Online Retail.xlsx` en la misma carpeta del notebook antes de ejecutarlo.

### Paso 5 — Abrir el notebook

```bash
jupyter notebook Untitled0.ipynb
```

> 💡 También puedes abrir el archivo directamente en \*\*Google Colab\*\* usando el botón de la parte superior sin instalar nada localmente.

\---

## 🔧 Configuración Inicial

El proyecto carga sus datos desde el archivo `Online Retail.xlsx`. Las primeras celdas del notebook realizan automáticamente la carga y limpieza:

```python
import pandas as pd
df = pd.read\_excel('Online Retail.xlsx')
```

**Limpieza automática aplicada al cargar:**

* Los valores nulos en `CustomerID` se rellenan con `'00001'`.
* Los valores nulos en `Description` se rellenan con `'Undescribed'`.
* Las columnas `Quantity` y `UnitPrice` se convierten a valores absolutos.
* Se eliminan filas donde `UnitPrice == 0`.
* Se eliminan los dos outliers más grandes de `Quantity`.
* Se genera la columna `costo unitario` con un factor aleatorio entre 0.3 y 0.7 por producto.
* Se calculan las columnas derivadas `Revenue` y `Revenue Total`.

**Columnas requeridas en el dataset de entrada:**

|Columna|Tipo|Descripción|
|-|-|-|
|`CustomerID`|float|Identificador único del cliente|
|`InvoiceNo`|str|Número de factura/transacción|
|`InvoiceDate`|datetime|Fecha y hora de la compra|
|`Description`|str|Descripción del producto|
|`Quantity`|int|Cantidad de unidades compradas|
|`UnitPrice`|float|Precio unitario del producto|

\---

## 🎛️ Parámetros Modificables

Estos son los parámetros clave que puedes ajustar dentro del notebook para adaptar el modelo a distintos escenarios:

|Parámetro|Variable en código|Valor por defecto|Descripción|
|-|-|-|-|
|Fecha de referencia RFM|`reference\_date`|`'2011-12-10'`|Fecha base para calcular la recencia de cada cliente. Cambiarla ajusta qué tan "recientes" se consideran las compras.|
|Fecha de corte train/test|`split\_date`|`'2011-09-01'`|Divide los datos en ventana de observación (train) y ventana de predicción (test).|
|Inicio de observación|`observation\_start\_date`|`date(2010, 12, 1)`|Inicio del periodo histórico usado para entrenar los modelos.|
|Fin de observación|`observation\_end\_date`|`date(2011, 9, 30)`|Fin del periodo histórico. Determina la antigüedad `T` de los clientes.|
|Horizonte de predicción|`t` en BG/NBD|`120` (días)|Número de días futuros para los que el modelo BG/NBD predice compras.|
|Tiempo CLV (Gamma-Gamma)|`time`|`4` (meses)|Meses a futuro para los que se calcula el CLV con `customer\_lifetime\_value`.|
|Tasa de descuento|`discount\_rate`|`0.01`|Tasa mensual para traer a valor presente los ingresos futuros. Mayor tasa = menor CLV.|
|Penalización BG/NBD|`penalizer\_coef` (bgf)|`0.01`|Regularización para evitar sobreajuste en el modelo BG/NBD.|
|Penalización Gamma-Gamma|`penalizer\_coef` (ggf)|`0.01`|Regularización para el modelo Gamma-Gamma.|
|Semilla aleatoria|`np.random.seed`|`42`|Controla la reproducibilidad del factor de costo unitario generado por producto.|

\---

## 💡 Ejemplos de Uso

### Ejemplo 1 — Calcular segmentos RFM de los clientes

```python
# Calcular puntuaciones RFM (escala 1-5 por cuantiles)
rfm\_f\['R\_Score'] = rfm\_f\['Recency'].rank(method='first', pct=True).apply(lambda x: int(np.ceil((1 - x) \* 5)))
rfm\_f\['F\_Score'] = rfm\_f\['Frequency'].rank(method='first', pct=True).apply(lambda x: int(np.ceil(x \* 5)))
rfm\_f\['M\_Score'] = rfm\_f\['MonetaryValue'].rank(method='first', pct=True).apply(lambda x: int(np.ceil(x \* 5)))

rfm\_f\['RFM\_Score'] = rfm\_f\['R\_Score'] + rfm\_f\['F\_Score'] + rfm\_f\['M\_Score']

# Asignar segmento según puntuación total
def assign\_rfm\_segment(score):
    if score >= 13:
        return 'Champions'
    elif score >= 10:
        return 'Loyal Customers'
    elif score >= 7:
        return 'Potential Loyalists'
    else:
        return 'At Risk'

rfm\_f\['RFM\_Segment'] = rfm\_f\['RFM\_Score'].apply(assign\_rfm\_segment)
print(rfm\_f\['RFM\_Segment'].value\_counts())
# Resultado esperado:
# At Risk                1298
# Potential Loyalists    1145
# Loyal Customers         995
# Champions               933
```

\---

### Ejemplo 2 — Entrenar el modelo BG/NBD y predecir compras futuras

```python
from lifetimes.utils import summary\_data\_from\_transaction\_data
from lifetimes import BetaGeoFitter

# Preparar datos en formato BG/NBD
summary\_bgnbd = summary\_data\_from\_transaction\_data(
    train\_data,
    customer\_id\_col='CustomerID',
    datetime\_col='InvoiceDate',
    monetary\_value\_col='Revenue Total',
    observation\_period\_end=observation\_end\_date
)

# Entrenar modelo
bgf = BetaGeoFitter(penalizer\_coef=0.01)
bgf.fit(summary\_bgnbd\['frequency'], summary\_bgnbd\['recency'], summary\_bgnbd\['T'])

# Predecir compras en los próximos 120 días
summary\_bgnbd\['predicted\_purchases'] = bgf.conditional\_expected\_number\_of\_purchases\_up\_to\_time(
    t=120,
    frequency=summary\_bgnbd\['frequency'],
    recency=summary\_bgnbd\['recency'],
    T=summary\_bgnbd\['T']
)
print(summary\_bgnbd\[\['frequency', 'recency', 'T', 'predicted\_purchases']].head())
```

\---

### Ejemplo 3 — Calcular CLV predicho con Gamma-Gamma y evaluar el modelo

```python
from lifetimes import GammaGammaFitter
from sklearn.metrics import mean\_absolute\_error, mean\_squared\_error, r2\_score
import numpy as np

# Filtrar solo clientes con compras repetidas y valor monetario positivo
summary\_gg = summary\_bgnbd\[(summary\_bgnbd\['frequency'] > 0) \& (summary\_bgnbd\['monetary\_value'] > 0)]

# Entrenar modelo Gamma-Gamma
ggf = GammaGammaFitter(penalizer\_coef=0.01)
ggf.fit(summary\_gg\['frequency'], summary\_gg\['monetary\_value'])

# Calcular CLV a 4 meses con tasa de descuento del 1% mensual
clv\_estimates\['CLV\_predicho'] = ggf.customer\_lifetime\_value(
    bgf,
    clv\_estimates\['frequency'],
    clv\_estimates\['recency'],
    clv\_estimates\['T'],
    clv\_estimates\['monetary\_value'],
    time=4,           # meses
    discount\_rate=0.01
)

# Evaluar el modelo
y\_true = evaluation\_df\['CLV\_real']
y\_pred = evaluation\_df\['CLV\_predicho']

print(f"MAE:  {mean\_absolute\_error(y\_true, y\_pred):.2f}")
print(f"RMSE: {np.sqrt(mean\_squared\_error(y\_true, y\_pred)):.2f}")
print(f"R²:   {r2\_score(y\_true, y\_pred):.2f}")

# Análisis Top 20% de clientes más valiosos
evaluation\_sorted = evaluation\_df.sort\_values('CLV\_predicho', ascending=False)
top\_20 = evaluation\_sorted.head(int(len(evaluation\_sorted) \* 0.2))
pct\_capturado = (top\_20\['CLV\_real'].sum() / evaluation\_df\['CLV\_real'].sum()) \* 100
print(f"El Top 20% de clientes captura el {pct\_capturado:.2f}% del CLV real total")
```

\---

## 📁 Estructura del Proyecto

```
GESTION-DE-PROYECTOS/
│
├── Untitled0.ipynb       # Notebook principal con el pipeline completo
├── README.md             # Documentación del proyecto
└── prueba                # Archivo de pruebas auxiliar
```

> \*\*Nota:\*\* El dataset `Online Retail.xlsx` debe colocarse en la raíz del proyecto antes de ejecutar el notebook.

\---

## 🔄 Pipeline del Análisis

```
Carga de datos (Online Retail.xlsx)
        ↓
Limpieza y EDA
(nulos, outliers, Revenue, Revenue Total)
        ↓
Segmentación RFM
(Recency, Frequency, Monetary → Champions / Loyal / Potential / At Risk)
        ↓
División train/test
(Observación: dic 2010 – sep 2011 | Predicción: oct – dic 2011)
        ↓
Modelo BG/NBD
(predice número de compras futuras por cliente)
        ↓
Modelo Gamma-Gamma
(predice gasto promedio por compra)
        ↓
CLV Estimado
(BG/NBD × Gamma-Gamma con tasa de descuento)
        ↓
Evaluación
(MAE, RMSE, R², Top 20% análisis de negocio)
```

\---

## 📈 Resultados Esperados

Al ejecutar el notebook completo obtendrás:

* **Tabla RFM** con segmentación de los \~4,371 clientes en 4 grupos (At Risk: 1298, Potential Loyalists: 1145, Loyal Customers: 995, Champions: 933).
* **Gráfica de distribución de segmentos** (bar plot con colores por segmento).
* **Tabla CLV predicho** por cliente con el valor estimado a 4 meses.
* **Gráfico de dispersión** CLV Real vs CLV Predicho con línea de predicción perfecta (y=x).
* **Métricas de evaluación**: MAE, RMSE y R² del modelo.
* **Análisis de negocio**: porcentaje del ingreso real capturado por el Top 20% de clientes predichos.

\---

## 🛠️ Tecnologías Utilizadas

|Herramienta|Versión recomendada|Uso principal|
|-|-|-|
|Python|3.8+|Lenguaje base|
|pandas|1.5+|Manipulación y limpieza de datos|
|numpy|1.23+|Operaciones numéricas y generación de factores aleatorios|
|matplotlib|3.5+|Visualización de boxplots y gráficos|
|seaborn|0.12+|Visualización estadística (barplots, scatterplots)|
|lifetimes|0.11+|Modelos BG/NBD y Gamma-Gamma|
|scikit-learn|1.0+|Métricas de evaluación (MAE, RMSE, R²)|
|openpyxl|3.0+|Lectura de archivos `.xlsx`|
|Jupyter|6.0+|Entorno de desarrollo interactivo|

\---

## 👤 Autor

**Andrea Román**
Estudiante — Ingeniería en Gestion Empresarial / TecNM

\---

*Proyecto desarrollado para la asignatura: **Herramientas para la Toma de Decisiones Basadas en Datos** — Unidad 4*

