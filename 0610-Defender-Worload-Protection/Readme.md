# Microsoft Defender for Cloud – Protección avanzada contra amenazas

## Introducción

Este módulo se centra en configurar y administrar protección integral contra amenazas mediante **Microsoft Defender for Cloud**.

### Objetivos

* Habilitar y configurar servicios de protección de cargas de trabajo.
* Proteger servidores, bases de datos y almacenamiento con los planes de Microsoft Defender.
* Implementar y administrar análisis sin agente para máquinas virtuales.
* Configurar y administrar Microsoft Defender Vulnerability Management.
* Integrar DevOps Security con GitHub, Azure DevOps y GitLab.

El objetivo es implementar, administrar y optimizar soluciones avanzadas de protección contra amenazas en cargas de trabajo y entornos DevOps.

---

# Enable workload protection services in Microsoft Defender for Cloud

Defender for Cloud proporciona alertas de seguridad basadas en **Microsoft Threat Intelligence** y protecciones avanzadas específicas para distintos tipos de cargas de trabajo mediante planes de Microsoft Defender.

Por ejemplo, **Defender for Storage** permite detectar actividades sospechosas relacionadas con cuentas de almacenamiento.

## Cloud workload dashboard

El panel de cargas de trabajo incluye:

1. **Microsoft Defender for Cloud coverage**

   * Muestra los tipos de recursos de la suscripción elegibles para protección.
   * Permite actualizar planes individualmente o seleccionar **Upgrade all**.

2. **Security alerts**

   * Defender for Cloud genera alertas cuando detecta amenazas.
   * Incluyen recursos afectados, gravedad, detalles, recomendaciones de remediación y, en algunos casos, acciones mediante Logic Apps.

3. **Advanced protection**

   * Muestra el estado de las protecciones disponibles para máquinas virtuales, SQL, contenedores, aplicaciones web, redes y otros recursos.
   * Permite acceder directamente a la configuración de cada protección.

4. **Insights**

   * Proporciona noticias, material recomendado y alertas de alta prioridad relacionadas con la suscripción, como CVE de alta severidad o información del equipo de Defender for Cloud.

## Protección de cargas de trabajo

Las protecciones de cargas de trabajo en la nube (**Cloud Workload Protections, CWP**) proporcionan recomendaciones específicas que orientan hacia los controles de seguridad adecuados.

Las alertas de seguridad indican la naturaleza y gravedad de las amenazas para facilitar una respuesta rápida.

| Capacidad                                 | Propósito                                                                                                                                                                    | Plan                                                                                                                                                |
| ----------------------------------------- | ---------------------------------------------------------------------------------------------------------------------------------------------------------------------------- | --------------------------------------------------------------------------------------------------------------------------------------------------- |
| **Protect cloud servers**                 | Proteger servidores mediante Microsoft Defender for Endpoint y capacidades como acceso Just-in-Time, supervisión de integridad de archivos y evaluación de vulnerabilidades. | Defender for Servers                                                                                                                                |
| **Identify threats to storage resources** | Detectar accesos o intentos de explotación sospechosos en cuentas de almacenamiento.                                                                                         | Defender for Storage                                                                                                                                |
| **Protect cloud databases**               | Detectar ataques y responder ante amenazas en bases de datos Azure y on-premises.                                                                                            | Defender for Azure SQL Databases, Defender for SQL servers on machines, Defender for Open-source relational databases, Defender for Azure Cosmos DB |
| **Protect containers**                    | Proteger clústeres, contenedores y aplicaciones mediante hardening, evaluación de vulnerabilidades y protección en tiempo de ejecución.                                      | Defender for Containers                                                                                                                             |
| **Infrastructure service insights**       | Detectar ataques y actividades sospechosas en App Service, Key Vault, Resource Manager y DNS.                                                                                | Defender for App Service, Key Vault, Resource Manager y DNS                                                                                         |
| **Security alerts**                       | Informar eventos de seguridad en tiempo real con niveles de severidad.                                                                                                       | Cualquier plan de protección                                                                                                                        |
| **Security incidents**                    | Correlacionar alertas para identificar patrones de ataque e integrarlas con SIEM, SOAR e ITSM.                                                                               | Cualquier plan de protección                                                                                                                        |

