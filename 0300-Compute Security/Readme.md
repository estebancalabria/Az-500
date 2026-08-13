# Seguridad avanzada de recursos de cómputo en Azure

**Objetivo:** Planificar, implementar y administrar medidas de seguridad avanzadas para proteger recursos de cómputo, aplicaciones y datos de Azure frente a accesos no autorizados, vulnerabilidades y amenazas.

## Azure Bastion y acceso remoto

* **Azure Bastion** proporciona conexiones RDP/SSH seguras a VMs mediante Azure Portal o clientes nativos, sin necesidad de IP pública, agentes ni software especial en las VMs.
* Las conexiones utilizan **TLS sobre el puerto 443**, evitando exponer los puertos RDP/SSH directamente a Internet.
* Es un servicio **PaaS completamente administrado** que protege las VMs frente a escaneo de puertos y vulnerabilidades.
* SKUs: **Developer, Basic, Standard y Premium**.

  * **Developer:** gratuito, compartido, una conexión simultánea, orientado a desarrollo/pruebas, sin peering.
  * **Basic:** 2 instancias fijas, hasta 40 sesiones RDP o 80 SSH.
  * **Standard/Premium:** 2-50 instancias configurables, hasta 1.000 sesiones RDP o 2.000 SSH.
* Standard y Premium permiten **host scaling**.
* **Premium** agrega grabación de sesiones, implementación exclusivamente privada e integración con Microsoft Entra PIM.
* En implementaciones dedicadas, Bastion utiliza `AzureBastionSubnet` con prefijo mínimo `/26`.
* Premium permite eliminar la exposición pública de Bastion y es adecuado para entornos con ExpressRoute/VPN y estricta segmentación de red.

## Azure Kubernetes Service (AKS)

* **AKS** es un servicio administrado de Kubernetes para desplegar y administrar aplicaciones contenerizadas, reduciendo la complejidad y sobrecarga operativa.
* Azure administra el **control plane**, incluyendo mantenimiento y monitoreo; se pagan los nodos que ejecutan las aplicaciones.
* Es adecuado para aplicaciones que requieren **alta disponibilidad, escalabilidad y portabilidad**, y permite despliegues multirregión e integración con herramientas DevOps.
* AKS es **CNCF-certified** y cumple con SOC, ISO, PCI DSS y HIPAA.
* Azure Linux 2.0 dejó de recibir soporte y actualizaciones de seguridad el **30 de noviembre de 2025**; debe migrarse a Azure Linux 3.

### Capacidades principales

* **Identidad y seguridad:** Azure Policy, Kubernetes RBAC, Microsoft Entra ID y Workload Identity.
* **Monitoreo:** Container Insights, Azure Monitor, Network Observability, Prometheus y Grafana.
* **Despliegue:** configuraciones predefinidas, KEDA y Draft for AKS.
* **Clusters y nodos:** múltiples node pools, Windows Server containers, GPUs, autoscaling y confidential computing.
* **Storage:** Azure Disks, Azure Files y Azure NetApp Files.
* **Networking:** Kubenet, Azure CNI, CNI externo y application routing.
* **Herramientas:** Helm, extensión de Kubernetes para Visual Studio Code e Istio service mesh.

### Aislamiento y multitenancy

* Kubernetes permite aislar lógicamente equipos y workloads mediante **Namespaces**, asignando el mínimo nivel de privilegios necesario.
* El aislamiento considera:

  * **Scheduling:** resource quotas, pod disruption budgets, taints/tolerations, node selectors y afinidad/anti-afinidad.
  * **Networking:** network policies para controlar el tráfico de los pods.
  * **Authentication/authorization:** Kubernetes RBAC, Microsoft Entra ID, workload identities y Azure Key Vault.
  * **Containers:** Pod Security Standards, Pod Security Admission, Azure Policy, Microsoft Defender for Containers, AppArmor y Seccomp.
* Se recomienda **aislamiento lógico** para separar equipos, proyectos o workloads dentro de un mismo cluster, reduciendo costos y mejorando la utilización de recursos.
* Para workloads **multitenant hostiles**, donde los tenants no son confiables, se recomienda utilizar **clusters físicamente aislados**.
* El aislamiento físico aumenta la administración y los costos, además de generar menor densidad de pods y recursos sin utilizar.

## Seguridad y monitoreo de AKS

