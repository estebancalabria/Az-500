# Introducción

Este módulo se centra en configurar y administrar soluciones avanzadas de supervisión de seguridad y automatización en Microsoft Defender for Cloud y Microsoft Sentinel.

## Objetivos de aprendizaje

Un ingeniero de seguridad de Azure tiene la responsabilidad de optimizar las capacidades de detección y respuesta ante amenazas de la organización mediante Microsoft Defender for Cloud y Microsoft Sentinel.

Sus responsabilidades incluyen:

* Configurar la automatización de flujos de trabajo.
* Integrar conectores de datos.
* Habilitar reglas de análisis.
* Administrar alertas de seguridad para una gestión eficiente de incidentes.

Este módulo tiene como objetivo proporcionar a los ingenieros de seguridad de Azure las habilidades necesarias para configurar, administrar y optimizar soluciones avanzadas de supervisión de seguridad, automatización y respuesta ante incidentes mediante Microsoft Defender for Cloud y Microsoft Sentinel.

# Microsoft Sentinel

Microsoft Sentinel permite recopilar datos de toda la organización y analizarlos continuamente para detectar amenazas de seguridad.

Proporciona reglas de detección de amenazas que se ejecutan periódicamente, consultan los datos recopilados y los analizan para descubrir amenazas.

El flujo principal de detección y respuesta es:

**Regla de análisis → Alerta → Correlación de alertas → Incidente → Investigación y respuesta**

Microsoft Sentinel permite:

* Conectar diferentes fuentes de datos mediante conectores.
* Analizar los datos recopilados mediante reglas de análisis.
* Generar alertas cuando se detectan amenazas o se cumplen determinadas condiciones.
* Correlacionar alertas relacionadas para formar incidentes.
* Clasificar e investigar incidentes.
* Automatizar respuestas mediante reglas de automatización y playbooks.
* Ejecutar playbooks automáticamente o bajo demanda.
* Integrarse con soluciones y servicios externos para automatizar la respuesta y corrección de amenazas.

Microsoft Sentinel también proporciona capacidades de **Security Orchestration, Automation, and Response (SOAR)** mediante reglas de automatización y playbooks.

Las reglas de automatización ayudan a realizar el triaje de incidentes y pueden:

* Asignar incidentes al personal correspondiente.
* Cerrar incidentes ruidosos o falsos positivos conocidos.
* Cambiar la gravedad.
* Agregar etiquetas.
* Ejecutar playbooks en respuesta a incidentes o alertas.

Los playbooks son colecciones de procedimientos que pueden ejecutarse desde Microsoft Sentinel en respuesta a:

* Un incidente completo.
* Una alerta individual.
* Una entidad específica.

Los playbooks permiten automatizar y orquestar la respuesta y están basados en flujos de trabajo de **Azure Logic Apps**.

Microsoft Sentinel está disponible dentro de la plataforma unificada de operaciones de seguridad de Microsoft en el portal de Microsoft Defender.

# Administrar y responder a alertas de seguridad en Microsoft Defender for Cloud

## ¿Qué son las alertas de seguridad?

Las alertas de seguridad son notificaciones generadas por los planes de protección de cargas de trabajo de Defender for Cloud cuando se identifican amenazas en entornos de Azure, híbridos o multinube.

* Las alertas se desencadenan mediante detecciones avanzadas disponibles al habilitar planes de Defender para tipos específicos de recursos.
* Cada alerta proporciona detalles sobre los recursos afectados, los problemas y los pasos de corrección.
* Defender for Cloud clasifica y prioriza las alertas según su gravedad.
* Las alertas se muestran en el portal durante 90 días, incluso si durante ese período se eliminó el recurso relacionado con la alerta. Esto se debe a que la alerta puede indicar una posible vulneración de la organización que requiere una investigación adicional.
* Las alertas se pueden exportar al formato CSV.
* **Las alertas también se pueden transmitir directamente a una solución SIEM como Microsoft Sentinel, a una solución SOAR o a una solución ITSM.**
* Defender for Cloud utiliza la matriz de ataques MITRE para asociar las alertas con su intención percibida, ayudando a formalizar el conocimiento del dominio de seguridad.

  * El marco ATT&CK de MITRE es una base de conocimientos de acceso universal y continuamente actualizada sobre los comportamientos de los ciberdelincuentes.

## ¿Cómo se clasifican las alertas?

Las alertas tienen asignado un nivel de gravedad para ayudar a priorizar cómo atender cada alerta. La gravedad se basa en:

* El desencadenador específico.
* El nivel de confianza de que hubo intención malintencionada detrás de la actividad que generó la alerta.

  * **Alta**

    * Existe una alta probabilidad de que el recurso esté comprometido. Se debe investigar inmediatamente.
  * **Media**

    * Probablemente se trate de una actividad sospechosa que podría indicar que un recurso está comprometido.
  * **Baja**

    * Puede tratarse de un positivo benigno o de un ataque bloqueado. Defender for Cloud no tiene suficiente confianza en que la intención sea maliciosa y la actividad podría ser inocente.
  * **Informativa**

    * Un incidente normalmente está compuesto por varias alertas. Algunas pueden parecer únicamente informativas por sí mismas, pero en el contexto de las demás alertas pueden justificar una investigación más detallada.

## ¿Qué son los incidentes de seguridad?

Un incidente de seguridad es una colección de alertas relacionadas.

Los incidentes proporcionan una vista única de un ataque y de sus alertas relacionadas, permitiendo comprender rápidamente las acciones realizadas por un atacante y los recursos afectados.

