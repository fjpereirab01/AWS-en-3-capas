

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
```
##Instalacion de la estructura
El primer paso es crear una vpc. Para ello en el buscador escribe VPC y crear una vpc. Pon el nombre que quieras ( en mi caso, VPC-fj ).

<img width="1425" height="280" alt="VPC" src="https://github.com/user-attachments/assets/0e724ab8-a92f-4cba-baa1-b29edc5ecd85" />

Ahora crea las subredes. Necesitaremos 3 subredes ( una pública y 2 privadas ) 
* Pública = 10.0.1.0
* Privada1 = 10.0.2.0
* Privada2 = 10.0.3.0
<img width="1548" height="282" alt="subredes" src="https://github.com/user-attachments/assets/d90b6615-f362-4ff1-9618-bb0d35e3dd27" />
Crea una gateway
<img width="1445" height="325" alt="gateway" src="https://github.com/user-attachments/assets/787a6287-dd69-4790-bd0f-e5416037e353" />
Ahora crea 2 tablas de enrutamiento. 1 de ellas se enlazara con la subred pública y la otra subred ira enlazada a las subredes privadas. Dentro de la tabla de enrutamiento elige la opción editar asociaciones de subredes.
<img width="1445" height="325" alt="gateway" src="https://github.com/user-attachments/assets/5a8a388b-d5c6-4f0f-9cf0-658e63e47622" />
Haz lo mismo pero añade las privadas
<img width="1532" height="327" alt="enrutamiento_privada" src="https://github.com/user-attachments/assets/b7ea0900-2b90-4fbd-b48e-d6ced84d0636" />
##Grupos de seguridad
Necesitas crear 1 grupo de seguridad por instancia:
* Balanceador
* Servidores web
* NFS
* Base de datos
  En el apartado de seguridad, clicka sobre grupo de seguridad y crear grupo de seguridad. Debes ponerle un nombre y añadir las siguientes reglas de entrada
  #Balanceador
  <img width="1513" height="327" alt="gs_balanceador" src="https://github.com/user-attachments/assets/fe9d685d-a04e-4fe5-8dc5-4619e432f56e" />
  #Web
  <img width="1478" height="336" alt="gs_web" src="https://github.com/user-attachments/assets/f1adaff7-9e0a-4606-a350-747b75340d1b" />
  #NFS
  <img width="1422" height="326" alt="gs_nfs" src="https://github.com/user-attachments/assets/b6d2d3d8-24d3-4f1a-96ae-8989da74559b" />
  #Base de datos
  <img width="1498" height="377" alt="image" src="https://github.com/user-attachments/assets/7fca4e34-b5a3-4fb9-9b97-ac9121387d87" />
##Instancias
Pon en el buscardor EC2 y en el apartado de Instancias, crear nueva instancia.
#Balanceador
La primera instancia a crear es el balanceador.
Debemos asignarle un nombre y como ISO usaremos debian. Elige tu vpc y como grupo de seguridad elige la sg del balanceador. Le asignamos la red pública y que amazon eliga nuestra ip pública. En detalles avanzados pon el siguiente scripts:
```
```
<img width="1385" height="387" alt="balanceador" src="https://github.com/user-attachments/assets/ddcb25d6-914f-486e-a2ef-0631c7b22ae6" />
#Servidores web ( 2 veces: web1 y web2 ) 

A esta instancia asignale tu vpc y la red privada 1. En este caso no permitiremos que amazon eliga ip pública y su sg sera el web. Pon el siguiente script para ambas instancias:
```
```
<img width="1318" height="352" alt="web" src="https://github.com/user-attachments/assets/a40caf7c-ad0e-4c67-a76b-79ba6957b533" />
#NFS
Realiza la misma configuracion para los servidores web cambiando el sg por el sg del nfs y pon el siguiente script:
```
```
<img width="1283" height="375" alt="image" src="https://github.com/user-attachments/assets/7be51094-a8d7-42ee-ad9e-3b40ae481a74" />
#Base de datos
Realiza la configuración asociandolo a tu vpc y eligiendo la red privada 2. El sg sera el de la BD y su script es:
```
```
<img width="1385" height="367" alt="image" src="https://github.com/user-attachments/assets/85c3d4ef-048f-4e00-8e64-e1b5705f461a" />

##Comprobación 

Ejecuta en tu navegado lo siguiente:
```
https://fjpereirab.online
```
##Conclusión

El despliegue de esta infraestructura **WordPress en arquitectura 3-Tier sobre AWS** cumple con los principales principios de los entornos cloud modernos: **alta disponibilidad, escalabilidad, seguridad y segmentación por capas**.

Gracias a la separación de responsabilidades entre:

-  **Capa de balanceo con terminación SSL**
-  **Capa web distribuida con almacenamiento compartido por NFS**
-  **Capa de base de datos totalmente aislada en red privada**

se garantiza un sistema robusto, tolerante a fallos y preparado para crecer horizontalmente sin afectar al servicio.

Además, el uso de **Security Groups, subredes privadas, VPC dedicada y comunicaciones internas controladas** refuerza la seguridad del entorno, reduciendo la superficie de ataque al mínimo imprescindible.

Este proyecto demuestra de forma práctica:

- La correcta implementación de una **arquitectura 3-Tier real en AWS**
- El uso de **alta disponibilidad en WordPress**
- La aplicación de **buenas prácticas en redes, seguridad y despliegue de servicios**
- El uso eficiente de **Linux, Apache, NFS, MariaDB y SSL**

Como resultado final, se dispone de una plataforma **100% funcional, segura y preparada para producción**, ideal tanto para entornos empresariales como para proyectos profesionales y formativos.

---