**Microsoft Defender for Containers** protege, monitorea y mantiene la seguridad de clusters Kubernetes, nodos, workloads, registros e imágenes en entornos multicloud y on-premises.

* **Security posture management:** inventario, monitoreo continuo, detección de configuraciones incorrectas, recomendaciones y evaluación de riesgos.
* **Vulnerability assessment:** análisis de vulnerabilidades de imágenes, contenedores y nodos, con recomendaciones de remediación y datos de explotabilidad.
* **Runtime threat protection:** detección de amenazas en tiempo real, inteligencia de amenazas, MITRE ATT&CK y respuesta automatizada.
* **Software supply chain protection:** controles desde el build hasta el deployment y bloqueo de imágenes que incumplen políticas.
* **Deployment & monitoring:** monitoreo de clusters, sensores y recursos no monitorizados.
* **Agentless discovery:** descubrimiento de clusters, configuraciones y deployments mediante APIs.
* **Agentless vulnerability assessment:** análisis de nodos, imágenes y contenedores en ejecución, con actualizaciones diarias.
* **Azure Policy for Kubernetes:** permite aplicar y hacer cumplir buenas prácticas de seguridad sobre las solicitudes al API de Kubernetes.
* **Binary drift detection:** detecta procesos externos no autorizados dentro de contenedores.
* **Runtime protection:** detecta eventos como dashboards Kubernetes expuestos, creación de roles con altos privilegios y mounts sensibles.
* **Kubernetes gated deployment:** utiliza admission control para bloquear o auditar deployments de imágenes que incumplan las políticas.
* Las alertas de runtime de workloads Kubernetes utilizan el prefijo `K8S.NODE_`.
* Las amenazas pueden investigarse y responderse desde **Microsoft Defender XDR**.

## Autenticación en AKS

* Integrar AKS con **Microsoft Entra ID** para centralizar la administración de identidades y mantener actualizado el acceso según los cambios en usuarios y grupos.
* Utilizar **Roles, ClusterRoles y Bindings** para aplicar el mínimo nivel de permisos.
* **Kubernetes RBAC** permite controlar granularmente el acceso a recursos a nivel de cluster o namespace.
* **Azure RBAC** controla el acceso a los recursos AKS dentro de las suscripciones.
* El acceso a AKS comprende:

  * Acceso al recurso AKS en Azure, por ejemplo para escalar, actualizar o recuperar `kubeconfig`.
  * Acceso al Kubernetes API mediante Kubernetes RBAC o Azure RBAC integrado.

### Workload Identity

* Utilizar **Workload Identity con federación OIDC** para autenticar pods frente a recursos de Azure.
* Evitar credenciales fijas dentro de pods o imágenes.
* El flujo utiliza cuentas de servicio Kubernetes y federación OIDC:

  1. Se establece una relación de confianza entre Microsoft Entra ID y la cuenta de servicio.
  2. AKS proyecta un token firmado en el pod.
  3. Azure Identity SDK intercambia el token por un token de acceso de Microsoft Entra mediante OIDC.
  4. La aplicación utiliza el token para acceder al recurso de Azure.
* Beneficios:

  * No requiere secretos.
  * Rotación automática de tokens.
  * Control de acceso granular.
  * Utiliza OIDC.
  * No requiere componentes a nivel de nodo.

## Seguridad y monitoreo de Azure Container Instances (ACI)

* **Azure Monitor** proporciona información sobre el uso de recursos de los container instances y permite determinar configuraciones adecuadas.
* Métricas disponibles:

  * CPU Usage.
  * Memory Usage.
  * Network bytes received.
  * Network bytes transmitted.
* Las métricas están disponibles para grupos de contenedores y contenedores individuales.
* Actualmente, las métricas están disponibles para **contenedores Linux**.
* Pueden consultarse desde Azure Portal o Azure CLI.
* En grupos con múltiples contenedores puede utilizarse `containerName` para obtener métricas individuales.

## Seguridad de Azure Container Apps (ACA)

* Utilizar **registros privados**, como Azure Container Registry, para reducir el riesgo asociado a imágenes públicas.
* Azure Container Registry admite autenticación mediante Microsoft Entra ID y control de acceso basado en roles.
* **Microsoft Defender for Containers** puede analizar imágenes de ACR y detectar vulnerabilidades.
* Proteger credenciales y secretos mediante soluciones de administración de secretos, cifrado y **least privilege Azure RBAC**.
* **Azure Key Vault** protege claves, certificados, connection strings y contraseñas.

