# Container Security en Microsoft Defender for Containers

Microsoft Defender for Containers es una solución cloud-native para mejorar, monitorear y mantener la seguridad de activos contenerizados, como **clusters de Kubernetes, nodos, workloads, registros e imágenes de contenedores**, en entornos **multicloud y on-premises**.

## Dominios principales de seguridad

Defender for Containers aborda cuatro áreas:

* **Security posture management:** monitoreo continuo de APIs cloud, APIs de Kubernetes y workloads para descubrir recursos, mantener inventarios, detectar configuraciones incorrectas, proporcionar recomendaciones, evaluar riesgos y realizar hunting mediante Security Explorer.
* **Vulnerability assessment:** evaluación de vulnerabilidades sin agentes para Azure, AWS y GCP, con recomendaciones de remediación, sin configuración, análisis diarios, cobertura de paquetes del sistema operativo y lenguajes, e información sobre explotabilidad.
* **Run-time threat protection:** detección de amenazas en tiempo real para clusters, nodos y workloads de Kubernetes, basada en inteligencia de amenazas de Microsoft, integración con MITRE ATT&CK, respuesta automatizada e integración con SIEM/XDR.
* **Deployment & monitoring:** monitoreo de clusters para detectar agentes faltantes, despliegue a escala de capacidades basadas en agentes, compatibilidad con herramientas estándar de monitoreo de Kubernetes y administración de recursos no monitoreados.

## Disponibilidad de Microsoft Defender for Containers

* **Estado:** General Availability (GA), aunque algunas funcionalidades están en preview.
* **Disponibilidad de funcionalidades:** depende de la matriz de soporte de Containers de Defender for Cloud.
* **Pricing:** se factura según la página de precios de Microsoft Defender for Containers.
* **Roles y permisos:** dependen de los componentes que se desplieguen. Security Admin puede descartar alertas y Security Reader puede consultar resultados de evaluación de vulnerabilidades.
* **Clouds:** la disponibilidad depende de la matriz de soporte de Containers de Defender for Cloud.

## Security posture management

### Capacidades agentless

* **Agentless discovery for Kubernetes:** descubrimiento basado en APIs y sin footprint de los clusters de Kubernetes, sus configuraciones y deployments.
* **Agentless vulnerability assessment:** evaluación de vulnerabilidades de las imágenes de contenedores, con recomendaciones para registry y runtime, análisis rápidos de nuevas imágenes, actualización diaria de resultados e información sobre explotabilidad.
* La información de vulnerabilidades se incorpora al **security graph** para evaluación contextual del riesgo, cálculo de attack paths y capacidades de hunting.
* **Comprehensive inventory:** permite explorar recursos, pods, servicios, repositorios, imágenes y configuraciones mediante Security Explorer.
* **Enhanced risk-hunting:** permite buscar problemas de postura de seguridad mediante consultas integradas o personalizadas y security insights en Security Explorer.
* **Control plane hardening:** evalúa continuamente las configuraciones de los clusters y las compara con las iniciativas aplicadas a las suscripciones. Cuando detecta configuraciones incorrectas, genera recomendaciones de seguridad en Defender for Cloud para investigar y remediar los problemas.

### Capacidades basadas en agentes

**Kubernetes data plane hardening:** permite proteger workloads de Kubernetes mediante recomendaciones de buenas prácticas utilizando Azure Policy for Kubernetes.

Con el add-on instalado en el cluster, cada solicitud al servidor API de Kubernetes se analiza contra un conjunto predefinido de buenas prácticas antes de persistirse. Puede configurarse para **hacer cumplir estas prácticas y bloquear futuras implementaciones que no las cumplan**.

Ejemplo: se puede impedir la creación de contenedores privilegiados.

## Vulnerability assessment

Defender for Containers analiza imágenes de contenedores en:

* Azure Container Registry (ACR)
* Amazon Elastic Container Registry (ECR)
* Google Artifact Registry (GAR)
* Google Container Registry (GCR)

La evaluación es agentless e incluye:

* Recomendaciones para registry y runtime.
* Guías de remediación.
* Análisis rápidos de nuevas imágenes.
* Información sobre exploits reales.
* Información sobre explotabilidad.

