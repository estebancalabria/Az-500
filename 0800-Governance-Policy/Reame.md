# Security Governance in Azure

## Introducción

El módulo se centra en planificar, implementar y administrar la gobernanza de seguridad en Azure, garantizando el cumplimiento de las políticas organizacionales y las mejores prácticas.

## Escenario

Un especialista en seguridad cloud debe proteger los recursos de una organización que utiliza múltiples proveedores y servicios cloud, manteniendo una postura de seguridad sólida e identificando y abordando proactivamente los riesgos.

## Objetivos de aprendizaje

* Aplicar **Azure Policy** para crear y administrar políticas de seguridad y garantizar el cumplimiento.
* Utilizar **Azure Blueprint** para facilitar implementaciones seguras y coherentes de infraestructura.
* Utilizar **landing zones** para mantener una seguridad consistente en Azure y **Azure Key Vault** para administrar datos confidenciales.
* Mejorar la seguridad de las claves mediante recomendaciones de **HSM**, controles de acceso efectivos y procesos periódicos de rotación y backup.

## Objetivo del módulo

Adquirir los conocimientos necesarios para implementar y administrar la gobernanza de seguridad en Azure, alinearla con las políticas organizacionales, identificar y remediar riesgos de seguridad multicloud, mantener el cumplimiento mediante **Azure Policy** y **Azure Blueprint**, protegerse frente a amenazas externas con **Microsoft Defender for Cloud** y garantizar la resiliencia cloud mediante evaluaciones, administración de claves y protección avanzada contra amenazas.

# Microsoft Cloud Security Benchmark

El **Microsoft Cloud Security Benchmark** define controles de seguridad para administrar recursos, proteger datos, identidades y endpoints, realizar backups y recuperación, responder a incidentes, registrar y detectar amenazas, administrar la postura de seguridad y las vulnerabilidades, y establecer estrategias de gobernanza multicloud.

## Asset Management

### AM-1: Track asset inventory and their risks

* Mantener un inventario actualizado de todos los recursos cloud, organizándolos mediante **tags**, agrupaciones y metadatos según servicio, ubicación u otras características.
* Centralizar la información de seguridad y riesgos para facilitar su monitoreo.
* En Azure, utilizar **Microsoft Defender for Cloud inventory** y **Azure Resource Graph** para descubrir recursos.
* Otorgar **Security Reader** a los equipos de seguridad para monitorear riesgos mediante Microsoft Defender for Cloud.

### AM-2: Use only approved services

* Permitir únicamente servicios cloud aprobados mediante auditoría y restricciones sobre los servicios que los usuarios pueden implementar.
* En Azure, utilizar **Azure Policy** para auditar y restringir servicios, **Azure Resource Graph** para descubrir recursos y **Azure Monitor** para generar alertas ante servicios no aprobados.

### AM-3: Ensure security of asset lifecycle management

* Mantener actualizados los atributos y configuraciones de seguridad durante todo el ciclo de vida de los recursos.
* Los procesos deben contemplar cambios en proveedores de identidad y acceso, sensibilidad de datos, configuración de red y privilegios administrativos.
* Identificar y eliminar recursos que ya no sean necesarios.

### AM-4: Limit access to asset management

* Limitar el acceso a las funciones de administración de activos para evitar modificaciones accidentales o maliciosas.
* Usar **Microsoft Entra Conditional Access** para limitar el acceso a Azure Resource Manager.
* Usar **Azure RBAC** para controlar permisos; por ejemplo, `Reader` permite visualizar recursos sin modificarlos.
* Usar **Resource Locks** para impedir eliminaciones o modificaciones.

### AM-5: Use only approved applications in virtual machines

* Permitir únicamente software autorizado mediante listas de aplicaciones aprobadas y bloquear software no autorizado.
* En Azure, utilizar **Microsoft Defender for Cloud adaptive application controls**.
* Utilizar **Azure Automation Change Tracking and Inventory** para recopilar información sobre software instalado en VMs.
* También pueden utilizarse configuraciones específicas del sistema operativo o soluciones externas para limitar la ejecución de scripts y software no aprobado.

