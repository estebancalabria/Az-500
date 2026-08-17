# Azure Monitor y Microsoft Defender for Cloud

## Log Analytics workspace

Un **Log Analytics workspace** es un almacén donde se recopilan datos de logs de recursos y aplicaciones de Azure y no Azure. Permite centralizar los datos para operaciones, análisis y auditoría.

Se integra con:

* **Azure Monitor**: Insights, alertas y acciones automáticas.
* **Microsoft Sentinel**.
* **Microsoft Defender for Cloud**.
* **Logic Apps**.
* **Power BI** y **Excel**.
* Aplicaciones personalizadas y de terceros.

> **Microsoft Sentinel workspace** es el mismo Log Analytics workspace, pero habilitado para Microsoft Sentinel. Todos los datos almacenados están sujetos a los precios de Microsoft Sentinel.

### Log tables

Un workspace contiene múltiples **tablas**, donde Azure Monitor Logs almacena los datos recopilados.

* Azure Monitor crea automáticamente las tablas necesarias para los datos de monitorización.
* Se pueden crear **tablas personalizadas** para datos provenientes de recursos y aplicaciones no Azure.
* Las tablas permiten administrar:

  * Acceso.
  * Modelo de datos.
  * Retención.
  * Costos.

### Retención de datos

Los datos se mantienen en dos estados:

* **Interactive retention**: permite consultar los datos y utilizarlos para monitorización, troubleshooting, visualizaciones, alertas y otras funcionalidades.
* **Long-term retention**: almacenamiento de menor costo. Los datos no están disponibles directamente para las funcionalidades del plan de tabla; se recuperan mediante **search jobs**.

Por defecto:

* La mayoría de las tablas tienen **30 días** de retención interactiva.
* Algunas tablas tienen **90 días** por defecto.
* Las tablas **Analytics** pueden extender la retención interactiva hasta **2 años**.
* Las tablas **Basic** y **Auxiliary** tienen 30 días de retención interactiva.
* La retención total puede llegar hasta **12 años (4.383 días)**.
* El plan Auxiliary está en preview y su retención total está fijada en **365 días**.

La retención interactiva de las tablas Analytics puede reducirse hasta 4 días mediante API o CLI, pero reducirla por debajo de 31 días no disminuye costos porque 31 días están incluidos en el precio de ingestión.

Cuando termina la retención interactiva, los datos permanecen en long-term retention durante el resto de la retención total configurada. Los datos necesarios pueden recuperarse mediante un **search job**, que los coloca en una tabla de resultados para consultas interactivas.

### Cambios en la retención

* Al **reducir la retención total**, Azure Monitor espera 30 días antes de eliminar los datos.
* Al **aumentar la retención total**, el nuevo período también se aplica a los datos existentes que todavía no fueron eliminados.
* Los cambios en la configuración de long-term retention de una tabla con datos existentes tienen efecto inmediato.
* Cambiar la retención predeterminada del workspace afecta únicamente a las tablas Analytics que todavía utilizan esa configuración predeterminada.

### Configuración de retención

A nivel de workspace se puede configurar la retención interactiva predeterminada de las tablas Analytics hasta 2 años.

En Azure Portal:

1. **Log Analytics workspaces** → seleccionar workspace.
2. **Usage and estimated costs**.
3. **Data Retention**.
4. Ajustar los días.

A nivel de tabla:

1. **Log Analytics workspaces** → **Tables**.
2. Seleccionar la tabla → **Manage table**.
3. Configurar **interactive retention** y **total retention**.

### Acceso a los datos

El acceso depende del **access control mode** configurado en el workspace.

Se puede otorgar acceso mediante:

* Roles integrados o personalizados.
* Acceso derivado de los permisos que el usuario tiene sobre los recursos Azure cuyos datos fueron recopilados.

### Workspace Insights

**Log Analytics Workspace Insights** proporciona una vista del uso, rendimiento, estado, ingestión, consultas y cambios del workspace.

### Transformación de datos

Los **Data Collection Rules (DCR)** pueden incluir transformaciones que filtran o modifican los datos antes de ingresarlos al workspace.

Esto permite:

* Reducir costos de ingestión eliminando registros innecesarios.
* Extraer o reorganizar información.
* Adaptar los datos al modelo requerido.

También existe un **workspace transformation DCR**, cuyas transformaciones se definen por tabla y se aplican a todos los datos enviados a esa tabla que no utilicen ya una DCR.

### Costos

No existe un costo directo por crear o mantener un workspace.

Los costos dependen principalmente de:

* Datos ingeridos.
* Retención de datos.
* Plan de cada tabla.

### Arquitectura

Se puede utilizar un único workspace para toda la recopilación de datos o varios workspaces según necesidades como:

