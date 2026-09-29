# SaludCoop IPS — Arquitectura Tecnológica TOGAF

## Descripción

Este repositorio contiene el desarrollo de la **Fase D — Arquitectura Tecnológica** del proyecto de arquitectura empresarial para **SaludCoop IPS**, desarrollado bajo el marco de trabajo **TOGAF**.

La propuesta busca establecer una arquitectura tecnológica que permita mejorar la **integración entre los sistemas existentes, la seguridad, la disponibilidad, la resiliencia, la trazabilidad y la interoperabilidad** de los servicios tecnológicos de SaludCoop IPS.

La arquitectura se plantea para el **MVP de la Región Central**, manteniendo los sistemas actuales y concentrando la transformación principalmente en la capa tecnológica y de integración.

---

## Integrantes

* **Violeta Sofía Carrasquilla**
* **Nicolas Steven Romero León**
* **Diego Ferney Rojas Romero**
* **Andres Felipe Correcha Meneses**

**Profesor:** Oscar Darío Sánchez Pérez
**Asignatura:** Arquitectura de Sistemas II
**Universidad Central — Bogotá D.C.**
**Proyecto:** Fase D — TOGAF

---

## Objetivo

Diseñar la arquitectura tecnológica objetivo de SaludCoop IPS, definiendo los componentes, servicios, estándares, mecanismos de integración y capacidades de infraestructura necesarios para soportar las arquitecturas de negocio, datos y aplicaciones previamente establecidas.

La propuesta busca principalmente:

* Mejorar la interoperabilidad entre los sistemas.
* Eliminar las integraciones directas mediante SQL, Excel y FTP.
* Centralizar la integración mediante un **API Gateway**.
* Adoptar **HL7/FHIR** como estándar para el intercambio de información clínica.
* Implementar mecanismos de resiliencia como **Circuit Breaker** y **Rate Limiting**.
* Implementar mensajería asíncrona para la sincronización.
* Mejorar la seguridad mediante **OAuth 2.0 / OpenID Connect**, TLS 1.3 y AES-256.
* Incorporar auditoría y trazabilidad de las operaciones.
* Permitir escalabilidad progresiva desde el MVP hasta un despliegue nacional.

---

## Situación actual

La arquitectura tecnológica existente presenta diferentes mecanismos de integración entre las aplicaciones:

| Aplicación            | Tecnología / Paradigma        | Principal problemática                            |
| --------------------- | ----------------------------- | ------------------------------------------------- |
| HCL (APP-01)          | Monolito + BD relacional      | Bloqueos y problemas con alta concurrencia        |
| SHEC (APP-02)         | Arquitectura N-Capas + SQLite | Sincronización mediante FTP y conflictos de datos |
| Telemedicina (APP-03) | Microservicios + sockets      | Límite de 5 conexiones simultáneas                |
| Agendamiento (APP-04) | SaaS externo                  | Falta de integración directa con HCL              |

Actualmente existen integraciones mediante **SQL directo, FTP y archivos Excel**, lo que dificulta el gobierno, mantenimiento, interoperabilidad y trazabilidad de la información.

---

## Arquitectura tecnológica objetivo

La arquitectura propuesta mantiene las cuatro aplicaciones existentes y agrega una capa tecnológica que permite controlar y estandarizar las comunicaciones.

### Componentes principales

```text
                    ┌─────────────────────────┐
                    │      Usuarios / IPS     │
                    └────────────┬────────────┘
                                 │
                                 ▼
                    ┌─────────────────────────┐
                    │      API Gateway        │
                    │ REST / HTTPS            │
                    │ Rate Limiting           │
                    │ Circuit Breaker         │
                    └────────────┬────────────┘
                                 │
             ┌───────────────────┼───────────────────┐
             │                   │                   │
             ▼                   ▼                   ▼
      ┌─────────────┐     ┌─────────────┐     ┌─────────────┐
      │   FHIR      │     │   Broker    │     │ Identidad   │
      │ Integration │     │  Mensajería │     │ OAuth/OIDC  │
      └──────┬──────┘     └──────┬──────┘     └─────────────┘
             │                   │
             └──────────┬────────┘
                        ▼
          ┌──────────────────────────────┐
          │      Sistemas existentes     │
          │                              │
          │ HCL │ SHEC │ Telemedicina   │
          │             │ Agendamiento   │
          └──────────────────────────────┘
                        │
                        ▼
          ┌──────────────────────────────┐
          │ MPI + Visor Clínico Único   │
          └──────────────────────────────┘

       Seguridad / Auditoría / Monitoreo
                 como servicios
                 transversales
```

