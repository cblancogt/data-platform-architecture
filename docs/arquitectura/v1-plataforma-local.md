# V1: Plataforma local

## Objetivo

Construir una plataforma local mínima y reproducible capaz de ejecutar un servicio de base de datos con networking y almacenamiento persistente definidos explícitamente.

## Alcance

La primera versión incluye:

- Linux como sistema operativo anfitrión.
- Docker como runtime de contenedores.
- Docker Compose para definir el despliegue.
- Una red dedicada para los servicios de la plataforma.
- SQL Server 2022 como primer servicio de base de datos.
- Almacenamiento persistente independiente del ciclo de vida del contenedor.
- Configuración separada de la definición de infraestructura.

## Requisitos

R01. Ejecutarse sobre Linux.

R02. Contenedores.

R03. Networking explícito.

R04. Disponer de almacenamiento persistente.

R05. Ejecutar SQL Server 2022.

R06. Separar configuración y despliegue.

R07. Permitir recrear el contenedor de SQL Server sin perder los datos persistentes.

R08. Permitir reproducir el despliegue desde el repositorio.

## Arquitectura conceptual
Linux Host  
└── Docker Engine  
    ├── SQL Server 2022  
    ├── Docker Network  
    └── Persistent Volume

## Criterio de validación

Desplegar base de datos SQL Server de forma persistente, para facilidad de trabajo. 