La información proporcionada por **Microsoft Defender Vulnerability Management** se incorpora al cloud security graph para evaluación contextual del riesgo, cálculo de attack paths y hunting.

En Azure existen dos soluciones de evaluación de vulnerabilidades:

* Una basada en **Microsoft Defender Vulnerability Management**.
* Otra basada en **Qualys**.

## Run-time protection para nodos y clusters de Kubernetes

Defender for Containers proporciona protección contra amenazas en tiempo real para entornos contenerizados compatibles y genera alertas ante actividades sospechosas.

La protección cubre:

* **Nivel de cluster.**
* **Nivel de nodo.**
* **Nivel de workload.**

Utiliza tanto:

* **Cobertura basada en agentes**, que requiere Defender agent.
* **Cobertura agentless**, basada en el análisis de los Kubernetes audit logs.

Las alertas de seguridad solo se generan para acciones y deployments realizados **después de habilitar Defender for Containers** en la suscripción.

### Ejemplos de eventos monitoreados

* Kubernetes dashboards expuestos.
* Creación de roles con privilegios elevados.
* Creación de mounts sensibles.

Las alertas se pueden consultar desde **Security alerts** en la página Overview de Defender for Cloud.

Las alertas de runtime workload en los clusters se identifican mediante el prefijo **`K8S.NODE_`** en el tipo de alerta.

Defender for Containers también incluye detección de amenazas a nivel de host, con **más de 60 analíticas, detecciones basadas en IA y detecciones de anomalías específicas de Kubernetes**, basadas en los workloads en runtime.

## Managed Kubernetes threat factors

La seguridad de un entorno Kubernetes administrado requiere identificar y mitigar amenazas en diferentes niveles:

### Compromised Account

* **Amenaza:** acceso al cluster mediante credenciales, API tokens o keys robadas, pudiendo provocar acceso no autorizado, robo de datos y deployments maliciosos.
* **Mitigación:** autenticación fuerte, MFA, rotación periódica de credenciales y principio de mínimo privilegio.

### Vulnerable or Misconfigured Images

* **Amenaza:** imágenes con vulnerabilidades, software desactualizado, configuraciones inseguras o secretos incorporados pueden ser explotadas una vez desplegadas.
* **Mitigación:** escanear imágenes antes del deployment, aplicar políticas de procedencia y utilizar firma de imágenes.

### Environment Misconfiguration

* **Amenaza:** configuraciones incorrectas de Kubernetes o de los deployments pueden exponer el cluster. Ejemplos: dashboards expuestos, RBAC demasiado permisivo y endpoints sin protección.
* **Mitigación:** seguir buenas prácticas de configuración, realizar auditorías automatizadas y utilizar admission controllers para aplicar políticas.

### Application Attack Level

* **Amenaza:** las aplicaciones contenerizadas pueden presentar vulnerabilidades tradicionales como SQL injection o XSS, que pueden comprometer el contenedor o facilitar una explotación posterior del cluster.
* **Mitigación:** validación de entradas, encoding de salidas, Web Application Firewalls (WAFs) e incorporación de seguridad en los pipelines CI/CD mediante herramientas de análisis estático y dinámico.

### Node Level Attack

* **Amenaza:** el acceso a un nodo puede permitir al atacante escalar privilegios y obtener control del cluster. Las vulnerabilidades pueden originarse en sistemas operativos o componentes de Kubernetes desactualizados.
* **Mitigación:** mantener los nodos actualizados, restringir su acceso mediante políticas de red y firewalls, y utilizar sistemas HIDS.

### Unauthorized Traffic

* **Amenaza:** configuraciones incorrectas de las políticas de red pueden permitir accesos no autorizados hacia o desde el cluster, facilitando exfiltración de datos, malware o explotación de servicios expuestos.
* **Mitigación:** aplicar políticas estrictas de ingress y egress, separar workloads sensibles mediante namespaces y monitorear el tráfico para detectar patrones anómalos.

Una estrategia de seguridad robusta para Kubernetes requiere un enfoque **multicapa**, combinando prácticas específicas de Kubernetes con medidas de seguridad tradicionales. El monitoreo continuo, las auditorías periódicas y el principio de mínimo privilegio son elementos fundamentales.

## Common attack techniques

### Exploit vulnerable images

