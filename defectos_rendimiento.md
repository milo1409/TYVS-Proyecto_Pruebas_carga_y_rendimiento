# Registro de defectos de rendimiento

## PERF-001 - Respuestas DUPLICATED durante pruebas de carga

| Campo | Descripción |
|---|---|
| Identificador | PERF-001 |
| Escenario | Baseline / Soak Test |
| Evidencia | Durante la ejecución se observaron múltiples respuestas `status=200 body='DUPLICATED'`. |
| Impacto | La prueba deja de representar registros exitosos y afecta el cumplimiento de los checks funcionales definidos en k6. |
| Severidad | Media |
| Prioridad | Alta |
| Causa probable | Reutilización de identificadores entre iteraciones o entre ejecuciones anteriores sobre la misma base H2 en memoria. |
| Propuesta de mejora | Generar identificadores únicos por ejecución usando `RUN_ID`, `SCENARIO`, `__VU` e `__ITER`. También se recomienda reiniciar el servicio antes de cada corrida crítica. |

---

## PERF-002 - Configuración inicial de Soak Test con duración excesiva

| Campo | Descripción |
|---|---|
| Identificador | PERF-002 |
| Escenario | Soak Test |
| Evidencia | El escenario estaba configurado inicialmente con `100 VUs` durante `2h`, lo cual excedía el tiempo disponible para la ejecución académica. |
| Impacto | La prueba no era práctica para el contexto de entrega, dificultaba la generación de evidencias y retrasaba la comparación de escenarios. |
| Severidad | Baja |
| Prioridad | Media |
| Causa probable | Configuración de resistencia pensada para un ambiente más cercano a producción, no para una práctica académica local. |
| Propuesta de mejora | Ajustar el Soak Test a una duración controlada de `3m` con `10 VUs`, manteniendo el objetivo de observar estabilidad durante carga sostenida. |

---

## PERF-003 - Respuestas 400 Bad Request por identificadores fuera de rango

| Campo | Descripción |
|---|---|
| Identificador | PERF-003 |
| Escenario | Baseline Test |
| Evidencia | Se observaron respuestas `status=400 Bad Request` al generar identificadores basados en `Date.now()` concatenado con VU e iteración. |
| Impacto | El backend rechazó solicitudes antes de ejecutar la lógica de negocio, invalidando la medición funcional del escenario. |
| Severidad | Alta |
| Prioridad | Alta |
| Causa probable | El campo `id` del backend espera un valor numérico dentro de un rango compatible con Java Integer. |
| Propuesta de mejora | Usar una fórmula de identificador controlada por escenario y ejecución, evitando valores numéricos excesivamente grandes. |