---

# Defender for Servers

**Defender for Servers** reduce el riesgo y la exposición de las máquinas mediante recomendaciones para mejorar y remediar la postura de seguridad, además de protección frente a amenazas y ataques en tiempo real.

Protege máquinas **Windows y Linux** en:

* Azure.
* AWS.
* GCP.
* Entornos on-premises.

> El soporte para **Log Analytics Agent** y **Azure Monitoring Agent (AMA)** en Defender for Servers ha finalizado. Para la mayoría de las funcionalidades se utilizan **agentless machine scanning** o la integración con **Microsoft Defender for Endpoint**.

## Beneficios

* Protección de máquinas multicloud, híbridas y on-premises.
* Administración y reporting centralizados desde Defender for Cloud.
* Integración nativa con Defender for Endpoint y Defender Vulnerability Management.
* Evaluación de postura de seguridad frente a estándares de cumplimiento.
* Recomendaciones accionables para reducir riesgos.
* **Plan 2:** análisis sin agente de inventario de software, vulnerabilidades, secretos y malware.
* Detección de amenazas prácticamente en tiempo real.
* Análisis mediante seguridad avanzada, machine learning y múltiples fuentes de inteligencia, incluido **Microsoft Security Response Center (MSRC)**.

## Integración con Defender for Endpoint

Defender for Endpoint y Defender Vulnerability Management se integran de forma nativa con Defender for Cloud.

La integración proporciona:

* Capacidades **EDR (Endpoint Detection and Response)**.
* Detección de amenazas.
* Análisis de vulnerabilidades.
* Inventario de software.
* Funcionalidades premium de Defender Vulnerability Management.

## Planes

* **Plan 1:** nivel inicial, centrado principalmente en las capacidades EDR proporcionadas por Defender for Endpoint.
* **Plan 2:** incluye las capacidades de Plan 1 y funcionalidades adicionales.

### Comparación de características

| Característica                                             | Plan 1 | Plan 2 |
| ---------------------------------------------------------- | ------ | ------ |
| Soporte multicloud e híbrido                               | ✓      | ✓      |
| Onboarding automático a Defender for Endpoint              | ✓      | ✓      |
| Defender for Endpoint EDR                                  | ✓      | ✓      |
| Detección de amenazas a nivel de SO                        | ✓      | ✓      |
| Alertas e incidentes integrados                            | ✓      | ✓      |
| Detección en la capa de red de Azure                       | —      | ✓      |
| Inventario de software                                     | ✓      | ✓      |
| Vulnerability scanning basado en agente                    | ✓      | ✓      |
| Vulnerability scanning sin agente                          | —      | ✓      |
| Evaluación de configuraciones del SO frente a baselines    | —      | ✓      |
| Evaluación de cumplimiento normativo                       | ✓      | ✓      |
| Evaluación de actualizaciones del SO                       | —      | ✓      |
| Funciones premium de Defender Vulnerability Management     | —      | ✓      |
| Malware scanning sin agente                                | —      | ✓      |
| Machine secrets scanning sin agente                        | —      | ✓      |
| File Integrity Monitoring                                  | —      | ✓      |
| Just-in-time VM access                                     | —      | ✓      |
| Network map                                                | —      | ✓      |
| Ingesta gratuita de 500 MB para tipos de datos específicos | —      | ✓      |

### Detalles importantes

* **Threat detection de red:** Plan 2 utiliza detección sin agente para amenazas dirigidas al plano de control de red de Azure.
* **OS baseline:** Plan 2 evalúa configuraciones mediante Azure Machine Configuration y baselines de seguridad de Windows y Linux.
* **OS updates:** Plan 2 utiliza Azure Update Manager. Para máquinas on-premises, AWS y GCP se requiere Azure Arc.
* **Premium Vulnerability Management:** incluye evaluaciones de certificados, baselines de seguridad del SO y otras funciones premium.
* **File Integrity Monitoring:** analiza archivos y registros para detectar cambios que puedan indicar ataques.
* **Just-in-time access:** bloquea puertos de las máquinas para reducir la superficie de ataque.
* **Network map:** proporciona una vista geográfica de recomendaciones para fortalecer recursos de red.

