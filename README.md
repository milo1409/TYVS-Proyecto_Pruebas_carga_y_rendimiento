# Proyecto Final – Pruebas de Carga y Rendimiento con k6

## 1. Descripción del proyecto

Este repositorio contiene la entrega final correspondiente a la fase de pruebas de carga y rendimiento del proyecto integrador del curso.

El objetivo principal es diseñar, ejecutar y analizar pruebas de rendimiento sobre un servicio REST desarrollado en Spring Boot, utilizando la herramienta k6. El análisis se enfoca en evaluar el comportamiento del sistema bajo diferentes condiciones de carga, validar objetivos de nivel de servicio —SLO—, identificar posibles cuellos de botella y documentar defectos relacionados con desempeño.

El servicio evaluado corresponde al endpoint:

```http
POST /register
```

Este endpoint permite registrar una persona en el sistema de registraduría.

## 2. Objetivos del proyecto

* Diseñar escenarios progresivos de pruebas de rendimiento.
* Ejecutar pruebas Baseline, Load, Stress, Spike, Soak y Regresión.
* Definir y validar objetivos de nivel de servicio —SLO—.
* Analizar métricas como latencia promedio, p95, p99, throughput y tasa de errores.
* Identificar posibles cuellos de botella.
* Documentar defectos de rendimiento.
* Garantizar que las pruebas sean reproducibles desde consola.

## 3. Herramientas utilizadas

* Java 17.
* Spring Boot 2.7.18.
* Maven.
* k6.
* Git.
* GitHub.
* GitHub Wiki.
* PowerShell.

## 4. Estructura del repositorio

```text
TYVS-Proyecto_Pruebas_carga_y_rendimiento/
│
├── README.md
├── integrantes.txt
│
├── registraduria/
│   ├── pom.xml
│   └── src/
│
├── perf/
│   ├── scripts/
│   │   ├── register_person_k6.js
│   │   └── register_voter_k6.js
│   │
│   ├── data/
│   │   ├── persons.csv
│   │   └── voters.csv
│   │
│   ├── results/
│   │   ├── baseline-summary.json
│   │   ├── load-summary.json
│   │   ├── stress-summary.json
│   │   ├── spike-summary.json
│   │   ├── soak-summary.json
│   │   └── regression-summary.json
│   │
│   ├── defectos_rendimiento.md
│   │
│   └── ci/
│       └── github-actions.yml
│
└── evidencias/
    ├── 01_servicio_ejecucion.png
    ├── 02_curl_endpoint_register.png
    ├── 03_k6_baseline.png
    ├── 04_k6_load.png
    ├── 05_k6_stress.png
    ├── 06_k6_spike.png
    ├── 07_k6_soak.png
    ├── 08_k6_regression.png
    └── 09_resultados_perf_results.png
```

## 5. Servicio evaluado

El servicio se ejecuta localmente en:

```text
http://localhost:8080
```

Endpoint evaluado:

```http
POST /register
```

Ejemplo de petición:

```json
{
  "name": "Ana",
  "id": 100,
  "age": 30,
  "gender": "FEMALE",
  "alive": true
}
```

Respuesta esperada:

```text
VALID
```

## 6. Ejecución del servicio

Desde la carpeta `registraduria`:

```powershell
cd registraduria
mvn spring-boot:run -Dmaven.test.skip=true
```

El servicio queda disponible cuando la consola muestra:

```text
Tomcat started on port(s): 8080
Started RegistryApplication
```

## 7. Prueba manual del endpoint

Desde otra terminal:

```powershell
$body = @{
  name = "Ana"
  id = 100
  age = 30
  gender = "FEMALE"
  alive = $true
} | ConvertTo-Json

Invoke-RestMethod `
  -Uri "http://localhost:8080/register" `
  -Method POST `
  -ContentType "application/json" `
  -Body $body
```

Resultado esperado:

```text
VALID
```

## 8. Escenarios de rendimiento implementados