## Backup and Recovery

### BR-2: Protect backup and recovery data

* Proteger los backups frente a exfiltración, compromiso de datos, ransomware/malware y usuarios maliciosos.
* Aplicar controles de acceso, cifrado en reposo y en tránsito.
* En Azure, proteger operaciones críticas de **Azure Backup** mediante **MFA** y **Azure RBAC**, separar responsabilidades y utilizar endpoints privados para los Recovery Services vaults.
* Los backups de Azure utilizan cifrado AES de 256 bits con claves administradas por la plataforma; también pueden utilizarse claves administradas por el cliente.
* Con claves administradas por el cliente, utilizar **Azure Key Vault**, **soft delete** y **purge protection**.
* Habilitar **soft delete**, MFA, almacenamiento georredundante o restauración entre regiones y, cuando corresponda, **ZRS** para proteger la recuperación frente a eliminaciones o fallos.

## Data Protection

### DP-2: Monitor anomalies and threats targeting sensitive data

* Detectar transferencias grandes o inusuales de datos que puedan indicar exfiltración no autorizada.
* En Azure, utilizar **Azure Information Protection (AIP)** para monitorear datos clasificados y etiquetados.
* Utilizar **Microsoft Defender for Storage, SQL, open-source relational databases y Cosmos DB** para detectar transferencias anómalas.
* Cuando sea necesario para DLP, utilizar soluciones de Azure Marketplace o Microsoft 365 DLP.

### DP-3: Encrypt sensitive data in transit

* Proteger los datos en tránsito mediante cifrado, especialmente en redes externas y públicas.
* En Azure, aplicar transferencia segura en servicios como Storage.
* Utilizar **HTTPS** y **TLS 1.2 o posterior** para aplicaciones web.
* Para administración remota, utilizar **SSH** en Linux y **RDP/TLS** en Windows.
* Para transferencia segura de archivos, utilizar **SFTP/FTPS** en lugar de FTP.
* El tráfico entre datacenters de Azure está cifrado y TLS 1.2 o posterior está habilitado en la mayoría de los servicios.

### DP-6: Use a secure key management process

* Definir e implementar un proceso empresarial para administrar el ciclo de vida de las claves criptográficas.
* Generar, distribuir, almacenar, rotar y revocar claves según un calendario definido y ante retiro o compromiso.
* En Azure, utilizar **Azure Key Vault** para administrar el ciclo de vida de las claves.
* Para claves administradas por el cliente, utilizar una jerarquía con **DEK** y **KEK**, registrar las claves en Key Vault y utilizar sus identificadores en los servicios.
* **BYOK** permite importar claves protegidas por HSM desde HSM on-premises.
* Niveles FIPS indicados para Azure Key Vault:

  * Claves protegidas por software: **FIPS 140-2 Level 1**.
  * Claves protegidas por HSM en Premium: **Level 2**.
  * Claves protegidas por HSM en Managed HSM: **Level 3**.
* Azure Key Vault Premium utiliza infraestructura HSM compartida; **Managed HSM** utiliza un HSM dedicado.

### DP-7: Use a secure certificate management process

* Definir procesos para controlar todo el ciclo de vida de los certificados.
* Inventariar, monitorear y renovar oportunamente los certificados de servicios críticos mediante mecanismos automatizados.
* En Azure Key Vault se puede gestionar creación/importación, rotación, revocación, almacenamiento y eliminación de certificados.
* Configurar rotación automática cuando esté soportada; de lo contrario, realizar rotación manual.
* Evitar certificados autofirmados y wildcard en servicios críticos.
* Utilizar únicamente autoridades certificadoras aprobadas. Azure Key Vault integra DigiCert y GlobalSign para certificados OV TLS/SSL.

### DP-8: Ensure security of key and certificate repository