## Deployment scope

Se recomienda habilitar Defender for Servers a nivel de **suscripción**.

| Alcance                        | Plan 1 | Plan 2 |
| ------------------------------ | -----: | -----: |
| Habilitar en suscripción Azure |     Sí |     Sí |
| Habilitar en recurso           |     Sí |     No |
| Deshabilitar en recurso        |     Sí |     Sí |

* Plan 1 puede habilitarse y deshabilitarse por servidor.
* Plan 2 no puede habilitarse a nivel de recurso, aunque puede deshabilitarse allí.

## Después de habilitar el plan

* Comienza un período de prueba de **30 días**, que no puede detenerse, pausarse ni extenderse.
* La extensión de **Defender for Endpoint** se instala automáticamente en máquinas compatibles conectadas a Defender for Cloud.
* Defender Vulnerability Management se habilita de forma predeterminada en máquinas con la extensión de Defender for Endpoint.
* El análisis sin agente se habilita automáticamente con Plan 2.
* Plan 2 evalúa la configuración del sistema operativo frente a baselines de seguridad del Microsoft Cloud Security Benchmark.
* File Integrity Monitoring se configura después de habilitar Plan 2.

---

# Configure Microsoft Defender plans for Servers, Databases, and Storage

Defender for Servers proporciona detección de amenazas y defensas avanzadas para máquinas Windows y Linux en Azure, AWS, GCP y entornos on-premises.

Incluye:

* Integración/licencia de Microsoft Defender for Endpoint.
* Security baselines.
* Evaluaciones a nivel de SO.
* Vulnerability assessment.
* Adaptive Application Controls (AAC).
* File Integrity Monitoring (FIM).
* Otras capacidades de protección.

## Habilitar Defender for Servers

Puede habilitarse desde **Environment settings** para proteger las máquinas de una suscripción Azure, cuenta AWS o proyecto GCP:

1. Abrir **Microsoft Defender for Cloud** en Azure Portal.
2. Seleccionar **Environment settings**.
3. Seleccionar la suscripción, cuenta AWS o proyecto GCP.
4. En **Defender plans**, activar **Servers**.
5. Seleccionar **Plan 1** o **Plan 2**.
6. Confirmar y guardar.

Al habilitar el plan pueden configurarse sus funcionalidades según las necesidades.

La cobertura habilitada en una suscripción **no se extiende automáticamente a los Log Analytics workspaces**.

## Habilitar Defender for Servers en Log Analytics

Defender for Servers debe habilitarse individualmente en cada workspace.

* Los workspaces solo admiten **Plan 2**.

Pasos:

1. **Microsoft Defender for Cloud**.
2. **Environment settings**.
3. Seleccionar el workspace.
4. Activar Servers.
5. Guardar.

## Nivel de recurso

Se recomienda habilitar Defender for Servers en toda la suscripción para proteger recursos existentes y futuros.

También puede configurarse a nivel de recurso para excluir recursos específicos o aplicar configuraciones más granulares.

Recursos soportados:

* Azure VMs.
* Máquinas on-premises conectadas mediante Azure Arc.
* Azure Virtual Machine Scale Sets Flex.

---

# Defender for Storage

**Microsoft Defender for Storage** es una capa de seguridad nativa de Azure que detecta posibles amenazas contra las cuentas de almacenamiento.

Ayuda a prevenir tres impactos principales:

* Carga de archivos maliciosos.
* Exfiltración de datos sensibles.
* Corrupción de datos.

Analiza telemetría del **data plane** y **control plane** generada por:

* Azure Blob Storage.
* Azure Files.
* Azure Data Lake Storage.

Utiliza:

* Microsoft Threat Intelligence.
* Microsoft Defender Antivirus.
* Sensitive Data Discovery.

## Capacidades

* **Activity Monitoring**
* **Sensitive data threat detection** — Preview.
* **Malware Scanning** — disponible en el nuevo plan.

## Habilitación

Es una solución **agentless** que puede habilitarse a escala:

