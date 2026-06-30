# Clase Seis - 30 de Junio del 2026

# Repaso

* Opciones de despligue SQL Server
  * SQL en una VM
  * SQL Managed Instance
  * Azure SQL
* Seguridad en Bases de Datos (Azure SQL)
  * Autenticacion
      * Autenticacion SQL
      * Microsoft Entra
  * Seguridad de Redes
      * Azure SQL Firewall
  * Encriptacion de Datos
      * Encription at Rest
        * Clave de Microsoft
        * Clave Propia
      * Encription in Transit
  * Auditoria y Monitoreo
      * Clasificacion de Columnas Sensible
      * Auditoria
  * Microsoft Defender for Cloud.
      * Defender for SQL
* Servicios
  * Log analytics Workspace
  * Storage Account
  * Key Vault

---

# Moniteo de Maquinas Virtuales

## Laboratorio

* Crear el Resource Group
  * rg-az500-clase-seis
* Crear un Log Analytics Worskpace
  * log-az500-clase-seis
* Crear un Storage Account (opcional)
  * csaz500c6esteban
* Crear una VM 
  * vm-az500-clase-seis
  * Creamos una pero nos imaginamos un entorno corporativo donde tengamos muchas

> [!NOTE]
> Objetivo: Centraliza el monitoreo de muchas VM en un solo lugar para no tener que conectarme individualmente a cada una de ellas

