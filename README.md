# 🏀 Miami Heat — Radiografía de una Cultura (2019–2024)

> 📊 Análisis de datos y modelado predictivo del desempeño del Miami Heat en cinco temporadas críticas de la NBA.

## 📋 Descripción

Este proyecto realiza un análisis exploratorio (EDA) y estadístico del Miami Heat desde la temporada 2019–2020 hasta 2023–2024. A partir de 391 partidos, estadísticas avanzadas y métricas de rendimiento individual, se exploran los patrones que definen la identidad competitiva de la franquicia.

Los datos provienen de **Basketball Reference** y **Universo Básquet**, procesados con Python y almacenados en SQLite.

---

## 🎯 Objetivos

- 🔍 Identificar las fortalezas y debilidades del equipo a lo largo del ciclo.
- ✈️ Cuantificar el impacto del factor fatiga (distancia viajada) en los resultados.
- 🆚 Comparar el rendimiento en temporada regular vs. playoffs.
- ⭐ Evaluar el impacto del jugador franquicia en el ciclo analizado.
- 🏟️ Medir la fortaleza del Kaseya Center como ventaja de local.

---

## 🛠️ Stack tecnológico

| Herramienta | Uso |
|---|---|
| 🐍 Python 3 | Lenguaje principal |
| 🐼 Pandas | Limpieza y manipulación de datos |
| 📈 Matplotlib / Seaborn | Visualizaciones |
| 🤖 Scikit-learn | Modelos de ML (Regresión Logística, K-Means, Random Forest) |
| 🗄️ SQLite3 | Almacenamiento relacional local |
| ☁️ Google Colab | Entorno de ejecución |
| 💾 Google Drive | Fuente del dataset |

---

## 🔬 Estructura del análisis (Ejes)

### ✈️ Eje 1 — Factor Fatiga
Correlación entre la distancia recorrida en viajes y la probabilidad de victoria.
Método: regresión logística. Resultado: leve tendencia negativa (efecto real pero pequeño, AUC 0.55–0.65).

### 🧬 Eje 2 — ADN de Playoffs (K-Means Clustering)
Agrupación de temporadas en clusters para separar el perfil de temporada regular del de playoffs.
Resultado: ARI = 1.0 — separación perfecta entre ambas fases.

### ⭐ Eje 3 — Impacto del Jugador Franquicia
Análisis del peso de Jimmy Butler en los resultados del equipo durante el ciclo 2019–2024.
Método: Random Forest Regressor por jugador.

### 🏟️ Eje 4 — La Mística del Kaseya Center
Evaluación del rendimiento de local vs. visitante para determinar si el estadio sigue siendo una fortaleza real.

---

## 🗃️ Dataset

- 📌 **Fuente:** Basketball Reference + Universo Básquet
- 📂 **Formato:** `.xlsx` con múltiples hojas (Regular, Playoffs, combinado)
- 🔢 **Registros:** 391 partidos — temporadas 2019–2020 a 2023–2024
- 📐 **Variables:** estadísticas por jugador/temporada/fase (32 columnas)

---

## ▶️ Cómo ejecutar

1. 📥 Clonar el repositorio o abrir `Miami_Heat (Final).ipynb` en Google Colab.
2. 🔗 Montar Google Drive cuando se solicite.
3. ⬇️ El notebook descarga automáticamente el dataset desde Drive con `gdown`.
4. ▶️ Ejecutar celdas en orden.

---

## 📁 Archivos del repositorio

| Archivo | Descripción |
|---|---|
| 📓 `Miami_Heat (Final).ipynb` | Notebook principal con el análisis completo |
| 📝 `Miami_Heat.ipynb` | Versión de desarrollo / borrador |
| 📄 `Radiografia-de-una-Cultura.pdf` | Informe narrativo del proyecto |
| 🤖 `Reporte Claude - Informe Miami Heat.pdf` | Informe generado con asistencia de IA |
| 🗺️ `Roadmap_Miami_Heat_ML.pdf` | Hoja de ruta para extensión con ML |
| 📋 `README.md` | Este archivo |

---

## 👤 Autor

Proyecto desarrollado como trabajo final de Data Science — **Coderhouse 2025**.

---

> *"Más allá del talento, el Heat se apoya en una estructura estadística que lo mantiene siempre en la conversación por el anillo."* 🏆
