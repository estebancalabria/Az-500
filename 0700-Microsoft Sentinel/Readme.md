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
    * Existe una alta probabilidad de que el recurso esté comprometido. Se debe investigar inmediatamente              |
* **Media**
    * Probablemente se trate de una actividad sospechosa que podría indicar que un recurso está comprometido.                                                                                      * **Baja**
    * Puede tratarse de un positivo benigno o de un ataque bloqueado. Defender for Cloud no tiene suficiente confianza en que la intención sea maliciosa y la actividad podría ser inocente.
* **Informativa**
  *  Un incidente normalmente está compuesto por varias alertas. Algunas pueden parecer únicamente informativas por sí mismas, pero en el contexto de las demás alertas pueden justificar una investigación más detallada.                                                                                                                                                                                                                                                                                                  |
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
- Defender for Cloud agrupa alertas relacionadas en **incidentes**.
- Combina señales de distintos recursos + IA para descubrir patrones de ataque.
- Permite descartar actividades que parecen ataque pero no lo son (reduce falsos positivos).

### Fuentes de telemetría integrada
Azure, Microsoft 365, Microsoft CRM Online, Dynamics AX, Outlook.com, MSN.com, Microsoft Digital Crimes Unit (DCU), Microsoft Security Response Center (MSRC).

### Tres técnicas de análisis de seguridad

| Técnica | Base | Detecta |
|---|---|---|
| **Inteligencia sobre amenazas integrada** | Feeds globales de threat intel (propios + terceros) | Actores maliciosos **conocidos** |
| **Análisis de comportamiento** | Patrones **conocidos**, derivados de ML sobre grandes datasets + análisis de analistas | Comportamiento malicioso ya catalogado (logs de VM, dispositivos de red virtual, fabric) |
| **Detección de anomalías** | Línea base **específica de cada implementación**, sin patrones previos | Desvíos atípicos → amenazas **no conocidas** (zero-day) |

**Punto clave** behavior analytics = patrones conocidos vs. anomaly detection = línea base propia, sin patrones previos.
---

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

## Planes de retención en Microsoft Sentinel — resumen simple

**Idea central:** no todos los logs valen lo mismo, así que Sentinel ofrece distintos planes de almacenamiento según cuánto necesitás consultarlos.

### Los 3 planes

| Plan | Para qué sirve | Retención interactiva | Costo | Consultas |
|---|---|---|---|---|
| **Analytics logs** | Logs que necesitás ver en tiempo real (autenticación, EDR, auditoría) | 90 días (hasta 2 años) | Caro al ingerir | Ilimitadas, gratis, alto rendimiento |
| **Auxiliary logs** (Preview) | Logs de volumen alto y valor bajo (firewall, NetFlow, proxy) | 30 días | Muy barato al ingerir | Limitadas, se paga por GB consultado, solo 1 tabla |
| **Basic logs** | Igual que Auxiliary pero sin ser Preview | Similar | Intermedio | Similar a Auxiliary |

### Reglas clave para el examen
- **Analytics** = corre reglas de detección automática + permite `restore`.
- **Auxiliary** = NO permite `restore`, solo `search job`. No corre detección automática directa.
- **Summary rules**: resumen datos de Auxiliary y los mandan a una tabla en Analytics, para poder detectar sobre ese resumen.
- Retención a largo plazo: hasta 12 años en cualquier plan, muy barata, pero solo accesible vía `search job` (o `restore` en Analytics).

### Impacto práctico
La elección del plan es una decisión de **costo vs. accesibilidad**: fuente que consultás seguido → Analytics; fuente que guardás "por las dudas" → Auxiliary/Basic.

---

# Alertas e incidentes de Microsoft Sentinel

Después de configurar Microsoft Sentinel para recopilar datos de toda la organización, es necesario analizar continuamente esos datos para detectar amenazas de seguridad. Para ello, Microsoft Sentinel proporciona reglas de detección de amenazas que se ejecutan periódicamente, consultan los datos recopilados y los analizan para descubrir amenazas.

Estas reglas tienen diferentes tipos y se conocen colectivamente como **reglas de análisis (analytics rules)**.

