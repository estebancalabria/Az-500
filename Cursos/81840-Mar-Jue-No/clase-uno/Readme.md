# Clase Uno - 11 de Junio del 2026

# Roadmap

* Fundamentos de Azure
* Seguridad de Identidades y Microsoft Entra
* Seguridad de Redes (Virtual Netrwork)
* Seguridad de Computo (Virtual Machines y Mas)
* Seguridad de Storage
   * Storage Account
   * Disks
* Seguridad en Bases de Datos
* Microsoft Defender For Cloud
* Microsoft Sentinel
   * SIEM (Base de datos de satos de seguridad de distintas fuentes)
   * SOAR (Automatizaciond de respuestas)

# Links

* https://portal.azure.com/
* https://learn.microsoft.com/en-us/credentials/certifications/azure-security-engineer/
* Github Oficial
    * https://github.com/MicrosoftLearning/AZ500-AzureSecurityTechnologies
    * https://microsoftlearning.github.io/AZ500-AzureSecurityTechnologies/
* IG PRofesional del profesor
   * Obligatorio seguirlo o no se puede aprobar el examen
   * https://www.instagram.com/mct.esteban.calabria/
* Azure Datacerter
   * https://www.youtube.com/watch?v=80aK2_iwMOs&t=2s
  

# Uso de Laboratorio

* Usar los laboratorios de
    * https://labs.xtremelabs.io/
* Nos conectamos en el laboratorio y sacamos el Cloud express pass
* Nos conectamos con ese usuario / Clave en una ventana de incognito al portal de azure
* 2 veces por clase

# Novedades

* Se puede sacar una cuenta para jugar con microsoft en:
   * https://developer.microsoft.com/en-us/microsoft-365/dev-program

# Microsoft Entra

* Se puede acceder desde:
   * Portal de Azure
   * O desde https://entra.microsoft.com/auth/login

* Conceptos
  * Autenticacion
      * Verificar que la persona es quien dice ser
  * Autorizacion
      * Permisos a donde puede acceder el usuario
  * Identidad
      * Algo que se puede autenticar en el microsoft entra
          * Usuario
              * De Mi Tennant
              * Externos 
          * Aplicaciones (Enterprise Applications, App Registration, ...)
              * Service Principal
          * Servicio de Azure (Virtual Machine)
              *  Managed Identity
          *  Devices (dispositivos)
* Sincronizacion con AD local
    * Entra Connect / Cloud Sync
    * Requiere instalar on Premise un software              

## Gobernanza de Identidades

* Authenticacion
  * Crear Usuarios y Grupos de Usuarios
  * Ver que usuarios de Loguearon
      * Entra -> Monitoring -> Sign-In-Logins
  * Darle permiso a un usuario tempral y on demand
      * PIM (Priviledge Identity Management)
      * Workflow : Me pide acceso, lo autorizo, el acceso tiene un tiempo X
  * Verificar permisos de los usuarios y darle de baja usuarios inactivos
      * Access Reviews
  * Dar accesos temporales (no on demand) que se renuevan
      * Etitlement Management
      * Accesos temporales con vencimiento automatomatico y posibilidad de renovacion
  * Definir reglas de acceso dependiendo ubicacion/dispositivo
      * Si un usuario accede desde una ubicacion atipica -> Le pido MFA
      * Conditional Access
  * Definir las reglas de password que exijo en mi organizacion
      * Password Protection
  * Ver usuarios riesgosos
      * Un usuario se conecta de Argentina al 12:00 y de tokio a las 12:05
      * Identity Protection
*Autorizacion
  * Administracion de permisos (RBAC) << Luego lo vemos

# Defender For cloud

* Se puede acceder desde:
   * Portal de azure
   * https://security.microsoft.com/ (XRD)

# Glosario

* OnPremise (Local)
* Tennant o Inquilino
   * Instancia de Microsoft Entra (ex Active Directory)
   * Dominio (@xtremelabs.io)
* Paired Regions
