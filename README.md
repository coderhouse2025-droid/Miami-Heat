<img width="1280" height="720" alt="FOTO DE LOGO(1)" src="https://github.com/user-attachments/assets/523216ed-fa07-4f99-8204-393d3ec142f7" />


# 🏀 Miami Heat — Radiografía de una Cultura (2019–2024)

> 📊 Análisis de datos y modelado predictivo del desempeño del Miami Heat en cinco temporadas críticas de la NBA.

---

## 📋 Descripción del proyecto

Este proyecto realiza un análisis exploratorio (EDA) y estadístico del Miami Heat desde la temporada 2019–2020 hasta 2023–2024. A partir de 392 partidos de la fase Regular y 71 partidos de la fase de Playoffs, estadísticas avanzadas y métricas de rendimiento individual, se exploran los patrones que definen la identidad competitiva de la franquicia.

El caso de negocio que guía el análisis es el siguiente: **¿qué variables estadísticas predicen mejor el rendimiento del Heat, y cómo puede la franquicia optimizar decisiones tácticas y de plantel usando datos históricos?** Este enfoque simula el rol de un analista interno que provee inteligencia accionable a un cuerpo técnico.

Los datos provienen de **Basketball Reference** y **Universo Básquet**, procesados con Python y almacenados en SQLite.

---

## 🎯 Objetivos

- 🔍 Identificar las fortalezas y debilidades del equipo a lo largo del ciclo.
- ✈️ Cuantificar el impacto del factor fatiga (distancia viajada) en los resultados.
- 🆚 Comparar el rendimiento en temporada regular vs. playoffs.
- ⭐ Evaluar el impacto del jugador franquicia en el ciclo analizado.
- 🏟️ Medir la fortaleza del Kaseya Center como ventaja de local.

---

## 🗃️ Dataset: origen, estado y decisiones de transformación

### Fuente

| Fuente | Tipo de datos | Formato |
|---|---|---|
| Basketball Reference | Estadísticas por partido y jugador | Scraping / CSV |
| Universo Básquet | Datos complementarios de contexto | Descarga manual |

### Estado del dataset crudo

El dataset original presentaba los siguientes problemas que requirieron intervención:

**Inconsistencias de formato:** los nombres de columnas no eran uniformes entre las hojas del archivo `.xlsx`. Algunas temporadas usaban abreviaturas distintas para las mismas métricas (e.g., `PTS` vs `points`), lo que obligó a una normalización de encabezados antes de cualquier análisis.

**Valores nulos y ausentes:** las temporadas de playoffs tenían registros incompletos para partidos donde algunos jugadores no participaron por lesión. Decidi**no imputar** estos valores con la media o mediana, sino tratarlos como ausencia real de datos, ya que imputar estadísticas deportivas introduce un sesgo semántico: un jugador que no jugó no tiene "rendimiento promedio", tiene cero impacto.

**Datos mixtos en una misma columna:** la columna de resultado del partido combinaba score propio y rival en un string (`"112-98"`). Fue necesario separar y castear a entero para poder calcular diferencias de puntos (point differential), variable clave para modelos posteriores.

**Dataset fragmentado:** los datos de temporada regular y playoffs estaban en hojas separadas. Se consolidaron en un único DataFrame con una columna categórica `fase` (Regular / Playoffs) para poder comparar ambos contextos sin perder la trazabilidad.

**Decisión sobre el período analizado:** eligi 2019–2024 porque cubre el ciclo completo de Jimmy Butler como franquicia, incluyendo las dos Finales (2020 en burbuja y 2023). Esto permite medir su impacto con datos suficientes y evitar el ruido de períodos donde el plantel era estructuralmente diferente.

---

## 🛠️ Stack tecnológico: justificación por caso de negocio

La elección de cada tecnología no fue arbitraria. Cada herramienta fue seleccionada en función de los requerimientos concretos del caso de análisis deportivo.

### 🐍 Python 3

**Por qué:** es el estándar de facto en Data Science, con el ecosistema de librerías más maduro para análisis estadístico y ML. Para un proyecto de análisis deportivo donde la prioridad es la velocidad de exploración y la reproducibilidad, Python supera a R en integración con bases de datos y a herramientas no-code en flexibilidad.

