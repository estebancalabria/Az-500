# Microsoft Defender for Cloud

## Introducción

Este módulo se centra en proporcionar a los administradores los conocimientos y habilidades necesarios para administrar y mejorar la postura de seguridad de su entorno de nube mediante Microsoft Defender for Cloud, garantizando la identificación y corrección proactivas de los riesgos de seguridad.

### Escenario

Imagine que es un especialista en seguridad en la nube responsable de proteger los recursos en la nube de su organización. Su organización utiliza múltiples proveedores y servicios en la nube y necesita mantener una sólida postura de seguridad en todo el entorno, identificando y abordando proactivamente los riesgos de seguridad.

### Objetivos de aprendizaje

Al finalizar este módulo, los participantes podrán:

* Identificar y corregir riesgos de seguridad mediante Secure Score e Inventory de Microsoft Defender for Cloud para evaluar y mejorar la postura de seguridad.
* Evaluar el cumplimiento de los marcos de seguridad y Microsoft Defender for Cloud para garantizar la alineación con los estándares y las prácticas recomendadas de seguridad.
* Agregar estándares industriales y regulatorios a Microsoft Defender for Cloud para mantener el cumplimiento de requisitos específicos.
* Agregar iniciativas personalizadas a Microsoft Defender for Cloud para abordar necesidades de seguridad específicas de la organización.
* Conectar entornos de nube híbrida y multinube a Microsoft Defender for Cloud para centralizar la supervisión y administración de la seguridad.
* Identificar y supervisar activos externos mediante Microsoft Defender External Attack Surface Management para protegerse contra amenazas externas.

### Metas

El módulo tiene como objetivo proporcionar los conocimientos y la experiencia necesarios para administrar y mejorar eficazmente la postura de seguridad del entorno de nube mediante Microsoft Defender for Cloud. Los participantes podrán identificar y corregir riesgos de seguridad, mantener el cumplimiento y protegerse contra amenazas externas, garantizando la seguridad y resiliencia de los recursos en la nube de la organización.

---

# Implementar Microsoft Defender for Cloud

Microsoft Defender for Cloud es una plataforma de protección de aplicaciones nativa de la nube (CNAPP) con un conjunto de medidas y prácticas de seguridad diseñadas para proteger las aplicaciones basadas en la nube frente a diversas ciberamenazas y vulnerabilidades.

Defender for Cloud combina las capacidades de:

* Una solución de operaciones de seguridad de desarrollo (DevSecOps) que unifica la administración de la seguridad a nivel de código en entornos multinube y con múltiples canalizaciones.
* Una solución de administración de la postura de seguridad en la nube (CSPM) que muestra acciones que se pueden realizar para prevenir brechas de seguridad.
* Una plataforma de protección de cargas de trabajo en la nube (CWPP) con protecciones específicas para servidores, contenedores, almacenamiento, bases de datos y otras cargas de trabajo.

---

# Identificar y corregir riesgos de seguridad mediante Microsoft Defender for Cloud Secure Score e Inventory

## Descripción general de Secure Score

Microsoft Defender for Cloud tiene dos objetivos principales:

1. Ayudar a comprender la situación actual de seguridad.
2. Ayudar a mejorar la seguridad de manera eficiente y eficaz.

La característica central de Defender for Cloud que permite alcanzar estos objetivos es **Secure Score**.

Defender for Cloud evalúa continuamente los recursos de diferentes nubes en busca de problemas de seguridad. Luego, agrega todos los hallazgos en una única puntuación para mostrar, de un vistazo, la situación actual de seguridad. **Cuanto mayor sea la puntuación, menor será el nivel de riesgo identificado.**

* En las páginas del portal de Azure, Secure Score se muestra como un valor porcentual y también se presentan claramente los valores subyacentes.
* En la aplicación móvil de Azure, Secure Score se muestra como un valor porcentual y se puede seleccionar la puntuación para consultar los detalles que la explican.