A medida que aumenta la cobertura de amenazas, también aumenta la necesidad de detectar incluso el compromiso más pequeño. Para los analistas de seguridad resulta difícil clasificar diferentes alertas e identificar un ataque real. Al correlacionar alertas y señales de baja fidelidad en incidentes de seguridad, Defender for Cloud ayuda a los analistas a gestionar la fatiga de alertas.

En la nube, los ataques pueden producirse entre diferentes inquilinos. Defender for Cloud puede combinar algoritmos de IA para analizar secuencias de ataque notificadas en cada suscripción de Azure. Esta técnica identifica las secuencias de ataque como patrones de alertas frecuentes, en lugar de considerarlas asociadas de forma incidental.

Durante una investigación de un incidente, los analistas suelen necesitar contexto adicional para determinar la naturaleza de la amenaza y cómo mitigarla. Por ejemplo, incluso cuando se detecta una anomalía de red, sin comprender qué más está ocurriendo en la red o en relación con el recurso afectado, resulta difícil determinar qué acciones realizar a continuación.

Para ayudar en estas investigaciones, un incidente de seguridad puede incluir:

* Artefactos.
* Eventos relacionados.
* Información adicional.

La información disponible para los incidentes de seguridad varía según el tipo de amenaza detectada y la configuración del entorno.

## Correlación de alertas en incidentes

Defender for Cloud correlaciona alertas y señales contextuales en incidentes.

### Correlación de alertas → Incidentes

* Defender for Cloud agrupa alertas relacionadas en **incidentes**.
* Combina señales de distintos recursos + IA para descubrir patrones de ataque.
* Permite descartar actividades que parecen ataque pero no lo son (reduce falsos positivos).

### Fuentes de telemetría integrada

Azure, Microsoft 365, Microsoft CRM Online, Dynamics AX, Outlook.com, MSN.com, Microsoft Digital Crimes Unit (DCU), Microsoft Security Response Center (MSRC).

### Tres técnicas de análisis de seguridad

| Técnica                                   | Base                                                                                   | Detecta                                                                                  |
| ----------------------------------------- | -------------------------------------------------------------------------------------- | ---------------------------------------------------------------------------------------- |
| **Inteligencia sobre amenazas integrada** | Feeds globales de threat intel (propios + terceros)                                    | Actores maliciosos **conocidos**                                                         |
| **Análisis de comportamiento**            | Patrones **conocidos**, derivados de ML sobre grandes datasets + análisis de analistas | Comportamiento malicioso ya catalogado (logs de VM, dispositivos de red virtual, fabric) |
| **Detección de anomalías**                | Línea base **específica de cada implementación**, sin patrones previos                 | Desvíos atípicos → amenazas **no conocidas** (zero-day)                                  |

**Punto clave:** behavior analytics = patrones conocidos vs. anomaly detection = línea base propia, sin patrones previos.

## Exportación de alertas

Existen varias opciones para visualizar las alertas fuera de Defender for Cloud:

* **Descarga de un informe CSV:** el panel de alertas permite realizar una exportación única a CSV.
* **Exportación continua:** desde la configuración del entorno se pueden configurar flujos de alertas de seguridad y recomendaciones hacia espacios de trabajo de Log Analytics y Event Hubs.
* **Conector de Microsoft Sentinel:** transmite las alertas de seguridad de Microsoft Defender for Cloud a Microsoft Sentinel.

---

## Configurar la automatización del flujo de trabajo mediante Microsoft Defender for Cloud

La automatización de los procesos de supervisión y respuesta ante incidentes de una organización puede mejorar considerablemente el tiempo necesario para investigar y mitigar incidentes de seguridad.

Para implementar las configuraciones de automatización en toda la organización, se utilizan las directivas de Azure Policy `DeployIfNotExist` proporcionadas para crear y configurar procedimientos de automatización de flujos de trabajo.

### Directivas disponibles

| Objetivo                                                                   | Directiva                                                                         | ID de directiva                        |
| -------------------------------------------------------------------------- | --------------------------------------------------------------------------------- | -------------------------------------- |
| Automatización del flujo de trabajo para alertas de seguridad              | Deploy Workflow Automation for Microsoft Defender for Cloud alerts                | `f1525828-9a90-4fcf-be48-268cdd02361e` |
| Automatización del flujo de trabajo para recomendaciones de seguridad      | Deploy Workflow Automation for Microsoft Defender for Cloud recommendations       | `73d6ab6c-2475-4850-afd6-43795f3492ef` |
| Automatización del flujo de trabajo para cambios de cumplimiento normativo | Deploy Workflow Automation for Microsoft Defender for Cloud regulatory compliance | `509122b9-ddd9-47ba-a5f1-d0dac20be63c` |

### Implementación de las directivas

1. Desde la tabla de directivas, seleccionar la directiva que se desea aplicar.
2. Desde la página correspondiente de Azure Policy, seleccionar **Assign**.
3. Abrir cada pestaña y establecer los parámetros deseados:

   * En la pestaña **Basics**, establecer el ámbito de la directiva. Para utilizar una administración centralizada, asignar la directiva al Management Group que contiene las suscripciones que utilizarán la configuración de automatización del flujo de trabajo.
   * En la pestaña **Parameters**, introducir la información necesaria.
   * Opcionalmente, en la pestaña **Remediation**, aplicar la asignación a una suscripción existente y seleccionar la opción para crear una tarea de corrección.
4. Revisar la página de resumen y seleccionar **Create**.

---

# Planes de retención en Microsoft Sentinel — resumen simple

**Idea central:** no todos los logs valen lo mismo, así que Sentinel ofrece distintos planes de almacenamiento según cuánto necesitás consultarlos.

### Los 3 planes

