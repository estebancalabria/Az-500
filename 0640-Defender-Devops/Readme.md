# Microsoft Defender for Cloud — DevOps Security

Microsoft Defender for Cloud proporciona **visibilidad, gestión de postura y protección contra amenazas** en entornos multicloud, incluyendo Azure, AWS, GCP y recursos on-premises.

## DevOps Security

DevOps Security utiliza una **consola centralizada** para proteger aplicaciones y recursos **desde el código hasta la nube**, en múltiples pipelines y plataformas como **Azure DevOps, GitHub y GitLab**.

Principales capacidades:

* **Visibilidad unificada:** inventario y postura de seguridad del código de aplicaciones en preproducción, incluyendo hallazgos de **código, secretos y dependencias open source**, además de configuraciones DevOps.
* **Protección durante el desarrollo:** seguridad para plantillas **Infrastructure as Code (IaC)** e imágenes de contenedores, reduciendo configuraciones incorrectas que puedan llegar a producción.
* **Priorización de problemas críticos:** contexto **code-to-cloud** para priorizar correcciones mediante **anotaciones en pull requests** y workflows personalizados.

Estas capacidades permiten **unificar, fortalecer y administrar recursos DevOps** en múltiples pipelines.

## Administración de entornos DevOps

Desde la consola de DevOps Security se pueden:

* Agregar entornos de **Azure DevOps, GitHub y GitLab**.
* Obtener una visión general de los problemas detectados.
* Personalizar el workbook de DevOps con las métricas deseadas.
* Configurar **anotaciones en pull requests**.

## Revisión de findings

El inventario de DevOps permite revisar los recursos incorporados y su información de seguridad.

* **Name:** recursos DevOps incorporados de Azure DevOps, GitHub o GitLab.

* **DevOps environment:** identifica Azure DevOps, GitHub o GitLab.

* **Advanced security status:**

  * `On`: habilitado.
  * `Off`: no habilitado.
  * `Partially enabled`: algunas funciones no están habilitadas.
  * `N/A`: Defender for Cloud no dispone de información.

  Disponible actualmente para repositorios de **Azure DevOps y GitHub**.

* **Pull request annotation status:**

  * `On`: habilitadas.
  * `Off`: no habilitadas.
  * `N/A`: Defender for Cloud no dispone de información.

  Disponible actualmente para repositorios de **Azure DevOps**.

* **Findings:** cantidad de hallazgos de **código, secretos, dependencias e Infrastructure as Code**.

La tabla puede visualizarse como una vista plana a nivel de recurso o mediante una jerarquía de **organizaciones/proyectos/grupos**. Puede filtrarse por **suscripción, tipo de recurso, tipo de finding o severidad**.

# DevOps Security — Soporte y prerrequisitos

## Soporte de nube y regiones

DevOps Security está disponible en **Azure Commercial Cloud** en:

* **Asia:** East Asia.
* **Australia:** Australia East.
* **Canadá:** Canada Central.
* **Europa:** West Europe, North Europe, Sweden Central.
* **Reino Unido:** UK South.
* **Estados Unidos:** East US, Central US.

## Plataformas DevOps compatibles

Actualmente soporta:

* **Azure DevOps Services**.
* **GitHub Enterprise Cloud**.
* **GitLab SaaS**.

## Permisos requeridos

| Función                                                      | Permisos                                                                                                                                                          |
| ------------------------------------------------------------ | ----------------------------------------------------------------------------------------------------------------------------------------------------------------- |
| Conectar entornos DevOps con Defender for Cloud              | Azure: **Subscription Contributor o Security Admin**; Azure DevOps: **Project Collection Administrator**; GitHub: **Organization Owner**; GitLab: **Group Owner** |
| Revisar insights y findings de seguridad                     | **Security Reader**                                                                                                                                               |
| Configurar anotaciones de pull requests                      | **Subscription Contributor o Owner**                                                                                                                              |
| Instalar Microsoft Security DevOps extension en Azure DevOps | **Azure DevOps Project Collection Administrator**                                                                                                                 |
| Instalar Microsoft Security DevOps action en GitHub          | **GitHub Write**                                                                                                                                                  |