Las reglas generan **alertas** cuando encuentran aquello que están buscando. Las alertas contienen información sobre los eventos detectados, como las entidades involucradas, entre ellas usuarios, dispositivos y direcciones.

Las alertas se agregan y correlacionan en **incidentes**, que funcionan como expedientes de casos que pueden asignarse e investigarse para comprender el alcance completo de la amenaza detectada y responder en consecuencia.

También es posible incorporar respuestas automatizadas predeterminadas en la configuración de las propias reglas.

Las reglas pueden crearse desde cero mediante el asistente integrado de reglas de análisis. Sin embargo, Microsoft recomienda utilizar las numerosas **plantillas de reglas de análisis** disponibles mediante las soluciones para Microsoft Sentinel del Content hub.

Estas plantillas son prototipos de reglas prediseñados por equipos de expertos y analistas de seguridad, basados en:

* Amenazas conocidas.
* Vectores de ataque comunes.
* Cadenas de escalada de actividad sospechosa.

Al activar reglas a partir de estas plantillas, se busca automáticamente en el entorno cualquier actividad que parezca sospechosa. Muchas plantillas pueden personalizarse para buscar tipos específicos de eventos o filtrarlos según las necesidades.

Microsoft Sentinel está disponible de forma general dentro de la plataforma unificada de operaciones de seguridad de Microsoft en el portal de Microsoft Defender. Como versión preliminar, Microsoft Sentinel está disponible en el portal de Defender sin Microsoft Defender XDR ni una licencia E5.

## Tipos de reglas de análisis

Las reglas y plantillas disponibles pueden consultarse en la página **Analytics** del menú **Configuration** de Microsoft Sentinel.

* Una pestaña muestra las reglas actualmente activas.
* Otra muestra las plantillas para crear nuevas reglas.
* Una tercera pestaña muestra **Anomalies**, un tipo especial de regla.

Para encontrar más plantillas que las mostradas actualmente, se puede acceder al **Content hub** de Microsoft Sentinel e instalar las soluciones de producto relacionadas o contenido independiente. Las plantillas de reglas de análisis están disponibles con casi todas las soluciones del Content hub.

Los tipos de reglas y plantillas disponibles incluyen:

* **Scheduled rules**.
* **Near-real-time (NRT) rules**.
* **Anomaly rules**.
* **Microsoft security rules**.

También existen tipos especializados de plantillas que pueden crear una única instancia de una regla y ofrecen opciones de configuración limitadas:

* **Threat intelligence**.
* **Advanced multistage attack detection (Fusion)**.
* **Machine learning (ML) behavior analytics**.

## Scheduled rules

Son, con diferencia, el tipo de regla de análisis más común.

Las **Scheduled rules** se basan en consultas **Kusto** configuradas para ejecutarse a intervalos regulares y examinar datos sin procesar de un período definido de **lookback**.

Si la cantidad de resultados obtenidos por la consulta supera el umbral configurado en la regla, esta genera una alerta.

Las consultas de las plantillas de scheduled rules son desarrolladas por expertos en seguridad y ciencia de datos de Microsoft o por el proveedor de la solución que proporciona la plantilla.

Las consultas pueden realizar operaciones estadísticas complejas sobre los datos objetivo, revelando líneas base y valores atípicos en grupos de eventos.

La lógica de consulta se muestra en la configuración de la regla. Se puede utilizar la lógica, la programación y la configuración de lookback definida en la plantilla o personalizarlas para crear nuevas reglas.

También es posible crear reglas completamente nuevas desde cero.

## Near-real-time (NRT) rules

Las **NRT rules** son un subconjunto limitado de las scheduled rules.

Están diseñadas para ejecutarse **una vez por minuto**, con el objetivo de proporcionar información lo más actualizada posible.

Funcionan principalmente como las scheduled rules y se configuran de forma similar, aunque tienen algunas limitaciones.

## Anomaly rules

Las **Anomaly rules** utilizan aprendizaje automático para observar determinados tipos de comportamiento durante un período de tiempo y determinar una línea base.

Cada regla tiene sus propios parámetros y umbrales, adecuados al comportamiento que se está analizando.

Una vez completado el período de observación, se establece la línea base. Cuando la regla observa comportamientos que superan los límites definidos en la línea base, marca esas ocurrencias como anomalías.