* A nivel de suscripción.
* A nivel de cuenta de almacenamiento.
* Mediante Azure Portal.
* Programáticamente.

Cuando se habilita a nivel de suscripción, las cuentas existentes y las nuevas quedan protegidas automáticamente. También es posible excluir cuentas específicas.

## Disponibilidad

* Activity Monitoring: GA.
* Malware Scanning: GA.
* Sensitive data threat detection: Preview.
* Malware Scanning tiene precio por GB escaneado.
* Sensitive data threat detection no tiene costo adicional dentro del nuevo plan.

### Tipos de almacenamiento

**Blob Storage:**

* Standard/Premium StorageV2.
* Data Lake Gen2.
* Activity Monitoring.
* Malware Scanning.
* Sensitive Data Discovery.

**Azure Files:**

* REST API.
* SMB.
* Activity Monitoring.

Azure DNS Zone no está soportado para Malware Scanning ni Sensitive Data Threat Detection.

## Beneficios

* **Protección contra malware:** analiza archivos cargados, incluidos archivos comprimidos.
* **Protección de datos sensibles:** prioriza alertas según la sensibilidad de los datos afectados.
* **Detección de entidades sin identidad:** identifica actividades sospechosas asociadas con SAS tokens mal configurados o comprometidos.
* **Cobertura de amenazas de almacenamiento:** detecta exfiltración de datos sensibles, corrupción de datos y cargas maliciosas.
* **Sin necesidad de habilitar diagnostic logs:** analiza continuamente la telemetría del data plane y control plane.
* **Implementación sin agentes y a escala.**

## Activity Monitoring

Analiza continuamente la actividad de las cuentas protegidas sin necesidad de habilitar resource logs.

Utiliza Microsoft Threat Intelligence para detectar indicadores como:

* Direcciones IP maliciosas.
* Tor exit nodes.
* Aplicaciones potencialmente peligrosas.

También utiliza modelos estadísticos y de machine learning para detectar anomalías respecto de la actividad normal.

Genera alertas de seguridad para actividades sospechosas sin afectar el rendimiento, capacidad de ingesta o acceso a los datos.

## Malware Scanning vs. Hash Reputation Analysis

Defender for Storage dispone de dos mecanismos para detectar contenido malicioso:

### Malware Scanning

* Solo disponible como add-on en el nuevo plan.
* Utiliza Microsoft Defender Antivirus.
* Realiza análisis profundo del contenido.
* Incluye hash reputation analysis.
* Disponible para Blob Storage.
* Es una funcionalidad de pago.

### Hash Reputation Analysis

* Disponible en todos los planes.
* Compara hashes de archivos nuevos con hashes de malware conocido de Microsoft Threat Intelligence.
* Aplica a Blob Storage y Azure Files.
* Tiene limitaciones de protocolos y operaciones, por ejemplo SMB y determinadas operaciones Put Block/Put Block List.

---

# Malware Scanning in Defender for Storage

Malware Scanning protege Azure Blob Storage contra contenido malicioso mediante análisis completo prácticamente en tiempo real utilizando **Microsoft Defender Antivirus**.

Es una solución:

* Agentless.
* SaaS.
* Sin mantenimiento.
* Implementable a escala.
* Capaz de automatizar respuestas.

## Características principales

* Detecta malware polimórfico y metamórfico.
* Analiza todos los tipos de archivos, incluidos ZIP.
* Tamaño máximo de archivo: **2 GB**.
* Devuelve un resultado por cada archivo analizado.
* Permite automatizar acciones como eliminar o poner en cuarentena archivos.
* Genera alertas detalladas de Defender for Cloud cuando detecta malware.
* Puede registrar todos los resultados para requisitos de seguridad y cumplimiento.

El análisis es prácticamente en tiempo real, pero el tiempo puede variar según tamaño y tipo de archivo y carga del servicio o de la cuenta de almacenamiento.

## Casos de uso

* Aplicaciones web que permiten cargas de archivos.
* Protección de contenido como vídeos y fotografías.
* Plataformas de distribución de contenido y CDN.
* Cumplimiento normativo.
* Integraciones con partners, desarrolladores y contratistas externos.
* Plataformas colaborativas.
* Data pipelines y procesos ETL.
* Datos utilizados para entrenamiento de machine learning.