* Regulaciones y cumplimiento.
* Ubicación específica de los datos.
* Separación de facturación.
* Resiliencia.

---

## Eliminación de tablas y retención

El comportamiento depende del tipo de tabla:

| Tipo de tabla                      | Comportamiento                                                                                                                                                                    |
| ---------------------------------- | --------------------------------------------------------------------------------------------------------------------------------------------------------------------------------- |
| **Azure table**                    | No puede eliminarse. Al dejar de recibir datos, estos permanecen hasta finalizar su período de retención.                                                                         |
| **Custom log table (`_CL`)**       | Se elimina de forma lógica (*soft delete*) hasta finalizar su retención. Durante ese período se continúa pagando por la retención. Puede recrearse con el mismo nombre y esquema. |
| **Search results table (`_SRCH`)** | Se elimina inmediatamente y de forma permanente junto con sus datos.                                                                                                              |
| **Restored table (`_RST`)**        | Se elimina la caché temporal de la restauración, pero los datos de la tabla origen no se eliminan.                                                                                |

Para minimizar costos, se recomienda reducir la retención a 4 días antes de dejar de enviar datos a una tabla Azure o eliminar una tabla personalizada.

### Tablas con 90 días de retención

Las tablas **Usage** y **AzureActivity** conservan datos durante al menos 90 días sin costo de retención ni ingestión.

Las tablas relacionadas con **Application Insights** también tienen 90 días de retención sin costo:

* AppAvailabilityResults
* AppBrowserTimings
* AppDependencies
* AppExceptions
* AppEvents
* AppMetrics
* AppPageViews
* AppPerformanceCounters
* AppRequests
* AppSystemEvents
* AppTraces

---

# Azure Monitor Agent (AMA)

El **Azure Monitor Agent (AMA)** recopila datos de monitorización del sistema operativo invitado de:

* Azure Virtual Machines.
* Virtual Machine Scale Sets.
* Servidores habilitados para Azure Arc.
* Máquinas virtuales híbridas en otros clouds y on-premises.

Envía los datos a Azure Monitor para utilizarlos en funcionalidades, Insights y servicios como:

* Microsoft Sentinel.
* Microsoft Defender for Cloud.

El agente reemplaza al **Log Analytics agent**, que está deprecated y dejó de recibir soporte el **31 de agosto de 2024**.

### Instalación

El agente puede instalarse:

* En una máquina individual.
* A escala mediante Azure Policy u otras herramientas.
* Automáticamente al habilitar determinadas funcionalidades, como Microsoft Sentinel.

Sin el agente se puede recopilar información del host, pero no se tiene acceso a los logs locales y procesos del sistema operativo invitado.

### Recopilación mediante DCR

El AMA utiliza **Data Collection Rules (DCR)** para definir:

* Qué datos recopilar.
* Cómo transformarlos, filtrarlos, agregarlos o estructurarlos.
* Dónde enviarlos.

Una DCR puede contener múltiples fuentes de datos.

Una DCR se asocia al agente mediante una **Data Collection Rule Association (DCRA)**:

* Una DCR puede asociarse a múltiples agentes.
* Un agente puede tener múltiples DCR asociadas.

El agente consulta periódicamente Azure Monitor para detectar cambios en las DCR y sus asociaciones.

### Datos compatibles

| Fuente               | Sistema operativo | Destino                                                  |
| -------------------- | ----------------- | -------------------------------------------------------- |
| Windows Events       | Windows           | Log Analytics workspace                                  |
| Performance Counters | Windows / Linux   | Azure Monitor Metrics (Preview), Log Analytics workspace |
| Syslog               | Linux             | Log Analytics workspace                                  |
| Text logs            | Windows / Linux   | Log Analytics workspace                                  |
| JSON logs            | Windows / Linux   | Log Analytics workspace                                  |
| IIS logs             | Windows           | Log Analytics workspace                                  |

También admite **SQL Best Practices Assessment**.

### Costos

El Azure Monitor Agent no tiene costo de uso directo, pero pueden generarse costos por:

* Ingestión de datos.
* Almacenamiento y retención.

---

# Data Collection Rules (DCR)

Las **DCR** forman parte de un proceso de recopilación similar a **ETL**, utilizando un pipeline común de ingestión para las diferentes fuentes de datos.

Ventajas:

* Configuración consistente para distintas fuentes.
* Transformación de datos antes de enviarlos al destino.
* Filtrado y modificación de datos.
* Configuración escalable.
* Integración con Infrastructure as Code y DevOps.
* Posibilidad de utilizar un **edge pipeline** para escenarios de alta escalabilidad, conectividad periódica o redes segmentadas.

