# ACP Interactivo — Análisis de Componentes Principales

Explorador visual e interactivo del Análisis de Componentes Principales (ACP) sobre datos sintéticos multivariados en tres dimensiones. Implementado como un archivo HTML autocontenido; no requiere instalación, servidor ni framework.

**Dependencia externa (CDN):**
[Plotly.js 2.27](https://cdnjs.cloudflare.com/ajax/libs/plotly.js/2.27.0/plotly.min.js) — visualizaciones 3D interactivas.

---

## ¿Qué es el ACP?

El **Análisis de Componentes Principales** es una técnica de reducción de dimensionalidad y análisis multivariado. Su objetivo es encontrar una nueva base ortonormal del espacio de variables —los *componentes principales*— tal que cada componente sucesivo capture la mayor varianza residual posible y sea ortogonal a los anteriores.

Formalmente, dado un conjunto de datos centrado **X** (n × p), el ACP resuelve la eigendescomposición de la matriz de covarianza muestral:

$$\mathbf{S} = \frac{1}{n-1}\mathbf{X}^\top\mathbf{X} = \mathbf{V}\,\mathbf{\Lambda}\,\mathbf{V}^\top$$

donde **Λ** = diag(λ₁, λ₂, …, λ_p) contiene los autovalores ordenados de mayor a menor y las columnas de **V** son los autovectores (direcciones de máxima varianza). Las puntuaciones (*scores*) de cada observación en el nuevo espacio se obtienen como **Z** = **X**_c **V**. La proporción de varianza explicada por el componente k es λ_k / Σλ_i.

---

## Implementación técnica

| Componente | Método |
|---|---|
| Generación normal multivariada | Box-Muller + factorización de Cholesky |
| Muestreo sin reemplazo | Reservoir sampling (Vitter, 1985) |
| Eigendescomposición | Algoritmo de Jacobi con rotaciones de Givens |
| Planos de componentes | Trazos `mesh3d` de Plotly (paralelogramos) |
| Visualización | Plotly.js — `scatter3d` + `mesh3d` + `scatter` 2D |

Todo el cálculo corre en el navegador (JavaScript puro), sin backend.

---

## Instructivo de uso

### 1. Configurar el dataset

En el panel izquierdo ajustá los parámetros de la distribución normal multivariada:

- **Medias (μ₁, μ₂, μ₃):** valor central de cada variable.
- **Desvíos estándar (σ₁, σ₂, σ₃):** dispersión de cada variable. Valores distintos generan variables con escalas diferentes, lo que influye en cuánto domina cada una en el ACP sobre covarianza.
- **Correlaciones (ρ):** los tres sliders controlan la correlación entre cada par de variables. El sistema valida en tiempo real que la matriz resultante sea definida positiva; si no lo es, aparece una advertencia y no se puede generar el dataset.

### 2. Elegir el tamaño de muestra

El slider **Tamaño de muestra** selecciona cuántas observaciones (entre 50 y 5.000) se extraen del dataset completo de 100.000 para calcular el ACP y renderizar los gráficos.

### 3. Generar el dataset

Presioná **Generar dataset y ACP**. El sistema:

1. Genera 100.000 observaciones con la distribución configurada.
2. Extrae una muestra aleatoria del tamaño indicado.
3. Calcula el ACP sobre esa muestra.
4. Renderiza todos los gráficos y tablas.

Para explorar la variabilidad muestral sin regenerar los datos, usá **Nueva muestra (mismo dataset)**, que extrae una muestra distinta del mismo dataset de 100.000 observaciones.

### 4. Visualización 3D — espacio original

Gráfico de dispersión rotable de la muestra en las coordenadas originales (X₁, X₂, X₃). Sobre este gráfico se pueden superponer los **planos de componentes principales** usando los checkboxes ubicados sobre el gráfico:

| Checkbox | Plano | Color |
|---|---|---|
| Plano CP₁–CP₂ | Primer plano principal (máxima varianza) | Azul |
| Plano CP₁–CP₃ | Plano del 1° y 3° componente | Violeta |
| Plano CP₂–CP₃ | Plano del 2° y 3° componente | Teal |

Cada plano está centrado en la media muestral y su extensión es proporcional a ±2.8√λ en cada dirección, reflejando la varianza capturada por ese componente.

### 5. Visualización 3D — espacio de componentes

Gráfico de dispersión rotable de las mismas observaciones proyectadas sobre los tres componentes principales (CP₁, CP₂, CP₃). Los puntos se colorean por su score en CP₁ (escala Viridis), lo que permite rastrear visualmente la correspondencia entre los dos espacios.

### 6. Resultados del modelo ACP

Debajo de los gráficos se presenta la especificación completa del modelo estimado:

- **Varianza explicada:** barras con el porcentaje individual y acumulado de cada componente.
- **Matriz de covarianza (S)** y **matriz de correlación (R)** muestrales.
- **Tabla de autovalores:** λ₁, λ₂, λ₃, proporción explicada y acumulada.
- **Matriz de autovectores (loadings):** coeficientes de cada variable en cada componente y comunalidades.

### 7. Biplot CP₁ vs CP₂

Proyección 2D de los scores en el plano de los dos primeros componentes, con vectores de carga superpuestos. La dirección y longitud de cada vector indica la contribución de cada variable original (X₁, X₂, X₃) al plano CP₁–CP₂.

---

## Referencias

> Pearson, K. (1901). On lines and planes of closest fit to systems of points in space. *Philosophical Magazine*, 2(11), 559–572.

> Hotelling, H. (1933). Analysis of a complex of statistical variables into principal components. *Journal of Educational Psychology*, 24(6), 417–441.

> Vitter, J. S. (1985). Random sampling with a reservoir. *ACM Transactions on Mathematical Software*, 11(1), 37–57.