Para aumentar la seguridad, se debe revisar la página de recomendaciones de Defender for Cloud y corregir las recomendaciones implementando las instrucciones de corrección correspondientes a cada problema.

Las recomendaciones se agrupan en **controles de seguridad**. Cada control es un grupo lógico de recomendaciones de seguridad relacionadas y refleja las superficies de ataque vulnerables.

La puntuación solo mejora cuando se **corrigen todas las recomendaciones para un único recurso dentro de un control**.

Para conocer qué tan bien la organización protege cada superficie de ataque individual, se deben revisar las puntuaciones de cada control de seguridad.

## Cómo se calcula Secure Score

Para obtener todos los puntos posibles de un control de seguridad, todos los recursos deben cumplir con todas las recomendaciones de seguridad incluidas en ese control.

Por ejemplo, Defender for Cloud tiene varias recomendaciones relacionadas con la protección de los puertos de administración. Es necesario corregirlas todas para que esto produzca una diferencia en Secure Score.

## Ejemplo de puntuaciones para un control

* **Control de seguridad de corrección de vulnerabilidades:** grupo de controles que contiene varias recomendaciones relacionadas con la detección y resolución de vulnerabilidades conocidas.
* **Puntuación máxima:** cantidad máxima de puntos que se pueden obtener al completar todas las recomendaciones de un control. La puntuación máxima de un control indica su importancia relativa y es fija para cada entorno. Los valores de puntuación máxima se pueden utilizar para priorizar los problemas que se deben abordar primero.
* **Puntuación actual:** puntuación actual del control.
  **Puntuación actual = [Puntuación por recurso] × [Número de recursos en buen estado]**.
* **Aumento potencial de puntuación:** puntos restantes disponibles. Si se corrigen todas las recomendaciones de este control, la puntuación aumentará.
  **Aumento potencial de puntuación = [Puntuación por recurso] × [Número de recursos en mal estado]**.
* **Insights:** proporciona detalles adicionales para cada recomendación:

  * **Preview recommendation:** la recomendación no afectará a Secure Score hasta que alcance disponibilidad general (GA).
  * **Fix:** permite resolver el problema desde la página de detalles de la recomendación.
  * **Enforce:** permite implementar automáticamente una política para corregir el problema cuando alguien crea un recurso no compatible.
  * **Deny:** permite impedir que se creen nuevos recursos que presenten ese problema.

## Recomendaciones incluidas en los cálculos de Secure Score

* Solo las recomendaciones integradas tienen impacto en Secure Score.
* Las recomendaciones marcadas como **Preview** no se incluyen en los cálculos de Secure Score.
* Las recomendaciones Preview deben corregirse siempre que sea posible para que, cuando finalice el período de Preview, contribuyan a la puntuación.

## Mejorar Secure Score

Para mejorar Secure Score, se deben corregir las recomendaciones de seguridad de la lista de recomendaciones.

Cada recomendación puede corregirse manualmente para cada recurso o se puede utilizar la opción **Fix**, cuando esté disponible, para resolver rápidamente un problema en varios recursos.

También se pueden configurar las opciones **Enforce** y **Deny** en las recomendaciones correspondientes para mejorar la puntuación y garantizar que los usuarios no creen recursos que afecten negativamente a Secure Score.

## Preguntas frecuentes sobre Secure Score

### Si se corrigen solo tres de cuatro recomendaciones de un control de seguridad, ¿cambiará Secure Score?

No. La puntuación no cambiará hasta que se corrijan todas las recomendaciones para un único recurso.

Para obtener la puntuación máxima de un control, se deben corregir todas las recomendaciones para todos los recursos.

### Si una recomendación no es aplicable y se deshabilita en la política, ¿se completará el control de seguridad y se actualizará Secure Score?

Sí. Se recomienda deshabilitar las recomendaciones cuando no sean aplicables al entorno.

