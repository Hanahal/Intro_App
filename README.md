#  Cemented Rock Fill (CRF) — Sistema Integral de Diseño Geomecánico y Mezclas

##  Descripción General

Este proyecto es una aplicación avanzada desarrollada en **Streamlit** para el análisis, diseño, simulación y evaluación geomecánica de sistemas de **Cemented Rock Fill (CRF)** aplicados a minería subterránea bajo el método **Sub Level Stoping (SLS)**.

La plataforma integra módulos técnicos orientados a:

- Diseño de mezclas cementadas
- Evaluación de resistencia mecánica
- Simulación geomecánica avanzada
- Modelado de infraestructura minera
- Análisis granulométrico ASTM
- Simulación Monte Carlo para UCS
- Generación automática de reportes PDF profesionales

El sistema está orientado a aplicaciones académicas, investigación minera, optimización de relleno cementado y soporte en ingeniería geotécnica.

---

#  Arquitectura General del Sistema

La aplicación está organizada en **6 módulos principales**:

| TAB | Módulo |
|---|---|
| 1 | Diseños de Mezcla |
| 2 | Diseño Seleccionado |
| 3 | Infraestructura Minera |
| 4 | Geomecánica Avanzada |
| 5 | UCS + Monte Carlo |
| 6 | Granulometría ASTM |

---

#  Gestión de Estado (Session State)

El sistema utiliza `st.session_state` para mantener persistencia dinámica de datos durante toda la sesión.

## Datos almacenados

- Diseños CRF
- Resistencias por edad (7–56 días)
- Especificaciones del concreto
- Propiedades físicas del agregado
- Infraestructura minera
- Parámetros geomecánicos
- Resultados Monte Carlo
- Datos granulométricos
- Diseño seleccionado
- Configuración de simulaciones

---

#  Módulo de Carga de Datos CSV Maestro

## Funcionalidades

- Descarga automática de plantilla CSV
- Importación personalizada de datos
- Parsing inteligente por secciones
- Restauración de datos por defecto
- Validación estructural automática

## Secciones del CSV

- `DESIGNS`
- `RESISTENCIAS`
- `ESPECIFICACIONES`
- `AGREGADO`
- `INFRAESTRUCTURA`
- `GEOMECANICA`

---

#  TAB 1 — Diseños de Mezcla

## Funciones Principales

- Diseño de mezclas CRF
- Conversión de materiales
- Relación Agua/Cemento (A/C)
- Evaluación de resistencia a compresión
- Comparación entre diseños

## Visualizaciones

- Curvas de resistencia
- Comparación de barras
- Evolución MPa vs tiempo

## Resultados

- Cemento (kg/m³)
- Agua (L/m³)
- Agregado total
- Densidad estimada
- Resistencia proyectada

---

#  TAB 2 — Diseño Seleccionado

## Funciones

- Selección dinámica del diseño óptimo
- Corrección por humedad y absorción
- Balance volumétrico de mezcla
- Dosificación por tanda
- Cálculo de densidad final

## Resultados Técnicos

- Relación A/C corregida
- Peso unitario del CRF
- Distribución porcentual
- Volumen por lote
- Ajuste de agregados

## Visualizaciones

- Gráfico de composición tipo pie
- Comparación de materiales
- Balance volumétrico

---

#  TAB 3 — Infraestructura Minera

## Funciones

- Diseño geométrico de cámara
- Evaluación de infraestructura CRF
- Cálculo de volúmenes operativos
- Estimación de equipos y personal
- Modelado de planta de relleno

## Parámetros

- Longitud
- Ancho
- Altura
- Pendiente
- Shotcrete
- Espesores estructurales

## Equipos Considerados

- Bombas de relleno
- Chancadoras
- Fajas transportadoras
- Tolvas
- LHD (Load-Haul-Dump)

## Visualización Técnica

- Plano tipo CAD
- Hatching geotécnico
- Representación de cámara minera
- Geometría operacional

---

#  TAB 4 — Geomecánica Avanzada

## Modelos Implementados

### 🔹 Mitchell (1970)

Cálculo de:

- Presión vertical
- Empuje lateral activo
- Distribución de esfuerzos

### 🔹 Dujisin (1992)

Cálculo de:

- Reducción por factor de seguridad
- Presión total sobre el caserón
- Evaluación de estabilidad

---

## Variables Geomecánicas

- Altura del caserón (H)
- Densidad de roca (γ)
- Ángulo de fricción (φ)
- Cohesión del CRF
- Factor de seguridad (FS)

---

## Resultados

- σ vertical
- σ lateral
- σ techo
- σ total
- Estabilidad del sistema