### Seguridad durante el ciclo de vida de contenedores

* Incorporar **vulnerability management** durante todo el ciclo de desarrollo.
* Escanear imágenes antes de enviarlas al registro y continuar analizándolas posteriormente.
* Relacionar vulnerabilidades de imágenes con los contenedores que las ejecutan.
* Utilizar únicamente **imágenes y registros aprobados**.
* Utilizar imágenes base mínimas y controlar su procedencia.
* Utilizar **image signing** o fingerprinting para verificar integridad.
* Mantener las imágenes de producción en registros privados y actualizarlas periódicamente.
* Utilizar pipelines **CI** con análisis de seguridad para impedir que imágenes vulnerables lleguen a producción.
* Aplicar **least privilege** durante el runtime y eliminar procesos y privilegios innecesarios.
* Preaprobar los archivos y ejecutables que los contenedores pueden utilizar.
* Implementar **network segmentation** entre contenedores y workloads.
* Monitorear continuamente la actividad, el acceso de usuarios y los recursos utilizados por los contenedores.
* **Container Insights**, integrado con Azure Monitor, proporciona visibilidad sobre workloads en AKS y Azure Arc-enabled Kubernetes.
* La **Azure Container Monitoring Solution** fue retirada el 31 de agosto de 2024; debe utilizarse Container Insights.
* Mantener registros de acceso administrativo a clusters, registros e imágenes para auditoría y análisis forense.

## Administración del acceso a Azure Container Registry (ACR)

* Utilizar **Azure RBAC** para asignar permisos a usuarios, service principals y otras identidades.
* Se pueden utilizar roles integrados o crear **custom roles** con permisos granulares.
* Roles principales:

  * **Owner / Contributor:** acceso amplio al registro y operaciones de imágenes.
  * **Reader:** acceso de lectura.
  * **AcrPush:** push y pull de imágenes.
  * **AcrPull:** pull de imágenes.
  * **AcrDelete:** eliminación de datos de imágenes.
  * **AcrImageSigner:** firma de imágenes.
* Aplicar siempre el **mínimo conjunto de permisos** necesario.
* Para CI/CD que realiza `docker push`, utilizar **AcrPush** en lugar de Contributor.
* Los nodos que ejecutan contenedores requieren **AcrPull**, sin necesidad de Reader.
* Herramientas como Visual Studio Code Docker extension pueden requerir Reader o Contributor para listar y administrar registros.
* El acceso a **Azure Resource Manager** es necesario para operaciones como listar registros desde Azure Portal o Azure CLI.
* Los permisos incluyen crear/eliminar registros, pull/push de imágenes, eliminar datos, modificar políticas y firmar imágenes.

### Métodos de autenticación de ACR

* **Microsoft Entra identities:** método recomendado para producción; soporta usuarios, service principals y managed identities.
* **Repository-scoped permissions:** tokens con permisos limitados a repositorios específicos.
* **Admin account:** permisos completos; debe deshabilitarse en producción y utilizarse principalmente para pruebas.

## Cifrado de discos y datos en reposo

* **Encryption at Rest** protege los datos almacenados y proporciona defensa en profundidad, además de ayudar a cumplir requisitos de gobernanza y regulaciones como HIPAA, PCI y FedRAMP.
* Azure permite utilizar claves administradas por el servicio o controlar las claves mediante soluciones administradas por el cliente.
* Las claves deben almacenarse en ubicaciones seguras con control de acceso y auditoría.
* **Azure Key Vault** es la solución recomendada para almacenar y administrar claves.
* **Microsoft Entra ID** controla los permisos para administrar o utilizar las claves.
* Azure utiliza **envelope encryption**:

  * **DEK (Data Encryption Key):** clave AES-256 utilizada para cifrar los datos.
  * **KEK (Key Encryption Key):** clave que protege las DEK y permanece en Key Vault.

### Azure Disk Encryption

* **Azure Disk Encryption (ADE)** permite cifrar discos y VMs IaaS Windows o Linux.
* Managed Disks, Snapshots e Images utilizan Storage Service Encryption con claves administradas por el servicio.
* ADE permite proteger datos que pueden persistirse durante el procesamiento, como page files, swap files, crash dumps o application logs.
* Para soluciones personalizadas se recomienda utilizar Azure Key Vault y managed identities para administrar las claves.