## Prerrequisitos y habilitación

Se requieren roles **Owner**, como Subscription Owner o Storage Account Owner, o roles específicos con las acciones de datos necesarias.

Puede habilitarse mediante:

* Azure built-in policy — método recomendado.
* Terraform.
* Bicep.
* ARM templates.
* Azure Portal.
* REST API.

## Funcionamiento

### On-upload scanning

Cada blob cargado en una cuenta protegida dispara un análisis.

* Todos los métodos de carga generan el análisis.
* Modificar un blob también se considera una operación de carga y provoca un nuevo análisis.

### Regiones y retención

* El contenido se analiza **in-memory**.
* Los archivos se eliminan inmediatamente después del análisis.
* El contenido no se conserva.
* El análisis se realiza en la misma región de la cuenta.
* Para archivos sospechosos puede compartirse metadata con Microsoft Defender for Endpoint, incluido el hash SHA-256.

### Acceso a los datos

Durante la habilitación se crea un recurso **StorageDataScanner** en la suscripción.

Este recurso recibe:

* System-assigned managed identity.
* Rol **Storage Blob Data Owner**.

Se utiliza para acceder a los datos durante Malware Scanning y Sensitive Data Discovery.

### Private Endpoint

Malware Scanning funciona con cuentas de almacenamiento que utilizan **Private Endpoints**, manteniendo la privacidad de los datos.

## Recursos creados automáticamente

Al habilitar Malware Scanning:

* Se crea un **Event Grid System Topic** para escuchar los eventos de carga de blobs.
* Se crea **StorageDataScanner** con identidad administrada.
* A nivel de suscripción puede crearse **DefenderForStorageSecurityOperator**, utilizado para habilitar y reparar la configuración de Defender for Storage y Malware Scanning en cuentas existentes y nuevas.

Modificar o eliminar recursos, identidades o configuraciones de red necesarias puede detener Malware Scanning. Para restaurarlo puede ser necesario deshabilitarlo y volverlo a habilitar.

## Resultados del análisis

Los resultados pueden obtenerse mediante:

### Blob Index Tags

Contienen:

* Resultado del análisis.
* Hora del análisis en UTC.

Son útiles para automatizar workflows, aunque **no son resistentes a manipulación**.

### Defender for Cloud Security Alerts

Cuando se detecta malware, se genera una alerta con:

* Archivo afectado.
* Tipo de malware.
* Contexto.
* Recomendaciones de investigación y remediación.

Las alertas pueden:

* Consultarse desde Defender for Cloud.
* Utilizarse para automatizaciones.
* Exportarse a SIEM, incluido Microsoft Sentinel.

### Event Grid

Es el método más rápido para automatización basada en eventos.

Puede utilizar:

* Function App.
* Webhook.
* Event Hubs.
* Service Bus Queue.

Permite automatizar acciones como mover, eliminar o poner en cuarentena archivos.

### Log Analytics

Permite almacenar los resultados para cumplimiento y auditoría.

La tabla utilizada es:

`StorageMalwareScanningResults`

## Costos

Malware Scanning se factura por **GB escaneado** y no está incluido gratuitamente en el período de prueba de 30 días.

Se puede establecer un límite mensual por cuenta de almacenamiento:

* Valor predeterminado: **5.000 GB / 5 TB por mes**.
* El límite puede establecerse por cuenta o suscripción.
* Al alcanzar el límite, el análisis se detiene, con una desviación de hasta 20 GB.
* El límite se reinicia mensualmente a medianoche UTC.
* Los cambios normalmente tardan hasta una hora en aplicarse.

También pueden generarse costos adicionales por:

* Azure Storage read operations.
* Azure Storage blob indexing.
* Azure Event Grid notifications.

## Falsos positivos y negativos

Los archivos pueden enviarse al portal de envío de muestras para análisis cuando existe un falso positivo o falso negativo.

Defender for Cloud permite suprimir falsos positivos, limitando la regla mediante nombre del malware o hash del archivo.

