# 🧭 Guía de buenas prácticas de desarrollo de software

**Versión 2.0 — Octubre 2026** · Autor: Alan Pérez
*Actualiza la v1.0. Ver sección 12 para el registro de cambios.*

---

## 1. Introducción

Esta guía reúne las buenas prácticas esenciales de desarrollo moderno, aplicables a proyectos web, móvil, backend, escritorio, videojuegos, embebido, IA y sistemas empresariales.

Busca que el software sea:

- **Seguro:** protegido desde el diseño, incluida su **cadena de suministro** (dependencias, build, despliegue).
- **Mantenible:** código limpio, modular, probado y documentado.
- **Escalable y operable:** crece ordenadamente y se puede observar y recuperar.
- **Trazable:** cada cambio y decisión se puede explicar y auditar.

> 💡 No impone una tecnología: propone una forma de pensar. Las reglas concretas del día a día están en [`CODE_STYLE.md`](../CODE_STYLE.md), [`COMMITS.md`](../COMMITS.md) y [`CONTRIBUTING.md`](../CONTRIBUTING.md).

---

## 2. Pilares

| Pilar | Descripción | Ejemplo |
|---|---|---|
| **Seguridad por diseño** | Se planea desde el inicio, no al final. | Modelado de amenazas, validación de entradas, mínimo privilegio. |
| **Calidad automatizada** | Lo repetible lo hace una máquina. | Lint, tests, escaneo de dependencias en CI. |
| **Cambios pequeños y frecuentes** | Menos riesgo, más feedback. | PRs < 400 líneas, despliegues frecuentes. |
| **Trazabilidad y cumplimiento** | Todo es justificable. | Commits convencionales, ADRs, SBOM. |
| **Observabilidad** | Si no lo puedes ver, no lo puedes arreglar. | Logs estructurados, métricas, trazas. |

---

## 3. Ciclo de vida (SDLC seguro)

### Fase 1 — Planificación y análisis
- Definir propósito, alcance, usuarios y requisitos (funcionales y **no funcionales**: rendimiento, seguridad, disponibilidad).
- Identificar normativas aplicables (privacidad, pagos, sector).
- Hacer un **modelado de amenazas** inicial (¿qué podría salir mal y quién querría atacarlo?). Un método simple: **STRIDE**.
- Crear documentos base: `README.md`, `SECURITY.md`, `CHANGELOG.md`, `CONTRIBUTING.md`.
- Definir criterios de éxito medibles y el alcance del MVP.

### Fase 2 — Diseño y arquitectura
- Estructura de carpetas, módulos y dependencias. Aplicar **SOLID, DRY, KISS, YAGNI, SoC**.
- Empezar con un **monolito modular** salvo que haya una razón clara para microservicios (equipos independientes, escalado muy distinto por componente).
- Registrar decisiones importantes como **ADR** (*Architecture Decision Record*): una página con contexto, decisión y consecuencias.
- Diseñar autenticación, autorización (RBAC/ABAC), cifrado, manejo de secretos y auditoría.
- Documentar con diagramas simples (modelo **C4**) y contratos de API (**OpenAPI**).

### Fase 3 — Desarrollo seguro
- Flujo de ramas simple (*trunk-based*): `main` protegida + ramas cortas. Ver `CONTRIBUTING.md`.
- Validar y sanear **toda** entrada externa.
- Cero secretos en el código; usar variables de entorno o gestor de secretos.
- Code review obligatorio; commits convencionales.
- Pruebas desde el inicio; **feature flags** para liberar de forma gradual.
- Uso responsable de **asistentes de IA para programar**: revisa y entiende todo lo que generen, nunca pegues secretos o datos de clientes en ellos, y trata su salida como código de un tercero (pruebas + revisión).

### Fase 4 — Pruebas e integración
- Pirámide de pruebas: muchas **unitarias**, algunas de **integración**, pocas **end-to-end**.
- Seguridad en el pipeline: **SAST** (análisis de código), **SCA** (dependencias), **escaneo de secretos**, **DAST** (app en ejecución) y escaneo de imágenes de contenedor.
- Entornos de QA/staging aislados, con datos sintéticos o anonimizados.
- Documentar métricas: cobertura, tiempo de build, vulnerabilidades abiertas.

### Fase 5 — Despliegue y operación
- Entornos separados: desarrollo, staging, producción.
- **CI/CD** con validaciones obligatorias antes de desplegar; despliegues reproducibles (**Infraestructura como Código**: Terraform, Pulumi, etc.).
- Estrategias seguras de liberación: *blue/green*, *canary*, *rollback* probado.
- Gestión de secretos con herramientas dedicadas (Vault, cloud secret managers). Rotación periódica.
- **Backups** con restauración **probada** (un backup no probado no es un backup).