### Si un control de seguridad ofrece cero puntos para Secure Score, ¿se debe ignorar?

En algunos casos, un control puede mostrar una puntuación máxima superior a cero, pero tener un impacto de cero. Cuando el incremento de puntuación derivado de corregir los recursos es insignificante, se redondea a cero.

Estas recomendaciones no deben ignorarse porque aún proporcionan mejoras de seguridad.

La única excepción es el control **Additional Best Practice**. Corregir estas recomendaciones no aumentará la puntuación, pero mejorará la seguridad general.

---

# Evaluar el cumplimiento de marcos de seguridad y Microsoft Defender for Cloud

Microsoft Defender for Cloud ayuda a simplificar el proceso para cumplir los requisitos de cumplimiento normativo mediante el **panel de cumplimiento normativo**.

Defender for Cloud evalúa continuamente el entorno de nube híbrida para analizar los factores de riesgo según los controles y las prácticas recomendadas de los estándares aplicados a las suscripciones. El panel refleja el estado de cumplimiento con estos estándares.

Al habilitar Defender for Cloud en una suscripción de Azure, el **Microsoft Cloud Security Benchmark** se asigna automáticamente a esa suscripción. Este benchmark se basa en los controles del **Center for Internet Security (CIS)**, **Payment Card Industry (PCI) Data Security Standards (DSS)** y **National Institute of Standards and Technology (NIST)**, con un enfoque en la seguridad centrada en la nube.

El panel de cumplimiento normativo muestra el estado de todas las evaluaciones del entorno para los estándares y regulaciones seleccionados. A medida que se aplican las recomendaciones y se reducen los factores de riesgo del entorno, mejora la postura de cumplimiento.

Los datos de cumplimiento de Defender for Cloud se integran con **Microsoft Purview Compliance Manager**, lo que permite evaluar y administrar de forma centralizada el cumplimiento en todo el entorno digital de la organización.

Cuando se agrega un estándar al panel de cumplimiento, incluidos los estándares que supervisan otras nubes como **AWS** y **GCP**, los datos de cumplimiento a nivel de recurso se muestran automáticamente en Compliance Manager para ese mismo estándar. Compliance Manager proporciona acciones de mejora y estado en toda la infraestructura en la nube y en los demás activos digitales desde esta herramienta central.

## Evaluar el cumplimiento normativo

El panel de cumplimiento normativo muestra los estándares de cumplimiento seleccionados junto con todos sus requisitos. Los requisitos compatibles se asignan a las evaluaciones de seguridad aplicables. El estado de estas evaluaciones refleja el cumplimiento con el estándar.

El panel permite centrar la atención en las brechas de cumplimiento de los estándares y regulaciones seleccionados. Esta vista también permite supervisar continuamente el cumplimiento a lo largo del tiempo en entornos dinámicos de nube e híbridos.

Para acceder al panel:

1. Iniciar sesión en el portal de Azure.
2. Ir a **Defender for Cloud** > **Regulatory compliance**.

El panel proporciona una descripción general del estado de cumplimiento y del conjunto de regulaciones de cumplimiento compatibles. Muestra la puntuación general de cumplimiento y la cantidad de evaluaciones aprobadas y fallidas asociadas a cada estándar.

## Investigar problemas de cumplimiento normativo

Pasos:

1. Iniciar sesión en el portal de Azure.
2. Ir a **Defender for Cloud** > **Regulatory compliance**.
3. Seleccionar un estándar de cumplimiento normativo.
4. Seleccionar un control de cumplimiento para expandirlo.
5. Seleccionar **Control details**.

Dentro de los detalles del control:

* **Overview:** muestra información específica sobre el control seleccionado.
* **Your Actions:** muestra las acciones automatizadas y manuales necesarias para mejorar la postura de cumplimiento.
* **Microsoft Actions:** muestra todas las acciones realizadas por Microsoft para garantizar el cumplimiento del estándar seleccionado.