Las DCR son recursos de Azure almacenados y administrados centralmente.

Se pueden visualizar desde:

**Azure Portal → Monitor → Data Collection Rules**

### Azure Monitor pipeline

El pipeline de Azure Monitor proporciona una ruta común para procesar los datos.

Una DCR puede definir:

* Datos que deben recopilarse.
* Esquema de entrada.
* Transformaciones.
* Destino.

### Formas de utilizar una DCR

#### Data Collection Rule Association (DCRA)

Relaciona una DCR con un recurso monitorizado.

Es una relación **many-to-many**:

* Una DCR → múltiples recursos.
* Un recurso → múltiples DCR.

Con AMA, el agente obtiene las DCR asociadas, recopila los datos definidos y los envía al pipeline. El pipeline aplica las transformaciones y entrega los datos al workspace y tabla especificados.

#### Direct ingestion

La DCR se especifica directamente para procesar los datos entrantes.

Por ejemplo, una aplicación personalizada puede utilizar **Logs ingestion API** y especificar qué DCR debe procesar cada llamada.

### Transformaciones

Las transformaciones son **consultas KQL** incluidas en una DCR que se ejecutan sobre cada registro enviado al cloud pipeline.

Permiten:

* Filtrar datos innecesarios y reducir costos de ingestión.
* Eliminar información sensible antes de almacenarla.
* Adaptar los datos al esquema del destino.
* Enviar datos a múltiples destinos.
* Enriquecer los datos con información adicional.

### Edge pipeline

El **Azure Monitor edge pipeline** extiende el pipeline hacia el propio datacenter.

Casos de uso:

* **Escalabilidad:** manejar grandes volúmenes de telemetría.
* **Conectividad periódica:** almacenar datos localmente y sincronizarlos cuando vuelve la conexión.
* **Redes segmentadas:** recopilar datos de recursos sin acceso directo a Azure y gestionar posteriormente la conexión con Azure Monitor.

El edge pipeline es opcional y requiere configuración.

### Regiones

Las DCR están disponibles en las regiones públicas donde se admiten Log Analytics y en Azure Government y China clouds.

No están soportadas en clouds aisladas (*air-gapped*).

Una DCR:

* Se crea y almacena en una región.
* Se replica en la región emparejada dentro de la misma geografía.
* Está desplegada en las tres Availability Zones de la región.
* Es un servicio **zone-redundant**.

---

# Crear y configurar una DCR con Azure Monitor Agent

El Azure Portal permite crear una DCR para máquinas virtuales y Virtual Machine Scale Sets.

Para escenarios avanzados, como transformaciones, se pueden utilizar métodos como:

* Azure CLI.
* PowerShell.
* ARM templates.
* Azure Policy.

Para enviar datos entre tenants es necesario habilitar **Azure Lighthouse**.

### Configuración básica de una DCR

Al crear una DCR se especifican:

* **Rule Name:** nombre descriptivo.
* **Subscription:** suscripción donde se almacena la DCR; no tiene que coincidir con la de las VMs.
* **Resource group:** grupo donde se almacena la DCR; no tiene que coincidir con el de las VMs.
* **Region:** debe coincidir con la región del Log Analytics workspace o Azure Monitor workspace utilizado como destino.
* **Platform Type:** Windows, Linux o ambos.
* **Data Collection Endpoint (DCE):** necesario cuando se utilizan Azure Monitor Private Links y debe estar en la misma región que la DCR.

### Asociar recursos

Al agregar VMs a la DCR:

* El AMA se instala automáticamente si todavía no está instalado.
* Se crea una **DCRA** entre la máquina y la DCR.
* El portal habilita una **managed identity** asignada por el sistema en los recursos objetivo, además de las identidades asignadas por el usuario existentes.

Si se requiere un DCE, se selecciona uno en la región correspondiente a las máquinas monitorizadas.

### Fuentes y destinos

En **Collect and deliver** se configuran:

* Fuente de datos.
* Uno o más destinos.

Una DCR admite hasta **10 fuentes de datos**.

Puede enviar una fuente a múltiples destinos, incluyendo múltiples Log Analytics workspaces (**multihoming**).

Normalmente se recomienda utilizar diferentes DCR para diferentes escenarios de recopilación.

Después de crear una DCR mediante el asistente, los datos pueden tardar hasta **5 minutos** en llegar a los destinos.

### Evitar datos duplicados

La recopilación duplicada puede generar costos adicionales. Puede ocurrir al:

* Asociar múltiples DCR con la misma fuente de datos y agente.
* Recopilar security logs mediante una DCR y habilitar Sentinel para los mismos agentes, pudiendo terminar con los mismos eventos en `Event` y `SecurityEvent`.
* Utilizar simultáneamente AMA y el antiguo Log Analytics agent durante una migración.