* Proteger el repositorio de claves y certificados mediante controles de acceso, seguridad de red, logging, monitoreo y backup.
* En Azure Key Vault:

  * Aplicar **RBAC**, mínimo privilegio y separación de responsabilidades.
  * Activar logging.
  * Utilizar **Private Link** y **Azure Firewall**.
  * Utilizar **managed identities** para acceder a las claves.
  * Activar **soft delete** y **purge protection**.
  * Realizar backup de claves y certificados.
  * Considerar deshabilitar claves en lugar de eliminarlas.
  * Para BYOK, generar las claves en un HSM on-premises e importarlas.
  * Nunca almacenar claves en texto plano fuera de Key Vault.
  * Utilizar claves respaldadas por HSM para obtener mayor protección.
  * Activar **Microsoft Defender for Key Vault** para protección avanzada.

## Endpoint Security

### ES-1: Use Endpoint Detection and Response (EDR)

* Habilitar capacidades **EDR** para VMs e integrarlas con SIEM y procesos de seguridad.
* En Azure, **Microsoft Defender for Servers**, integrado con Microsoft Defender for Endpoint, proporciona EDR para prevenir, detectar, investigar y responder ante amenazas avanzadas.
* Utilizar Microsoft Defender for Cloud para desplegar Defender for Servers e integrar alertas con **Microsoft Sentinel**.

### ES-2: Use modern anti-malware software

* Utilizar soluciones antimalware con protección en tiempo real y análisis periódicos.
* Microsoft Defender for Cloud puede identificar soluciones antimalware instaladas y evaluar su estado.
* **Microsoft Defender Antivirus** es la solución antimalware predeterminada para Windows Server 2016 y posteriores.
* Para Linux, utilizar **Microsoft Defender for Endpoint on Linux**.
* Defender for Cloud también puede detectar malware cargado en Azure Storage mediante Defender for Storage.

### ES-3: Ensure anti-malware software and signatures are updated

* Mantener las firmas antimalware actualizadas rápida y consistentemente.
* Seguir las recomendaciones de Microsoft Defender for Cloud.
* Microsoft antimalware para Windows y Microsoft Defender for Endpoint para Linux instalan automáticamente las últimas firmas y actualizaciones del motor por defecto.
* Las soluciones externas deben mantener sus propias firmas actualizadas.

## Governance and Strategy

### GS-5: Define and implement security posture management strategy

* Establecer políticas, procedimientos y estándares para la administración de configuraciones y vulnerabilidades.
* Definir **baselines de configuración segura** para distintos tipos de recursos y abordar riesgos de red, identidad, acceso privilegiado y protección de datos.
* Medir, auditar y aplicar continuamente las configuraciones para evitar desviaciones.
* Mantenerse actualizado respecto de las características de seguridad.
* Utilizar mecanismos como **Secure Score** y el **Compliance Dashboard de Microsoft Defender for Cloud** para revisar la postura y corregir brechas.
* Evaluar y remediar vulnerabilidades periódicamente en servicios cloud, sistemas operativos y componentes de aplicaciones.
* Priorizar la evaluación y remediación según riesgo y cumplir los requisitos organizacionales de compliance.

### GS-11: Define and implement multicloud security strategy

* Definir una estrategia multicloud dentro de la gobernanza, gestión de riesgos y operaciones de seguridad.
* Educar a los equipos sobre las diferencias entre plataformas cloud.
* Diseñar soluciones portables que reduzcan el vendor lock-in sin dejar de aprovechar las capacidades nativas.
* Centralizar los procesos de gobernanza y operaciones de seguridad independientemente de dónde esté desplegada la solución.
* Seleccionar herramientas que permitan una administración unificada y centralizada de los distintos dominios de seguridad.

## Identity Management

### IM-8: Restrict the exposure of credentials and secrets

