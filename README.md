# 🛡️ Cloudflare WAF Bypass — PHP2Shell
### Security Research | Web Application Firewall Evasion | Memory Corruption | CTF

<p align="center">
  <img src="https://img.shields.io/badge/Research-Web%20Security-blue?style=for-the-badge">
  <img src="https://img.shields.io/badge/Environment-PHP%208.5.11-purple?style=for-the-badge">
  <img src="https://img.shields.io/badge/Platform-Docker-2496ED?style=for-the-badge&logo=docker&logoColor=white">
  <img src="https://img.shields.io/badge/Focus-WAF%20Bypass-red?style=for-the-badge">
</p>

<p align="center">
  <b>Investigación técnica sobre evasión de WAF, análisis de memoria y evaluación de vulnerabilidades en PHP2Shell.</b>
</p>

---

## 📌 1. Descripción del proyecto

**ES — Español**

Este repositorio documenta una investigación de seguridad enfocada en el análisis de mecanismos de protección de aplicaciones web mediante Cloudflare Web Application Firewall (WAF), utilizando un entorno PHP2Shell como escenario de pruebas.

Durante la investigación se evaluó el comportamiento del WAF frente a estructuras de parámetros anidados, técnicas de codificación URL y mecanismos de detección de solicitudes potencialmente maliciosas.

El análisis también contempló escenarios relacionados con errores de gestión de memoria, específicamente Out Of Memory (OOM) y Use After Free (UAF), así como la identificación de un offset de memoria asociado a la compilación de PHP 8.5.11.

Se documentan las pruebas realizadas, los resultados obtenidos y las evidencias técnicas recopiladas durante el proceso.

**EN — English**

This repository documents a security research project focused on analyzing web application protection mechanisms through Cloudflare Web Application Firewall (WAF), using a PHP2Shell environment as a testing scenario.

The research evaluates WAF behavior against deeply nested parameters, URL encoding techniques, and potentially malicious request patterns.

The analysis also covers memory management issues, specifically Out Of Memory (OOM) and Use After Free (UAF), along with memory offset identification associated with the PHP 8.5.11 build.

Testing procedures, observed results, and technical evidence collected throughout the investigation are documented.

---

## 🎯 2. Objetivos de la investigación

- Analizar el comportamiento de Cloudflare WAF frente a solicitudes manipuladas.
- Evaluar mecanismos de detección de parámetros anidados.
- Investigar técnicas de codificación URL y su impacto en la inspección de solicitudes.
- Examinar escenarios de corrupción de memoria OOM/UAF.
- Identificar parámetros técnicos del entorno PHP 8.5.11.
- Documentar los resultados mediante pruebas reproducibles en un laboratorio controlado.

---

## 🖥️ 3. Entorno de investigación

| Componente | Especificación |
|---|---|
| Aplicación objetivo | PHP2Shell |
| Host de pruebas | `php-rce.cloudflare.app` |
| Lenguaje | PHP |
| Versión | PHP 8.5.11 |
| Servidor web | Apache |
| Integración PHP | mod_php |
| Protección perimetral | Cloudflare WAF |
| Contenedorización | Docker |
| Arquitectura | x86_64 |
| Vulnerabilidades investigadas | CVE-2026-109618 / CVE-2026-109619 |

**Endpoint de laboratorio:**

`https://php-rce.cloudflare.app/php/index.php`

---

## 🧪 4. Metodología y desarrollo

La investigación se dividió en cinco etapas principales para facilitar el análisis y la documentación de los resultados.

### 4.1. Bloqueo inicial — HTTP 403 Forbidden

En la primera etapa se realizaron solicitudes de prueba para evaluar la capacidad de inspección del WAF.

Se observó que Cloudflare identificaba determinadas estructuras de parámetros anidados y respondía con un código HTTP 403 Forbidden, impidiendo que las solicitudes llegaran al comportamiento esperado de la aplicación.

**Resultado:** Solicitud bloqueada por el mecanismo de protección.

![Bloqueo inicial del WAF](ctf/bloqueo%20ctf.png)

### 4.2. Evaluación de codificación URL — HTTP 200 OK

Posteriormente, se investigó el tratamiento de los parámetros codificados mediante URL encoding.

Se observó una diferencia en el procesamiento de determinadas estructuras cuando se representaban mediante secuencias codificadas, como `%5B` y `%5D`.

Durante la prueba documentada, el servidor respondió con HTTP 200 OK, evidenciando una diferencia entre el comportamiento de inspección del WAF y el procesamiento de la solicitud.

**Resultado:** Se identificó un escenario de evasión de las reglas de inspección evaluadas.

![Prueba de bypass](ctf/bypass_exitoso.png)


**Evidencia adicional:**