| Escenario       | Objetivo                                            | Tipo de carga                |
| --------------- | --------------------------------------------------- | ---------------------------- |
| Baseline Test   | Medir el comportamiento base del servicio           | Carga mínima                 |
| Load Test       | Evaluar el sistema bajo carga esperada              | Carga progresiva normal      |
| Stress Test     | Identificar degradación bajo alta concurrencia      | Carga superior a la esperada |
| Spike Test      | Evaluar respuesta ante aumento brusco de usuarios   | Pico repentino               |
| Soak Test       | Validar estabilidad durante carga sostenida         | Carga prolongada             |
| Regression Test | Comparar comportamiento en una ejecución controlada | Carga estable repetible      |

## 9. SLO definidos

| SLO                               | Valor esperado | Métrica k6                |
| --------------------------------- | -------------: | ------------------------- |
| Latencia p95                      |       < 300 ms | `http_req_duration p(95)` |
| Latencia p99                      |       < 800 ms | `http_req_duration p(99)` |
| Tasa de errores HTTP              |          < 1 % | `http_req_failed`         |
| Validaciones funcionales fallidas |          < 1 % | `register_failed`         |

## 10. Ejecución de pruebas k6

Desde la raíz del repositorio:

### Baseline Test

```powershell
k6 run perf/scripts/register_person_k6.js --env BASE_URL=http://localhost:8080 --env SCENARIO=baseline --env RUN_ID=20 --summary-export=perf/results/baseline-summary.json
```

### Load Test

```powershell
k6 run perf/scripts/register_person_k6.js --env BASE_URL=http://localhost:8080 --env SCENARIO=load --env RUN_ID=21 --summary-export=perf/results/load-summary.json
```

### Stress Test

```powershell
k6 run perf/scripts/register_person_k6.js --env BASE_URL=http://localhost:8080 --env SCENARIO=stress --env RUN_ID=22 --summary-export=perf/results/stress-summary.json
```

### Spike Test

```powershell
k6 run perf/scripts/register_person_k6.js --env BASE_URL=http://localhost:8080 --env SCENARIO=spike --env RUN_ID=23 --summary-export=perf/results/spike-summary.json
```

### Soak Test

```powershell
k6 run perf/scripts/register_person_k6.js --env BASE_URL=http://localhost:8080 --env SCENARIO=soak --env RUN_ID=24 --summary-export=perf/results/soak-summary.json
```

### Regression Test

```powershell
k6 run perf/scripts/register_person_k6.js --env BASE_URL=http://localhost:8080 --env SCENARIO=regression --env RUN_ID=25 --summary-export=perf/results/regression-summary.json
```

## 11. Resultados

Los resultados de las ejecuciones se almacenan en:

```text
perf/results/
```

Archivos esperados:

```text
baseline-summary.json
load-summary.json
stress-summary.json
spike-summary.json
soak-summary.json
regression-summary.json
```

## 12. Métricas analizadas

En cada escenario se revisan las siguientes métricas:

* Latencia promedio.
* Percentil 95 —p95—.
* Percentil 99 —p99—.
* Throughput.
* Tasa de errores HTTP.
* Validaciones funcionales exitosas y fallidas.

## 13. Registro de defectos

Los defectos de rendimiento se documentan en:

```text
perf/defectos_rendimiento.md
```

Cada defecto incluye:

* Identificador.
* Escenario donde ocurre.
* Evidencia.
* Impacto.
* Causa probable.
* Propuesta de mejora.

## 14. Wiki del proyecto

La documentación oficial del proyecto se encuentra en la Wiki del repositorio. La Wiki contiene:

* Introducción y arquitectura del sistema.
* Definición de SLO.
* Configuración de escenarios.
* Resultados detallados.
* Comparación entre escenarios.
* Identificación de cuellos de botella.
* Registro de defectos.
* Propuestas de mejora.
* Reflexión técnica.

## 15. Conclusión

Las pruebas de carga y rendimiento permiten evaluar la dimensión temporal del sistema. A través de k6 se validó el comportamiento del endpoint `/register` bajo diferentes condiciones de carga, identificando métricas clave, riesgos de desempeño, defectos asociados a datos de prueba y oportunidades de mejora técnica.

## 16. Ejecución adicional con JMeter

Como evidencia complementaria, se incluyó un plan de pruebas en Apache JMeter ubicado en:

```text
perf/jmeter/register_performance_jmeter.jmx
