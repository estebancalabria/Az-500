# Clase Cinco - 25 de Junio del 2026

# Repaso

* Seguridad en Contenedores
  * App Services
  * Container Instances
  * Container Apps
  * Kubernetes
* Registros de Contenedores
  * Publicos
    * Docker Hub
    * Microsoft Artifact Registru
  * Privados
    * (ACR) Azure Container Registry
* Desplegar un contenedor de un container intance (ACI)
* Empezamos a Deplegar contenedores en Kubernetes
    * Dockerfile
* Policies
  * Gobernanza
  * Una forma proactiva de tomar medidas de seguridad antes de que el problema ocurra

---

# Seguridad en Base de Datos

* Opciones para desplegar BD en Azure
    * SQL Server
      * SQL on VM             
      * SQL Managed Intance
      * Azure SQL Server
    * Oracle
    * MySQL / Postgres
    * No relacionales : CosmoDB
    * Cache for Redis

## Auticancion en SQL Server

<img width="524" height="239" alt="image" src="https://github.com/user-attachments/assets/92ba8a62-476e-4c8e-996d-7144521e1e20" />

## Encriptacion de Bases de Datos

* Tipos de Encriptaciones
  * Encription at Rest
    * TDE (Transparent Data Encription)
      * Clave de Microsoft
      * Clave Propia 
  * Encription in transit


## PAsos a Seguir

* Crear el Resource Group

* Crear SQL Server
  * Elegir un nombre unico
  * Autenticacion tanto SQL Server como por Entra
    * Definir un usuario y password

* En la parte de networking habilito mi ip para poder conectarme a ese Server
  * Aca configuro los permisos de conectividad a mi servidor
  * Por defecto no se puede conectar nadie

 * Probar conectarme al SQL server de Azure desde mi Maquina con el Management Studio

 * Crear una base de datos
   * Ponerle un nombre
   * Permitir el acceso a mi ip
   * Cargarle una base de datos de prueba por defecto
  

* Probar una consulta a la Base de datos en el Query Editor en el portal y desde el Management Studio desde mi pc

```
select * from SalesLT.Product
```

* Habilitar la seguridad y clasificacion de datos
    * Azure me detecta automaticamente las columnas sensibles
    * Si yo quiero puedo configurar cual es la columna es sensible
 
* Habilitar la auditoria en un stroage account

* Tirar el query sobre la base (Este query hace consulta sobre datos sensibles)

```
select FirstName,LastName from SalesLT.Customer
```

* Ver los registros de auditoria y como me avisa cuando se accede a datos sensibles
  
# Microsoft Defender

* Microsoft Defender for Cloud
  * Mira seguridad en todos los recursos de Azure
    * La VM tiene puerto 80 http abierto y eso es inseguro
    * El SQL server no tiene bloqueos por IP especifica
    * El Storage Account no tiene seguridad configurada
* Microsoft Defender For SQL
  * Vulnerabilidades mas detalladas de SQL
  * SQL Injection
  * Acceso a base de datos desde una ubicacion desconocida
  * Hay datos sensibles que fueron consultados por unsuario desconocido