| Plan                         | Para qué sirve                                                        | Retención interactiva  | Costo                 | Consultas                                          |
| ---------------------------- | --------------------------------------------------------------------- | ---------------------- | --------------------- | -------------------------------------------------- |
| **Analytics logs**           | Logs que necesitás ver en tiempo real (autenticación, EDR, auditoría) | 90 días (hasta 2 años) | Caro al ingerir       | Ilimitadas, gratis, alto rendimiento               |
| **Auxiliary logs** (Preview) | Logs de volumen alto y valor bajo (firewall, NetFlow, proxy)          | 30 días                | Muy barato al ingerir | Limitadas, se paga por GB consultado, solo 1 tabla |
| **Basic logs**               | Igual que Auxiliary pero sin ser Preview                              | Similar                | Intermedio            | Similar a Auxiliary                                |

### Reglas clave para el examen

* **Analytics** = corre reglas de detección automática + permite `restore`.
* **Auxiliary** = NO permite `restore`, solo `search job`. No corre detección automática directa.
* **Summary rules**: resumen datos de Auxiliary y los mandan a una tabla en Analytics, para poder detectar sobre ese resumen.
* Retención a largo plazo: hasta 12 años en cualquier plan, muy barata, pero solo accesible vía `search job` (o `restore` en Analytics).

### Impacto práctico

La elección del plan es una decisión de **costo vs. accesibilidad**: fuente que consultás seguido → Analytics; fuente que guardás "por las dudas" → Auxiliary/Basic.

# Alertas e incidentes de Microsoft Sentinel

Después de configurar Microsoft Sentinel para recopilar datos de toda la organización, es necesario analizar continuamente esos datos para detectar amenazas de seguridad. Para ello, Microsoft Sentinel proporciona reglas de detección de amenazas que se ejecutan periódicamente, consultan los datos recopilados y los analizan para descubrir amenazas.

**Flujo:** regla de análisis → alerta → correlación de alertas → **incidente** (expediente a investigar).

## Tipos de reglas "normales"

| Regla                        | Frecuencia / lógica                                     | Genera incidente directo | Notas                                                                                                                          |
| ---------------------------- | ------------------------------------------------------- | ------------------------ | ------------------------------------------------------------------------------------------------------------------------------ |
| **Scheduled**                | KQL a intervalos regulares, sobre ventana de *lookback* | Sí                       | La más común                                                                                                                   |
| **NRT**                      | Cada 1 minuto                                           | Sí                       | Subconjunto de scheduled, con limitaciones                                                                                     |
| **Anomaly**                  | ML, arma línea base de comportamiento                   | No                       | Solo escribe en tabla **Anomalies** (da contexto). No editable directo → duplicar y probar en **Flighting** vs. **Production** |
| **Microsoft security rules** | Toma alertas de otras soluciones MS de seguridad        | Sí                       | Se deshabilitan si hay Defender XDR integration o Sentinel onboardeado al portal Defender (ahí XDR crea los incidentes)        |

## Tipos especiales

* **Threat Intelligence**: correlaciona CEF/Syslog/Windows DNS con indicadores de MS Threat Intelligence (IP/dominio/URL).
* **Fusion**: motor ML de correlación multi-etapa. Junta alertas de baja fidelidad → incidente de alta fidelidad. Habilitado por defecto. También se desactiva con Defender XDR integration.
* **ML Behavior Analytics** (Preview): detecta anomalías de login SSH/RDP usando IP + geolocalización + historial de usuario.

## Notas adicionales

* Anomaly rules no generan alerta/incidente propio — solo alimentan la tabla Anomalies.
* Microsoft security rules y Fusion dejan de operar (o de crear incidentes) si está activa la integración con Defender XDR.

# Permisos de reglas y conectores de datos en Microsoft Sentinel

## Permisos de acceso para reglas de análisis

Al crear una regla se le asigna un token de acceso propio, guardado junto con la regla. Esto permite que siga funcionando aunque el creador pierda acceso al workspace.

**Excepción — MSSP (cross-subscription / cross-tenant):**

* No se usa token independiente, se usan las **credenciales del usuario creador**.
* Si ese usuario pierde acceso a la otra suscripción/tenant, la regla deja de funcionar.
* Síntoma: error **"insufficient access to resource"** → la regla se **auto-deshabilita** tras varios fallos.

Las reglas pueden exportarse/importarse como plantilla **ARM** para administración como código.

## Conectores de datos — métodos de integración

| Método                        | Cómo funciona                                                                                                          | Destino de datos                                                                         |
| ----------------------------- | ---------------------------------------------------------------------------------------------------------------------- | ---------------------------------------------------------------------------------------- |
| **Service-to-service**        | Integración nativa en tiempo real (servicios MS y AWS)                                                                 | Ej: M365 Defender integra O365, Entra ID, Defender for Identity, Defender for Cloud Apps |
| **REST API**                  | Del lado del proveedor, o vía Azure Functions (formatea y envía)                                                       | Vía Azure Monitor Data Collector API → tablas custom                                     |
| **Basado en agente — Syslog** | AMA recibe eventos por UDP del daemon Syslog (reenviados vía UDS), instalado en el dispositivo o en un forwarder Linux | Tabla **Syslog**                                                                         |
| **Basado en agente — CEF**    | Log Analytics agent convierte el formato CEF                                                                           | Tabla **CommonSecurityLog**                                                              |
| **Custom logs**               | Log Analytics custom log collection agent, archivos en Windows/Linux                                                   | Tablas personalizadas                                                                    |

Para agregar más conectores hay que instalar la solución asociada desde **Content Hub**. Las soluciones incluyen conector + workbooks + analytics rules + playbooks juntos.

## Soporte de conectores

