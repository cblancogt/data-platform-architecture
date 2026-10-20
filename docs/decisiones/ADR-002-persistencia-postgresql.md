# ADR-0002: Persistencia de datos de PostgreSQL

## Estado

Aceptado

## Contexto

PostgreSQL 18 se ejecuta dentro de un contenedor Docker. El ciclo de vida del contenedor no determina el ciclo de vida de los datos almacenados por el motor.

El contenedor puede detenerse, eliminarse o recrearse como parte de la operación normal de la plataforma.

## Decisión

Utilizar un Docker Named Volume independiente del contenedor para almacenar los archivos administrados por PostgreSQL.

El volumen se denomina: `dp-postgresql-volume`

y se monta dentro del contenedor en: `/var/lib/postgresql`

La definición y asociación del volumen se mantiene en Docker Compose.

## Justificación

La separación entre cómputo y almacenamiento permite tratar el contenedor PostgreSQL como un componente reemplazable.

PostgreSQL puede ejecutarse nuevamente en un contenedor recreado mientras el volumen persistente conserva las bases de datos y sus objetos.

Esta decisión evita depender del ciclo de vida de un contenedor específico para conservar la información.

## Consecuencias

### Positivas

- El contenedor puede recrearse sin perder las bases de datos.
- El almacenamiento posee un ciclo de vida independiente.
- El despliegue mantiene explícita la relación entre PostgreSQL y su almacenamiento.
- La infraestructura puede reproducirse mediante Docker Compose.

### Consideraciones

- Eliminar explícitamente el volumen elimina también los datos persistentes.
- La persistencia del volumen no sustituye una estrategia de backup.
- La disponibilidad de los datos continúa dependiendo del host que contiene el volumen.
- Alta disponibilidad y recuperación ante desastres quedan fuera del alcance de V2.

## Validación

La decisión fue validada mediante una prueba de destrucción y recreación del contenedor.

Se utilizó la base de datos `PlatformLab` y se creó la tabla `PersistenceTest`, almacenando un registro de prueba.

Posteriormente se eliminó `dp-postgresql-container`, conservando `dp-postgresql-volume`.

Al desplegar nuevamente el servicio mediante Docker Compose, PostgreSQL utilizó el volumen existente.

La base de datos, la tabla y el registro permanecieron disponibles después de recrear el contenedor.

La información fue validada mediante pgAdmin.

## Seguridad y protección de datos

La validación realizada en V2 demuestra persistencia de los datos ante la eliminación y recreación del contenedor.

Esta prueba no constituye una validación completa de seguridad, disponibilidad ni recuperación de la información.

Los siguientes controles permanecen pendientes de diseño, implementación y validación:

- Cifrado de datos en reposo.
- Cifrado de comunicaciones mediante TLS.
- Gestión y rotación de credenciales.
- Principio de mínimo privilegio y control de acceso.
- Estrategia de backup y restore.
- Protección y cifrado de backups.
- Auditoría y trazabilidad.
- Validación de permisos sobre el almacenamiento persistente.
- Alta disponibilidad.
- Recuperación ante desastres.

### Estado actual de protección

| Capacidad | Estado |
|---|---|
| Persistencia ante recreación del contenedor | Validada |
| Separación de credenciales del repositorio | Validada |
| Cifrado de comunicaciones | Pendiente |
| Cifrado en reposo | Pendiente |
| Backup y restore | Pendiente |
| Cifrado de backups | Pendiente |
| Control de acceso y mínimo privilegio | Pendiente |
| Auditoría y trazabilidad | Pendiente |
| Protección del almacenamiento persistente | Pendiente |
| Alta disponibilidad | Fuera del alcance de V2 |
| Recuperación ante desastres | Fuera del alcance de V2 |

## Resultado

La prueba confirma que el ciclo de vida de los datos de PostgreSQL es independiente del ciclo de vida del contenedor.

V2 demuestra el despliegue de PostgreSQL 18 con almacenamiento persistente y separación básica de configuración sensible.

Las capacidades de protección pendientes quedan registradas para su implementación y validación en versiones posteriores.