La arquitectura objetivo contempla un **API Gateway, servidor/repositorio FHIR, broker de eventos, clúster de alta disponibilidad, MPI, proveedor de identidad, gestión de secretos, auditoría y monitoreo**.

---

## Principios tecnológicos

La arquitectura se basa en seis principios principales:

1. **Acceso a datos mediante servicios**
   Los sistemas no deben acceder directamente a las bases de datos de otros sistemas.

2. **Prioridad a estándares abiertos**
   Se priorizan tecnologías y estándares como HL7/FHIR, REST y OAuth 2.0/OIDC.

3. **Reutilizar antes de reemplazar**
   Las aplicaciones existentes se conservan y adaptan cuando sea posible.

4. **Resiliencia y degradación controlada**
   La falla de un componente no debe provocar una falla en cadena.

5. **Seguridad y auditoría por diseño**
   El cifrado, control de acceso y trazabilidad forman parte de la plataforma.

6. **Escalabilidad gradual**
   La arquitectura comienza con el MVP de la Región Central y permite su posterior expansión.

---

## Estándares y tecnologías

| Estándar / Tecnología       | Uso                                |
| --------------------------- | ---------------------------------- |
| **HL7 FHIR**                | Intercambio de información clínica |
| **REST / HTTPS**            | Comunicación entre servicios       |
| **TLS 1.3**                 | Cifrado de comunicaciones          |
| **AES-256**                 | Cifrado de información almacenada  |
| **OAuth 2.0 / OIDC**        | Autenticación y autorización       |
| **SSO**                     | Inicio de sesión centralizado      |
| **Mensajería asíncrona**    | Sincronización de sistemas         |
| **API Gateway**             | Control y gobierno de APIs         |
| **Circuit Breaker**         | Prevención de fallas en cascada    |
| **Rate Limiting**           | Control del tráfico                |
| **OpenTelemetry + Grafana** | Monitoreo y trazabilidad           |
| **ArchiMate / UML / BPMN**  | Modelado de arquitectura           |

---

## Componentes tecnológicos propuestos

Los principales bloques de construcción de la arquitectura objetivo son:

* **API Gateway**
* **Servidor / repositorio FHIR**
* **Broker de eventos**
* **Clúster activo-activo**
* **Módulo Identificador Maestro de Pacientes (MPI)**
* **Visor Clínico Único**
* **Proveedor de identidad**
* **Gestión de llaves y secretos**
* **Repositorio de auditoría inmutable**
* **Monitoreo y observabilidad**
* **Entornos controlados de desarrollo, pruebas y producción**
* **Soporte para operación offline**

La selección definitiva de productos debe realizarse posteriormente mediante una evaluación de alternativas considerando estándares, costos, compatibilidad, disponibilidad y facilidad de reemplazo.

---

## Mejoras propuestas

### Integración

Se eliminan progresivamente:

* Consultas SQL directas.
* Integraciones manuales mediante Excel.
* Transferencias nocturnas mediante FTP.

Y se reemplazan por:

* APIs REST.
* API Gateway.
* HL7/FHIR.
* Mensajería asíncrona.
* Contratos de integración.

### Seguridad

Se propone implementar:

* TLS 1.3.
* AES-256.
* OAuth 2.0 / OIDC.
* SSO.
* MFA.
* Gestión centralizada de llaves y secretos.
* Pseudonimización en ambientes no productivos.
* Auditoría inmutable.

### Disponibilidad y resiliencia

Se incorporan:

* Clúster activo-activo.
* Balanceo.
* Circuit Breaker.
* Rate Limiting.
* Réplica de lectura.
* Mecanismos de contingencia.
* Operación offline.
* Monitoreo extremo a extremo.

---

## Flujo de ejemplo: Telemedicina

El nuevo flujo de una solicitud de teleconsulta es:

```text
SaaS Agendamiento
        │
        ▼
   API Gateway
        │
        ├── Validación del token
        │
        ├── Rate Limiting
        │
        ▼
       MPI
        │
        ├── Verificación del paciente
        │
        ▼
   Telemedicina
        │
        ▼
   Confirmación / "Sin cupo"
```

De esta forma, el sistema evita enviar directamente solicitudes hacia los sistemas internos y permite controlar el tráfico antes de llegar a los servicios críticos.

---

## Sincronización offline de SHEC

Para las tabletas utilizadas por SHEC se propone reemplazar la sincronización mediante FTP por un esquema basado en eventos:

```text
Tableta SHEC
     │
     ▼
SQLite local cifrado
     │
     │ Sin conectividad
     ▼
Almacenamiento local
     │
     │ Recuperación de conexión
     ▼
Broker de eventos
     │
     ▼
HCL
     │
     ├── Sin conflicto → Aplicar
     │
     └── Con conflicto → Bandeja de conciliación
```

La meta establecida es realizar la sincronización en un máximo de **15 minutos después de recuperar la conectividad**.

---

## Requisitos tecnológicos principales

Entre los requisitos definidos se encuentran:

* Disponibilidad mínima del **99,9 %** para componentes críticos.
* TLS 1.3 para información en tránsito.
* AES-256 para información almacenada.
* Rate Limiting.
* Circuit Breaker.
* Colas asíncronas.
* OAuth 2.0 / OIDC y SSO.
* Auditoría inmutable.
* Despliegues sin interrupción.
* Escalabilidad de Telemedicina.
* Persistencia y exposición de recursos FHIR.
* Monitoreo y trazabilidad extremo a extremo.
* Mecanismos de contingencia.
* Escalabilidad del MVP hacia un despliegue nacional.

---

## Hoja de ruta

La implementación propuesta se organiza alrededor de los siguientes componentes:

### 1. API Gateway y FHIR

Implementación de la plataforma central de integración para habilitar REST/HTTPS, HL7/FHIR, Rate Limiting y Circuit Breaker.

### 2. Broker de mensajería

Reemplazo de la sincronización nocturna mediante FTP por mensajería asíncrona y eventos.

### 3. Seguridad e identidad

Implementación de OAuth 2.0/OIDC, SSO, auditoría inmutable y gestión de llaves.

### 4. MPI y Visor Clínico

Implementación del identificador maestro de pacientes y del Visor Clínico Único.

### 5. Escalamiento de sistemas existentes

Ajuste de Telemedicina y cierre de las consultas SQL directas sobre HCL.

### 6. Alta disponibilidad y monitoreo

Implementación de clúster, monitoreo extremo a extremo y despliegue controlado para el MVP.

La hoja de ruta completa está definida en la sección 11.3.5 del documento.

---

## Indicadores objetivo

| Indicador                                  | Situación actual | Objetivo |
| ------------------------------------------ | ---------------: | -------: |
| Conexiones directas entre sistemas         |                6 |        0 |
| Intercambios mediante FHIR                 |              0 % |    100 % |
| Disponibilidad Gateway / Visor             |        No medida | ≥ 99,9 % |
| Errores críticos 504                       |        Por medir |        0 |
| Sobrescrituras por sincronización          |        Por medir |        0 |
| Transacciones en auditoría inmutable       |              0 % |    100 % |
| Citas en pacientes inactivos / overbooking |        Por medir |        0 |

Estos indicadores se plantean como mecanismos para medir posteriormente la efectividad de la arquitectura implementada.

---

## Documentación

La documentación completa de la arquitectura se encuentra en el documento:

**Proyecto Fase D — Caso de estudio: SaludCoop IPS**

El documento contiene:

* Principios tecnológicos.
* Modelos de referencia.
* Catálogos tecnológicos.
* Matrices.
* Diagramas.
* Requisitos tecnológicos.
* Arquitectura tecnológica base.
* Arquitectura tecnológica objetivo.
* Análisis de brechas.
* Hoja de ruta.
* Impactos sobre las demás arquitecturas.
* Revisión formal con las partes interesadas.

---

## Herramientas utilizadas

* **TOGAF** — Marco de arquitectura empresarial.
* **ArchiMate** — Modelado de arquitectura.
* **Archi** — Elaboración de modelos y diagramas.
* **draw.io / Lucidchart** — Diagramación.
* **UML / BPMN** — Modelado de procesos y comportamiento.
* **GitHub** — Control y publicación del proyecto.

---

## Repositorio

Repositorio oficial del proyecto:

**Fase-D-TOGAF-Saludcoop**

https://github.com/nromerol/Fase-D-TOGAF-Saludcoop

---

## Conclusión

La Arquitectura Tecnológica propuesta para SaludCoop IPS busca evolucionar la infraestructura existente sin reemplazar innecesariamente los sistemas actuales.

La transformación se concentra en establecer una capa tecnológica común basada en **integración, interoperabilidad, seguridad, resiliencia, auditoría y observabilidad**, permitiendo que las aplicaciones existentes puedan continuar operando mientras se reducen las dependencias directas entre ellas.

La arquitectura propuesta establece una base para el MVP de la Región Central y permite una evolución posterior hacia un despliegue de mayor escala.