* **Microsoft supported**: MS es proveedor/autor de los datos, soporte oficial vía planes de Azure.
* **Partner supported**: desarrollado por terceros, soporte a cargo del partner (ISV, MSP, SI, etc.).
* **Community supported**: sin contacto de soporte/mantenimiento definido.

# Habilitar reglas de análisis en Microsoft Sentinel

Después de conectar las fuentes de datos a Microsoft Sentinel, se pueden crear **reglas de análisis personalizadas** para ayudar a descubrir amenazas y comportamientos anómalos en el entorno.

Las reglas de análisis:

* Buscan eventos específicos o conjuntos de eventos en todo el entorno.
* Generan alertas cuando se alcanzan determinados umbrales o condiciones.
* Generan incidentes para que el SOC los clasifique e investigue.
* Responden a amenazas mediante procesos automatizados de seguimiento y corrección.

## Crear una regla de análisis personalizada con una consulta programada

1. Desde el menú de navegación de Microsoft Sentinel, seleccionar **Analytics**.
2. En la barra de acciones superior, seleccionar **+ Create** y luego **Scheduled query rule**. Esto abre el asistente de reglas de análisis.

## Asistente de reglas de análisis - pestaña General

* Proporcionar un **Name** único y una **Description**.
* En **Tactics and techniques**, seleccionar categorías de ataques para clasificar la regla. Estas categorías se basan en el framework **MITRE ATT&CK**.
* Los incidentes creados a partir de alertas detectadas por reglas asignadas a tácticas y técnicas de MITRE ATT&CK heredan automáticamente la asignación de la regla.
* Establecer la **Alert Severity** correspondiente.

### Niveles de gravedad

* **Informational:** no existe impacto sobre el sistema, pero la información puede indicar pasos futuros planificados por un actor de amenazas.
* **Low:** el impacto inmediato sería mínimo. Probablemente el actor de amenazas necesitaría realizar varios pasos antes de conseguir un impacto en el entorno.
* **Medium:** el actor de amenazas podría tener cierto impacto en el entorno con esta actividad, pero estaría limitado en alcance o requeriría actividad adicional.
* **High:** la actividad identificada proporciona al actor de amenazas acceso amplio para realizar acciones en el entorno o se desencadena por un impacto sobre el entorno.

Los valores predeterminados de gravedad no garantizan el nivel actual de impacto ni el impacto específico del entorno.

Los detalles de la alerta pueden personalizarse para ajustar la gravedad, las tácticas y otras propiedades de una instancia determinada de alerta utilizando valores relevantes de los campos de salida de la consulta.

Las definiciones de gravedad de las plantillas de reglas de análisis de Microsoft Sentinel son relevantes únicamente para alertas creadas por reglas de análisis. Para alertas ingeridas desde otros servicios, la gravedad está definida por el servicio de seguridad de origen.

Al crear la regla, su estado es **Enabled** de forma predeterminada, lo que significa que se ejecutará inmediatamente después de finalizar su creación.

Si no se desea que se ejecute inmediatamente, se puede seleccionar **Disabled**. La regla se agregará a la pestaña **Active rules** y podrá habilitarse posteriormente.

## Definir la lógica de consulta y configurar los ajustes

En la pestaña **Set rule logic** se puede:

* Escribir directamente una consulta en el campo **Rule query**.
* Crear la consulta en Log Analytics y copiarla y pegarla posteriormente.

Las consultas se escriben en **Kusto Query Language (KQL)**.

El ejemplo mostrado consulta la tabla **SecurityEvent** para mostrar un tipo de evento de inicio de sesión de Windows fallido.

Otro ejemplo genera una alerta cuando se crea una cantidad anómala de recursos en Azure Activity:

```kusto
AzureActivity
| where OperationNameValue == "MICROSOFT.COMPUTE/VIRTUALMACHINES/WRITE" or OperationNameValue == "MICROSOFT.RESOURCES/DEPLOYMENTS/WRITE"
| where ActivityStatusValue == "Succeeded"
| make-series dcount(ResourceId) default=0 on EventSubmissionTimestamp in range(ago(7d), now(), 1d) by Caller
```

Se recomienda que la consulta utilice un **Advanced Security Information Model (ASIM) parser** y no una tabla nativa. Esto permite que la consulta admita cualquier fuente de datos relevante actual o futura, en lugar de depender de una única fuente de datos.

### Mejores prácticas para las consultas de reglas

* La longitud de la consulta debe estar entre **1 y 10.000 caracteres**.
* No puede contener `search *` ni `union *`.
* Se pueden utilizar funciones definidas por el usuario para superar la limitación de longitud de la consulta.
* No se admite el uso de funciones ADX para crear consultas de Azure Data Explorer dentro de la ventana de consulta de Log Analytics.
* Al utilizar la función `bag_unpack`, si se proyectan columnas como campos mediante `project field1` y la columna no existe, la consulta fallará.
* Para evitarlo, se debe proyectar la columna como:
  `project field1 = column_ifexists("field1","")`.

## Enriquecimiento de alertas

### Entity mapping

La sección **Entity mapping** permite asignar parámetros de los resultados de la consulta a entidades reconocidas por Microsoft Sentinel.

Las entidades enriquecen la salida de las reglas, tanto alertas como incidentes, con información esencial que sirve como base para los procesos de investigación y acciones de corrección posteriores.

También son los criterios utilizados para agrupar alertas en incidentes en la pestaña **Incident settings**.

### Custom details

La sección **Custom details** permite extraer elementos de datos de eventos de la consulta y mostrarlos en las alertas producidas por la regla, proporcionando visibilidad inmediata del contenido de los eventos en las alertas e incidentes.

### Alert details

La sección **Alert details** permite reemplazar valores predeterminados de las propiedades de una alerta con detalles procedentes de los resultados de la consulta.

