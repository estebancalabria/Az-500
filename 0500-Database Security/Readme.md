# Seguridad de Azure SQL Database y SQL Managed Instance

## Introducción

Este módulo está diseñado para proporcionar a los administradores los conocimientos y habilidades necesarios para planificar e implementar medidas de seguridad sólidas para Azure SQL Database y Azure SQL Managed Instance, garantizando la protección de los datos y el cumplimiento normativo.

## Objetivos de aprendizaje

Al finalizar este módulo, los participantes podrán:

* Planificar e implementar medidas de seguridad para Azure SQL Database y Azure SQL Managed Instance para proteger datos confidenciales.
* Habilitar la autenticación de bases de datos mediante Microsoft Entra ID para un control de acceso seguro.
* Implementar auditoría de bases de datos para realizar un seguimiento y monitoreo de las actividades de las bases de datos para fines de cumplimiento y seguridad.
* Identificar casos de uso del portal de gobernanza de Microsoft Purview y aprovechar sus capacidades para la gobernanza de datos.
* Implementar la clasificación de datos confidenciales mediante el portal de gobernanza de Microsoft Purview para garantizar la protección de datos y el cumplimiento.
* Planificar e implementar el enmascaramiento dinámico para controlar la exposición de datos confidenciales en los resultados de las consultas.
* Implementar Transparent Database Encryption (TDE) para cifrar datos en reposo y mejorar la seguridad.
* Proporcionar recomendaciones sobre cuándo utilizar Azure SQL Database Always Encrypted para el cifrado de datos del lado del cliente.

---

# Seguridad de Azure SQL Database y SQL Managed Instance

Esta unidad presenta los conceptos básicos para proteger la capa de datos de una aplicación utilizando Azure SQL Database, Azure SQL Managed Instance y Azure Synapse Analytics. La estrategia de seguridad descrita emplea un enfoque por capas de defensa en profundidad, avanzando desde el exterior hacia el interior.

Las capas son:

* **Seguridad de red**
* **Administración de acceso**
* **Protección contra amenazas**
* **Protección de la información y cifrado**

---

# Seguridad de red

Azure SQL Database, Managed Instance y Synapse Analytics usan firewalls que bloquean el acceso hasta que se autoriza explícitamente por IP o por red virtual.

* **Reglas de firewall IP**: autorizan según la IP de origen de cada solicitud.
* **Reglas de firewall de red virtual**: usan puntos de conexión de servicio para que Azure SQL identifique la subred de origen. Las etiquetas de servicio SQL permiten el tráfico saliente vía NSG, y estas reglas hacen que el servicio solo acepte tráfico de las subredes autorizadas.

---

# Administración de acceso

La administración de bases de datos y servidores dentro de Azure está controlada por las asignaciones de roles de la cuenta de usuario del portal.

## Autenticación

* **SQL Database y SQL Managed Instance** admiten autenticación SQL y autenticación con Microsoft Entra ID. Managed Instance suma autenticación de Windows para entidades de Entra.
* **Autenticación SQL**: usuario/contraseña. Al crear el servidor se define un login de administrador del servidor, que puede autenticarse como propietario en cualquier base de ese servidor/instancia y luego crear logins y usuarios SQL adicionales.
* **Autenticación de Microsoft Entra**: conecta a SQL Database, Managed Instance y Synapse usando identidades de Entra ID, con gestión centralizada de identidades/permisos, menos contraseñas almacenadas y rotación centralizada. Requiere crear un administrador de Microsoft Entra en el servidor. Admite cuentas administradas y federadas (estas últimas con usuarios/grupos de Windows de un dominio federado), y opciones como MFA, autenticación integrada de Windows y acceso condicional.
* **Autenticación de Windows para entidades de Microsoft Entra**: vía Kerberos, disponible solo en Managed Instance. Permite migrar servicios existentes manteniendo la experiencia de usuario. Requiere convertir el tenant en dominio Kerberos independiente y crear una confianza entrante con el dominio del cliente.

## Autorización

La autorización se refiere al control del acceso a recursos y comandos dentro de una base de datos. Se realiza asignando permisos a un usuario dentro de una base de datos en Azure SQL Database o Azure SQL Managed Instance.

Los permisos se administran idealmente agregando cuentas de usuario a roles de base de datos y asignando permisos a nivel de base de datos a esos roles. Como alternativa, se pueden conceder permisos específicos a nivel de objeto a un usuario individual.