En **Your Actions**, se puede seleccionar la flecha hacia abajo para consultar más detalles y resolver la recomendación para ese recurso.

Las evaluaciones se ejecutan aproximadamente cada **12 horas**, por lo que el impacto sobre los datos de cumplimiento se verá después de la siguiente ejecución de la evaluación correspondiente.

## Corregir una evaluación automatizada

El cumplimiento normativo incluye evaluaciones automatizadas y manuales que pueden requerir corrección.

Pasos:

1. Iniciar sesión en el portal de Azure.
2. Ir a **Defender for Cloud** > **Regulatory compliance**.
3. Seleccionar un estándar de cumplimiento normativo.
4. Seleccionar un control de cumplimiento para expandirlo.
5. Seleccionar una evaluación fallida para consultar los detalles de la recomendación.
6. Seleccionar un recurso específico para consultar más detalles y resolver la recomendación.
7. Seleccionar **Take action** desde la página de detalles de la recomendación para acceder a las páginas de la máquina virtual de Azure, donde se puede habilitar el cifrado desde la pestaña **Security**.
8. Después de resolver las recomendaciones, el resultado aparece en el informe del panel de cumplimiento y la puntuación de cumplimiento mejora.

## Corregir una evaluación manual

Las evaluaciones manuales requieren información proporcionada por el cliente para corregirlas.

Pasos:

1. Iniciar sesión en el portal de Azure.
2. Ir a **Defender for Cloud** > **Regulatory compliance**.
3. Seleccionar un estándar de cumplimiento normativo.
4. Seleccionar un control de cumplimiento para expandirlo.
5. En **Manual attestation and evidence**, seleccionar una evaluación.
6. Seleccionar las suscripciones correspondientes.
7. Seleccionar **Attest**.
8. Introducir la información correspondiente y adjuntar las evidencias de cumplimiento.
9. Seleccionar **Save**.

---

# Agregar iniciativas personalizadas a Microsoft Defender for Cloud

## ¿Qué son las políticas e iniciativas de seguridad?

Microsoft Defender for Cloud aplica iniciativas de seguridad a las suscripciones. Estas iniciativas contienen una o más políticas de seguridad. Cada una de estas políticas genera una recomendación de seguridad para mejorar la postura de seguridad.

## ¿Qué es una iniciativa de seguridad?

Una iniciativa de seguridad es una colección de definiciones o reglas de **Azure Policy** agrupadas hacia un objetivo o propósito específico.

Las iniciativas de seguridad simplifican la administración de las políticas al agrupar lógicamente un conjunto de políticas como un único elemento.

Una iniciativa de seguridad define la configuración deseada de las cargas de trabajo y ayuda a garantizar el cumplimiento de los requisitos de seguridad de la empresa o de los organismos reguladores.

Al igual que las políticas de seguridad, las iniciativas de Defender for Cloud también se crean en **Azure Policy**.

Azure Policy permite administrar políticas, crear iniciativas y asignarlas a varias suscripciones o a grupos de administración completos.

La iniciativa predeterminada asignada automáticamente a cada suscripción en Microsoft Defender for Cloud es **Microsoft Cloud Security Benchmark**.

Este benchmark es un conjunto de directrices de Microsoft para las prácticas recomendadas de seguridad y cumplimiento, basado en marcos de cumplimiento comunes.

Este benchmark se basa en los controles del **Center for Internet Security (CIS)** y del **National Institute of Standards and Technology (NIST)**, con un enfoque centrado en la seguridad de la nube.

## Opciones para trabajar con iniciativas y políticas de seguridad

Defender for Cloud ofrece las siguientes opciones:

* **Ver y editar la iniciativa predeterminada integrada:** cuando se habilita Defender for Cloud, la iniciativa denominada **Microsoft Cloud Security Benchmark** se asigna automáticamente a todas las suscripciones registradas en Defender for Cloud. Para personalizarla, se pueden habilitar o deshabilitar políticas individuales editando los parámetros de una política.
* **Agregar iniciativas personalizadas:** se pueden personalizar las iniciativas de seguridad aplicadas a una suscripción desde Defender for Cloud. Se recibirán recomendaciones si las máquinas no cumplen las políticas creadas.
* **Agregar estándares de cumplimiento normativo como iniciativas:** el panel de cumplimiento normativo de Defender for Cloud muestra el estado de todas las evaluaciones del entorno en el contexto de un estándar o regulación determinados, como **Azure Center for Internet Security (CIS)**, **National Institute of Standards and Technology (NIST) Special Publications (SP) SP 800-53 Rev.4** y **Swift’s Customer Security Program (CSP) Call Session Control Function (CSCF) v2020**.

## ¿Qué es una política de seguridad?

Una definición de **Azure Policy**, creada en Azure Policy, es una regla relacionada con condiciones de seguridad específicas que se desean controlar.

Las definiciones integradas incluyen reglas para controlar qué tipos de recursos pueden implementarse o exigir el uso de etiquetas en todos los recursos.

También se pueden crear definiciones de políticas personalizadas.

Para implementar estas definiciones de políticas, tanto integradas como personalizadas, es necesario asignarlas. Las políticas se pueden asignar mediante el portal de Azure, PowerShell o Azure CLI.

Las políticas se pueden deshabilitar o habilitar desde Azure Policy.

Existen diferentes tipos de políticas en Azure Policy. Defender for Cloud utiliza principalmente políticas **Audit**, que comprueban condiciones y configuraciones específicas y posteriormente informan sobre el cumplimiento.

También existen políticas **Enforce** que se pueden utilizar para aplicar configuraciones de seguridad.

## Control de acceso a las políticas

Defender for Cloud utiliza **Azure role-based access control (Azure RBAC)**, que proporciona roles integrados que se pueden asignar a usuarios, grupos y servicios de Azure.

Cuando los usuarios abren Defender for Cloud, solo ven información relacionada con los recursos a los que pueden acceder.

Los usuarios reciben los roles **owner**, **contributor** o **reader** para la suscripción del recurso.

Existen dos roles específicos para Defender for Cloud:

1. **Security Administrator:** tiene los mismos derechos de visualización que Security Reader. También puede actualizar la política de seguridad y descartar alertas.
2. **Security Reader:** puede visualizar elementos de Defender for Cloud, como recomendaciones, alertas, políticas y estado. No puede realizar cambios.

## Administración de políticas de seguridad

Las políticas de seguridad se pueden editar mediante el portal de Azure Policy, a través de una **Representational State Transfer Application Programming Interface (REST API)** o utilizando Windows PowerShell.

La pantalla **Security Policy** refleja las acciones realizadas por las políticas asignadas a la suscripción o al grupo de administración seleccionado.

* Los enlaces de la parte superior permiten abrir una asignación de política que se aplica a la suscripción o al grupo de administración. Estos enlaces permiten acceder a la asignación y editar o deshabilitar la política.
* En la lista de políticas se puede ver la aplicación efectiva de cada política en la suscripción o grupo de administración.
* Se tienen en cuenta las configuraciones de cada política que se aplican al ámbito y se muestra el resultado acumulativo de las acciones realizadas por las políticas.
* Si una asignación de una política está deshabilitada, pero otra está configurada como **AuditIfNotExist**, el efecto acumulativo será **AuditIfNotExist**. El efecto más activo siempre tiene prioridad.
* Los efectos de las políticas pueden ser: **Append**, **Audit**, **AuditIfNotExists**, **Deny**, **DeployIfNotExists** o **Disabled**.

---

# Conectar entornos de nube híbrida y multinube a Microsoft Defender for Cloud