Por ejemplo, permite mostrar la dirección IP o el nombre de cuenta de un atacante en el título de la alerta, de forma que aparezca en la cola de incidentes y proporcione una visión más clara del panorama de amenazas.

### Tamaño de las alertas

El tamaño máximo de una alerta completa es de **64 KB**.

Las alertas que superen los 64 KB se truncan.

A medida que se identifican entidades, estas se agregan una por una hasta alcanzar el límite de 64 KB. Las entidades restantes se descartan.

Los demás elementos de enriquecimiento de alertas también contribuyen al tamaño de la alerta.

Para reducir el tamaño de una alerta, se puede utilizar el operador `project-away` en la consulta para eliminar campos innecesarios. También se puede considerar el operador `project` cuando solo sean necesarios unos pocos campos.

## Programación de consultas y umbral de alertas

En la sección **Query scheduling** se configuran los parámetros de ejecución de la consulta.

### Run query every

Controla la frecuencia con la que se ejecuta la consulta:

* Puede ejecutarse como máximo cada **5 minutos**.
* Puede ejecutarse como mínimo una vez cada **14 días**.

### Lookup data from the last

Determina el período de datos que cubre la consulta.

Por ejemplo:

* Los últimos 10 minutos.
* Las últimas 6 horas.

El máximo es de **14 días**.

### Start running

Esta configuración se encuentra en **Preview**.

* **Automatically:** mantiene el comportamiento original. La regla se ejecuta por primera vez inmediatamente después de ser creada y posteriormente en el intervalo definido en **Run query every**.
* **At specific time:** permite determinar cuándo se ejecutará la regla por primera vez en lugar de ejecutarla inmediatamente. Se selecciona la fecha mediante el calendario y se introduce la hora.

Las ejecuciones futuras se producirán en el intervalo especificado después de la primera ejecución.

La línea de texto situada debajo de **Start running** resume la configuración actual de programación y lookback de la consulta.

### Intervalos de consulta y período de lookback

Las dos configuraciones son independientes hasta cierto punto.

Se puede ejecutar una consulta con un intervalo corto cubriendo un período más largo que el intervalo, generando consultas superpuestas.

Sin embargo, no se puede ejecutar una consulta en un intervalo superior al período de cobertura, ya que esto produciría brechas en la cobertura general de las consultas.

### Retraso de ingesta

Para tener en cuenta la latencia que puede producirse entre la generación de un evento en el origen y su ingesta en Microsoft Sentinel, y para garantizar una cobertura completa sin duplicación de datos, Microsoft Sentinel ejecuta las reglas de análisis programadas con un retraso de **cinco minutos** respecto de su hora programada.

### Umbral de alertas

La sección **Alert threshold** permite definir el nivel de sensibilidad de la regla.

Por ejemplo, se puede establecer:

* **Generate alert when number of query results:** `Is greater than`
* Valor: `1000`

De esta forma, la regla genera una alerta únicamente si la consulta devuelve más de 1000 resultados en cada ejecución.

El campo es obligatorio. Si no se desea establecer un umbral y se quiere que la alerta registre cada evento, se debe introducir **0**.

## Configurar los ajustes de creación de incidentes

En la pestaña **Incident Settings** se puede elegir si Microsoft Sentinel convierte las alertas en incidentes accionables y cómo hacerlo.

Si no se modifica esta pestaña, Microsoft Sentinel crea un incidente separado para cada alerta.

También se puede:

* No crear incidentes.
* Agrupar varias alertas en un único incidente.

### Incident settings

En la sección **Incident settings**, la opción **Create incidents from alerts triggered by this analytics rule** está habilitada de forma predeterminada.

Esto significa que Microsoft Sentinel creará un único incidente separado para cada alerta generada por la regla.

Si no se desea que esta regla genere incidentes, por ejemplo, cuando se utiliza únicamente para recopilar información para análisis posteriores, se puede establecer esta opción en **Disabled**.

También se puede configurar la creación de un único incidente a partir de un grupo de alertas.

## Agrupación de alertas

En la sección **Alert grouping**, se puede habilitar **Group related alerts, triggered by this analytics rule, into incidents** para generar un único incidente a partir de un grupo de hasta **150 alertas** similares o recurrentes.

### Parámetros de agrupación

* **Limit the group to alerts created within the selected time frame:** determina el período durante el cual se agruparán las alertas similares o recurrentes. Las alertas correspondientes generadas dentro de ese período producirán colectivamente un incidente o un conjunto de incidentes. Las alertas fuera de ese período generarán un incidente o conjunto de incidentes separado.
* **Group alerts triggered by this analytics rule into a single incident by:** permite seleccionar el criterio utilizado para agrupar las alertas.

### Opciones de agrupación

| Opción                                                                             | Descripción                                                                                                                                                                                                                                                                                                                                                                                                                                                                                         |
| ---------------------------------------------------------------------------------- | --------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------- |
| **Group alerts into a single incident if all the entities match**                  | Las alertas se agrupan si comparten valores idénticos para cada una de las entidades asignadas. Es la configuración recomendada.                                                                                                                                                                                                                                                                                                                                                                    |
| **Group all alerts triggered by this rule into a single incident**                 | Todas las alertas generadas por esta regla se agrupan, incluso si no comparten valores idénticos.                                                                                                                                                                                                                                                                                                                                                                                                   |
| **Group alerts into a single incident if the selected entities and details match** | Las alertas se agrupan si comparten valores idénticos para todas las entidades asignadas, los detalles de alerta y los detalles personalizados seleccionados. Puede utilizarse para crear incidentes separados según direcciones IP de origen o destino, o para agrupar alertas que coincidan con una entidad y gravedad específicas. Al utilizar esta opción, se debe seleccionar al menos un tipo de entidad o campo; de lo contrario, la validación de la regla fallará y la regla no se creará. |