---

# Verificación y troubleshooting de DCR

Una vez creada y asociada una DCR:

### Verificar el agente

Consultar la tabla **Heartbeat** en Log Analytics.

Cada agente debería enviar un registro aproximadamente cada minuto.

### Verificar los datos

Después de unos minutos se pueden consultar las tablas correspondientes a las fuentes configuradas.

Por ejemplo:

* Eventos de Windows → tabla **Event**.

### Troubleshooting

Si no se reciben los datos:

1. Verificar que el AMA esté instalado y ejecutándose.
2. Revisar el troubleshooting específico de la fuente de datos.
3. Utilizar las herramientas de monitorización de DCR para:

   * Consultar métricas.
   * Determinar si se están recopilando datos.
   * Detectar registros descartados.
   * Revisar logs y errores de recopilación.

---

# Reemplazo de métodos de recopilación anteriores

Las DCR reemplazan o están reemplazando métodos de recopilación anteriores:

| Método anterior                   | Nuevo método        |
| --------------------------------- | ------------------- |
| Log Analytics Agent               | Azure Monitor Agent |
| Diagnostic settings para métricas | Metrics export      |
| Data Collector API                | Logs ingestion API  |

Los **Diagnostic settings** continúan utilizándose para recopilar resource logs de recursos Azure.

---

# Microsoft Defender for Cloud

**Microsoft Defender for Cloud** es una plataforma **CNAPP (Cloud-Native Application Protection Platform)** que proporciona protección de extremo a extremo para aplicaciones cloud mediante:

* **DevSecOps:** gestión de seguridad a nivel de código en entornos multicloud y múltiples pipelines.
* **CSPM (Cloud Security Posture Management):** identifica acciones para prevenir brechas de seguridad.
* **CWPP (Cloud Workload Protection Platform):** protección específica para servidores, contenedores, almacenamiento, bases de datos y otros workloads.

Incluye gratuitamente:

* **Foundational CSPM**.
* Acceso a **Microsoft Defender XDR**.

Se pueden habilitar planes adicionales de pago para proteger diferentes recursos.

Existe una prueba gratuita de Defender for Cloud durante los primeros **30 días**, después de los cuales se aplican cargos según los planes habilitados.

> **Importante:** el malware scanning de **Defender for Storage** no está incluido en la prueba gratuita de 30 días y se cobra desde el primer día.

Defender for Cloud permite:

* Identificar y corregir vulnerabilidades.
* Aplicar controles de acceso y aplicaciones para bloquear actividades maliciosas.
* Detectar amenazas mediante análisis e inteligencia.
* Responder rápidamente ante ataques.

## Habilitar Defender for Cloud en una suscripción

Para consultar información de recursos se requiere uno de estos roles:

* **Owner**
* **Contributor**
* **Reader**

La habilitación se realiza desde:

**Azure Portal → Microsoft Defender for Cloud**

Al habilitarlo se obtiene acceso a las funcionalidades básicas:

* Foundational CSPM.
* Recommendations.
* Asset inventory.
* Workbooks.
* Secure score.
* Regulatory compliance con Microsoft Cloud Security Benchmark.

La página Overview proporciona una vista unificada de la postura de seguridad de los workloads híbridos y permite identificar y mitigar riesgos.

También se pueden seleccionar y filtrar suscripciones para adaptar la vista a la postura de seguridad correspondiente.

Después de habilitar Defender for Cloud pueden aparecer:

* Recomendaciones para mejorar la seguridad.
* Inventario de recursos y su postura de seguridad.

## Habilitar planes de pago

Para obtener protección adicional se deben habilitar los planes correspondientes a los workloads.

Se pueden habilitar a nivel de suscripción o recurso:

* Microsoft Defender for Storage.
* Microsoft Defender for SQL.
* Microsoft Defender for open-source relational databases.

A nivel de workspace están disponibles:

* Microsoft Defender for Servers.
* Microsoft Defender for SQL servers on machines.

**Microsoft Defender for SQL** es un bundle a nivel de suscripción que utiliza un workspace predeterminado o personalizado.

Para habilitar los planes adicionales:

1. Abrir **Microsoft Defender for Cloud**.
2. Seleccionar **Environment settings**.
3. Seleccionar la suscripción o workspace.
4. Seleccionar **Enable all**.
5. Seleccionar **Save**.

Al habilitarlos, se activan los planes y se despliegan los componentes de monitorización necesarios en los recursos protegidos.

Si se deshabilita un plan, sus extensiones no se desinstalan inmediatamente, pero dejan de recopilar datos después de un período corto.