Las configuraciones de las reglas incluidas de fábrica no pueden modificarse ni ajustarse. Sin embargo, se puede duplicar una regla y modificar y ajustar el duplicado.

En estos casos:

* Ejecutar el duplicado en modo **Flighting**.
* Mantener simultáneamente la regla original en modo **Production**.
* Comparar los resultados.
* Cambiar el duplicado a **Production** cuando el ajuste sea satisfactorio.

Las anomalías no necesariamente indican por sí mismas un comportamiento malicioso o incluso sospechoso.

Por este motivo, las anomaly rules no generan sus propias alertas. En su lugar, registran los resultados de su análisis, es decir, las anomalías detectadas, en la tabla **Anomalies**.

Esta tabla puede consultarse para proporcionar contexto que mejore:

* Las detecciones.
* Las investigaciones.
* El threat hunting.

## Microsoft security rules

Las scheduled rules y NRT rules crean automáticamente incidentes para las alertas que generan.

Sin embargo, las alertas generadas por servicios externos e ingeridas en Microsoft Sentinel no crean sus propios incidentes.

Las **Microsoft security rules** crean automáticamente incidentes de Microsoft Sentinel a partir de las alertas generadas por otras soluciones de seguridad de Microsoft, en tiempo real.

Se pueden utilizar plantillas de seguridad de Microsoft para crear nuevas reglas con una lógica similar.

Las Microsoft security rules no están disponibles si:

* Se habilitó **Microsoft Defender XDR incident integration**.
* Microsoft Sentinel fue incorporado al **Defender portal**.

En estos escenarios, Microsoft Defender XDR crea los incidentes.

Las reglas previamente definidas de este tipo se deshabilitan automáticamente.

## Threat intelligence

Microsoft Sentinel puede utilizar la inteligencia sobre amenazas producida por Microsoft para generar alertas e incidentes de alta fidelidad mediante la regla de análisis **Microsoft Threat Intelligence Analytics**.

Esta regla no es personalizable y, cuando está habilitada, relaciona automáticamente:

* Registros Common Event Format (CEF).
* Datos Syslog.
* Eventos Windows DNS.

con indicadores de amenazas de dominio, IP y URL procedentes de Microsoft Threat Intelligence.

Algunos indicadores contienen información contextual adicional mediante **MDTI (Microsoft Defender Threat Intelligence)**.

## Advanced multistage attack detection (Fusion)

Microsoft Sentinel utiliza el motor de correlación **Fusion**, con algoritmos de aprendizaje automático escalables, para detectar ataques avanzados de múltiples etapas.

Fusion correlaciona numerosas alertas y eventos de baja fidelidad procedentes de múltiples productos para generar incidentes de alta fidelidad y accionables.

La regla **Advanced multistage attack detection** está habilitada de forma predeterminada.

Como la lógica está oculta y no es personalizable, solo puede existir una regla con esta plantilla.

El motor Fusion también puede correlacionar alertas producidas por scheduled analytics rules con alertas procedentes de otros sistemas, generando como resultado incidentes de alta fidelidad.

El tipo de regla Advanced multistage attack detection no está disponible si:

* Se habilitó Microsoft Defender XDR incident integration.
* Microsoft Sentinel fue incorporado al Defender portal.

En estos escenarios, Microsoft Defender XDR crea los incidentes.

Además, algunas plantillas de detección de Fusion se encuentran actualmente en **PREVIEW**.

## Machine learning (ML) behavior analytics

Las reglas **ML Behavior Analytics** utilizan algoritmos propietarios de aprendizaje automático de Microsoft para generar alertas e incidentes de alta fidelidad.

Estas reglas son actualmente **PREVIEW**, no son personalizables y, cuando están habilitadas, detectan comportamientos anómalos específicos de inicio de sesión mediante:

* SSH.
* RDP.

La detección se basa en información de:

* Dirección IP.
* Geolocalización.
* Historial del usuario.

## Permisos de acceso para las reglas de análisis

Al crear una regla de análisis, se aplica un token de permisos de acceso a la regla y se guarda junto con ella.

Este token garantiza que la regla pueda acceder al workspace que contiene los datos consultados y que ese acceso se mantenga incluso si el creador de la regla pierde acceso al workspace.