### Fase 6 — Mantenimiento y mejora continua
- Actualizar dependencias de forma continua (Dependabot / Renovate), no una vez al año.
- Gestión de incidentes: detección, respuesta, **post-mortem sin culpables** y acciones de mejora.
- Revisar métricas de uso, errores y costos (**FinOps** básico en la nube).
- Auditorías internas periódicas y retiro ordenado de lo obsoleto.

---

## 4. Seguridad moderna

### 4.1 OWASP Top 10:2025

La edición vigente de OWASP es la **2025** (publicada en noviembre de 2025). Reemplaza a la 2021:

| # | Categoría 2025 | Idea en una línea |
|---|---|---|
| A01 | Broken Access Control | Que cada usuario solo acceda a lo suyo (incluye SSRF). |
| A02 | Security Misconfiguration | Configuraciones por defecto o inseguras. |
| A03 | **Software Supply Chain Failures** *(nueva)* | Dependencias, builds y distribución comprometidos. |
| A04 | Cryptographic Failures | Cifrado ausente o mal usado. |
| A05 | Injection | Datos del usuario interpretados como código (SQL, comandos...). |
| A06 | Insecure Design | Fallas de diseño, no solo de código. |
| A07 | Authentication Failures | Login, sesiones y recuperación débiles. |
| A08 | Software or Data Integrity Failures | Actualizaciones o datos sin verificar. |
| A09 | Security Logging and Alerting Failures | No se registra ni se alerta lo importante. |
| A10 | **Mishandling of Exceptional Conditions** *(nueva)* | Errores y casos extremos mal manejados. |

### 4.2 Cadena de suministro de software

- Fija versiones con **lockfile** y revisa cambios de dependencias en los PR.
- Genera un **SBOM** (lista de componentes del software) en cada release.
- Prefiere dependencias mantenidas y con pocas sub-dependencias.
- Protege el pipeline: permisos mínimos en CI, acciones fijadas por versión/hash, firmado de artefactos.
- Referencias: **SLSA**, **OpenSSF Scorecard**.

### 4.3 Identidad y acceso

- **MFA** para todo el equipo; preferir **passkeys/WebAuthn** para usuarios.
- Mínimo privilegio y credenciales de corta vida (tokens temporales).
- Hash de contraseñas con **Argon2id** o bcrypt.
- Enfoque **Zero Trust**: nunca confiar solo por estar "dentro de la red".

### 4.4 Datos y privacidad

- Recolecta **solo** los datos necesarios (minimización) y define cuánto tiempo se guardan.
- Cifra en tránsito (TLS) y en reposo.
- No registres datos personales ni secretos en logs.
- Para proyectos en México, revisa la **Ley Federal de Protección de Datos Personales en Posesión de los Particulares (LFPDPPP)** vigente y el aviso de privacidad; si hay usuarios en la UE, aplica el **GDPR**. *(Verifica con asesoría legal la versión y autoridad vigentes.)*

### 4.5 IA / ML

- Documenta origen de los datos, sesgos y limitaciones del modelo.
- Protege contra **prompt injection** y fuga de datos si usas LLMs; no des a un agente más permisos de los necesarios.
- Revisa obligaciones regulatorias según mercado (por ejemplo, el **EU AI Act**).

---

## 5. Calidad y código

