# V1: Arquitectura de plataforma local

```mermaid
flowchart TB

    subgraph ACCESS[" "]
        direction LR
        REPO["GitHub<br/><b>data-platform-architecture</b>"]
        CLIENT["Windows<br/>SSMS"]
    end

    subgraph HOST["Ubuntu VM · datalab-dev"]
        direction TB

        COMPOSE["Docker Compose<br/>compose.yaml"]

        subgraph DOCKER["Docker Engine"]
            direction TB

            subgraph NETWORK["dp-network"]
                SQL["dp-sql-server-container<br/><b>SQL Server 2022</b>"]
            end

            VOL[("dp-sqlserver-volume<br/>Persistent Storage")]

            SQL ---|"/var/opt/mssql"| VOL
        end

        COMPOSE -->|"deploy"| SQL
    end

    REPO -->|"git clone"| COMPOSE
    CLIENT -->|"TCP 1433"| SQL

    style ACCESS fill:none,stroke:none
```

## Componentes

| Componente | Función |
|---|---|
| `datalab-dev` | Host Linux de la plataforma |
| Docker Engine | Runtime de contenedores |
| `dp-network` | Red dedicada de la plataforma |
| `dp-sql-server-container` | Contenedor del servicio de base de datos |
| SQL Server 2022 | Motor de base de datos |
| `dp-sqlserver-volume` | Almacenamiento persistente |
| `/var/opt/mssql` | Punto de montaje de los datos |
| TCP 1433 | Acceso al servicio desde el exterior |

---

**Carlos Roberto**