Como práctica recomendada:

* Crear roles personalizados cuando sea necesario.
* Agregar usuarios al rol con los privilegios mínimos necesarios para realizar su función.
* No asignar permisos directamente a los usuarios.
* La cuenta de administrador del servidor es miembro del rol integrado `db_owner`, que tiene permisos amplios y debería asignarse únicamente a pocos usuarios con funciones administrativas.
* Para limitar aún más lo que un usuario puede hacer, se puede utilizar `EXECUTE AS` para especificar el contexto de ejecución del módulo llamado.

## Seguridad a nivel de fila

Row-Level Security permite controlar el acceso a las filas de una tabla de base de datos según las características del usuario que ejecuta una consulta, por ejemplo, pertenencia a grupos o contexto de ejecución.

También puede utilizarse para implementar conceptos personalizados de seguridad basados en etiquetas.

---

# Protección contra amenazas

Azure SQL Database y SQL Managed Instance protegen los datos de los clientes proporcionando capacidades de auditoría y detección de amenazas.

## Auditoría SQL en registros de Azure Monitor y Event Hubs

La auditoría de SQL Database y SQL Managed Instance realiza un seguimiento de las actividades de las bases de datos y ayuda a mantener el cumplimiento de los estándares de seguridad mediante el registro de eventos de la base de datos en un registro de auditoría almacenado en una cuenta de Azure Storage propiedad del cliente.

La auditoría permite:

* Monitorear las actividades actuales de las bases de datos.
* Analizar e investigar actividades históricas.
* Identificar posibles amenazas.
* Identificar posibles abusos y violaciones de seguridad.

## Advanced Threat Protection

Advanced Threat Protection analiza los registros para detectar comportamientos inusuales e intentos potencialmente dañinos de acceder o explotar bases de datos.

Genera alertas para actividades sospechosas como:

* Inyección SQL.
* Posible infiltración de datos.
* Ataques de fuerza bruta.
* Anomalías en los patrones de acceso.
* Posibles escalaciones de privilegios.
* Uso de credenciales comprometidas.

Las alertas se visualizan desde Microsoft Defender for Cloud, donde se proporcionan detalles de las actividades sospechosas, recomendaciones para una investigación posterior y acciones para mitigar la amenaza.

Advanced Threat Protection puede habilitarse por servidor con un coste adicional.

---

## Habilitar y monitorear la auditoría de bases de datos

La auditoría de Azure SQL Database y Azure Synapse Analytics realiza un seguimiento de los eventos de las bases de datos y los escribe en un registro de auditoría ubicado en:

* Una cuenta de Azure Storage.
* Un workspace de Log Analytics.
* Event Hubs.

La auditoría también:

* Ayuda a mantener el cumplimiento normativo.
* Permite comprender la actividad de las bases de datos.
* Permite obtener información sobre discrepancias y anomalías que podrían indicar problemas empresariales o posibles violaciones de seguridad.
* Facilita el cumplimiento de estándares, aunque no garantiza el cumplimiento.

### Uso de la auditoría SQL

La auditoría de bases de datos permite:

* Conservar un registro de auditoría de eventos seleccionados.
* Definir categorías de acciones de base de datos que deben auditarse.
* Generar informes sobre la actividad de la base de datos.
* Utilizar informes preconfigurados y un dashboard para comenzar rápidamente con los informes de actividad y eventos.
* Analizar informes para encontrar eventos sospechosos, actividad inusual y tendencias.

La auditoría de Azure SQL Database, Azure Synapse Analytics SQL pools y Azure SQL Managed Instance está optimizada para la disponibilidad y el rendimiento de la base de datos o instancia auditada. Durante períodos de actividad muy alta o elevada carga de red, la auditoría puede permitir que las transacciones continúen sin registrar todos los eventos marcados para auditoría.

### Limitaciones de la auditoría

