# V1: Arquitectura de plataforma local

```mermaid
flowchart TB

    CLIENT["Windows<br/>SQL Server Management Studio"]

    subgraph HOST["Ubuntu VM · datalab-dev"]
        direction TB

        subgraph DOCKER["Docker Engine"]
            direction TB

            subgraph NETWORK["dp-network"]
                SQL["dp-sql-server-container<br/><b>SQL Server 2022</b>"]
            end

            VOL[("dp-sqlserver-volume<br/>Persistent Storage")]

            SQL ---|"/var/opt/mssql"| VOL
        end
    end

    CLIENT -->|"TCP 1433"| SQL

    style HOST text-align:left
    style DOCKER text-align:left
    style NETWORK text-align:left
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