* No incluir credenciales ni secretos directamente en código o archivos de configuración.
* Utilizar un **key vault** o almacén seguro de secretos.
* Analizar el código fuente en busca de credenciales.
* Estas prácticas deben formar parte del **SDLC seguro y DevOps security**.
* En Azure, utilizar **Azure Key Vault** cuando no sea posible utilizar managed identities.
* En Azure DevOps utilizar **Credential Scanner** y en GitHub **Secret Scanning**.
* Azure Functions, App Services y VMs pueden utilizar managed identities para acceder de forma segura a Key Vault.
* Rotar automáticamente los secretos cuando sea posible y realizar rotación manual periódica cuando no lo sea; eliminar los secretos que ya no se utilicen.

## Incident Response

### IR-4: Detection and analysis — investigate an incident

* El equipo de seguridad debe poder consultar diversas fuentes de datos para construir una visión completa del incidente.
* Correlacionar logs de identidad, acceso, sistemas, aplicaciones y redes para seguir las actividades del atacante.
* Utilizar un SIEM cloud-native o integrar los datos en un SIEM existente.
* En Azure, utilizar:

  * **Microsoft Entra logs** para identidad y acceso.
  * **NSG flow logs, Azure Network Watcher y Azure Monitor** para datos de red.
  * Snapshots de discos y memory dumps para preservar información de sistemas afectados.
  * **Microsoft Sentinel** para análisis de múltiples fuentes y administración del ciclo de vida de incidentes.
* Proteger los datos utilizados durante la investigación frente a modificaciones o eliminación por parte de atacantes.

### IR-6: Containment, eradication, and recovery — automate incident handling

* Automatizar tareas manuales y repetitivas para acelerar la respuesta, reducir la carga de los analistas y disminuir errores.
* En Azure, utilizar automatización de **Microsoft Defender for Cloud** y **Microsoft Sentinel** para ejecutar acciones o playbooks ante alertas.
* Los playbooks pueden enviar notificaciones, deshabilitar cuentas y aislar redes problemáticas.
* AWS y GCP también permiten automatizar acciones de respuesta mediante sus herramientas de automatización y playbooks.

## Logging and Threat Detection

### LT-1: Enable threat detection capabilities

* Monitorear los tipos de recursos conocidos para detectar amenazas y anomalías.
* Configurar filtros y reglas analíticas para obtener alertas de calidad y reducir falsos positivos.
* En Azure, utilizar **Microsoft Defender for Cloud** y centralizar alertas y logs en **Azure Monitor** o **Microsoft Sentinel**.
* Para entornos OT/ICS/SCADA, utilizar **Microsoft Defender for IoT** para inventario y detección de amenazas y vulnerabilidades.
* Cuando un servicio no tenga detección nativa, recopilar sus logs y analizarlos mediante Microsoft Sentinel.

### LT-3: Enable logging for security investigation

* Habilitar logging de los recursos cloud, sistemas operativos y aplicaciones para investigación, respuesta y compliance.
* En Azure existen tres tipos principales:

  * **Azure resource logs:** operaciones realizadas dentro del recurso, en el data plane.
  * **Azure Activity Log:** operaciones sobre recursos a nivel de suscripción, en el management plane.
  * **Microsoft Entra ID logs:** actividad de inicio de sesión y auditoría de cambios del tenant.
* Microsoft Defender for Cloud y Azure Policy pueden utilizarse para habilitar logs y recopilarlos.

### LT-4: Enable network logging for security investigation

* Habilitar logs de red para investigaciones, threat hunting y generación de alertas.
* En Azure recopilar:

  * **NSG resource logs**.
  * **NSG flow logs**.
  * **Azure Firewall logs**.
  * **WAF logs**.
  * Datos de tráfico de VMs.
  * Logs de consultas DNS.
* Enviar flow logs a **Azure Monitor Log Analytics** y utilizar **Traffic Analytics** para obtener información.

### LT-5: Centralize security log management and analysis

* Centralizar almacenamiento y análisis de logs para permitir correlación entre fuentes.
* Definir propietario, acceso, ubicación, herramientas de procesamiento y requisitos de retención para cada fuente.
* En Azure, integrar Activity Logs en un **Log Analytics workspace**, utilizar **Azure Monitor** para consultas y alertas y habilitar **Microsoft Sentinel** para capacidades SIEM y SOAR.