Conectar entornos de nube híbrida y multinube a Microsoft Defender for Cloud es fundamental para mantener una postura de seguridad unificada en diferentes entornos de TI.

Mediante **Azure Arc-enabled servers** para máquinas que no son de Azure, el **Native Cloud Connector** y el **Classic Connector**, se pueden ampliar las capacidades de Microsoft Defender for Cloud a recursos que no son de Azure.

Esta integración permite supervisar, detectar y responder de forma integral a las amenazas de seguridad.

## Conectar máquinas que no son de Azure a Microsoft Defender for Cloud

Microsoft Defender for Cloud puede supervisar la postura de seguridad de las máquinas que no son de Azure, pero primero es necesario conectarlas a Azure.

Las computadoras que no son de Azure se pueden conectar mediante:

* **Incorporación con Azure Arc:**

  * Mediante **Azure Arc-enabled servers** (recomendado).
  * Mediante el portal de Azure.
* Incorporación directamente con **Microsoft Defender for Endpoint**.

## Conectar máquinas locales mediante Azure Arc

Una máquina que tiene **Azure Arc-enabled servers** se convierte en un recurso de Azure.

Cuando se instala el **Log Analytics agent** en ella, aparece en Defender for Cloud con recomendaciones, al igual que los demás recursos de Azure.

Azure Arc-enabled servers proporciona capacidades adicionales, como habilitar políticas de configuración de invitados en la máquina y simplificar la implementación con otros servicios de Azure.

Para implementar Azure Arc en varias máquinas a escala, se utilizan las instrucciones para conectar máquinas híbridas a Azure a escala.

Las herramientas de Defender for Cloud para implementar automáticamente el Log Analytics agent funcionan con máquinas que ejecutan Azure Arc. Sin embargo, esta capacidad se encuentra actualmente en **Preview**.

Al conectar las máquinas mediante Azure Arc, se puede utilizar la recomendación correspondiente de Defender for Cloud para implementar el agente:

* El **Log Analytics agent** debe estar instalado en las máquinas Azure Arc basadas en Linux.
* El **Log Analytics agent** debe estar instalado en las máquinas Azure Arc basadas en Windows.

# Conectar una cuenta de AWS a Microsoft Defender for Cloud

Las cargas de trabajo suelen abarcar varias plataformas de nube. Los servicios de seguridad en la nube deben hacer lo mismo.

Microsoft Defender for Cloud ayuda a proteger las cargas de trabajo de **Amazon Web Services (AWS)**, pero es necesario configurar la conexión entre AWS y Defender for Cloud.

Si se conecta una cuenta de AWS que anteriormente se conectó mediante el **classic connector**, primero se debe eliminar esa conexión.

Utilizar una cuenta de AWS conectada mediante los conectores clásico y nativo puede producir recomendaciones duplicadas.

## Requisitos previos

Para completar los procedimientos se necesita:

* Una suscripción de Microsoft Azure.
* Microsoft Defender for Cloud configurado en la suscripción de Azure.
* Acceso a una cuenta de AWS.
* Permiso **Contributor** para la suscripción de Azure correspondiente y permiso **Administrator** en la cuenta de AWS.

## Defender for Containers

Si se selecciona el plan **Microsoft Defender for Containers**, se necesita:

* Al menos un clúster de **Amazon EKS** con permisos para acceder al servidor de API de EKS Kubernetes.
* Capacidad para crear una nueva cola **Amazon Simple Queue Service (SQS)**, un flujo de entrega **Kinesis Data Firehose** y un bucket **Amazon S3** en la región del clúster.

## Defender for SQL

Si se selecciona el plan **Microsoft Defender for SQL**, se necesita:

* Microsoft Defender for SQL habilitado en la suscripción.
* Una cuenta de AWS activa, con instancias EC2 que ejecuten SQL Server o **Relational Database Service (RDS) Custom for SQL Server**.
* Azure Arc for servers instalado en las instancias EC2 o en RDS Custom for SQL Server.

