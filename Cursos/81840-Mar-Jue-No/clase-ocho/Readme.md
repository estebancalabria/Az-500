# Clase Ocho - 7 de julio 2026

# Repaso

* Seguridad en Base de Datos
  * Guardamos en el Key Vault el String de conexion
  * Hicimos una maquina virtual con el SSMS y el dotnet
  * Nos conectamos al SQL Server desde la virtual
  * Creamos una app de.net que leia el connection string del key vault
* Conecion mediante VPN Gatewat
  * Formas de conectar una maquina a la VPN
    * Por usuario y clave  <<<<<
    * Por certificado

# Seguridad en los Storage Account

* Crear el Resource Group
  * rg-az500-clase-ocho

* Empezamos a crear el storafe account csaz500c8esteban

## Seguridad de redundancia de datos

* Por defecto los Storage Account tienen replicacion
* Tengo dos opciones de replicacion
  * LRS : Se copia en otro rack el SA  << Es la que elegimos por defecto
  * GRS : Se copia en otra region

# Seguridad de Costos (Access Tiers)

* Hot  : Barato y rapido el acceso, mas caro el almacenamiento
* Cool : Sale intermedio el acceso y el almacenamiento
* Cold : Caro el acceso, barato el almacenamiento (Backups)
* Archive : Baratisimo el almacenamiento, El acceso es caro y hay que esperar dos horas para acceder

# Seguridad de acceso

* El acceso puede ser
  * Publico (Desde interenet)
  * Privado (Solo dentro de una vnet de Azure)
 
# Seguridad ante ataques

* Un usuario malicioso (hacker) suele querer borrar los storage account para generar un danio en la empresa
* Soft Delete : Cantidad de dias que un elemento del storage account (blob, file share, queue, table..)
* Versioning : Aunditoria y cambios que se hacen sobre los blob

# Segurada Automatica

* Microsoft Defender
  * Microsoft defender for Storage
    * Acceso desde una ip sospechosa
    * Acceso desde una app sospechosa
    * Acceso desde una ubicacion inusial
    * ...
    * Cantidad de datos extraidos inusal

> [!NOTE]
> Alertas que se registran en el Defender For Storage
> https://learn.microsoft.com/en-us/azure/defender-for-cloud/alerts-azure-storage

# Seguridad de datos

* Toda la informacion que se guarda en un SA esta enctiptada puedo elegir
  * Encriptacion con clave de Microsoft (Transparente)
  * Encriptacion con clave propia (Guardar la clave en un Key Vault)

> Creamos el Storage account

## Seguridad de acceso a Blobs

* Si creo un contenedor publico y un blob publico cualquiera puede acceder al mismo
* Si creo un conetedor privado, sus blobs van a ser privados y no se pueden acceder mendiante su URL
  * Como le doy acceso electivo a algo en un contenedor privado
    * SAS (Shared Access Signature) : Permite crear una url especial y temporal para acceder al recurso
      * Independiente (defininendo los permisos individuales del archivo)
      * Asociada a un Access Policy : (donde ya tengo definido los permisos de ante mano)

## Seguridad en File Shares 

* Publicamente me puedo conectar
  * Autenticacion con Entra
  * Clave del Storage Account
    * Si vamos al SA en Security + Networking -> Access Key
    * Ojo que si se filtra la clave del storage se puede conectar cualquiera

# Crear una VM 

* Crear una VM
  * vm-az500-clase-ocho
* Conectarme a la virtual por RDP
* Conectarme al File Share
  * Nos copiamos el mismo Script que usamos en mi pc local

 * Restrigir el acceso publico  al file share (Firewall del Storage Account)
   * En el SA en Security + Networking -> Solapa Public Access -> Deshabilitar el acceso publico y habilitar solo el acceso para la virtual network de la VM

  * En la misma pantalla Security + Networking voy a crear un private endpoint
    * "+ Create Private Endpoint"
    * Le asigno la ip 10.0.0.5 (una mas que la VM)   