### LT-6: Configure log storage retention

* Definir la retención de logs según requisitos de compliance, regulación y negocio.
* Los **Azure Activity Logs** se conservan durante 90 días y luego se eliminan si no se enrutan a otro destino.
* Utilizar:

  * **Log Analytics** para retención de hasta un año o según necesidades.
  * **Azure Storage, Data Explorer o Data Lake** para almacenamiento de largo plazo y archivo.
  * **Event Hubs** para enviar logs a recursos externos.
* Microsoft Sentinel utiliza Log Analytics como backend, por lo que debe considerarse una estrategia de almacenamiento a largo plazo para logs del SIEM.

## Posture and Vulnerability Management

### PV-4: Audit and enforce secure configurations for compute resources

* Monitorear continuamente desviaciones respecto de las baselines de configuración y generar alertas.
* Aplicar la configuración deseada rechazando configuraciones no conformes o desplegando la configuración requerida.
* En Azure utilizar **Microsoft Defender for Cloud** y **Azure Automanage Machine Configuration** para evaluar y remediar desviaciones en VMs, contenedores y otros recursos.
* Utilizar ARM templates, imágenes personalizadas, Azure Automation State Configuration y Change Tracking and Inventory para mantener y controlar configuraciones.
* Utilizar Guest Attestation para monitorear la integridad de arranque de confidential VMs.

### PV-5: Perform vulnerability assessments

* Realizar evaluaciones de vulnerabilidades de forma programada o bajo demanda en todos los niveles de recursos cloud.
* Comparar resultados de distintos análisis para verificar la remediación.
* Evaluar vulnerabilidades de servicios, redes, aplicaciones web, sistemas operativos y configuraciones.
* Proteger las cuentas privilegiadas utilizadas por los scanners.
* En Azure utilizar las capacidades de **Microsoft Defender for Cloud** para VMs, imágenes de contenedores y SQL Server.
* Utilizar cuentas temporales o **JIT provisioning** para los análisis remotos, evitando cuentas administrativas permanentes.
* Exportar resultados periódicamente y comparar históricos.
* Configurar notificaciones por email en Microsoft Defender for Cloud.

### PV-6: Rapidly and automatically remediate vulnerabilities

* Aplicar rápidamente parches y actualizaciones de forma automatizada.
* Priorizar la remediación según riesgo; las vulnerabilidades más graves en activos de mayor valor tienen mayor prioridad.
* En Azure utilizar **Azure Automation Update Management** u otra solución para mantener actualizadas las VMs Windows y Linux.
* En Windows, habilitar Windows Update y las actualizaciones automáticas.
* Para software externo, utilizar una solución externa de patch management o Microsoft System Center Updates Publisher.

# Azure Governance

**Governance** es un área de **Azure Management**. La administración de Azure comprende las tareas y procesos necesarios para mantener las aplicaciones empresariales y los recursos que las soportan.

Azure proporciona múltiples servicios y herramientas que trabajan conjuntamente para ofrecer una administración completa, no solo de recursos en Azure sino también de otras nubes y entornos **on-premises**.

La administración puede considerarse como un **ciclo de vida de los recursos**, que comienza con el despliegue inicial, continúa durante la operación y mantenimiento y finaliza cuando el recurso es retirado.

No existe un único servicio de Azure que cubra completamente un área de administración. Cada área se implementa mediante varios servicios que trabajan conjuntamente. Por ejemplo, **Application Insights** proporciona funcionalidades específicas de monitoreo para aplicaciones web, mientras que **Azure Monitor logs** almacena datos de administración de otros servicios y permite analizar datos de distintos tipos recopilados por diferentes servicios.

## Monitor

* Consiste en recopilar y analizar datos para auditar el rendimiento, estado y disponibilidad de los recursos.
* Una estrategia efectiva de monitoreo permite comprender el funcionamiento de los componentes, aumentar el uptime y recibir notificaciones.
* Azure proporciona diferentes servicios para monitorear aplicaciones y recursos.

