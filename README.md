
#  Cemented Rock Fill (CRF) – Sistema Integral de Diseño Geomecánico y Mezclas

##  Descripción General

Este proyecto es una aplicación avanzada desarrollada en **Streamlit** para el análisis, diseño y simulación de sistemas de **Cemented Rock Fill (CRF)** en minería subterránea, bajo el método **Sub Level Stoping**.

El sistema permite integrar:
- Diseño de mezclas cementadas
- Análisis de resistencia a compresión (MPa)
- Modelado de infraestructura minera
- Cálculos geomecánicos (Mitchell + Dujisin)
- Visualización técnica tipo CAD
- Exportación automática de reportes PDF profesionales

---

#  Arquitectura General del Sistema

La aplicación está estructurada en 4 módulos principales:

##  1. Diseños de mezcla
##  2. Diseño seleccionado
##  3. Infraestructura minera
##  4. Geomecánica avanzada

---

#  Gestión de estado (Session State)

El sistema mantiene datos persistentes en memoria:

- Diseños de mezcla CRF
- Resistencias por edad (7–56 días)
- Especificaciones del concreto
- Propiedades de agregados
- Infraestructura minera
- Parámetros geomecánicos

---

#  Módulo de carga de datos (CSV maestro)

##  Funcionalidades:
- Descarga de plantilla estructurada
- Carga de CSV personalizado
- Parsing automático por secciones
- Restauración de datos por defecto

##  Secciones del CSV:
- DESIGNS → Diseños de mezcla
- RESISTENCIAS → Curvas de resistencia
- ESPECIFICACIONES → Parámetros de diseño
- AGREGADO → Propiedades físicas
- INFRAESTRUCTURA → Equipos y geometría
- GEOMECANICA → Parámetros del macizo rocoso

---

#  TAB 1 — Diseños de mezcla

## Funciones principales:
- Cálculo de proporciones CRF
- Conversión a porcentajes
- Relación Agua/Cemento (A/C)
- Evaluación de resistencia a 28 días

## Visualizaciones:
- Curvas de resistencia por diseño
- Comparación de barras a 28 días

---

#  TAB 2 — Diseño seleccionado

## Funciones:
- Selección dinámica de diseño CRF
- Balance de materiales
- Ajuste por humedad y absorción
- Cálculo de densidad de mezcla

## Resultados:
- Cemento (kg/m³)
- Agua industrial (L/m³)
- Agregado corregido
- Relación A/C
- Densidad final del CRF

## Visualización:
- Gráfico de torta (composición)
- Gráfico de barras comparativo
- Cálculo por tanda (m³)

---

#  TAB 3 — Infraestructura minera

## Funciones:
- Dimensionamiento de cámara de mezclado
- Equipos mineros (bombas, chancadoras, fajas)
- Personal operativo requerido
- Cálculo de volúmenes

## Parámetros:
- Longitud, ancho y altura de cámara
- Espesores estructurales (concreto, shotcrete)
- Pendiente del piso

## Visualización técnica:
- Plano tipo CAD de cámara minera
- LHD integrado (Load-Haul-Dump)
- Hatching técnico de terreno
- Representación geométrica realista

---

#  TAB 4 — Geomecánica avanzada

## Modelos implementados:

###  Mitchell (1970)
- Presión vertical del macizo
- Coeficiente activo de empuje lateral

###  Dujisin (1992)
- Reducción por factor de seguridad
- Presión total sobre el caserón

## Variables clave:
- Altura del caserón (H)
- Ángulo de fricción (φ)
- Cohesión del CRF
- Densidad de roca
- Factor de seguridad

## Resultados:
- σ vertical
- σ lateral
- σ techo
- σ total del sistema

## Visualización:
- Distribución de presión lateral
- Comparación Mitchell vs Dujisin
- Barras de soporte estructural

---

#  Generación de Reporte PDF

## Características:
- Reporte técnico automático completo
- Portada profesional
- Tablas estructuradas
- Gráficos incrustados
- Diagramas técnicos (Matplotlib + Plotly)

## Contenido del PDF:
- Diseño de mezcla seleccionado
- Infraestructura minera
- Cálculos geomecánicos
- Curvas de resistencia
- Composición del CRF

---

#  Interfaz de usuario (UI)

## Diseño:
- Tema oscuro tipo ingeniería minera
- Tipografía Lato
- Colores técnicos:
  - Dorado (#f0a500)
  - Azul (#388bfd)
  - Verde (#3fb950)
  - Rojo (#f85149)

## Componentes:
- Tabs estructurados
- Tablas técnicas estilo ingeniería
- Gráficos interactivos
- Panel de descarga PDF

---

#  Flujo de trabajo del sistema

1. Cargar o usar datos por defecto
2. Seleccionar diseño CRF
3. Analizar resistencia y composición
4. Definir infraestructura minera
5. Evaluar estabilidad geomecánica
6. Generar reporte PDF final

---

#  Alcance del sistema

Este sistema permite:

✔ Diseño completo de CRF  
✔ Simulación geomecánica avanzada  
✔ Evaluación estructural de cámaras mineras  
✔ Optimización de mezcla cementada  
✔ Reporte técnico automático  
✔ Visualización tipo ingeniería CAD  

---

#  Autor

**Bradoc Chambilla**