### Reapertura de incidentes

**Re-open closed matching incidents** permite determinar qué ocurre cuando un incidente ya fue resuelto y cerrado y posteriormente se genera otra alerta que debería pertenecer al mismo incidente.

* **Enabled:** se vuelve a abrir el incidente cerrado.
* **Disabled:** la alerta genera un nuevo incidente.

Se pueden agrupar hasta **150 alertas** en un único incidente.

El incidente solo se crea después de que se hayan generado todas las alertas. Todas las alertas se agregan inmediatamente al incidente cuando este se crea.

Si una regla genera más de 150 alertas y está configurada para agruparlas en un único incidente, se genera un nuevo incidente con los mismos detalles del incidente original y las alertas excedentes se agrupan en el nuevo incidente.

## Configurar respuestas automatizadas y crear la regla

En la pestaña **Automated responses** se pueden utilizar reglas de automatización para establecer respuestas automáticas en tres tipos de ocasiones:

1. Cuando una alerta es generada por esta regla de análisis.
2. Cuando se crea un incidente con alertas generadas por esta regla de análisis.
3. Cuando se actualiza un incidente con alertas generadas por esta regla de análisis.

La cuadrícula **Automation rules** muestra las reglas de automatización que ya se aplican a esta regla de análisis porque cumple las condiciones definidas en ellas.

Estas reglas pueden editarse seleccionando los tres puntos al final de cada fila, o se puede crear una nueva regla de automatización.

Las reglas de automatización permiten realizar:

* Clasificación básica.
* Asignación.
* Flujos de trabajo.
* Cierre de incidentes.

También permiten automatizar tareas más complejas e invocar respuestas desde sistemas remotos para corregir amenazas mediante **playbooks**.

Los playbooks pueden ejecutarse tanto para incidentes como para alertas individuales.

### Alert automation (classic)

En la parte inferior de la pantalla, bajo **Alert automation (classic)**, aparecen los playbooks configurados para ejecutarse automáticamente cuando se genera una alerta utilizando el método anterior.

* Desde junio de 2023 ya no se pueden agregar playbooks a esta lista.
* Los playbooks que ya aparecen en la lista continuarán ejecutándose hasta que este método quede obsoleto, con vigencia desde marzo de 2026.
* Si todavía existen playbooks en esta lista, se debe crear una regla de automatización basada en el trigger de creación de alertas e invocar el playbook desde ella.
* Después de hacerlo, se debe seleccionar los tres puntos al final de la fila del playbook y seleccionar **Remove**.

Para crear la regla:

1. Seleccionar **Review and create**.
2. Revisar todas las configuraciones.
3. Cuando aparezca el mensaje **Validation passed**, seleccionar **Create**.

## Ver la regla y sus resultados

La nueva regla personalizada, de tipo **Scheduled**, aparece en la tabla de la pestaña **Active rules** de la pantalla principal de Analytics.

Desde esta lista se puede:

* Habilitar la regla.
* Deshabilitar la regla.
* Eliminar la regla.

Para visualizar los resultados de las reglas de análisis creadas, se debe acceder a la página **Incidents**, donde se pueden:

* Clasificar incidentes.
* Investigarlos.
* Corregir las amenazas.

La consulta de la regla puede actualizarse para excluir falsos positivos.

# Configurar automatización en Microsoft Sentinel

## ¿Qué son las reglas de automatización y los playbooks?

Las **reglas de automatización** ayudan a realizar el triaje de incidentes en Microsoft Sentinel.

Se pueden utilizar para:

* Asignar automáticamente incidentes al personal correspondiente.
* Cerrar incidentes ruidosos o falsos positivos conocidos.
* Cambiar su gravedad.
* Agregar etiquetas.
* Ejecutar playbooks en respuesta a incidentes o alertas.

Los **playbooks** son colecciones de procedimientos que pueden ejecutarse desde Microsoft Sentinel en respuesta a:

* Un incidente completo.
* Una alerta individual.
* Una entidad específica.

Un playbook puede automatizar y orquestar la respuesta y puede configurarse para ejecutarse automáticamente cuando se generan determinadas alertas o cuando se crean o actualizan incidentes, mediante una regla de automatización.

También puede ejecutarse manualmente bajo demanda sobre incidentes, alertas o entidades específicas.

Los playbooks de Microsoft Sentinel se basan en flujos de trabajo creados en **Azure Logic Apps**.

Cada playbook se crea para la suscripción específica a la que pertenece, aunque la vista **Playbooks** muestra todos los playbooks disponibles en las suscripciones seleccionadas.

## Ejemplo de respuesta automatizada

Para detener a usuarios potencialmente comprometidos e impedir que se muevan por la red y roben información, se puede crear una respuesta automatizada y multifacética.

El playbook puede:

1. Recibir un incidente mediante una regla de automatización.
2. Abrir un ticket en **ServiceNow** u otro sistema de tickets de TI.
3. Enviar un mensaje al canal de operaciones de seguridad en **Microsoft Teams** o **Slack**.
4. Enviar toda la información del incidente por correo electrónico al administrador de red y al administrador de seguridad.
5. Incluir botones **Block** e **Ignore** en el mensaje.
6. Esperar una respuesta de los administradores.
7. Si seleccionan **Block**:

   * Enviar un comando a Microsoft Entra ID para deshabilitar al usuario.
   * Enviar un comando al firewall para bloquear la dirección IP.
8. Si seleccionan **Ignore**:

   * Cerrar el incidente en Microsoft Sentinel.
   * Cerrar el ticket en ServiceNow.