* No se admite habilitar la auditoría en un Azure Synapse SQL pool pausado. Para habilitarla, se debe reanudar el SQL pool.
* No se admite habilitar la auditoría mediante User Assigned Managed Identity (UAMI) en Azure Synapse.
* La auditoría para Azure Synapse SQL pools admite únicamente los grupos de acciones de auditoría predeterminados.
* Al configurar la auditoría para un servidor lógico en Azure o Azure SQL Database con el destino de registros en una cuenta de almacenamiento, el modo de autenticación debe coincidir con la configuración de esa cuenta de almacenamiento.
* Si se utilizan claves de acceso de almacenamiento como tipo de autenticación, la cuenta de almacenamiento de destino debe tener habilitado el acceso mediante sus claves.
* Si la cuenta de almacenamiento está configurada para utilizar únicamente autenticación mediante Microsoft Entra ID, la auditoría puede configurarse para utilizar identidades administradas para la autenticación.

### Consideraciones

* Se admite Premium Storage con BlockBlobStorage.
* Se admite Standard Storage.
* Para escribir auditorías en una cuenta de almacenamiento detrás de una VNet o firewall, se requiere una cuenta de almacenamiento general-purpose v2.
* Las cuentas general-purpose v1 o Blob Storage deben actualizarse a general-purpose v2 para este escenario.
* Se admite el espacio de nombres jerárquico para todos los tipos de cuentas de almacenamiento estándar y para cuentas Premium Storage con BlockBlobStorage.
* Los registros de auditoría se escriben en Append Blobs en Azure Blob Storage dentro de la suscripción de Azure.
* Los registros de auditoría utilizan el formato `.xel` y pueden abrirse con SQL Server Management Studio (SSMS).
* Para configurar un almacén de registros inmutable para eventos de auditoría a nivel de servidor o base de datos, se deben seguir las instrucciones proporcionadas por Azure Storage y seleccionar **Allow additional appends** al configurar el almacenamiento de blobs inmutable.
* Los registros de auditoría pueden escribirse en una cuenta de Azure Storage detrás de una VNet o firewall.
* Las réplicas de solo lectura tienen la auditoría habilitada automáticamente.
* Cuando se utiliza autenticación de Microsoft Entra, los inicios de sesión fallidos no aparecen en el registro de auditoría SQL. Para consultar estos registros se debe utilizar el centro de administración de Microsoft Entra, donde se registran los detalles de estos eventos.
* Los inicios de sesión son dirigidos por el gateway a la instancia específica donde se encuentra la base de datos.
* Con los inicios de sesión de Microsoft Entra, las credenciales se verifican antes de intentar iniciar sesión en la base de datos solicitada. Si la autenticación falla, la base de datos solicitada nunca se accede y, por lo tanto, no se genera auditoría.
* Con los inicios de sesión SQL, las credenciales se verifican en los datos solicitados y pueden ser auditadas.
* Los inicios de sesión exitosos, que llegan a la base de datos, se auditan en ambos casos.
* Después de configurar la auditoría, se puede activar la función de detección de amenazas y configurar correos electrónicos para recibir alertas de seguridad.
* La detección de amenazas proporciona alertas proactivas sobre actividades anómalas de las bases de datos que pueden indicar posibles amenazas de seguridad.
* Después de copiar una base de datos con auditoría habilitada a otro servidor lógico, puede recibirse un correo electrónico notificando que la auditoría falló. Es un problema conocido y la auditoría debería funcionar correctamente en la nueva base de datos.

---

# Protección de la información y cifrado

## Transport Layer Security — Cifrado en tránsito

SQL Database, SQL Managed Instance y Azure Synapse Analytics protegen los datos en movimiento mediante Transport Layer Security (TLS).

SQL Database, SQL Managed Instance y Azure Synapse Analytics aplican cifrado (SSL/TLS) en todo momento para todas las conexiones. Esto garantiza que todos los datos estén cifrados **en tránsito** entre el cliente y el servidor independientemente de la configuración de `Encrypt` o `TrustServerCertificate` en la cadena de conexión.

Como práctica recomendada, en la cadena de conexión utilizada por la aplicación se debe especificar una conexión cifrada y **no confiar en el certificado del servidor**. Esto obliga a la aplicación a verificar el certificado del servidor y evita que la aplicación sea vulnerable a ataques de tipo man-in-the-middle.

Con el controlador ADO.NET esto se consigue mediante:

```text
Encrypt=True
TrustServerCertificate=False
```

Si la cadena de conexión se obtiene desde Azure Portal, tendrá la configuración correcta.

Algunos controladores que no son de Microsoft pueden no utilizar TLS de forma predeterminada o depender de versiones antiguas de TLS (<1.2). Aunque el servidor permite la conexión, se recomienda evaluar los riesgos de seguridad de permitir que estos controladores y aplicaciones se conecten a SQL Database, especialmente cuando se almacenan datos confidenciales.