El rol **Security Reader** puede asignarse a nivel de **Resource Group o connector scope** para evitar permisos elevados a nivel de suscripción al consultar insights y findings de DevOps Security.

## Disponibilidad de funcionalidades

Desde el **7 de marzo de 2024**, es necesario habilitar **Defender CSPM** en al menos una suscripción o conector multicloud del tenant para acceder a capacidades premium de DevOps Security, incluyendo:

* Contextualización **code-to-cloud**.
* **Security Explorer**.
* **Attack paths**.
* Anotaciones de pull requests para findings de seguridad de **Infrastructure as Code**.

## Azure DevOps

| Funcionalidad                                  | Prerrequisito                                                                                       |
| ---------------------------------------------- | --------------------------------------------------------------------------------------------------- |
| Conectar repositorios Azure DevOps             | Prerrequisitos específicos de onboarding                                                            |
| Vulnerabilidades de código                     | **GitHub Advanced Security for Azure DevOps** para CodeQL + **Microsoft Security DevOps extension** |
| Secretos expuestos                             | **GitHub Advanced Security for Azure DevOps**                                                       |
| Vulnerabilidades open source                   | **GitHub Advanced Security for Azure DevOps**                                                       |
| Configuraciones incorrectas de IaC             | **Microsoft Security DevOps extension**                                                             |
| Configuraciones incorrectas del entorno DevOps | N/A                                                                                                 |
| Pull request annotations                       | Prerrequisitos específicos                                                                          |
| Code-to-cloud para contenedores                | **Microsoft Security DevOps extension**                                                             |
| Code-to-cloud para plantillas IaC              | **Microsoft Security DevOps extension**                                                             |
| Attack path analysis                           | **Defender CSPM** habilitado en Azure, AWS Connector o GCP Connector del mismo tenant               |
| Cloud Security Explorer                        | **Defender CSPM** habilitado en Azure, AWS Connector o GCP Connector del mismo tenant               |

## GitHub

| Funcionalidad                                  | Prerrequisito                                                                         |
| ---------------------------------------------- | ------------------------------------------------------------------------------------- |
| Conectar repositorios GitHub                   | Prerrequisitos específicos de onboarding                                              |
| Vulnerabilidades de código                     | **GitHub Advanced Security** + **Microsoft Security DevOps action**                   |
| Secretos expuestos                             | **GitHub Advanced Security**                                                          |
| Vulnerabilidades open source                   | **GitHub Advanced Security**                                                          |
| Configuraciones incorrectas de IaC             | **GitHub Advanced Security** + **Microsoft Security DevOps action**                   |
| Configuraciones incorrectas del entorno DevOps | N/A                                                                                   |
| Code-to-cloud para contenedores                | **Microsoft Security DevOps action**                                                  |
| Code-to-cloud para plantillas IaC              | **Microsoft Security DevOps action**                                                  |
| Attack path analysis                           | **Defender CSPM** habilitado en Azure, AWS Connector o GCP Connector del mismo tenant |
| Cloud Security Explorer                        | **Defender CSPM** habilitado en Azure, AWS Connector o GCP Connector del mismo tenant |

## GitLab

| Funcionalidad                      | Prerrequisito                                                                         |
| ---------------------------------- | ------------------------------------------------------------------------------------- |
| Conectar proyectos GitLab          | Prerrequisitos específicos de onboarding                                              |
| Vulnerabilidades de código         | **GitLab Ultimate**                                                                   |
| Secretos expuestos                 | **GitLab Ultimate**                                                                   |
| Vulnerabilidades open source       | **GitLab Ultimate**                                                                   |
| Configuraciones incorrectas de IaC | **GitLab Ultimate**                                                                   |
| Cloud Security Explorer            | **Defender CSPM** habilitado en Azure, AWS Connector o GCP Connector del mismo tenant |

# DevOps Environment Security Posture