Para activar el playbook se crea una regla de automatización que se ejecuta cuando se generan estos incidentes.

La regla puede:

1. Cambiar el estado del incidente a **Active**.
2. Asignar el incidente al analista encargado de ese tipo de incidente.
3. Agregar la etiqueta **compromised user**.
4. Ejecutar el playbook.

## Crear un playbook

1. Para Microsoft Sentinel en Azure Portal, seleccionar **Configuration > Automation**.
2. Para Microsoft Sentinel en Defender Portal, seleccionar **Microsoft Sentinel > Configuration > Automation**.
3. Seleccionar **Create**.

Existen cuatro opciones para crear playbooks:

* Para un playbook **Standard**, seleccionar **Blank playbook**.
* Para un playbook **Consumption**, seleccionar según el trigger:

  * **Playbook with incident trigger**
  * **Playbook with alert trigger**
  * **Playbook with entity trigger**

### Pestaña Basics

1. Seleccionar **Subscription**, **Resource group** y **Region**.
2. Introducir el **Playbook name**.
3. Opcionalmente, habilitar **diagnostics logs in Log Analytics** y seleccionar el workspace.
4. Si el playbook necesita acceder a recursos protegidos dentro o conectados a una red virtual de Azure, se puede asociar un **Integration Service Environment (ISE)**.
5. Seleccionar **Next: Connections**.

### Pestaña Connections

Idealmente se debe dejar esta sección configurada para que Logic Apps se conecte a Microsoft Sentinel mediante **managed identity**.

Seleccionar **Next: Review and create**.

### Pestaña Review and create

Revisar la configuración y seleccionar **Create and continue to designer**.

Después de crear y desplegar el playbook, se abre el **Logic App Designer**.

El trigger seleccionado inicialmente se agrega automáticamente como primer paso y desde allí se puede continuar diseñando el flujo de trabajo.

Si se seleccionó el trigger **Microsoft Sentinel entity (Preview)**, se debe seleccionar el tipo de entidad que el playbook recibirá como entrada.

## Agregar acciones

En el diseñador se pueden agregar:

* Acciones.
* Condiciones lógicas.
* Bucles.
* Condiciones switch/case.

Al seleccionar **New step**, se abre un nuevo marco donde se puede elegir un sistema, aplicación o condición.

En cada paso, al seleccionar un campo aparecen los menús:

* **Dynamic content:** permite agregar referencias a atributos de la alerta o incidente recibido, incluidos valores y atributos de las entidades asignadas y detalles personalizados.
* **Expression:** permite utilizar una biblioteca de funciones para agregar lógica adicional a los pasos.

## Automatizar respuestas ante amenazas

Una vez creado el playbook y definidos el trigger, las condiciones, las acciones y las salidas, se deben determinar los criterios bajo los cuales se ejecutará y configurar el mecanismo de automatización.

### Responder a incidentes y alertas

Para utilizar un playbook en respuesta automática a un **incidente completo** o a una **alerta individual**, se crea una **automation rule** que se ejecutará cuando se cree o actualice el incidente, o cuando se genere la alerta.

La regla incluye una acción que ejecuta el playbook.

### Crear una automation rule

1. Desde la página **Automation** de Microsoft Sentinel, seleccionar **Create > Automation rule**.
2. Introducir un nombre para la regla.
3. Seleccionar el trigger correspondiente:

   * **When incident is created**
   * **When incident is updated**
   * **When alert is created**
4. Configurar las condiciones.
5. En **Actions**, seleccionar **Run playbook**.
6. Seleccionar el playbook disponible.
7. Agregar otras acciones si son necesarias.
8. Opcionalmente, establecer una fecha de expiración.
9. Introducir un número en **Order** para determinar el orden de ejecución.
10. Seleccionar **Apply**.

Un playbook solo puede ejecutarse desde una regla de automatización si comienza con el mismo trigger que la regla, es decir, de incidente o de alerta.

## Permisos para ejecutar playbooks

Microsoft Sentinel debe recibir permisos explícitos para ejecutar playbooks, tanto manualmente como mediante reglas de automatización.

Si un playbook aparece deshabilitado en la lista de selección, significa que Sentinel no tiene permisos sobre el grupo de recursos del playbook.

Desde **Manage playbook permissions** se pueden seleccionar los grupos de recursos que contienen los playbooks y aplicar los permisos.

Se requiere:

* Permisos **Owner** sobre cualquier grupo de recursos al que se quieran conceder permisos a Microsoft Sentinel.
* Rol **Logic App Contributor** en cualquier grupo de recursos que contenga playbooks que se quieran ejecutar.

En una implementación multitenant, si el playbook está en otro tenant, se debe conceder permiso a Microsoft Sentinel para ejecutar el playbook en el tenant donde reside.

En un escenario MSSP, cuando se ejecuta un playbook en el tenant de un cliente desde una regla de automatización creada en el tenant del proveedor, se deben conceder permisos a Microsoft Sentinel en ambos tenants.

El rol **Microsoft Sentinel Automation Contributor** tiene el GUID:

`f4c81013-99ee-4d62-a7ee-b3f1f648599a`

## Método heredado para responder a alertas

Otra forma de ejecutar automáticamente playbooks ante alertas es llamarlos desde una regla de análisis.

Cuando la regla genera una alerta, se ejecuta el playbook.

Este método quedó obsoleto desde **marzo de 2026**.

Desde junio de 2023 ya no se pueden agregar nuevos playbooks a las reglas de análisis de esta manera. Los playbooks existentes continuaron ejecutándose hasta marzo de 2026.

Se recomienda utilizar **automation rules** para ejecutar los playbooks.