Malware Scanning **no bloquea automáticamente el acceso ni modifica permisos** del blob aunque sea malicioso.

## Limitaciones

No soporta:

* Legacy Storage Accounts v1.
* Azure Files.
* Regiones Jio India West, Korea South y South Africa West.
* Append Blobs.
* Page Blobs.
* Client-side encrypted blobs.
* Resultados mediante index tags en cuentas con Hierarchical Namespace habilitado.
* Event Grid topics sin acceso público.

Los blobs cifrados con **Customer Managed Key (CMK)** sí están soportados.

### Límites de capacidad

* Hasta **2 GB/minuto por cuenta de almacenamiento**.
* Hasta **2.000 archivos/minuto por cuenta**.
* Máximo **2 GB por blob**.

Si el volumen de carga supera constantemente estos límites, algunos blobs pueden no ser analizados.

El análisis genera una operación adicional de lectura y actualiza los index tags, pero normalmente el impacto sobre la carga y los IOPS es mínimo.

---

# Detect threats to sensitive data

**Sensitive data threat detection** permite priorizar y analizar alertas considerando la sensibilidad de los datos que podrían estar en riesgo.

Ayuda a:

* Detectar eventos de exposición.
* Identificar actividades sospechosas en recursos con datos sensibles.
* Priorizar incidentes.
* Reducir la probabilidad de filtraciones de datos.

Es una funcionalidad configurable del **nuevo Defender for Storage plan**, incluida sin costo adicional.

## Sensitive Data Discovery

Utiliza un motor **agentless** que emplea un método de muestreo inteligente para identificar recursos que contienen datos sensibles.

Está integrado con:

* Microsoft Purview Sensitive Information Types (SITs).
* Microsoft Purview classification labels.

Esto permite heredar la configuración de sensibilidad de la organización.

### Escaneos

Al habilitar la funcionalidad:

* Se inicia automáticamente el análisis de las cuentas compatibles.
* Los resultados normalmente aparecen dentro de **24 horas**.
* Las nuevas cuentas de almacenamiento bajo suscripciones protegidas se analizan dentro de **6 horas** de su creación.
* Después se realizan análisis recurrentes semanalmente.

Es el mismo motor utilizado por **Defender CSPM** para descubrir datos sensibles.

## Almacenamiento soportado

Está disponible para Blob Storage:

* Standard general-purpose V1.
* Standard general-purpose V2.
* Azure Data Lake Storage Gen2.
* Premium block blobs.

Se requieren permisos relacionados con datos de **Subscription Owner** o **Storage Account Owner**.

## Habilitación

Sensitive data threat detection:

* Está habilitado por defecto al activar Defender for Storage.
* Puede habilitarse o deshabilitarse desde Azure Portal u otros métodos de implementación a escala.
* Está incluido en el precio de Defender for Storage.

## Información de sensibilidad en las alertas

Las alertas pueden incluir:

* **Sensitivity scanning time UTC:** última fecha de análisis.
* **Top sensitivity label:** etiqueta más sensible encontrada.
* **Sensitive information types:** tipos de información encontrados y si provienen de reglas personalizadas.
* **Sensitive file types:** tipos de archivos que contienen datos sensibles.

## Integración con Microsoft Purview

La funcionalidad utiliza tipos de información sensible integrados de Microsoft Purview.

También permite:

* Modificar los valores predeterminados.
* Crear SITs personalizados.
* Integrar la configuración organizacional.
* Crear y publicar sensitivity labels.
* Utilizar reglas de auto-labeling.

---

# Deploy Microsoft Defender for Storage

Microsoft Defender for Storage es una solución nativa de Azure que utiliza:

* Microsoft Threat Intelligence.
* Microsoft Defender Antimalware.
* Sensitive Data Discovery.

Protege:

* Azure Blob Storage.
* Azure Files.
* Azure Data Lake Storage.

Proporciona:

* Suite de alertas.
* Malware Scanning prácticamente en tiempo real como add-on.
* Sensitive data threat detection sin costo adicional.

Ayuda a prevenir:

* Cargas de archivos maliciosos.
* Exfiltración de datos sensibles.
* Corrupción de datos.