## Configure

* Comprende el despliegue y configuración inicial de recursos y su mantenimiento continuo.
* La automatización reduce tareas repetitivas, tiempo y esfuerzo, aumentando la precisión y eficiencia.
* **Azure Automation** proporciona gran parte de los servicios para automatizar tareas de configuración.
* Los **runbooks** gestionan la automatización de procesos.
* La configuración y la administración de actualizaciones ayudan a administrar las configuraciones.

## Govern

* Proporciona mecanismos y procesos para mantener el control sobre aplicaciones y recursos en Azure.
* Implica planificar iniciativas y establecer prioridades estratégicas.
* **Azure Policy** permite crear, asignar y administrar definiciones de políticas para imponer reglas sobre los recursos y mantenerlos en cumplimiento con los estándares corporativos.
* **Azure Cost Management** permite realizar seguimiento del uso de la nube y los gastos de los recursos de Azure y de otros proveedores cloud.

## Secure

* Consiste en administrar la seguridad de los recursos y datos.
* Un programa de seguridad implica evaluar amenazas, recopilar y analizar datos y garantizar el cumplimiento de las aplicaciones y recursos.
* **Microsoft Defender for Cloud** proporciona monitoreo de seguridad y análisis de amenazas, con administración unificada de seguridad y protección avanzada contra amenazas para cargas de trabajo híbridas.
* La seguridad de Azure abarca la protección de los recursos y datos del entorno.

## Protect

* Consiste en mantener disponibles las aplicaciones y los datos incluso ante interrupciones fuera del control de la organización.
* **Azure Backup** proporciona backup y recuperación de datos tanto en la nube como on-premises.
* **Azure Site Recovery** proporciona continuidad empresarial y recuperación inmediata durante un desastre.

## Migrate

* Consiste en trasladar workloads que actualmente se ejecutan on-premises hacia Azure.
* **Azure Migrate** permite evaluar la adecuación de máquinas virtuales on-premises para su migración a Azure.
* **Azure Site Recovery** permite migrar máquinas virtuales desde entornos on-premises o desde **Amazon Web Services**.
* **Azure Database Migration Service** ayuda a migrar fuentes de bases de datos hacia plataformas de datos de Azure.

# Azure Policy

**Azure Policy** permite crear, asignar y administrar políticas para aplicar reglas a los recursos de Azure y mantener el cumplimiento con los estándares organizacionales.

## Azure Security Policies

Una **Azure Security Policy** define y aplica reglas específicas sobre los recursos.

### Componentes

* **Policy Definition:** especifica las condiciones que se desean controlar, como tipos de recursos permitidos o tags obligatorios.
* **Policy Assignment:** determina dónde se aplica la política: recursos individuales, resource groups o management groups.
* **Policy Parameters:** permiten personalizar el comportamiento de la política, como SKU de máquinas virtuales o ubicación.

### Casos de uso

* Aplicar reglas específicas de forma consistente.
* Garantizar un etiquetado uniforme.
* Controlar los tipos de recursos permitidos.

## Azure Security Initiatives

Una **Azure Security Initiative** agrupa varias definiciones de Azure Policy relacionadas para cumplir un objetivo específico.

### Componentes

* **Definitions (Policies):** colección de políticas agrupadas en una única iniciativa.
* **Assignment:** determina el scope donde se aplica la iniciativa, como una suscripción o resource group.
* **Parameters:** permiten personalizar el comportamiento de la iniciativa.

### Casos de uso

* Alcanzar objetivos de compliance más amplios, como **PCI-DSS** o **HIPAA**.
* Administrar políticas relacionadas como una unidad cohesiva.

## Azure Policy vs. Azure Initiatives