Existe una excepción cuando una regla se crea para acceder a workspaces ubicados en otras suscripciones o tenants, como ocurre en escenarios MSSP.

En estos casos, Microsoft Sentinel aplica medidas de seguridad adicionales para evitar el acceso no autorizado a los datos de los clientes.

Las credenciales del usuario que creó la regla se aplican a la regla en lugar de un token de acceso independiente.

Por lo tanto, cuando el usuario deja de tener acceso a la otra suscripción o tenant, la regla deja de funcionar.

En escenarios cross-subscription o cross-tenant, cuando un analista o ingeniero pierde acceso a un workspace determinado, las reglas creadas por ese usuario dejan de funcionar.

En esta situación se recibe un mensaje de supervisión de estado relacionado con **"insufficient access to resource"** y la regla se **auto-deshabilita** después de haber fallado una determinada cantidad de veces.

## Exportar reglas a una plantilla ARM

Las reglas pueden exportarse a una plantilla de **Azure Resource Manager (ARM)** para administrarlas y desplegarlas como código.

También se pueden importar reglas desde archivos de plantilla para visualizarlas y editarlas en la interfaz de usuario.

# Configurar conectores de datos en Microsoft Sentinel

Después de incorporar Microsoft Sentinel al workspace, se utilizan **data connectors** para comenzar a ingerir datos en Microsoft Sentinel.

Microsoft Sentinel incluye numerosos conectores listos para usar para servicios de Microsoft, que se integran en tiempo real.

Por ejemplo, el conector **Microsoft 365 Defender** es un conector service-to-service que integra datos de:

* Office 365.
* Microsoft Entra ID.
* Microsoft Defender for Identity.
* Microsoft Defender for Cloud Apps.

Los conectores integrados permiten conectarse a un ecosistema de seguridad más amplio para productos que no son de Microsoft.

Por ejemplo, se pueden utilizar:

* **Syslog**.
* **Common Event Format (CEF)**.
* **REST APIs**.

para conectar las fuentes de datos con Microsoft Sentinel.

La página **Data connectors** de Microsoft Sentinel muestra la lista completa de conectores y su estado para el workspace. Próximamente, esta página solo mostrará la lista de conectores que estén en uso.

> **Nota:** Para agregar más conectores de datos, se debe instalar la solución asociada al conector desde **Content Hub**.

## Habilitar un conector de datos

Desde la página **Data connectors**:

1. Seleccionar el conector activo o personalizado que se desea conectar.
2. Seleccionar **Open connector page**.
3. Si el conector de datos deseado no aparece, instalar la solución asociada desde **Content Hub**.
4. Cumplir todos los requisitos previos indicados en la pestaña **Instructions**.
5. Seguir las instrucciones de la página del conector para ingerir los datos en Microsoft Sentinel.

El inicio de la llegada de datos puede tardar algún tiempo.

Después de conectar el origen, aparece un resumen de los datos en el gráfico **Data received** y el estado de conectividad de los tipos de datos.

## Integración mediante REST API para conectores de datos

Muchas tecnologías de seguridad proporcionan un conjunto de API para recuperar archivos de registro y algunas fuentes de datos pueden utilizar estas API para conectarse a Microsoft Sentinel.

Los conectores de datos que utilizan API se integran de una de las siguientes maneras:

* Integración desde el lado del proveedor.
* Integración mediante Azure Functions.

### Integración REST API desde el lado del proveedor

Una integración API desarrollada por el proveedor se conecta con las fuentes de datos del proveedor y envía los datos a tablas de registros personalizadas de Microsoft Sentinel mediante **Azure Monitor Data Collector API**.

### Integración REST API mediante Azure Functions

Las integraciones que utilizan Azure Functions para conectarse a una API de proveedor:

1. Formatean primero los datos.
2. Envían los datos a tablas de registros personalizadas de Microsoft Sentinel mediante **Azure Monitor Data Collector API**.

## Integración basada en agentes para conectores de datos

Microsoft Sentinel puede utilizar el protocolo **Syslog** para conectar un agente a cualquier fuente de datos que pueda realizar streaming de registros en tiempo real.