Una aplicación vulnerable expuesta públicamente puede proporcionar el acceso inicial al cluster.

Ejemplos de casos conocidos: **SolarWinds** y **Log4j**.

### Access exposed applications

Una interfaz sensible expuesta a Internet puede representar un riesgo, especialmente cuando determinados frameworks no requieren autenticación de forma predeterminada.

Una interfaz expuesta puede permitir acceso no autenticado, ejecución de código o deployment de contenedores en el cluster.

Ejemplos de interfaces que han sido explotadas:

* Apache NiFi
* Kubeflow
* Argo Workflows
* Weave Scope
* Kubernetes Dashboard

### Deploy backdoor containers

Los atacantes pueden ejecutar código malicioso dentro de un contenedor del cluster.

Mediante controladores de Kubernetes como **DaemonSets** o **Deployments**, pueden mantener una cantidad constante de contenedores maliciosos ejecutándose en uno o varios nodos.

### Abuse over-permissive roles — Service Accounts

Una **Service Account (SA)** representa la identidad de una aplicación en Kubernetes.

Por defecto, una SA se monta en cada pod creado y permite que los contenedores del pod realicen solicitudes al Kubernetes API server.

Si un atacante obtiene acceso a un pod, puede acceder al token de la SA y realizar acciones en el cluster según los permisos asignados.

* Sin **RBAC**, la SA tiene permisos ilimitados en el cluster.
* Con **RBAC**, sus permisos están determinados por los **RoleBindings** y **ClusterRoleBindings** asociados.

### Escape to the host

`hostPath` permite montar un directorio o archivo del host dentro de un contenedor.

Un atacante con permisos para crear un contenedor puede crear uno utilizando un `hostPath` con permisos de escritura y obtener persistencia en el host subyacente.

Por ejemplo, puede conseguirlo creando un **cron job en el host**.

# Defender for Containers architecture

Defender for Containers adapta su arquitectura según el entorno de Kubernetes:

* **Azure Kubernetes Service (AKS):** servicio administrado de Microsoft para desarrollar, implementar y administrar aplicaciones contenerizadas.
* **Amazon Elastic Kubernetes Service (EKS):** servicio administrado de AWS para ejecutar Kubernetes sin administrar directamente el control plane o los nodos.
* **Google Kubernetes Engine (GKE):** entorno administrado de Google para desplegar, administrar y escalar aplicaciones.
* **Kubernetes no administrado mediante Azure Arc-enabled Kubernetes:** clusters certificados por CNCF alojados on-premises o sobre IaaS.

Para proteger los contenedores, Defender for Containers recibe y analiza:

* Audit logs y eventos de seguridad del API server.
* Información de configuración del cluster desde el control plane.
* Configuración de workloads mediante Azure Policy.
* Señales y eventos de seguridad a nivel de nodo.

## Arquitectura con AKS

Cuando Defender for Cloud protege un cluster de **Azure Kubernetes Service**, los audit logs se recopilan de forma **agentless**, automáticamente mediante la infraestructura de Azure, sin configuración adicional ni costo adicional por esta recopilación.

Para obtener la protección completa se requieren:

### Defender agent

Es un **DaemonSet desplegado en cada nodo** que:

* Recopila señales de los hosts mediante tecnología **Extended Berkeley Packet Filter (eBPF)**.
* Proporciona protección en runtime.
* Está registrado con un Log Analytics workspace y funciona como pipeline de datos.
* Los audit logs no se almacenan en Log Analytics.
* Se despliega como parte del **AKS Security profile**.

### eBPF

**Extended Berkeley Packet Filter (eBPF)** es un framework del kernel de Linux que permite analizar y filtrar paquetes de red y realizar tareas a nivel del sistema.

Los programas eBPF:

* Se escriben en un subconjunto restringido de C.
* Se cargan directamente en el kernel.
* Se ejecutan en un entorno seguro y aislado.
* Permiten filtrado de paquetes, monitoreo de tráfico, enforcement de seguridad, análisis de protocolos y otras tareas de observabilidad y control.
* Pueden conectarse dinámicamente a diferentes hooks del kernel.
* Permiten procesamiento eficiente y en tiempo real.

eBPF se utiliza en aplicaciones de networking, monitoreo, detección de intrusiones, análisis de tráfico, seguridad y optimización de rendimiento.