El aumento de ataques contra sistemas de gestión de código fuente y pipelines **CI/CD** hace necesario proteger las plataformas DevOps frente a amenazas que pueden provocar **inyección de código, escalación de privilegios y exfiltración de datos**.

**DevOps posture management** en Microsoft Defender for Cloud:

* Proporciona información sobre la postura de seguridad de todo el ciclo de vida de la **cadena de suministro de software**.
* Utiliza scanners avanzados para evaluaciones detalladas.
* Cubre recursos como **organizaciones, pipelines y repositorios**.
* Permite reducir la superficie de ataque mediante recomendaciones de seguridad.

## DevOps Scanners

DevOps posture management utiliza **DevOps scanners** para detectar debilidades en sistemas de gestión de código y pipelines CI/CD mediante comprobaciones de:

* Configuraciones de seguridad.
* Controles de acceso.

Los scanners de **Azure DevOps y GitHub** se utilizan internamente en Microsoft para identificar riesgos en recursos DevOps.

Una vez conectado un entorno DevOps, Defender for Cloud configura automáticamente los scanners para realizar análisis recurrentes **cada 24 horas** sobre:

* Builds.
* Secure Files.
* Variable Groups.
* Service Connections.
* Organizations.
* Repositories.

# DevOps Threat Matrix y reducción de riesgos

DevOps posture management ayuda a descubrir y corregir configuraciones inseguras en plataformas DevOps, contribuyendo a un entorno **resiliente y zero-trust**.

Principales controles:

* **Scoped secret access:** limitar cada pipeline a los secretos estrictamente necesarios para reducir accesos no autorizados, filtraciones y movimientos laterales.
* **Restricción de self-hosted runners y permisos elevados:** evitar ejecuciones no autorizadas y escalaciones de privilegios; los permisos de los pipelines deben establecerse por defecto como **read-only**.
* **Enhanced branch protection:** aplicar reglas de protección de ramas para preservar la integridad del código y evitar inyecciones maliciosas.
* **Permisos optimizados y repositorios seguros:** aplicar permisos mínimos y habilitar **secret push protection** para reducir accesos y modificaciones no autorizadas.

## DevOps Threat Matrix

La **DevOps Threat Matrix** es una base de conocimiento para identificar técnicas de ataque relevantes en entornos DevOps y desarrollar defensas contra ellas.

Está basada en **MITRE ATT&CK**, adaptando las técnicas y vectores de ataque al contexto de:

* Sistemas de gestión de código fuente.
* Pipelines CI/CD.
* Recursos DevOps.

Las tácticas deben interpretarse desde la perspectiva DevOps. Por ejemplo, **Execution** en una máquina Windows/Linux implica ejecutar código en el sistema operativo, mientras que en DevOps implica ejecutar código dentro del pipeline o sobre recursos DevOps.

La matriz también puede utilizarse junto con equipos **red team** para probar defensas, validar supuestos y descubrir nuevas técnicas de ataque.

# MITRE ATT&CK

MITRE ATT&CK es una base de conocimiento pública para comprender las **tácticas y técnicas utilizadas por atacantes**.

Las categorías incluyen:

* Pre-attack.
* Initial access.
* Execution.
* Persistence.
* Privilege escalation.
* Defense evasion.
* Credential access.
* Discovery.
* Lateral movement.
* Collection.
* Exfiltration.
* Command and control.

**Tactics (T)** representan el **por qué** de una técnica: el objetivo táctico del atacante.

**Techniques (T)** representan el **cómo**: la acción utilizada para alcanzar ese objetivo.

**Common Knowledge (CK)** representa el conocimiento documentado sobre el modus operandi de las tácticas y técnicas utilizadas por los atacantes.

# Técnicas de ataque en DevOps

## Initial Access

Busca obtener acceso a recursos DevOps como **repositorios, pipelines y dependencias**.

Principales técnicas:

* **Source Code Management authentication:** acceso mediante PAT, SSH key u otras credenciales permitidas. Un ejemplo es el phishing.
* **CI/CD service authentication:** uso de credenciales del servicio CI/CD para acceder al entorno.
* **Organization's public repositories:** acceso a repositorios públicos configurados con capacidades CI/CD que pueden activar pipelines mediante PR.
* **Endpoint compromise:** utilizar una estación de trabajo de desarrollador comprometida para acceder a SCM, registry u otros recursos.
* **Configured webhooks:** utilizar webhooks configurados para activar solicitudes desde SCM hacia redes o servicios internos.

## Execution

Busca obtener ejecución sobre recursos del pipeline o de deployment.

### Poisoned Pipeline Execution (PPE)

Consiste en inyectar código en un repositorio para provocar su ejecución en el sistema CI/CD.

* **Direct PPE (d-PPE):** modificación directa del archivo de configuración del pipeline para introducir comandos maliciosos.
* **Indirect PPE (i-PPE):** modificación de scripts utilizados por el pipeline, como make-files, test scripts o build scripts.
* **Public PPE:** explotación de pipelines activados por proyectos open source mediante d-PPE o i-PPE.

### Dependency Tampering

Inyección de código malicioso en dependencias de un repositorio para que se ejecute al descargarlas.

Subtécnicas:

* **Public dependency confusion:** publicación de paquetes maliciosos con el mismo nombre que paquetes privados.
* **Public package hijack (repo-jacking):** toma de control de un paquete público, por ejemplo mediante el control de la cuenta del mantenedor.
* **Typosquatting:** publicación de paquetes con nombres similares a paquetes legítimos.

Otras técnicas:

* **DevOps resources compromise:** explotación de vulnerabilidades del sistema operativo, agentes, software instalado o dispositivos de red utilizados por los pipelines.
* **Control of common registry:** toma de control de un registry para introducir imágenes o paquetes maliciosos que luego ejecutan los pipelines o recursos de producción.

## Persistence

Busca mantener el acceso al entorno comprometido.

### Changes in repository

Uso de tokens automáticos del pipeline para modificar el repositorio cuando tienen permisos suficientes.

Subtécnicas:

* **Change/add scripts in code:** modificar o agregar scripts para descargar y ejecutar código controlado por el atacante.
* **Change the pipeline configuration:** agregar pasos al pipeline para descargar scripts controlados por el atacante.
* **Change the configuration for dependencies locations:** utilizar paquetes controlados por el atacante.

Otras técnicas:

* **Inject in Artifacts:** introducir código malicioso en artifacts compartidos entre ejecuciones de pipelines.
* **Modify images in registry:** modificar o introducir imágenes maliciosas en el registry cuando el pipeline tiene permisos de escritura.
* **Create service credentials:** crear nuevas credenciales o tokens para conservar el acceso si se pierde el método de acceso inicial.

## Privilege Escalation

Busca elevar privilegios sobre recursos ya comprometidos.

* **Secrets in private repositories:** buscar secretos ocultos en repositorios privados.
* **Commit/push to protected branches:** utilizar permisos del pipeline para insertar código directamente en ramas protegidas.
* **Certificates and identities from metadata services:** desde pipelines cloud-hosted, acceder a servicios de metadata y extraer certificados e identidades.

## Credential Access

Busca robar credenciales.

* **User credentials:** credenciales necesarias para acceder desde pipelines a servicios externos, almacenadas en CI secrets o variables de entorno.
* **Service credentials:** credenciales de servicios como **SPN** y tokens **SAS**, que pueden permitir acceso a otros servicios.

## Lateral Movement

Busca desplazarse entre diferentes recursos del entorno CI/CD.

* **Compromise build artifacts:** modificar artifacts de compilación para introducir código malicioso.
* **Registry injection:** infectar el registry con imágenes maliciosas que posteriormente serán descargadas y ejecutadas.
* **Spread to deployment resources:** utilizar el acceso del pipeline a recursos de deployment para propagarse y potencialmente ejecutar código o exfiltrar datos.

## Defense Evasion

Busca evadir las defensas existentes.