## Transparent Data Encryption — Cifrado en reposo

Transparent Data Encryption (TDE) para SQL Database, SQL Managed Instance y Azure Synapse Analytics añade una capa de seguridad para proteger los datos en reposo frente a accesos no autorizados o sin conexión a archivos sin procesar o copias de seguridad.

Escenarios habituales:

* Robo de un centro de datos.
* Eliminación no segura de hardware o medios.
* Discos y cintas de backup.

TDE cifra toda la base de datos mediante un algoritmo de cifrado AES y no requiere cambios en las aplicaciones existentes.

En Azure, todas las bases de datos nuevas se cifran de forma predeterminada y la clave de cifrado de la base de datos está protegida mediante un certificado de servidor integrado.

El mantenimiento y la rotación del certificado son administrados por el servicio y no requieren intervención del usuario.

Los clientes que prefieran controlar las claves de cifrado pueden administrarlas en Azure Key Vault.

## Administración de claves con Azure Key Vault

El soporte Bring Your Own Key (BYOK) para Transparent Data Encryption (TDE) permite que los clientes tomen el control de la administración y rotación de claves mediante Azure Key Vault.

Si se revoca el acceso de la base de datos al Key Vault, la base de datos no puede descifrarse ni leerse en memoria.

Azure Key Vault proporciona:

* Una plataforma centralizada de administración de claves.
* Módulos de seguridad de hardware (HSM) estrechamente supervisados.
* Separación de responsabilidades entre la administración de claves y los datos.
* Capacidades para ayudar a cumplir requisitos de seguridad y cumplimiento.

## Always Encrypted — Cifrado en uso

Always Encrypted está diseñado para proteger datos confidenciales almacenados en columnas específicas de una base de datos frente al acceso, incluidos administradores de bases de datos u otros usuarios privilegiados que pueden administrar la base de datos pero no necesitan acceder a determinados datos cifrados.

Ejemplos de datos:

* Números de tarjetas de crédito.
* Números de identificación nacionales/regionales.
* Datos cuyo acceso se limita a quienes tienen necesidad de conocerlos.

Los datos permanecen cifrados y solo se descifran para su procesamiento por aplicaciones cliente que tienen acceso a la clave de cifrado.

La clave de cifrado nunca se expone a SQL Database o SQL Managed Instance y puede almacenarse:

* En Windows Certificate Store.
* En Azure Key Vault.

## Dynamic Data Masking

Dynamic Data Masking limita la exposición de datos confidenciales mediante su enmascaramiento para usuarios sin privilegios.

Puede descubrir automáticamente datos potencialmente confidenciales en Azure SQL Database y Azure SQL Managed Instance y proporcionar recomendaciones para enmascarar estos campos, con un impacto mínimo en la capa de aplicación.

Funciona ofuscando los datos confidenciales en el conjunto de resultados de una consulta sobre campos de base de datos designados, sin modificar los datos almacenados en la base de datos.

---

# Gestión de seguridad

## Evaluación de vulnerabilidades

Vulnerability Assessment es un servicio fácil de configurar que puede descubrir, realizar un seguimiento y ayudar a corregir posibles vulnerabilidades de bases de datos, con el objetivo de mejorar proactivamente la seguridad general de las bases de datos.

Vulnerability Assessment forma parte de Microsoft Defender for SQL, un paquete unificado de capacidades avanzadas de seguridad SQL.

La evaluación de vulnerabilidades puede administrarse desde el portal central de Microsoft Defender for SQL.

## Descubrimiento y clasificación de datos

Data discovery and classification proporciona capacidades integradas en Azure SQL Database y SQL Managed Instance para descubrir, clasificar y etiquetar datos confidenciales en las bases de datos.

Descubrir y clasificar datos especialmente confidenciales, como datos empresariales/financieros, sanitarios y personales, puede desempeñar un papel importante en la protección de la información de una organización.

Puede servir como infraestructura para:

* Escenarios de seguridad, como monitoreo mediante auditoría y alertas sobre accesos anómalos a datos confidenciales.
* Controlar el acceso y reforzar la seguridad de bases de datos que contienen información altamente confidencial.
* Ayudar a cumplir estándares de privacidad de datos y requisitos de cumplimiento normativo.

---