**Alternativa descartada:** R ofrece ventajas en estadística pura, pero su integración con SQLite y el ecosistema de ML (scikit-learn) es menos directa. Para un pipeline end-to-end, Python es más eficiente.

### 🐼 Pandas

**Por qué:** el dataset tiene estructura tabular con múltiples hojas, joins entre temporadas y operaciones de group-by frecuentes (por jugador, por temporada, por fase). Pandas es la herramienta más directa para este tipo de manipulación. Su integración nativa con Excel y SQLite simplificó la carga de datos considerablemente.

**Alternativa descartada:** Polars es más rápido en datasets grandes, pero con ~400 registros el cuello de botella no es el procesamiento sino la legibilidad del código. Pandas fue la elección correcta para este volumen.

### 📈 Matplotlib / Seaborn

**Por qué:** el objetivo de las visualizaciones en este proyecto es comunicar insights a un potencial cuerpo técnico o dirección deportiva, no construir dashboards interactivos. Matplotlib ofrece control total sobre los gráficos estáticos; Seaborn añade una capa semántica (e.g., `hue` por fase) que facilita comparaciones entre grupos sin código adicional.

**Alternativa descartada:** Plotly permite interactividad, pero para un informe en PDF o notebook estático, el overhead de configuración no justificaba el beneficio. Si el proyecto evolucionara hacia un dashboard en producción, Plotly o Dash serían la siguiente elección.

### 🗄️ SQLite3

**Por qué:** el caso de negocio implica cruzar datos de múltiples fuentes (partidos, jugadores, temporadas) con queries relacionales. SQLite permite estructurar esas relaciones sin necesidad de levantar un servidor de base de datos. Para un análisis de ~400 partidos con 3-4 tablas, SQLite es la solución con menor fricción operacional.

**Alternativa descartada:** PostgreSQL o MySQL serían overkill para este volumen y contexto. CSV plano habría funcionado, pero pierde la capacidad de hacer JOINs declarativos y mantener integridad referencial.

### ☁️ Google Colab + Google Drive

**Por qué:** el entorno compartido de Colab permitió desarrollar el proyecto de forma colaborativa sin depender de configuración local. Google Drive actúa como capa de persistencia del dataset, lo que facilita la reproducibilidad: cualquier revisor puede ejecutar el notebook apuntando al mismo archivo en Drive.

**Limitación conocida:** Colab tiene límites de RAM y tiempo de sesión. Si el dataset escalara significativamente, sería necesario migrar a un entorno con recursos garantizados (e.g., instancia en GCP o AWS).

---

## 🔬 Decisiones metodológicas por eje de análisis

### ✈️ Eje 1 — Factor Fatiga (Regresión Logística)

**Problema de negocio:** ¿afecta la fatiga acumulada por viajes la probabilidad de ganar? Esta es una pregunta táctica real: los equipos de la NBA planifican rotaciones y descansos considerando los back-to-backs y los viajes largos.

**Por qué regresión logística:** la variable target es binaria (victoria / derrota), lo que hace de la regresión logística el modelo baseline natural. Es interpretable, no requiere grandes volúmenes de datos y produce probabilidades calibradas. En un contexto donde el resultado debe ser explicable a un cuerpo técnico no técnico, la interpretabilidad supera a la precisión marginal de modelos más complejos.

**Por qué no un árbol de decisión aquí:** con una sola variable predictora (distancia viajada), un árbol produciría overfitting o resultados idénticos a la regresión. La logística es más honesta con la incertidumbre.

**Resultado y su interpretación:** AUC de 0.55–0.65 indica que la fatiga tiene efecto real pero pequeño. Esto es coherente con la literatura deportiva: el Heat, conocido por su cultura de esfuerzo, parece más resistente a la fatiga que el promedio de la liga. El hallazgo es accionable: no ignorar el factor, pero no priorizarlo sobre otras variables.

---

### 🧬 Eje 2 — ADN de Playoffs (K-Means Clustering)

**Problema de negocio:** ¿existe un perfil estadístico diferenciado entre el Heat de temporada regular y el de playoffs? Identificarlo permite saber si el equipo "cambia de modo" o si su rendimiento es consistente.