Se recomienda utilizar el proceso de aprovisionamiento automático para instalar Azure Arc en todas las instancias EC2 existentes y futuras.

Para habilitar el aprovisionamiento automático de Azure Arc se necesita permiso **Owner** en la suscripción de Azure correspondiente.

**AWS Systems Manager (SSM)** administra el aprovisionamiento automático mediante el **SSM Agent**.

El SSM Agent debe tener la política administrada **AmazonSSMManagedInstanceCore**, que habilita la funcionalidad principal del servicio AWS Systems Manager.

También se deben habilitar las siguientes extensiones en las máquinas conectadas mediante Azure Arc:

* **Microsoft Defender for Endpoint**.
* Una solución de evaluación de vulnerabilidades (**Threat and Vulnerability Management** o **Qualys**).
* **Log Analytics agent** en las máquinas conectadas mediante Azure Arc o **Azure Monitor agent**.

El workspace de Log Analytics seleccionado debe tener una solución de seguridad instalada.

El Log Analytics agent y el Azure Monitor agent se configuran actualmente a nivel de suscripción. Todas las cuentas de AWS y los proyectos de Google Cloud Platform (GCP) bajo la misma suscripción heredan la configuración de suscripción correspondiente para estos agentes.

## Defender for Servers

Si se selecciona el plan **Microsoft Defender for Servers**, se necesita:

* Microsoft Defender for Servers habilitado en la suscripción.
* Una cuenta de AWS activa con instancias EC2.
* Azure Arc for servers instalado en las instancias EC2.

Se recomienda utilizar el proceso de aprovisionamiento automático para instalar Azure Arc en todas las instancias EC2 existentes y futuras.

Para habilitar el aprovisionamiento automático de Azure Arc se necesita permiso **Owner** en la suscripción de Azure correspondiente.

AWS Systems Manager administra el aprovisionamiento automático mediante el **SSM Agent**.

El SSM Agent debe tener la política administrada **AmazonSSMManagedInstanceCore**, que habilita la funcionalidad principal del servicio AWS Systems Manager.

Si se desea instalar manualmente Azure Arc en las instancias EC2 existentes y futuras, se puede utilizar la recomendación **EC2 instances should be connected to Azure Arc** para identificar las instancias que no tienen Azure Arc instalado.

También se deben habilitar las siguientes extensiones en las máquinas conectadas mediante Azure Arc:

* **Microsoft Defender for Endpoint**.
* Una solución de evaluación de vulnerabilidades (**Threat and Vulnerability Management** o **Qualys**).
* **Log Analytics agent** en las máquinas conectadas mediante Azure Arc o **Azure Monitor agent**.

El workspace de Log Analytics seleccionado debe tener una solución de seguridad instalada.

El Log Analytics agent y el Azure Monitor agent se configuran actualmente a nivel de suscripción. Todas las cuentas de AWS y los proyectos de GCP bajo la misma suscripción heredan la configuración de suscripción correspondiente para estos agentes.

Defender for Servers asigna etiquetas a los recursos de AWS para administrar el proceso de aprovisionamiento automático. Los recursos deben tener correctamente asignadas las siguientes etiquetas:

* `AccountId`
* `Cloud`
* `InstanceId`
* `MDFCSecurityConnector`

## Defender CSPM

Si se selecciona el plan **Microsoft Defender Cloud Security Posture Management (CSPM)**, se necesita:

* Una suscripción de Azure.
* Microsoft Defender for Cloud habilitado en la suscripción de Azure.
* Conectar las máquinas que no son de Azure y las cuentas de AWS.
* Para acceder a todas las características disponibles del plan CSPM, el plan debe estar habilitado por el **Subscription Owner**.

---

# Implementar y usar Microsoft Defender External Attack Surface Management

**Microsoft Defender External Attack Surface Management (Defender EASM)** descubre y asigna continuamente la superficie de ataque digital para proporcionar una vista externa de la infraestructura en línea.