### Azure Policy for Kubernetes

Es un pod que extiende **Gatekeeper v3** y se registra como webhook del admission control de Kubernetes.

Permite aplicar **enforcements y safeguards a escala**, de forma centralizada y consistente.

En AKS se despliega como un **AKS add-on** y solo se instala en un nodo del cluster.

## Componentes del Defender agent

| Pod                                   | Namespace     | Kind       | Función                                                                                                        | Capabilities                              | Límites de recursos      | Egress    |
| ------------------------------------- | ------------- | ---------- | -------------------------------------------------------------------------------------------------------------- | ----------------------------------------- | ------------------------ | --------- |
| `microsoft-defender-collector-ds-*`   | `kube-system` | DaemonSet  | Recopila inventario y eventos de seguridad del entorno Kubernetes                                              | `SYS_ADMIN`, `SYS_RESOURCE`, `SYS_PTRACE` | memory: 296Mi, CPU: 360m | No        |
| `microsoft-defender-collector-misc-*` | `kube-system` | Deployment | Recopila inventario y eventos de seguridad no asociados a un nodo específico                                   | N/A                                       | memory: 64Mi, CPU: 60m   | No        |
| `microsoft-defender-publisher-ds-*`   | `kube-system` | DaemonSet  | Publica los datos recopilados al backend de Microsoft Defender for Containers para su procesamiento y análisis | N/A                                       | memory: 200Mi, CPU: 60m  | HTTPS 443 |

## Agentless discovery para Kubernetes en Azure

El descubrimiento se basa en **snapshots tomados periódicamente**.

### Create

Cuando se habilita la extensión:

* Desde Defender CSPM se crea la identidad `CloudPosture/securityOperator/DefenderCSPMSecurityOperator`.
* Desde Defender for Containers se crea `CloudPosture/securityOperator/DefenderForContainersSecurityOperator`.

### Assign

Defender for Cloud asigna a la identidad el rol integrado **Kubernetes Agentless Operator** a nivel de suscripción.

El rol incluye:

* `Microsoft.ContainerService/managedClusters/read`
* `Microsoft.ContainerService/managedClusters/trustedAccessRoleBindings/write`
* `Microsoft.ContainerService/managedClusters/trustedAccessRoleBindings/read`
* `Microsoft.ContainerService/managedClusters/trustedAccessRoleBindings/delete`

### Discover

Mediante la identidad asignada por el sistema, Defender for Cloud descubre los clusters AKS mediante llamadas API al API server de AKS.

### Bind

Después de descubrir un cluster AKS, Defender for Cloud crea un **ClusterRoleBinding** entre la identidad creada y el ClusterRole:

`aks:trustedaccessrole:defender-containers:microsoft-defender-operator`

Este ClusterRole proporciona a Defender for Cloud permisos de lectura sobre el data plane del cluster.

# AKS Trusted Access

**Trusted Access** permite que servicios de Azure accedan de forma segura al API server de AKS sin concederles acceso administrativo ni hacer públicos los clusters.

El acceso utiliza el backend de Azure y **system-assigned managed identity**, sin requerir un private endpoint.

Trusted Access permite que los recursos autorizados accedan explícitamente a clusters AKS mediante un Azure role binding. El acceso se realiza a través del **AKS regional gateway** utilizando autenticación mediante system-assigned managed identity, mientras que los permisos de Kubernetes se asignan mediante roles.

Puede utilizarse con diferentes configuraciones, incluyendo:

* Clusters privados.
* Clusters con local accounts deshabilitadas.
* Clusters integrados con Microsoft Entra.
* Clusters con rangos de IP autorizados.

### Problemas que resuelve Trusted Access

* En clusters privados o con rangos IP autorizados, los servicios de Azure pueden no poder acceder al API server sin implementar un modelo de private endpoint.
* Conceder acceso administrativo a un servicio de Azure no sigue el principio de mínimo privilegio y puede aumentar el riesgo de escalación de privilegios o filtración de credenciales.

### Estado

Las funcionalidades de preview de AKS son opt-in y se proporcionan bajo modalidad "as is" y "as available".

La API de Trusted Access está generalmente disponible, mientras que el uso mediante Azure CLI continúa en preview y requiere la extensión `aks-preview`.