- **Código limpio:** nombres claros, funciones pequeñas, pocas dependencias entre módulos.
- **Revisión de código** en todo cambio. Ver `CONTRIBUTING.md`.
- **Deuda técnica visible:** `TODO(#issue)` y backlog dedicado.
- **Versionado semántico (SemVer)** + `CHANGELOG` basado en [Keep a Changelog](https://keepachangelog.com/es-ES/).
- **Documentación como código**: vive en el repo, se revisa en PRs.

### Métricas útiles (DORA)

| Métrica | Pregunta que responde |
|---|---|
| Frecuencia de despliegue | ¿Qué tan seguido liberamos? |
| Tiempo de entrega de cambios | ¿Cuánto tarda un commit en llegar a producción? |
| Tasa de fallos de cambio | ¿Qué porcentaje de despliegues causa problemas? |
| Tiempo de recuperación | ¿Qué tan rápido nos recuperamos de un fallo? |

> Mide para **mejorar**, no para castigar personas.

---

## 6. Escalabilidad y rendimiento

- **Mide antes de optimizar:** perfila, no adivines.
- Caché, colas y trabajos asíncronos para tareas lentas.
- Diseña servicios **sin estado** (*stateless*) cuando sea posible.
- Índices de base de datos y revisión de consultas lentas.
- Pruebas de carga antes de eventos grandes.
- Define **SLIs/SLOs** (objetivos de nivel de servicio) para lo que importa al usuario.

---

## 7. DevOps y observabilidad

- **Los tres pilares:** logs estructurados, métricas y trazas. Estándar abierto recomendado: **OpenTelemetry**.
- Alertas **accionables** (si despiertan a alguien, debe ser por algo que requiera acción).
- Contenedores: imágenes mínimas, usuario no root, escaneo de vulnerabilidades.
- Runbooks para incidentes frecuentes.
- Principios de **12-Factor App** como base para apps en la nube.

---

## 8. Documentación y cultura

- Documentos mínimos por proyecto: `README`, `CONTRIBUTING`, `SECURITY`, `CHANGELOG`, ADRs.
- Escribe pensando en quien llega nuevo al proyecto.
- Cultura **sin culpables** ante errores: se corrige el sistema, no a la persona.
- Compartir conocimiento: sesiones técnicas, pares (*pair programming*), revisiones constructivas.

---

## 9. Especialidades

| Tipo | Puntos clave extra |
|---|---|
| **Web / APIs** | CORS, CSP, rate limiting, versionado de API, OpenAPI. |
| **Móvil** | Almacenamiento seguro, permisos mínimos, certificate pinning cuando aplique. |
| **Escritorio** | Firmado de binarios, actualizaciones seguras, datos locales cifrados. |
| **Videojuegos** | Presupuesto de rendimiento (frame budget), gestión de assets, anti-trampas en servidor, sincronización de red, accesibilidad. |
| **Embebido / IoT** | Arranque seguro, actualizaciones OTA firmadas, sin credenciales por defecto. |
| **IA / ML** | Versionado de datos y modelos, evaluación de sesgos, monitoreo de deriva. |
| **Empresarial** | Integraciones, auditoría, segregación de funciones, cumplimiento. |

---

## 10. Checklist global

| Categoría | Buenas prácticas clave | Estado |
|---|---|---|
| **Estructura** | Repo con estructura modular, `main` protegida, PRs obligatorios. | ☐ |
| **Seguridad** | Modelado de amenazas, validación de entradas, MFA, secretos fuera del código. | ☐ |
| **Cadena de suministro** | Lockfile, Dependabot/Renovate, escaneo de dependencias, SBOM. | ☐ |
| **Código limpio** | Linter y formateador automáticos, revisión de código activa. | ☐ |
| **Pruebas** | Unitarias + integración en CI, cobertura monitoreada. | ☐ |
| **Automatización** | CI/CD con validaciones, IaC, rollback probado. | ☐ |
| **Observabilidad** | Logs estructurados, métricas, alertas accionables. | ☐ |
| **Datos y privacidad** | Minimización, cifrado, aviso de privacidad, retención definida. | ☐ |
| **Resiliencia** | Backups con restauración probada, plan de respuesta a incidentes. | ☐ |
| **Documentación** | README, SECURITY, CHANGELOG, ADRs y API docs al día. | ☐ |

> 💡 Aplícala antes de cada despliegue importante o auditoría.

---

## 11. Referencias

**Seguridad y normativa** *(verifica siempre la edición vigente)*
- [OWASP Top 10:2025](https://owasp.org/Top10/2025/)
- OWASP ASVS (Application Security Verification Standard) y OWASP SAMM
- ISO/IEC 27001:2022
- PCI DSS v4.0.x
- NIST Cybersecurity Framework 2.0
- NIST SSDF (SP 800-218) — Secure Software Development Framework
- [GDPR (texto oficial, EUR-Lex)](https://eur-lex.europa.eu/eli/reg/2016/679/oj)
- SLSA y OpenSSF Scorecard

**Ingeniería**
- [The Twelve-Factor App](https://12factor.net/)
- [Conventional Commits](https://www.conventionalcommits.org/es/v1.0.0/)
- [Versionado Semántico](https://semver.org/lang/es/)
- [Keep a Changelog](https://keepachangelog.com/es-ES/)
- Google Engineering Practices · Microsoft SDL · DORA (*Accelerate*)

---

## 12. Registro de cambios (v1.0 → v2.0)

- ➕ **OWASP Top 10:2025** (reemplaza la edición anterior), con sección propia de seguridad.
- ➕ **Cadena de suministro**, SBOM, SLSA, escaneo de secretos.
- ➕ **Modelado de amenazas**, ADRs, Zero Trust, passkeys/MFA.
- ➕ **Observabilidad** (OpenTelemetry), SLOs, métricas DORA.
- ➕ **IA**: uso responsable de asistentes de código y riesgos en apps con LLMs.
- ➕ **Videojuegos** con puntos concretos y **privacidad** para México (LFPDPPP).
- 🔄 Flujo de ramas simplificado (trunk-based) en lugar de `main/dev/feature/hotfix`.
- 🔄 Enlaces a módulos "próximamente" sustituidos por documentos reales del repo.
- 🔄 GDPR enlaza a la fuente oficial; NIST CSF actualizado a 2.0.
- ❌ Se elimina la mención a "Sandbox Solutions" como framework base (no era verificable).