### Azure Storage y Azure SQL Database

* **Azure Storage** utiliza server-side encryption de forma predeterminada con claves administradas por el servicio.
* Blob Storage y Azure Files soportan claves administradas por el cliente en Azure Key Vault.
* Blob Storage, Tables y Queues soportan **client-side encryption**.
* **Azure SQL Database** soporta:

  * **Transparent Data Encryption (TDE)** para cifrado en reposo.
  * Claves administradas por el cliente en Azure Key Vault.
  * **Always Encrypted** para cifrado de columnas mediante claves administradas por el cliente.

## Configuración de seguridad para Azure API Management

* Utilizar como referencia el **Microsoft Cloud Security Benchmark** para establecer controles de seguridad en API Management.
* Supervisar el cumplimiento mediante **Microsoft Defender for Cloud** y **Azure Policy**.

### Seguridad de red

* Implementar **Azure API Management dentro de una VNet** para aislarlo y permitir acceso a servicios backend internos.
* El gateway y Developer Portal pueden configurarse como:

  * **External:** accesibles desde Internet.
  * **Internal:** accesibles únicamente desde la VNet.
* Utilizar **Network Security Groups (NSG)** para restringir o monitorear tráfico por puerto, protocolo, IP de origen y destino.
* Mantener abiertos los puertos requeridos por API Management para evitar interrupciones del servicio.
* Cuando no sea posible utilizar VNet, implementar **Azure Private Link/private endpoint** para proporcionar acceso privado.
* Deshabilitar el acceso público cuando sea posible.
* Azure Policy incluye controles para:

  * Recomendar el uso de VNet.
  * Deshabilitar el acceso público a endpoints de configuración.
* Para APIs HTTP críticas, utilizar **API Management en una VNet interna junto con Azure Application Gateway**, aprovechando sus capacidades de reverse proxy, balanceo L7, routing y WAF.

### Identidad y autenticación

* Utilizar **Microsoft Entra ID** como método de autenticación predeterminado cuando sea posible.
* Configurar Microsoft Entra ID para autenticar usuarios del Developer Portal.
* Proteger APIs mediante **OAuth 2.0 con Microsoft Entra ID**.
* Evitar y restringir métodos de autenticación locales siempre que sea posible.
* Las suscripciones de API Management proporcionan claves que pueden rotarse.
* Utilizar **Managed Identities** para que API Management acceda de forma segura a recursos protegidos por Microsoft Entra ID, como Azure Key Vault.
* Las credenciales de Managed Identity son administradas, rotadas y protegidas por la plataforma.
* Microsoft Entra ID puede utilizarse como proveedor de identidad para implementar **SSO** en el Developer Portal.

### Secretos y acceso privilegiado

* Integrar API Management con **Azure Key Vault** para almacenar de forma segura secretos, Named values, claves y certificados.
* Evitar cuentas administrativas locales y utilizar Microsoft Entra ID.
* Aplicar **Azure RBAC** para controlar de forma granular el acceso a API Management, APIs y políticas.
* Utilizar el principio de **least privilege**.
* Utilizar **Customer Lockbox** para revisar y aprobar o rechazar solicitudes de acceso de Microsoft a los datos durante escenarios de soporte.

### Protección de datos y certificados

* Las comunicaciones utilizan **TLS**; las llamadas al management plane se realizan mediante Azure Resource Manager sobre TLS y requieren un JWT válido.
* Las llamadas al data plane pueden protegerse mediante TLS y mecanismos como certificados de cliente o JWT.
* Utilizar Azure Key Vault para administrar claves y certificados.
* Gestionar el ciclo de vida de certificados: creación, importación, rotación, revocación, almacenamiento y eliminación.
* Configurar rotación automática cuando sea posible y utilizar rotación manual cuando no exista soporte automático.

### Azure Policy, logging y auditoría

* Utilizar **Azure Policy** para monitorear y aplicar configuraciones seguras en API Management.
* Habilitar **resource logs** para auditoría, troubleshooting y análisis de operaciones y errores.
* Categorías de logs:

  * `GatewayLogs`
  * `WebSocketConnectionLogs`

### Backup y recuperación

* Utilizar las capacidades nativas de **backup y restore de Azure API Management**.
* Los backups se almacenan en cuentas de **Azure Storage propiedad del cliente**.
* Estas capacidades permiten realizar backup y restore completo del sistema.
