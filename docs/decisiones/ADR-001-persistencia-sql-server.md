# ADR-0001: Persistencia de datos de SQL Server

## Estado

Aceptado

## Contexto

SQL Server 2022 se ejecuta dentro de un contenedor Docker. El ciclo de vida del contenedor no determina el ciclo de vida de los datos almacenados por el motor.

Un contenedor puede detenerse, eliminarse o recrearse como parte de la operación normal de la plataforma.

## Decisión

Utilizar un Docker Named Volume independiente del contenedor para almacenar los archivos administrados por SQL Server.

El volumen se denomina: dp-sqlserver-volume

y se monta dentro del contenedor en: /var/opt/mssql

La definición y asociación del volumen se mantiene en Docker Compose.

## Justificación

La separación entre cómputo y almacenamiento permite tratar el contenedor como un componente reemplazable.

SQL Server puede ejecutarse nuevamente en un contenedor recreado mientras el volumen persistente conserva los archivos del motor y las bases de datos.

Esta decisión también evita depender del identificador o ciclo de vida de un contenedor específico para conservar la información.

## Consecuencias

### Positivas

- El contenedor puede recrearse sin perder las bases de datos.
- El almacenamiento posee un ciclo de vida independiente.
- El despliegue mantiene explícita la relación entre SQL Server y su almacenamiento.
- La infraestructura puede reproducirse mediante Docker Compose.

### Consideraciones

- Eliminar explícitamente el volumen elimina también los datos persistentes.
- La persistencia del volumen no sustituye una estrategia de backup.
- La disponibilidad de los datos continúa dependiendo del host que contiene el volumen.
- Alta disponibilidad y recuperación ante desastres quedan fuera del alcance de V1.

## Validación

La decisión fue validada mediante una prueba de destrucción y recreación del contenedor.

Se creó la base de datos `PlatformLab` y la tabla `dbo.PersistenceTest`, almacenando un registro con fecha y hora.

Posteriormente se eliminó el contenedor mediante Docker Compose, conservando `dp-sqlserver-volume`.

Al desplegar nuevamente el servicio, Docker creó un nuevo contenedor y montó el volumen existente en `/var/opt/mssql`.

La base de datos, la tabla y el registro original permanecieron disponibles.

## Seguridad y protección de datos

La validación realizada en V1 demuestra persistencia de los datos ante la eliminación y recreación del contenedor.

Esta prueba no constituye una validación completa de seguridad, confidencialidad, disponibilidad ni recuperación de la información.

Los siguientes controles permanecen pendientes de diseño, implementación y validación:

- Cifrado de datos en reposo.
- Transparent Data Encryption (TDE).
- Cifrado de las comunicaciones mediante TLS y certificados confiables.
- Gestión y rotación de credenciales.
- Principio de mínimo privilegio y control de acceso.
- Estrategia de backup y restore.
- Protección y cifrado de backups.
- Gestión y protección de claves criptográficas.
- Auditoría y trazabilidad de accesos.
- Validación de permisos sobre el almacenamiento persistente.
- Alta disponibilidad.
- Recuperación ante desastres.

Estos controles se incorporarán progresivamente en versiones posteriores de la plataforma. Cada capacidad deberá contar con su propia implementación, prueba y evidencia antes de considerarse validada.

### Estado actual de protección

| Capacidad | Estado |
|---|---|
| Persistencia ante recreación del contenedor | Validada |
| Separación de credenciales del repositorio | Validada |
| Cifrado de comunicaciones | Pendiente |
| Certificados confiables | Pendiente |
| Cifrado en reposo / TDE | Pendiente |
| Backup y restore | Pendiente |
| Cifrado de backups | Pendiente |
| Gestión de claves criptográficas | Pendiente |
| Control de acceso y mínimo privilegio | Pendiente |
| Auditoría y trazabilidad | Pendiente |
| Protección del almacenamiento persistente | Pendiente |
| Alta disponibilidad | Fuera del alcance de V1 |
| Recuperación ante desastres | Fuera del alcance de V1 |

## Resultado

La prueba confirma que el ciclo de vida de los datos de SQL Server es independiente del ciclo de vida del contenedor.

V1 demuestra persistencia y separación básica de configuración sensible, pero no declara la plataforma como segura, altamente disponible ni recuperable ante desastres.

Las capacidades de protección pendientes quedan registradas explícitamente para su implementación y validación en versiones posteriores.