## Ejecutar un playbook bajo demanda

Los playbooks también pueden ejecutarse manualmente bajo demanda como respuesta a:

* Alertas.
* Incidentes (Preview).
* Entidades (Preview).

Esto permite disponer de mayor intervención y control humano sobre los procesos de orquestación y respuesta.

Para ejecutar manualmente un playbook sobre una alerta:

1. En **Incidents**, seleccionar un incidente.
2. Abrir los detalles completos del incidente.
3. En **Incident timeline**, seleccionar la alerta.
4. Seleccionar los tres puntos de la alerta.
5. Seleccionar **Run playbook**.
6. En **Alert playbooks**, seleccionar **Run** en el playbook deseado.

El historial de ejecución puede consultarse desde la pestaña **Runs** del panel **Alert playbooks**.

# Automatización de la respuesta ante amenazas con Microsoft Sentinel

Las reglas de automatización ayudan a realizar el triaje de incidentes en Microsoft Sentinel y también se utilizan para ejecutar playbooks en respuesta a incidentes o alertas.

Los playbooks permiten automatizar y orquestar la respuesta ante amenazas mediante flujos de trabajo de Azure Logic Apps.

## Costos de Logic Apps

Los playbooks utilizan Azure Logic Apps, por lo que pueden aplicarse cargos adicionales.

## Prerrequisitos y roles

Los siguientes roles son necesarios para utilizar Azure Logic Apps para crear y ejecutar playbooks en Microsoft Sentinel:

| Rol                                           | Descripción                                                                                                     |
| --------------------------------------------- | --------------------------------------------------------------------------------------------------------------- |
| **Owner**                                     | Permite conceder acceso a los playbooks en el grupo de recursos.                                                |
| **Microsoft Sentinel Contributor**            | Permite asociar un playbook a una regla de análisis o automatización.                                           |
| **Microsoft Sentinel Responder**              | Permite acceder a un incidente para ejecutar un playbook manualmente, pero no permite ejecutar el playbook.     |
| **Microsoft Sentinel Playbook Operator**      | Permite ejecutar manualmente un playbook.                                                                       |
| **Microsoft Sentinel Automation Contributor** | Permite que las reglas de automatización ejecuten playbooks. Este rol no se utiliza para ningún otro propósito. |

### Roles de Azure Logic Apps según el tipo de Logic App

| Logic App       | Rol de Azure                        | Descripción                                                                                  |
| --------------- | ----------------------------------- | -------------------------------------------------------------------------------------------- |
| **Consumption** | **Logic App Contributor**           | Editar y administrar Logic Apps. Ejecutar playbooks. No permite conceder acceso a playbooks. |
| **Consumption** | **Logic App Operator**              | Leer, habilitar y deshabilitar Logic Apps. No permite editarlas ni actualizarlas.            |
| **Standard**    | **Logic Apps Standard Operator**    | Habilitar, volver a enviar y deshabilitar workflows en una Logic App.                        |
| **Standard**    | **Logic Apps Standard Developer**   | Crear y editar Logic Apps.                                                                   |
| **Standard**    | **Logic Apps Standard Contributor** | Administrar todos los aspectos de una Logic App.                                             |

La pestaña **Active playbooks** de la página **Automation** muestra todos los playbooks activos disponibles en las suscripciones seleccionadas.

De forma predeterminada, un playbook solo puede utilizarse dentro de la suscripción a la que pertenece, salvo que se concedan explícitamente permisos de Microsoft Sentinel al grupo de recursos del playbook.

## Permisos adicionales para ejecutar playbooks sobre incidentes

Microsoft Sentinel utiliza una **cuenta de servicio** para ejecutar playbooks sobre incidentes, agregar seguridad y permitir que la API de reglas de automatización admita escenarios de CI/CD.

Esta cuenta de servicio se utiliza para:

* Playbooks activados por incidentes.
* Ejecución manual de un playbook sobre un incidente específico.

Además de los roles y permisos propios del usuario, la cuenta de servicio de Microsoft Sentinel necesita permisos propios sobre el grupo de recursos donde reside el playbook mediante el rol:

**Microsoft Sentinel Automation Contributor**

Una vez que Microsoft Sentinel tiene este rol, puede ejecutar cualquier playbook del grupo de recursos, manualmente o mediante una regla de automatización.

Para conceder los permisos necesarios a Microsoft Sentinel se requiere un rol **Owner** o **User Access Administrator**.

Para ejecutar los playbooks también se necesita el rol **Logic App Contributor** en el grupo de recursos que contiene los playbooks.

# Ejemplo: detener usuarios potencialmente comprometidos

Los equipos SOC buscan impedir que usuarios potencialmente comprometidos puedan desplazarse por la red y robar información.

Se recomienda crear una respuesta automatizada y multifacética para los incidentes generados por reglas que detecten usuarios comprometidos.

### Flujo

1. Se crea un incidente para un usuario potencialmente comprometido.
2. Una regla de automatización se activa y ejecuta el playbook.
3. El playbook abre un ticket en el sistema de tickets de TI, como ServiceNow.
4. Envía un mensaje al canal de operaciones de seguridad en Microsoft Teams o Slack.
5. Envía toda la información del incidente por correo electrónico al administrador de red y al administrador de seguridad.
6. El correo incluye las opciones **Block** e **Ignore**.
7. El playbook espera la respuesta de los administradores.
8. Si seleccionan **Block**:

   * Envía un comando a Microsoft Entra ID para deshabilitar al usuario.
   * Envía un comando al firewall para bloquear la dirección IP.
9. Si seleccionan **Ignore**:

   * Cierra el incidente en Microsoft Sentinel.
   * Cierra el ticket en ServiceNow.