![Resultado de la evaluación del WAF](ctf/waf_burlado.png)

### 4.3. Análisis UAF — Time-Based Testing

En esta fase se examinó el comportamiento del entorno frente a escenarios asociados a Use After Free (UAF).

Se documentaron las pruebas realizadas sobre el comportamiento de memoria y una evaluación temporal utilizada para analizar la respuesta de la aplicación.

La medición registrada fue de aproximadamente **2.44 segundos**.

**Resultado:** Evidencia temporal recopilada durante la evaluación del escenario.

![Prueba del escenario UAF](ctf/waf_burlado.png)

![Medición temporal](ctf/2.44s_tiempo.png)

### 4.4. Identificación del offset de memoria

Se realizó un análisis técnico de la compilación utilizada para identificar el desplazamiento de memoria asociado a la función `zif_system`.

El offset documentado durante la investigación fue:

| Parámetro | Valor |
|---|---|
| Función analizada | `zif_system` |
| Offset identificado | `0xA5B9E8` |
| Versión PHP | 8.5.11 |
| Arquitectura | x86_64 |

**Resultado:** Identificación del offset observado en el entorno de pruebas.

![Identificación del offset](ctf/offset.png)

### 4.5. Replicación del entorno mediante Docker

Finalmente, se preparó un entorno local mediante Docker para disponer de una instancia controlada de PHP 8.5.11.

El objetivo fue facilitar la repetición de pruebas, observar el comportamiento de la aplicación y reducir la dependencia del entorno remoto durante el análisis.

**Resultado:** Entorno de laboratorio preparado para pruebas controladas.

![Entorno Docker](ctf/DOCKER.png)

---

## ⚙️ 5. Detalles técnicos

### Codificación URL

Durante la investigación se examinó el tratamiento de los caracteres especiales utilizados en estructuras de parámetros anidados.

| Carácter | Representación URL |
|---|---|
| `[` | `%5B` |
| `]` | `%5D` |

### Fragmento de análisis en Python

El siguiente fragmento representa la construcción de una estructura de parámetros codificados utilizada durante la investigación:

```python
# Fragmento de análisis de parámetros URL
nested_array = "a" + "%5B0%5D" * 62

# Estructura generada para evaluación controlada
print(nested_array)
```

---

## 📊 6. Resultados obtenidos

| Etapa | Prueba | Resultado observado |
|---|---|---|
| 01 | Solicitud con parámetros anidados | HTTP 403 |
| 02 | Evaluación de codificación URL | HTTP 200 |
| 03 | Análisis del escenario UAF | Evidencia temporal |
| 04 | Identificación de memoria | `0xA5B9E8` |
| 05 | Replicación mediante Docker | Entorno local preparado |

### Hallazgos principales

- Se observó un bloqueo inicial de solicitudes por parte de Cloudflare WAF.
- Se identificó una diferencia en la inspección de parámetros codificados.
- Se documentó un escenario de evaluación relacionado con UAF.
- Se identificó un offset de memoria asociado al entorno PHP analizado.
- Se preparó un entorno Docker para facilitar la investigación y las pruebas controladas.

---

## 📁 7. Estructura del repositorio

```text
CLOUDFARE-WAF-BYPASS/
│
├── ctf/
│   ├── 2.44s_tiempo.png
│   ├── DOCKER.png
│   ├── bloqueo ctf.png
│   ├── bypass_exitoso.png
│   ├── offset.png
│   ├── trigger_uaf.png
│   └── waf_burlado.png
│
└── README.md
```

---

## 🔐 8. Consideraciones de seguridad

Esta investigación tiene fines exclusivamente educativos y de investigación en ciberseguridad.

Las pruebas deben realizarse únicamente en entornos propios, laboratorios controlados o plataformas donde exista autorización expresa para realizar evaluaciones de seguridad.

La documentación no debe utilizarse para acceder sin autorización a sistemas, evadir mecanismos de protección de terceros o ejecutar acciones que comprometan la disponibilidad e integridad de servicios.

Los identificadores CVE incluidos corresponden a los identificadores investigados en este proyecto; su asignación y estado oficial deben verificarse mediante fuentes públicas de CVE.

---

## 👨‍💻 9. Autor

**Sebas533**

Cybersecurity Student | Web Security Research | Vulnerability Analysis

GitHub: [@sebas533](https://github.com/sebas533)

Repositorio: [CLOUDFARE-WAF-BYPASS](https://github.com/sebas533/CLOUDFARE-WAF-BYPASS)

---

<p align="center">
  <b>Security Research • Web Application Security • WAF Analysis</b>
  <br>
  <i>Understanding vulnerabilities is the first step toward building stronger defenses.</i>
</p>