Permite aplicar políticas de seguridad consistentes a nivel de suscripción y cuenta de almacenamiento, manteniendo controles granulares.

Si se utiliza el **Microsoft Defender for Storage classic**, se recomienda migrar al nuevo plan.

## Configuración

Puede configurarse:

* Habilitar/deshabilitar Defender for Storage a nivel de suscripción.
* Habilitar/deshabilitarlo a nivel de cuenta.
* Activar/desactivar Malware Scanning.
* Activar/desactivar Sensitive Data Threat Detection.
* Configurar un límite mensual de Malware Scanning.
* Configurar métodos de respuesta ante resultados.
* Configurar métodos de almacenamiento de logs.
* Sobrescribir configuraciones de suscripción para cuentas específicas.

El límite predeterminado de Malware Scanning es **5.000 GB mensuales por cuenta**.

## Métodos de implementación

* **Azure built-in policy** — recomendado.
* Terraform.
* Bicep.
* ARM templates.
* Azure Portal.
* PowerShell.
* REST API.

La utilización de una policy facilita la habilitación a escala y mantiene una configuración consistente en cuentas existentes y futuras dentro del ámbito definido, incluso management groups.

---

# Enable/configure Azure built-in policy

La habilitación mediante policy es el método recomendado porque permite aplicar Defender for Storage a escala y mantener una configuración consistente en cuentas existentes y futuras.

También es posible configurar cuentas específicas con valores diferentes mediante **override de la configuración de suscripción**.

## Procedimiento

1. Abrir **Azure Portal**.
2. Ir a **Policy**.
3. Seleccionar **Definitions**.
4. Buscar **Configure Microsoft Defender for Storage to be enabled**.
5. Revisar la policy.
6. Seleccionar **Assign**.
7. Configurar los detalles y reglas necesarias.
8. Seleccionar **Review + create**.
9. Seleccionar **Create**.

La policy **Configure Microsoft Defender for Storage to be enabled** habilita:

* Activity Monitoring.
* Malware Scanning.
* Sensitive Data Threat Detection.

Existe también **Configure basic Microsoft Defender for Storage to be enabled (Activity Monitoring only)** para habilitar únicamente Activity Monitoring.

## Resultados de Malware Scanning

Los resultados pueden enviarse a:

### Event Grid custom topic

Permite respuestas automáticas prácticamente en tiempo real ante cada resultado del análisis.

### Log Analytics workspace

Permite almacenar todos los resultados en un repositorio centralizado para:

* Compliance.
* Auditoría.
* Investigación.

---

# Implement and manage Microsoft Defender Vulnerability Management

La evaluación de vulnerabilidades para Azure, basada en **Microsoft Defender Vulnerability Management**, permite descubrir y remediar vulnerabilidades en imágenes de contenedores sin configuración adicional ni despliegue de agentes.

La funcionalidad analiza imágenes almacenadas en **Azure Container Registry (ACR)**.

Las imágenes almacenadas en otros registros deben importarse a ACR para obtener cobertura.

## Funcionamiento general

Una vez habilitada la capacidad:

* Las imágenes de ACR que cumplen los criterios de análisis se escanean automáticamente.
* No es necesario configurar individualmente registros o usuarios.
* Se generan recomendaciones con reportes de vulnerabilidades.
* Se analizan imágenes almacenadas en ACR.
* También se proporciona información sobre imágenes ejecutándose en AKS que fueron obtenidas desde ACR u otros registros soportados por Defender for Cloud, como ECR, GCR y GAR.
* Las imágenes se analizan después de agregarse al registro.
* Se vuelven a analizar para detectar nuevas vulnerabilidades.

## Capacidades

### OS packages

Analiza vulnerabilidades en paquetes instalados mediante gestores de paquetes de Linux y Windows.

### Language-specific packages

En Linux analiza paquetes, archivos y dependencias específicos de lenguajes instalados o copiados sin utilizar el gestor de paquetes del SO.

### Azure Private Link

Permite analizar imágenes almacenadas en registros accesibles mediante Azure Private Link, con acceso a trusted services y autenticación del registro.

### Exploitability information

