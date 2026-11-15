# LIA_IDL03: Lógica para la Inteligencia Artificial - Indicador de Logro 3

## Clasificación Imbalanceada de Riesgo Crediticio con Machine Learning y Deep Learning
**Instituto Continental — Carrera Profesional de Ciencia de Datos e Inteligencia Artificial**

---

### 📌 Información Institucional
* **Estudiante:** **Christian Henry Casso Quispe** (Código: `i2513568`)
* **Docente:** **Max Walter Alonzo Huamán**
* **Unidad Didáctica:** Lógica para la Inteligencia Artificial
* **Indicador de Logro 3:** *Implementa la lógica de algoritmos de IA en función de soluciones prácticas en proyectos de ciencia de datos.*
* **Carrera:** Ciencia de Datos e Inteligencia Artificial
* **Ubicación:** Huancayo, Perú

---

### 📊 Contexto de Negocio
Evaluación del riesgo crediticio de una cartera bancaria de **50,591 clientes** entre 201701 y 201803 con un total de 10 variables predictivas preseleccionadas de información interna y del buró financiero (RCC). El objetivo es predecir el evento de **Default** a 12 meses (`FLG_CLI_DEF60`), definido como un atraso continuo $\ge 60$ días.

* **Tasa Base de Default:** 10.70% (5,415 clientes en mora vs. 45,176 clientes cumplidores).
* **Ratio de Desbalance:** 8.34 a 1 (`scale_pos_weight = 8.3426`).
* **Hallazgo Clave de Políticas:**
  * Clientes **SIN atraso** en 12M (`MAX_ATR_I_12M == 0`): Tasa de default de **2.34%** (17.43% de la cartera).
  * Clientes **CON atraso** en 12M (`MAX_ATR_I_12M > 0`): Tasa de default de **12.47%** (82.57% de la cartera).
  * **Riesgo Relativo:** Los clientes con atrasos son **5.3 veces más propensos al default**.

---

### 🧠 Modelos Implementados
1. **XGBoost (Extreme Gradient Boosting):**
   * Ensamble de árboles aditivos con regularización $L_1$ y $L_2$.
   * Ponderación analítica de clases mediante `scale_pos_weight = 8.343`.
   * Early stopping optimizando el área bajo la curva Precision-Recall (`aucpr`).
   * Explicabilidad y ranking de importancia por Ganancia de Información (*Gain*).

2. **Tabular ResNet (Deep Learning en PyTorch):**
   * Arquitectura Feedforward con bloques residuales (*Skip Connections*) para datos tabulares.
   * Normalización por lotes (`nn.BatchNorm1d`) y regularización `Dropout(0.25)`.
   * Función de costo ponderada numéricamente estable: `nn.BCEWithLogitsLoss(pos_weight=8.343)`.
   * Optimizador `AdamW` y scheduler adaptativo `ReduceLROnPlateau`.

---

### 📈 Tabla Comparativa de Rendimiento (Test Set: 10,119 clientes)

| Indicador de Evaluación | Algoritmo 1: XGBoost | Algoritmo 2: Red Neuronal Tabular | Criterio de Selección |
| :--- | :---: | :---: | :--- |
| **ROC-AUC** | **0.8297** | 0.8269 | Capacidad de ordenamiento global (+0.28% superior en XGBoost) |
| **Coeficiente de GINI ($2\cdot\text{AUC}-1$)** | **0.6593** | 0.6538 | Métrica regulatoria estándar para carteras de crédito |
| **PR-AUC (Average Precision)** | **0.5085** | 0.5038 | Métrica de oro en desbalance frente a tasa base del 10.7% |
| **Estadístico KS (Separación Máxima)** | **49.88%** | 49.32% | Separación entre acumulada de clientes buenos y malos |
| **Punto de Corte KS (Score)** | 0.443 | 0.419 | Umbral empírico de máxima discriminación |
| **Brier Score (Calibración)** | **0.1608** | 0.1635 | Calibración probabilística (menor es mejor) |
| **Recall @ Umbral Estándar 0.50** | 68.79% | **72.15%** | Captura inicial de clientes en default |
| **Recall @ Umbral Óptimo de Costos ($t=0.41$)** | **78.49%** | 76.20% | Captura considerando pérdida por default ($C_{FN} \gg C_{FP}$) |
| **Interpretabilidad / Gobernanza** | **Alta (Gain / SHAP)** | Baja (Caja Negra) | Auditabilidad regulatoria y de comités de riesgo |

**Modelo Seleccionado:** **XGBoost** por superioridad en todas las métricas de Basilea, robustez natural ante datos tabulares y plena transparencia explicativa.

---

### 📂 Estructura del Repositorio
```
├── .gitignore                                      # Archivos y cachés ignorados por Git
├── README.md                                       # Documentación ejecutiva del proyecto
├── Logica_para_la_inteligencia_artificial_idl03.ipynb  # Cuaderno Jupyter interactivo y ejecutado
├── INFORME_EJECUTIVO_INSTITUTO_CONTINENTAL.pdf     # Informe formal en PDF según plantilla institucional
├── INFORME_EJECUTIVO_INSTITUTO_CONTINENTAL.tex     # Código fuente LaTeX del informe formal
├── base10.csv                                      # Base de datos con las 10 variables y target (50,591 filas)
├── DICCIONARIO.xlsx                                # Diccionario oficial de campos y definiciones
└── latex_report/                                   # Recursos gráficos y figuras de soporte para LaTeX
    └── images/                                     # Gráficos de alta resolución generados
```

---

### 🚀 Requisitos e Instalación
Para ejecutar el cuaderno localmente:
```bash
git clone https://github.com/i2513568-ALESTI/LIA_IDL03.git
cd LIA_IDL03
pip install pandas numpy scikit-learn xgboost torch matplotlib seaborn openpyxl
```
Luego abrir y ejecutar:
```bash
jupyter notebook Logica_para_la_inteligencia_artificial_idl03.ipynb
```