* **Azure Policy:** apropiado para aplicar reglas individuales y controles granulares; en algunos casos una sola política es suficiente.
* **Azure Initiatives:** recomendadas incluso cuando existe una sola política porque simplifican la administración y permiten agrupar múltiples políticas como una unidad.
* Ejemplo: en lugar de administrar 20 políticas separadas para cumplimiento **PCI-DSS**, utilizar una iniciativa que las evalúe conjuntamente.

**Azure Security Policies** se enfocan en el control granular, mientras que **Azure Security Initiatives** proporcionan un enfoque consolidado.

## Crear y administrar políticas para aplicar compliance

Azure Policy permite realizar tareas como:

* Asignar una política para aplicar una condición a los recursos creados en el futuro.
* Crear y asignar una definición de iniciativa para realizar seguimiento del cumplimiento de múltiples recursos.
* Resolver recursos **non-compliant** o **denied**.
* Implementar una nueva política en toda la organización.

## Asignar una Policy

Una **Policy Definition** define bajo qué condición se aplica una política y qué efecto debe producir.

Ejemplo: utilizar la política integrada **Inherit a tag from the resource group if missing** para agregar a los recursos nuevos o actualizados un tag heredado del resource group cuando no lo tengan.

### Proceso de asignación

1. Acceder a **Azure Portal** y seleccionar **Policy**.
2. Seleccionar **Assignments**.
3. Seleccionar **Assign Policy**.
4. En **Basics**, seleccionar el **Scope**:

   * Management group.
   * Subscription.
   * Opcionalmente, Resource group.
5. Definir **Exclusions** opcionalmente. Las exclusiones comienzan un nivel por debajo del scope seleccionado.
6. Seleccionar la **Policy definition** y elegir la política requerida.
7. Definir el **Assignment name** y, opcionalmente, una descripción.
8. Mantener **Policy enforcement** como **Enabled**. Si se establece en **Disabled**, permite probar el resultado de la política sin activar su efecto.
9. **Assigned by** se completa automáticamente según el usuario conectado, aunque puede personalizarse.
10. En **Parameters**, configurar los parámetros requeridos. En el ejemplo, **Tag Name = Environment**.
11. En **Remediation**, decidir si se crea una tarea de remediación para modificar recursos existentes además de recursos nuevos o actualizados.
12. Cuando la política utiliza el efecto **modify**, se crea automáticamente una **Managed Identity** y se asigna **Contributor** para permitir la remediación.
13. En **Non-compliance messages**, configurar el mensaje que se mostrará para recursos rechazados o non-compliant. Ejemplo: `This resource doesn't have the required tag`.
14. Revisar la configuración en **Review + create**.
15. Seleccionar **Create** para crear la asignación.

# Azure Landing Zones

Una **Azure landing zone** es un entorno que sigue principios de diseño en ocho áreas y permite soportar portfolios de aplicaciones, migración, modernización e innovación a escala.

Utiliza **subscriptions** para aislar y escalar los recursos de aplicaciones y de plataforma:

* **Application landing zones:** subscriptions destinadas a recursos de aplicaciones.
* **Platform landing zones:** subscriptions destinadas a recursos de plataforma.

La arquitectura de Azure landing zones es **escalable y modular**, permitiendo implementar configuraciones y controles de forma repetible y consistente en todas las subscriptions. Los módulos facilitan desplegar y modificar componentes específicos de la arquitectura a medida que evolucionan los requisitos.

## Platform landing zones vs. Application landing zones

### Platform landing zone

Es una subscription que proporciona servicios compartidos para las aplicaciones alojadas en application landing zones:

* **Identity**
* **Connectivity**
* **Management**

La consolidación de estos servicios compartidos mejora la eficiencia operativa. Una o más equipos centrales administran las platform landing zones.

### Application landing zone

Es una subscription destinada a alojar una aplicación.

* Se pueden preaprovisionar mediante código.
* Se utilizan **management groups** para asignar controles de políticas.
* Las aplicaciones se ejecutan dentro de un entorno con controles de seguridad y gobernanza definidos.

## Modelos de administración de Application Landing Zones

### 1. Central team management