Por ejemplo, la mayoría de las fuentes de datos locales se conectan mediante integración basada en agentes.

Existen diferentes tipos de conectores de datos de Microsoft Sentinel basados en agentes. Los pasos para configurar cada conexión se encuentran en la página correspondiente del conector de datos de Microsoft Sentinel.

### Syslog

Se pueden transmitir eventos desde dispositivos basados en Linux que admitan Syslog hacia Microsoft Sentinel mediante **Azure Monitor Agent (AMA)**.

Dependiendo del tipo de dispositivo, el agente se instala:

* Directamente en el dispositivo.
* En un reenviador de registros dedicado basado en Linux.

El AMA recibe eventos del daemon Syslog mediante UDP.

El daemon Syslog reenvía internamente los eventos al agente mediante **UDS (Unix Domain Sockets)**.

El AMA transmite posteriormente estos eventos al workspace de Microsoft Sentinel.

#### Flujo de Syslog

1. El daemon Syslog integrado del dispositivo recopila los eventos locales de los tipos especificados y los reenvía localmente al agente.
2. El agente transmite los eventos al workspace de Log Analytics.
3. Después de una configuración correcta, los datos aparecen en la tabla **Syslog** de Log Analytics.

### Common Event Format (CEF)

Los formatos de registro varían, pero muchas fuentes admiten el formato basado en **CEF**.

El agente de Microsoft Sentinel, que en realidad es el **Log Analytics agent**, convierte los registros con formato CEF a un formato que Log Analytics puede ingerir.

Para las fuentes de datos que generan datos en CEF:

1. Configurar el agente Syslog.
2. Configurar el flujo de datos CEF.
3. Después de una configuración correcta, los datos aparecen en la tabla **CommonSecurityLog**.

### Custom logs

Para algunas fuentes de datos, se pueden recopilar registros como archivos en equipos Windows o Linux mediante el **Log Analytics custom log collection agent**.

Se deben seguir los pasos indicados en la página de cada conector de datos de Microsoft Sentinel para conectar mediante el agente de recopilación de registros personalizados de Log Analytics.

Después de una configuración correcta, los datos aparecen en tablas personalizadas.

## Integración service-to-service para conectores de datos

Microsoft Sentinel utiliza la infraestructura de Azure para proporcionar soporte integrado **service-to-service** para:

* Servicios de Microsoft.
* Amazon Web Services.

## Implementar conectores de datos como parte de una solución

Las soluciones de Microsoft Sentinel proporcionan paquetes de contenido de seguridad que incluyen:

* Data connectors.
* Workbooks.
* Analytics rules.
* Playbooks.
* Otros contenidos.

Cuando se implementa una solución que contiene un conector de datos, se obtiene el conector junto con el contenido relacionado en la misma implementación.

## Soporte de conectores de datos

Tanto Microsoft como otras organizaciones desarrollan conectores de datos para Microsoft Sentinel.

Cada conector de datos tiene uno de los siguientes tipos de soporte:

| Tipo de soporte         | Descripción                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                       |
| ----------------------- | ----------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------- |
| **Microsoft supported** | Se aplica a conectores de datos para fuentes donde Microsoft es el proveedor y autor de los datos, así como a algunos conectores desarrollados por Microsoft para fuentes de datos que no son de Microsoft. Microsoft admite y mantiene estos conectores de acuerdo con los planes de soporte de Microsoft Azure. Los conectores desarrollados por cualquier entidad distinta de Microsoft cuentan con soporte de Partners o Community.                                                                           |
| **Partner supported**   | Se aplica a conectores de datos desarrollados por entidades distintas de Microsoft. La empresa asociada proporciona soporte o mantenimiento para estos conectores. Puede ser un Independent Software Vendor, Managed Service Provider, Systems Integrator u otra organización cuya información de contacto aparezca en la página del conector de datos de Microsoft Sentinel. Para problemas con un conector con soporte de partner, se debe contactar con el contacto de soporte especificado para ese conector. |
| **Community supported** | Se aplica a conectores de datos desarrollados por Microsoft o por desarrolladores asociados que no tienen contactos de soporte y mantenimiento indicados en la página correspondiente del conector de datos de Microsoft Sentinel.                                                                                                                                                                                                                                                                                |

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