**Por qué K-Means:** el objetivo es segmentación no supervisada: no tenemos una etiqueta previa de "perfil regular" vs "perfil playoffs", queremos que el algoritmo la descubra. K-Means con K=2 es la elección más directa para este caso, donde la hipótesis inicial tiene exactamente dos grupos naturales.

**Por qué no DBSCAN o clustering jerárquico:** con ~400 registros bien distribuidos y una hipótesis de dos clusters claros, K-Means converge rápido y es más interpretable. DBSCAN sería útil si buscáramos anomalías o clusters de forma irregular.

**Resultado y su interpretación:** ARI = 1.0 (separación perfecta) valida la hipótesis: el Heat de playoffs es estadísticamente distinguible del de temporada regular. Esto confirma que el equipo tiene una identidad diferenciada en el postemporada, un activo competitivo real.

---

### ⭐ Eje 3 — Impacto del Jugador Franquicia (Random Forest Regressor)

**Problema de negocio:** ¿cuánto depende el rendimiento del equipo de Jimmy Butler? Este análisis tiene implicaciones directas en decisiones de gestión: renovación de contrato, manejo de minutos, construcción de plantel.

**Por qué Random Forest:** a diferencia de los ejes anteriores, aquí la variable target es continua (e.g., point differential o win shares del equipo) y los predictores son múltiples estadísticas individuales correlacionadas entre sí. Random Forest maneja bien la multicolinealidad y ofrece feature importance nativa, que en este contexto equivale a "cuánto pesa este jugador en los resultados del equipo".

**Por qué no regresión lineal:** la relación entre estadísticas individuales y resultado de equipo no es necesariamente lineal. Butler puede tener un impacto desproporcionado en situaciones de alta presión (clutch) que una regresión lineal no captura.

---

### 🏟️ Eje 4 — La Mística del Kaseya Center (Análisis estadístico descriptivo + test de hipótesis)

**Problema de negocio:** ¿sigue siendo el Kaseya Center una ventaja real o es un mito? La respuesta impacta en cómo el equipo planifica su calendario y maneja el factor local frente a playoffs en casa.

**Método:** comparación de medias (porcentaje de victorias local vs visitante) con test estadístico para validar significancia. Eligi este enfoque antes que un modelo predictivo porque la pregunta es descriptiva, no predictiva.

---

## 📁 Archivos del repositorio

| Archivo | Descripción |
|---|---|
| 📓 `Miami_Heat (Final).ipynb` | Notebook principal con el pipeline completo |
| 📝 `Miami_Heat.ipynb` | Versión de desarrollo / iteraciones previas |
| 📄 `Radiografia-de-una-Cultura.pdf` | Informe narrativo del proyecto |
| 🤖 `Reporte Claude - Informe Miami Heat.pdf` | Informe generado con asistencia de IA |
| 🗺️ `Roadmap_Miami_Heat_ML.pdf` | Hoja de ruta para extensión con ML |
| 📋 `README.md` | Este archivo |

---

## 🔄 Pipeline completo del proyecto

```
Fuentes externas (Basketball Reference / Universo Básquet)
        ↓
  Carga y consolidación (Pandas + Excel)
        ↓
  Limpieza y transformación (normalización, separación de fases, tratamiento de nulos)
        ↓
  Almacenamiento relacional (SQLite3)
        ↓
  EDA y visualizaciones (Matplotlib / Seaborn)
        ↓
  ┌─────────────────────────────────────────────┐
  │  Eje 1: Fatiga → Regresión Logística        │
  │  Eje 2: Clusters → K-Means (K=2)            │
  │  Eje 3: Jugador franquicia → Random Forest  │
  │  Eje 4: Local vs visitante → Test estadístico│
  └─────────────────────────────────────────────┘
        ↓
  Interpretación y comunicación de resultados
```

---

## 👤 Autor

Proyecto desarrollado como trabajo final de Data Science — **Coderhouse 2025**.

---

> *"Más allá del talento, el Heat se apoya en una estructura estadística que lo mantiene siempre en la conversación por el anillo."* 🏆