* Un equipo central de IT opera completamente la landing zone.
* Aplica controles y herramientas de plataforma tanto a platform como application landing zones.

### 2. Application team management

* El equipo de administración de la plataforma delega completamente la application landing zone al equipo de aplicaciones.
* El equipo de aplicaciones administra y soporta el entorno.
* Las políticas de los **management groups** mantienen la gobernanza del equipo de plataforma.
* Se pueden agregar políticas a nivel de subscription y utilizar herramientas alternativas para desplegar, proteger o monitorear las application landing zones.

### 3. Shared management

* Una organización central administra el servicio subyacente de plataformas tecnológicas como **AKS** o **AVS**.
* Los equipos de aplicaciones son responsables de las aplicaciones que se ejecutan sobre dichas plataformas.
* Este modelo requiere controles y permisos diferentes de los utilizados para administrar centralmente las application landing zones.

## Azure Landing Zone Accelerators

Los **accelerators** son implementaciones de infraestructura como código que ayudan a desplegar correctamente una Azure landing zone.

Existen:

* **Platform landing zone accelerator**.
* Varios **application landing zone accelerators**.

### Platform landing zone accelerator

El **Azure landing zone portal accelerator** proporciona una experiencia de despliegue preparada que:

* Implementa la arquitectura conceptual.
* Aplica configuraciones predeterminadas a componentes clave, como **management groups** y **policies**.
* Es apropiado para organizaciones cuya arquitectura conceptual coincide con el modelo operativo y la estructura de recursos planificados.
* Está orientado a organizaciones que planean administrar su entorno mediante **Azure Portal**.

## Construir Azure Landing Zones escalables y modulares

El **Cloud Adoption Framework** proporciona un punto de partida para el proceso de adopción de cloud, incluyendo la metodología **Secure**.

Dentro de la metodología **Ready**, las Azure landing zones permiten acelerar la adopción mediante la implementación automatizada de arquitecturas y entornos operativos completos, incluyendo elementos de seguridad.

Las landing zones integran buenas prácticas de:

* Seguridad.
* Gobernanza.
* Administración.
* Automatización de plataforma.
* DevOps.

Proporcionan un punto de partida arquitectónico para:

* Desplegar nuevos workloads.
* Migrar workloads existentes.
* Mejorar workloads ya desplegados.

Su implementación puede realizarse de forma completa desde el principio o incrementalmente.

La arquitectura puede personalizarse para satisfacer los requisitos específicos de cada organización.

Las landing zones proporcionan un método **repetible y predecible**, basado en implementaciones parametrizadas que incluyen:

* Enfoque de despliegue.
* Principios de diseño.
* Áreas de diseño.
* Seguridad.
* Administración.
* Gobernanza.
* Automatización de plataforma.
* DevOps.

## Zero Trust

Las Azure landing zones pueden adaptarse siguiendo las buenas prácticas del **Azure Security Benchmark (ASB)** y los principios **Zero Trust**.

Zero Trust se basa en:

> **Never trust and always verify.**

La estrategia debe aplicarse de extremo a extremo sobre:

* Identidades.
* Endpoints.
* Redes.
* Datos.
* Aplicaciones.
* Infraestructura.

La arquitectura puede evolucionar progresivamente hacia un modelo alineado con las mejores prácticas, incorporando nuevas consideraciones de seguridad y principios Zero Trust para mejorar la seguridad y gobernanza.

## Azure Security Benchmark

Se recomienda seguir las recomendaciones de alto impacto del **Azure Security Benchmark** y utilizar también las directrices incluidas en Azure landing zones y **Cloud Adoption Framework**.

Las recomendaciones de ASB deben formar parte de la estrategia arquitectónica mediante la revisión de la documentación correspondiente y de las baselines específicas de cada servicio.

Las Azure landing zones asignan **ASB Policy** de forma predeterminada en la parte superior de su jerarquía, garantizando que todas las subscriptions y workloads dentro de la landing zone sean monitoreados para comprobar el cumplimiento con el **Azure Security Benchmark**.