* **Service logs manipulation:** modificar logs para evitar que los defensores detecten las actividades del atacante.
* **Compilation manipulation:**

  * **Changing the code on the fly:** modificar código justo antes de la compilación sin alterar el repositorio.
  * **Tampered compiler:** modificar el compilador utilizado durante el build para introducir código malicioso.
* **Reconfigure branch protections:** modificar las protecciones de ramas para introducir código sin intervención de los usuarios.

## Impact

Busca utilizar los recursos CI/CD comprometidos para producir efectos perjudiciales.

* **Distributed Denial-of-Service (DDoS):** utilizar recursos de cómputo comprometidos para realizar ataques DDoS.
* **Cryptocurrency mining:** utilizar recursos de cómputo para minería controlada por el atacante.
* **Local Denial-of-Service (DoS):** apagar agentes, reiniciar o sobrecargar VMs desde los pipelines.
* **Resource deletion:** eliminar permanentemente recursos cloud, repositorios u otros recursos para provocar una denegación de servicio.

## Exfiltration

Busca extraer información sensible del entorno.

* **Clone private repositories:** utilizar el acceso de los pipelines para clonar y acceder a repositorios privados.
* **Pipeline logs:** acceder a logs de ejecución que pueden contener información sobre builds, deployments e incluso credenciales.
* **Exfiltrate data from production resources:** aprovechar el acceso de los pipelines a recursos de producción para extraer datos.

# DevOps Posture Management Recommendations

Cuando los DevOps scanners detectan desviaciones respecto de las buenas prácticas de seguridad en sistemas SCM y pipelines CI/CD, Defender for Cloud genera **recomendaciones específicas y accionables**.

Beneficios:

* **Enhanced visibility:** información completa sobre la postura de seguridad e identificación de problemas como ausencia de protección de ramas, riesgos de escalación de privilegios y conexiones inseguras.
* **Priority-based action:** filtrar resultados por severidad para abordar primero las vulnerabilidades más críticas.
* **Attack surface reduction:** corregir las brechas identificadas para reducir la superficie de ataque.
* **Real-time notifications:** integrar workflows para recibir alertas inmediatas cuando cambien configuraciones seguras y mantener el cumplimiento de las políticas de seguridad.

# Conectar GitHub con Microsoft Defender for Cloud

Conectar las organizaciones de **GitHub** con Defender for Cloud extiende las capacidades de seguridad de Defender for Cloud a los recursos de GitHub.

## Capacidades

### Foundational CSPM

Permite evaluar la postura de seguridad de GitHub mediante recomendaciones específicas para recursos de GitHub.

### Defender CSPM

Los clientes de Defender CSPM reciben:

* **Attack paths** contextualizados **code-to-cloud**.
* Evaluaciones de riesgo.
* Insights para identificar las debilidades más críticas.
* Contextualización de findings de DevOps Security con workloads cloud.
* Identificación del origen y del desarrollador para facilitar la remediación.

## Prerrequisitos

Para conectar GitHub con Defender for Cloud se requiere:

* Una cuenta de **Azure** con Defender for Cloud incorporado.
* **GitHub Enterprise** con **GitHub Advanced Security** habilitado para evaluar:

  * Secretos.
  * Dependencias.
  * Configuraciones incorrectas de IaC.
  * Calidad del código.

El rol **Security Reader** puede aplicarse sobre el **Resource Group o GitHub connector scope** para evitar permisos elevados a nivel de suscripción al consultar la postura de seguridad de DevOps.

## Conectar GitHub

1. Iniciar sesión en **Azure Portal**.
2. Ir a **Microsoft Defender for Cloud > Environment settings**.
3. Seleccionar **Add environment**.
4. Seleccionar **GitHub**.
5. Introducir un nombre de hasta **20 caracteres** y seleccionar:

   * Subscription.
   * Resource group.
   * Region.
