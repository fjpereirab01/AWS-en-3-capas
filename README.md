

Infraestructura en AWS organizada en **3 capas** con balanceador Apache, servidores web compartiendo WordPress por NFS y MariaDB aislada en red privada.

---

## Arquitectura General

```text
[Internet] --> [Capa 1: Balanceador Apache + EIP + SSL]
                    |
            Red Privada 10.0.2.0/24
                    |
      +----------------+----------------+
      |                |                |
 [Capa 2 Web1]   [Capa 2 Web2]     [NFS Server]
 10.0.2.45        10.0.2.184        10.0.2.24
      |                |                |
      +----------------+----------------+
                    |
            Red Privada 10.0.3.0/24
                    |
            [Capa 3: MariaDB]
                 10.0.3.111



 Creación de la Infraestructura en AWS