### Prerrequisitos

* Cuenta de Azure con una suscripción activa.
* Recursos compatibles con **system-assigned managed identity**.
* Para Azure CLI, extensión `aks-preview` versión **0.5.74 o posterior**.

# Arquitectura con Azure Arc-enabled Kubernetes

Para obtener la protección completa en clusters conectados mediante Azure Arc se requieren:

### Azure Arc-enabled Kubernetes

Solución basada en agentes instalada en un nodo del cluster que conecta el cluster con Defender for Cloud.

Defender for Cloud puede desplegar mediante Arc extensions:

* Defender agent.
* Azure Policy for Kubernetes.

### Defender agent

Es un **DaemonSet desplegado en cada nodo** que:

* Recopila señales del host mediante eBPF.
* Recopila Kubernetes audit logs.
* Proporciona protección en runtime.
* Utiliza Log Analytics como pipeline de datos.
* No almacena los audit logs en Log Analytics.
* Se despliega como una Arc-enabled Kubernetes extension.

### Azure Policy for Kubernetes

* Extiende Gatekeeper v3.
* Se registra como webhook del admission control.
* Permite aplicar políticas y safeguards a escala.
* Se despliega como Arc-enabled Kubernetes extension.
* Se instala en un único nodo del cluster.

# Arquitectura con Amazon EKS

Cuando Defender for Cloud protege un cluster de **Amazon Elastic Kubernetes Service**, la recopilación de audit logs es **agentless**.

Los componentes necesarios para la protección completa son:

### Kubernetes audit logs

AWS CloudWatch habilita y recopila los audit logs mediante un collector agentless y los envía al backend de Microsoft Defender for Cloud para su análisis.

### Azure Arc-enabled Kubernetes

Agente instalado en un nodo que conecta el cluster con Defender for Cloud y permite desplegar extensiones.

### Defender agent

DaemonSet desplegado en cada nodo que:

* Recopila señales de los hosts mediante eBPF.
* Proporciona protección en runtime.
* Utiliza Log Analytics como pipeline de datos.
* No almacena los audit logs en Log Analytics.
* Se despliega como Arc-enabled Kubernetes extension.

### Azure Policy for Kubernetes

Pod basado en Gatekeeper v3 que se registra como webhook del admission control y permite aplicar políticas y safeguards a escala.

Se despliega como Arc-enabled Kubernetes extension y solo se instala en un nodo.

## Agentless discovery para Kubernetes en AWS

El descubrimiento se basa en **snapshots periódicos**.

### Create

Debe agregarse el rol `MDCContainersAgentlessDiscoveryK8sRole` al `aws-auth ConfigMap` de los clusters EKS. El nombre puede personalizarse.

### Assign

Defender for Cloud asigna al rol los permisos:

* `eks:UpdateClusterConfig`
* `eks:DescribeCluster`

### Discover

Mediante la identidad asignada por el sistema, Defender for Cloud descubre los clusters EKS mediante llamadas API al API server de EKS.

# Arquitectura con Google GKE

Cuando Defender for Cloud protege un cluster de **Google Kubernetes Engine**, la recopilación de audit logs es **agentless**.

Los componentes necesarios son:

### Kubernetes audit logs

GCP Cloud Logging habilita y recopila los audit logs mediante un collector agentless y los envía al backend de Microsoft Defender for Cloud para su análisis.

### Azure Arc-enabled Kubernetes

Agente instalado en un nodo que conecta el cluster con Defender for Cloud y permite desplegar las extensiones necesarias.

### Defender agent

DaemonSet desplegado en cada nodo que:

* Recopila señales de los hosts mediante eBPF.
* Proporciona protección en runtime.
* Utiliza Log Analytics como pipeline de datos.
* No almacena los audit logs en Log Analytics.
* Se despliega como Arc-enabled Kubernetes extension.

### Azure Policy for Kubernetes

Pod basado en Gatekeeper v3 que se registra como webhook del admission control y permite aplicar políticas y safeguards a escala.

Se despliega como Arc-enabled Kubernetes extension y solo necesita instalarse en un nodo.

## Agentless discovery para Kubernetes en GCP

El descubrimiento se basa en **snapshots periódicos**.

### Create

Se crea la service account:

`mdc-containers-k8s-operator`

El nombre puede personalizarse.

### Assign

Defender for Cloud asigna a la service account:

* Rol personalizado `MDCGkeClusterWriteRole`, que contiene el permiso `container.clusters.update`.
* Rol integrado `container.viewer`.

### Discover

Mediante la identidad asignada por el sistema, Defender for Cloud descubre los clusters GKE mediante llamadas API al API server de GKE.

# Configure Microsoft Defender for Containers components

Microsoft Defender for Containers protege clusters de Kubernetes ejecutándose en:

* **Azure Kubernetes Service (AKS)**
* **Amazon Elastic Kubernetes Service (EKS)** en una cuenta AWS conectada.
* **Google Kubernetes Engine (GKE)** en un proyecto GCP conectado.
* **Otras distribuciones de Kubernetes** mediante Azure Arc-enabled Kubernetes, incluyendo clusters CNCF certificados alojados on-premises o sobre IaaS.

## Network requirements

Se debe garantizar el acceso **outbound** a los endpoints necesarios para que el Defender agent pueda conectarse con Microsoft Defender for Cloud y enviar datos y eventos de seguridad.

* Validar las reglas **FQDN/application** necesarias para Microsoft Defender for Containers.
* Por defecto, los clusters AKS tienen acceso a Internet outbound (egress) sin restricciones.

## Enable the plan

Para habilitar Defender for Containers:

1. En Defender for Cloud, abrir **Settings** y seleccionar la suscripción correspondiente.
2. En **Defender plans**, seleccionar **Defender for Containers** y luego **Settings**.
3. Activar los componentes correspondientes.

Si la suscripción ya tiene habilitado **Defender for Kubernetes** y/o **Defender for container registries**, se muestra una notificación de actualización; de lo contrario, aparece únicamente Defender for Containers.

## Enablement method per capability

Por defecto, al habilitar el plan desde Azure Portal, Microsoft Defender for Containers configura automáticamente todas las capacidades y componentes necesarios, incluyendo la asignación de un workspace predeterminado.

También es posible seleccionar manualmente las capacidades:

* Seleccionar **Edit configuration** para Containers.
* En **Settings & monitoring**, elegir las capacidades que se desean habilitar.
* La configuración puede modificarse posteriormente desde Defender plans.

## Custom workspace para Defender agent

Se puede asignar un **workspace personalizado** mediante Azure Policy.

## Despliegue manual de agentes mediante recomendaciones

Las capacidades que requieren instalación de agentes pueden desplegarse en clusters específicos utilizando las recomendaciones correspondientes:

| Agente                                        | Recomendación                                                                                  |
| --------------------------------------------- | ---------------------------------------------------------------------------------------------- |
| Defender Agent for Kubernetes                 | Azure Kubernetes Service clusters should have Defender profile enabled                         |
| Defender Agent for Arc-enabled Kubernetes     | Azure Arc-enabled Kubernetes clusters should have the Defender extension installed             |
| Azure Policy agent for Kubernetes             | Azure Kubernetes Service clusters should have the Azure Policy Add-on for Kubernetes installed |
| Azure Policy agent for Arc-enabled Kubernetes | Azure Arc-enabled Kubernetes clusters should have the Azure Policy extension installed         |

### Desplegar Defender agent en clusters específicos

1. En Microsoft Defender for Cloud, abrir **Recommendations**.
2. Abrir el control **Enable enhanced security** o buscar directamente la recomendación correspondiente.
3. En la pestaña **Unhealthy**, visualizar los clusters sin agente.
4. Seleccionar los clusters y elegir **Fix**.
5. Seleccionar **Fix X resources**.

La vista permite consultar:

* **Visibility:** qué clusters tienen Defender agent.
* **Fix:** despliegue del agente en clusters sin agente.
* **Workspace:** `DefaultWorkspace-[subscription-ID]-[geo]`.
* **Resource Group:** `DefaultResourceGroup-[geo]`.

El auto provisioning puede afectar a máquinas existentes y futuras. Deshabilitar el auto provisioning de una extensión solo afecta a máquinas futuras; no desinstala la extensión de las existentes.

## Despliegue de Defender agent — opciones

El plan Defender for Containers y sus componentes pueden habilitarse mediante:

* Azure Portal.
* REST API.
* Resource Manager template.

Una vez desplegado Defender agent, se asigna automáticamente un **workspace predeterminado**. Puede reemplazarse por uno personalizado mediante Azure Policy.

## Uso del botón Fix desde una recomendación

El proceso permite habilitar Defender for Cloud y configurar el auto provisioning de los componentes necesarios para proteger clusters Kubernetes a escala.

1. En **Recommendations**, abrir **Enable enhanced security**.
2. Buscar **Azure Kubernetes Service clusters should have Defender profile enabled**.
3. Seleccionar los clusters para consultar recursos healthy y unhealthy.
4. En los recursos unhealthy, seleccionar un cluster y elegir **Remediate**.
5. Seleccionar **Fix X resources**.

## Simular security alerts

Se puede simular una alerta de seguridad ejecutando desde el cluster:

```bash
kubectl get pods --namespace=asc-alerttest-662jfi039
```

La respuesta esperada es:

```text
No resource found
```

Dentro de los siguientes **30 minutos**, Defender for Cloud detecta la actividad y genera una security alert.

Para simular alertas agentless de Defender for Containers, **Azure Arc no es un prerrequisito**.

Las alertas se consultan desde la página **Security alerts** de Microsoft Defender for Cloud.

## Default Log Analytics workspace para AKS

El **Log Analytics workspace** funciona como pipeline de datos utilizado por Defender agent para enviar información del cluster a Defender for Cloud.

Los datos **no se almacenan en el workspace** en este escenario, por lo que los usuarios no reciben cargos por este uso.

Defender agent utiliza un workspace predeterminado. Si no existe uno, Defender for Cloud crea:

* Un nuevo resource group.
* Un workspace predeterminado.

La creación depende de la región.

Los nombres utilizan la siguiente convención:

* `DefaultWorkspace-[subscription-ID]-[geo]`
* `DefaultResourceGroup-[geo]`

## Asignar un custom workspace

Cuando se habilita auto provisioning, se asigna automáticamente un workspace predeterminado. Puede reemplazarse mediante Azure Policy.

### Verificar el workspace asignado

1. Ingresar a Azure Portal.
2. Buscar y seleccionar **Policy**.
3. Seleccionar **Definitions**.
4. Buscar el policy ID:

`64def556-fbad-4622-930e-72d1d5589bf5`

5. Si la policy no está asignada al scope correspondiente, utilizar **Create a new assignment with custom workspace**.
6. Si ya está asignada, utilizar **Update assignment with custom workspace**.

### Crear una nueva asignación

1. Seleccionar **Assign**.
2. En **Parameters**, desactivar **Only show parameters that need input or review**.
3. Seleccionar el **LogAnalyticsWorkspaceResource ID** desde el desplegable.
4. Seleccionar **Review + create**.
5. Seleccionar **Create**.

### Actualizar una asignación existente

1. Seleccionar la asignación correspondiente.
2. Seleccionar **Edit assignment**.
3. En **Parameters**, desactivar **Only show parameters that need input or review**.
4. Seleccionar el **LogAnalyticsWorkspaceResource ID**.
5. Seleccionar **Review + save**.
6. Seleccionar **Save**.

## Remove the Defender sensor

Deshabilitar el auto provisioning **no elimina** automáticamente una extensión ya instalada.

Para desactivar completamente el plan:

1. Ir a **Environment settings**.
2. Deshabilitar **Microsoft Defender for Containers**.

Además, para evitar que los componentes vuelvan a aprovisionarse automáticamente:

* Deshabilitar el auto provisioning de las extensiones.
* La extensión existente puede eliminarse mediante REST API, Resource Manager template o Azure CLI.

## Eliminar Defender sensor mediante Azure CLI

Ejecutar:

```bash
az login
az account set --subscription <subscription-id>
az aks update --disable-defender --resource-group <your-resource-group> --name <your-cluster-name>
```

La eliminación de la extensión puede tardar algunos minutos.

Para verificar que fue eliminada:

```bash
kubectl get pods -n kube-system | grep microsoft-defender
```

Si la extensión fue eliminada correctamente, no deberían aparecer pods correspondientes a `microsoft-defender`. La eliminación de los pods puede tardar algunos minutos.