---

## Visualizaciones

- Distribución lateral de presión
- Comparación Mitchell vs Dujisin
- Diagramas estructurales

---

#  TAB 5 — UCS + Simulación Monte Carlo

## Descripción

Módulo avanzado para evaluación de la **Resistencia a Compresión Uniaxial (UCS)** mediante:

- Métodos determinísticos
- Simulación estocástica Monte Carlo
- Sensibilidad de parámetros
- Distribuciones probabilísticas

---

## Métodos Implementados

### 🔹 Yu (2006)

Estimación UCS basada en profundidad y densidad.

### 🔹 Potvin (2005)

Modelo geomecánico para estabilidad de relleno cementado.

### 🔹 Mitchell (1982)

Evaluación UCS considerando geometría y fricción.

### 🔹 Obert & Duvall (1967)

Modelo aplicado a estabilidad de techo y resistencia del relleno.

---

## Simulación Monte Carlo

### Características

- Distribuciones normales truncadas
- Miles de iteraciones configurables
- Variabilidad geotécnica
- Evaluación probabilística

### Parámetros Simulados

- Densidad del macizo
- Altura del caserón
- Ancho hidráulico
- Factor de seguridad
- Ángulo de fricción
- Luz del techo

---

## Resultados Estadísticos

- Media
- Desviación estándar
- Percentiles P5–P95
- Distribuciones acumuladas
- Sensibilidad por correlación

---

## Visualizaciones

###  Histogramas

Distribución probabilística UCS.

###  Curvas CDF

Probabilidad acumulada y excedencia.

###  Tornado de Sensibilidad

Correlación de Pearson entre variables y UCS.

###  Diagramas de Dispersión

Relación entrada–salida Monte Carlo.

###  Box Plot

Comparación estadística entre métodos.

---

## Exportación

- Excel completo UCS + Monte Carlo
- Estadísticas
- Datos de entrada
- Sensibilidad
- Muestras Monte Carlo

---

#  TAB 6 — Análisis Granulométrico ASTM

## Normas Implementadas

- ASTM D6913-17
- ASTM D2487-17
- ASTM D1140-00

---

## Funcionalidades

### 🔹 Análisis Granulométrico

- Retenido parcial
- Retenido acumulado
- % que pasa
- Distribución granulométrica

### 🔹 Clasificación USCS

Determinación automática de:

- GW
- GP
- SW
- SP
- Suelos con finos

### 🔹 Parámetros Geotécnicos

Cálculo de:

- D10
- D30
- D60
- Cu
- Cc
- Módulo de Fineza

---

## Modos de Ingreso

### Automático

Generación de curva basada en:

- Tamaño máximo
- Tamaño nominal
- Módulo de fineza

### Manual

Ingreso de:

- % retenido por tamiz
- Masa total de muestra

---

## Visualizaciones

###  Curva Granulométrica

- Escala logarítmica
- D10–D60
- Zonas de grava, arena y finos

###  Distribución por Fracciones

- Grava
- Arena
- Finos

###  Retenido Parcial

Distribución porcentual por tamiz.

---

## Verificaciones ASTM

### ASTM D1140

- Finos pasante N°200
- Clasificación preliminar

### ASTM D2487

- Verificación Cu y Cc
- Evaluación de gradación

---

#  Generación de Reporte PDF Profesional

## Características

- Reporte técnico automático
- Portada profesional
- Diagramas incrustados
- Tablas estructuradas
- Exportación instantánea

---

## Contenido del Reporte

### Incluye:

- Diseños de mezcla
- Diseño seleccionado
- Infraestructura minera
- Geomecánica avanzada
- Análisis UCS Monte Carlo
- Granulometría ASTM
- Gráficos y diagramas
- Interpretaciones técnicas

---

#  Interfaz de Usuario (UI)

## Diseño Visual

Tema oscuro orientado a ingeniería minera.

## Colores Técnicos

| Color | Código |
|---|---|
| Dorado | `#ffb830` |
| Azul | `#5898f8` |
| Verde | `#5fb840` |
| Rojo | `#f85149` |

---

## Componentes UI

- Tabs estructurados
- Métricas dinámicas
- Tablas técnicas
- Plotly interactivo
- Matplotlib científico
- Exportación Excel/PDF

---

#  Flujo General del Sistema

```text
1. Cargar datos CSV o usar datos por defecto
2. Diseñar mezclas CRF
3. Seleccionar diseño óptimo
4. Evaluar infraestructura minera
5. Ejecutar análisis geomecánico
6. Simular UCS Monte Carlo
7. Analizar granulometría ASTM
8. Generar reporte PDF completo