6. Seleccionar **Next: select plans** y configurar el estado de **Defender CSPM** para el GitHub connector.
7. Seleccionar **Next: Configure access**.
8. Seleccionar **Authorize** para otorgar a la suscripción de Azure acceso a los repositorios de GitHub.
9. Seleccionar **Install**.
10. Seleccionar las organizaciones donde instalar la aplicación. Se recomienda otorgar acceso a **todos los repositorios**.
11. En **Organizations**, seleccionar:

    * **All existing organizations**.
    * **All existing and future organizations**.
12. Seleccionar **Next: Review and generate**.
13. Seleccionar **Create**.

Si transcurre demasiado tiempo entre la autorización y la instalación de la aplicación GitHub de DevOps Security, la sesión puede expirar.

Para las capacidades avanzadas de **DevOps posture**, solo se puede incorporar **una instancia de una organización de GitHub** al Azure Tenant donde se crea el connector.

# Microsoft Security DevOps — GitHub Action

**Microsoft Security DevOps (MSDO)** es una aplicación de línea de comandos que integra herramientas de **análisis estático** en el ciclo de desarrollo.

MSDO:

* Instala, configura y ejecuta versiones recientes de herramientas de análisis estático.
* Incluye herramientas de **Security Development Lifecycle (SDL)** y seguridad y compliance.
* Utiliza configuraciones portables y basadas en datos para permitir ejecuciones deterministas en distintos entornos.

## Herramientas incluidas

| Herramienta           | Lenguaje / ámbito                                                                                                                                                                | Licencia           |
| --------------------- | -------------------------------------------------------------------------------------------------------------------------------------------------------------------------------- | ------------------ |
| **AntiMalware**       | Protección antimalware de Windows mediante Microsoft Defender for Endpoint; analiza malware y detiene el build si encuentra malware. Se ejecuta por defecto en `windows-latest`. | No Open Source     |
| **Bandit**            | Python                                                                                                                                                                           | Apache License 2.0 |
| **BinSkim**           | Binary — Windows, ELF                                                                                                                                                            | MIT License        |
| **ESLint**            | JavaScript                                                                                                                                                                       | MIT License        |
| **Template Analyzer** | ARM Template, Bicep                                                                                                                                                              | MIT License        |
| **Terrascan**         | Terraform (HCL2), Kubernetes (JSON/YAML), Helm v3, Kustomize, Dockerfiles, CloudFormation                                                                                        | Apache License 2.0 |
| **Trivy**             | Container images, Infrastructure as Code (IaC)                                                                                                                                   | Apache License 2.0 |

## Prerrequisitos

Para configurar la GitHub Action se requiere:

* Una **suscripción de Azure**.
* Tener conectados los repositorios de **GitHub** con Defender for Cloud.
* Configurar **GitHub Advanced Security** para visualizar las evaluaciones de DevOps posture en Defender for Cloud.
* Acceder a la **Microsoft Security DevOps GitHub Action**.
* Configurar los permisos de workflow del repositorio GitHub como **Read and Write**.
* Incluir `id-token: write` en los permisos del GitHub Workflow para la federación con Defender for Cloud.

## Configurar Microsoft Security DevOps GitHub Action

1. Iniciar sesión en **GitHub**.
2. Seleccionar el repositorio donde se configurará la action.
3. Seleccionar **Actions**.
4. Seleccionar **New workflow**.
5. En **Get started with GitHub Actions**, seleccionar **set-up a workflow yourself**.
6. Introducir un nombre para el archivo de workflow, por ejemplo `msdevopssec.yml`.
7. Copiar el **sample action workflow** en la pestaña **Edit new file**.
8. Seleccionar **Start commit**.
9. Seleccionar **Commit new file**.
10. Seleccionar **Actions** y verificar que la nueva action se esté ejecutando.

## Visualizar resultados de los scans

Para consultar los resultados:

1. Iniciar sesión en **GitHub**.
2. Ir a **Security > Code scanning alerts > Tool**.
3. Seleccionar **Filter by tool**.

Los findings de code scanning pueden filtrarse por las herramientas específicas de **MSDO** en GitHub.

Estos resultados también se incorporan a las **recomendaciones de Microsoft Defender for Cloud**.