Los reportes de vulnerabilidades se contrastan con bases de datos de explotación para ayudar a determinar el riesgo real de cada vulnerabilidad.

## Recomendaciones

### Nuevas recomendaciones Preview

Estas recomendaciones están orientadas a la vista basada en riesgo y no cuentan para **Secure Score** mientras están en Preview.

* **Container images in Azure registry should have vulnerability findings resolved**

  * Detecta CVE conocidas en imágenes de registro.
  * Proporciona resultados detallados para cada imagen.

* **Containers running in Azure should have vulnerability findings resolved**

  * Genera un inventario de workloads de contenedores ejecutándose en clústeres Kubernetes.
  * Relaciona las imágenes utilizadas con los reportes de vulnerabilidades.

### Recomendaciones GA actuales

Orientadas a la vista clásica de recomendaciones:

* **Azure registry container images should have vulnerabilities resolved**

  * Analiza imágenes del registro para detectar CVE conocidas.
  * Proporciona un reporte detallado por imagen.

* **Azure running container images should have vulnerabilities resolved**

  * Proporciona visibilidad de imágenes vulnerables actualmente ejecutándose en Kubernetes.
  * Permite identificar y remediar vulnerabilidades en workloads activos.

## Consultas y excepciones

La información de vulnerabilidades puede consultarse mediante:

* **Azure Resource Graph**.
* **REST API**.

También se admiten:

* Exemptions para management groups, resource groups o subscriptions.
* Deshabilitación de vulnerabilidades específicas en imágenes.

---

# Scan triggers

## Análisis inicial

* Cada imagen **pushed o imported** a un registro se analiza.
* Normalmente el análisis termina en pocos minutos, aunque en casos excepcionales puede tardar hasta una hora.
* Cada imagen **pulled** desde un registro genera un análisis dentro de las 24 horas.

## Reanálisis continuo

Los reanálisis se realizan una vez al día para:

* Imágenes enviadas al registro durante los últimos **90 días**.
* Imágenes descargadas durante los últimos **30 días**.
* Imágenes actualmente ejecutándose en clústeres Kubernetes monitorizados por Defender for Cloud.

La nueva recomendación Preview para imágenes del registro se genera para imágenes enviadas durante los últimos **30 días**.

---

# Cómo funciona el análisis de imágenes

Al habilitar Container Vulnerability Assessment:

1. Se autoriza a Defender for Cloud a analizar imágenes en Azure Container Registries.
2. Defender for Cloud descubre automáticamente:

   * Registros.
   * Repositorios.
   * Imágenes existentes y nuevas.
3. Cuando se publica una nueva imagen en ACR, Defender for Cloud recibe una notificación.
4. La imagen se incorpora al catálogo y se programa para análisis.
5. Diariamente:

   * Se descubren y analizan nuevas imágenes.
   * Se crea un inventario de cada imagen.
   * Se generan reportes de vulnerabilidades.
   * Se actualizan los reportes de imágenes previamente analizadas según los criterios de reanálisis.
6. Los resultados se presentan mediante recomendaciones de seguridad.

Para imágenes ejecutándose en AKS, Defender for Cloud utiliza:

* **Agentless discovery for Kubernetes**.
* Inventario recopilado mediante el **Defender sensor** en los nodos AKS.

### Frecuencia del inventario en AKS

* Solo Agentless discovery: actualización cada **7 horas**.
* Agentless discovery + Defender sensor: actualización cada **2 horas**.
* Los resultados del análisis de imágenes se actualizan cada **24 horas**.

## Defender for Container Registries deprecated

Para **Defender for Container Registries**, actualmente deprecated:

* Las imágenes se analizan una vez al hacer push.
* Se analizan al hacer pull.
* El reanálisis se realiza una vez por semana.

---

# Eliminación de imágenes

Cuando una imagen se elimina de Azure Container Registry:

* ACR notifica a Defender for Cloud.
* El assessment de vulnerabilidades se elimina normalmente dentro de **una hora**.
* En casos excepcionales en los que Defender for Cloud no recibe la notificación, la eliminación de las vulnerabilidades asociadas puede tardar hasta **tres días**.