Esta visibilidad permite a los equipos de seguridad y TI:

* Identificar elementos desconocidos.
* Priorizar riesgos.
* Eliminar amenazas.
* Extender el control de vulnerabilidades y exposición más allá del firewall.

Los **Attack Surface Insights** se generan utilizando datos de vulnerabilidades e infraestructura para mostrar las principales áreas de preocupación para la organización.

## Descubrimiento e inventario

La tecnología de descubrimiento propietaria de Microsoft busca recursivamente infraestructura con conexiones observadas a activos legítimos conocidos.

A partir de estas conexiones, realiza inferencias sobre la relación de esa infraestructura con la organización y descubre propiedades que anteriormente eran desconocidas y no supervisadas.

Estos activos legítimos conocidos se denominan **discovery seeds**.

Defender EASM primero descubre conexiones fuertes con estas entidades seleccionadas, continúa recursivamente para descubrir más conexiones y finalmente compila la superficie de ataque.

Defender EASM incluye el descubrimiento de los siguientes tipos de activos:

* Dominios.
* Nombres de host.
* Páginas web.
* Bloques IP.
* Direcciones IP.
* ASN.
* Certificados SSL.
* Contactos WHOIS.

## Paneles

Defender EASM proporciona una serie de paneles que ayudan a comprender rápidamente la infraestructura en línea y los principales riesgos para la organización.

Estos paneles proporcionan información sobre áreas específicas de riesgo, entre ellas:

* Vulnerabilidades.
* Cumplimiento.
* Higiene de seguridad.

Estos insights ayudan a abordar rápidamente los componentes de la superficie de ataque que presentan el mayor riesgo para la organización.

## Administración de activos

Los clientes pueden filtrar su inventario para mostrar los insights específicos que más les interesan.

El filtrado ofrece flexibilidad y personalización para acceder a un subconjunto específico de activos.

Esto permite utilizar los datos de Defender EASM según el caso de uso específico, ya sea para buscar activos que se conectan a infraestructura en proceso de obsolescencia o para identificar nuevos recursos en la nube.

## Permisos de usuario

Los usuarios que tienen asignados los roles **Owner** o **Contributor** pueden crear, eliminar y editar recursos de Defender EASM y los activos del inventario que contiene.

Estos roles pueden utilizar todas las capacidades ofrecidas por la plataforma.

Los usuarios con el rol **Reader** pueden consultar los datos de Defender EASM, pero no pueden crear, eliminar ni editar activos del inventario ni el propio recurso.

## Residencia, disponibilidad y privacidad de los datos

Microsoft Defender External Attack Surface Management contiene datos globales y datos específicos del cliente.

Los datos subyacentes de Internet son datos globales de Microsoft, mientras que las etiquetas aplicadas por los clientes se consideran datos del cliente.

Todos los datos del cliente se almacenan en la región elegida por el cliente.

Por motivos de seguridad, Microsoft recopila las direcciones IP de los usuarios cuando inician sesión. Estos datos se almacenan durante un máximo de **30 días**, aunque pueden conservarse durante más tiempo si es necesario investigar posibles usos fraudulentos o maliciosos del producto.

En caso de una interrupción regional, solo los clientes de la región afectada experimentarán tiempo de inactividad.

El marco de cumplimiento de Microsoft requiere que todos los datos de los clientes se eliminen dentro de los **180 días** posteriores a que la organización deje de ser cliente de Microsoft. Esto también incluye el almacenamiento de datos de clientes en ubicaciones sin conexión, como copias de seguridad de bases de datos.

Una vez eliminado un recurso, los equipos de Microsoft no pueden restaurarlo.

Los datos del cliente se conservarán en los almacenes de datos durante **75 días**, aunque el recurso real no podrá restaurarse.

Después de los 75 días, los datos del cliente se eliminarán permanentemente.
