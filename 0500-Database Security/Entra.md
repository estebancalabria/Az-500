# Habilitar la autenticación de bases de datos con Microsoft Entra

Esta unidad muestra cómo utilizar Microsoft Entra ID para la autenticación con Azure SQL Database, Azure SQL Managed Instance y Azure Synapse Analytics.

Microsoft Entra ID se conocía anteriormente como Azure Active Directory (Azure AD).

## Prerrequisitos

Para utilizar la autenticación de Microsoft Entra con un recurso de Azure SQL se necesita:

* Un tenant de Microsoft Entra con usuarios y grupos.
* Un recurso de Azure SQL existente, como Azure SQL Database o Azure SQL Managed Instance.

## Establecer el administrador de Microsoft Entra

Para utilizar la autenticación de Microsoft Entra con el recurso, debe estar configurado el administrador de Microsoft Entra.

Para Azure SQL Database y Azure Synapse Analytics, establecer el administrador de Microsoft Entra habilita la autenticación de Microsoft Entra para el servidor lógico.

El administrador puede configurarse mediante:

* Azure Portal.
* PowerShell.
* Azure CLI.
* REST APIs.

El administrador de Microsoft Entra puede configurarse también durante la creación del recurso de Azure SQL.

Para Azure SQL Managed Instance también se puede establecer el administrador mediante:

* Azure Portal.
* PowerShell.
* Azure CLI.
* REST APIs.

El administrador puede eliminarse mediante **Remove admin**. Al eliminarlo, se deshabilita la autenticación de Microsoft Entra para el servidor lógico.

El administrador de Microsoft Entra se almacena en la base de datos master del servidor como un principal de base de datos. Como los nombres de los principales deben ser únicos, el nombre para mostrar del administrador no puede ser igual al nombre de otro usuario de la base de datos master.

## Permisos de Microsoft Graph

SQL Managed Instance necesita permisos para leer Microsoft Entra ID en escenarios como:

* Autorizar usuarios que se conectan mediante pertenencia a grupos de seguridad.
* Crear nuevos usuarios.

Para que funcione la autenticación de Microsoft Entra, la identidad de la instancia administrada debe tener asignado el rol **Directory Readers**.

Azure SQL Database y Azure Synapse Analytics también requieren permisos para consultar Microsoft Graph en determinadas operaciones.

Azure SQL Database y Azure Synapse Analytics admiten permisos de Graph específicos para estos escenarios, mientras que SQL Managed Instance requiere el rol Directory Readers.

### Rol Directory Readers

La página de Microsoft Entra ID de SQL Managed Instance en Azure Portal muestra un banner cuando la instancia no tiene asignados permisos de Directory Readers.

1. Seleccionar el banner situado en la parte superior de la página Microsoft Entra ID.
2. Conceder permiso a la identidad administrada asignada por el sistema o por el usuario que representa a la instancia.
3. Solo un **Privileged Role Administrator** o un rol superior del tenant puede realizar esta operación.

Una vez completada correctamente la operación, el administrador de Microsoft Entra puede utilizarse para crear principales de servidor (logins) y principales de base de datos (usuarios).

## Crear principales de Microsoft Entra en SQL

Para conectarse a una base de datos de SQL Database o Azure Synapse Analytics mediante autenticación de Microsoft Entra, debe configurarse un principal para esa identidad en la base de datos con al menos el permiso `CONNECT`.

### Permisos de usuarios de base de datos

Cuando se crea un usuario de base de datos, recibe el permiso `CONNECT` a la base de datos de forma predeterminada.

Un usuario de base de datos también hereda permisos en dos circunstancias:

* Si el usuario pertenece a un grupo de Microsoft Entra que tiene permisos asignados en el servidor.
* Si el usuario se crea a partir de un login, hereda los permisos asignados al servidor que correspondan al login en la base de datos.

La administración de permisos para principales de servidor y de base de datos funciona de la misma forma independientemente del tipo de principal.

Se recomienda conceder permisos a roles de base de datos en lugar de concederlos directamente a los usuarios. Los usuarios pueden agregarse a roles con los permisos adecuados, simplificando la administración a largo plazo y reduciendo la posibilidad de que una identidad conserve acceso cuando ya no corresponde.

## Usuarios de base de datos contenidos

Un usuario de base de datos contenido es un tipo de usuario SQL que no está conectado a un login de la base de datos master.

Para crear un usuario de base de datos contenido de Microsoft Entra, se debe conectar a la base de datos con una identidad de Microsoft Entra que tenga al menos el permiso `ALTER ANY USER`.

Ejemplos:

```sql
CREATE USER [<Microsoft_Entra_principal_name>] FROM EXTERNAL PROVIDER;
```

```sql
CREATE USER [ICU Nurses] FROM EXTERNAL PROVIDER;
```

```sql
CREATE USER [appName] FROM EXTERNAL PROVIDER;
```

```sql
CREATE USER [adrian@contoso.com] FROM EXTERNAL PROVIDER;
```

## Usuarios basados en login

Los principales de servidor de Microsoft Entra (logins) están actualmente en versión preliminar pública para Azure SQL Database y Azure Synapse Analytics.

Los logins de Microsoft Entra están disponibles de forma general para Azure SQL Managed Instance y SQL Server 2022.

Los principales de servidor o logins de Microsoft Entra permiten crear principales de base de datos a partir de un principal de servidor, por lo que los usuarios de Microsoft Entra pueden heredar los permisos asignados a nivel de servidor de un login.

```sql
CREATE USER [appName] FROM LOGIN [appName];
```

## Usuarios externos

No se puede crear directamente un usuario de base de datos para una identidad administrada en un tenant de Microsoft Entra diferente al asociado a la suscripción de Azure.

Sin embargo, los usuarios de otros directorios pueden importarse en el directorio asociado como usuarios externos. Posteriormente pueden utilizarse para crear usuarios de base de datos contenidos que accedan a la base de datos.

Los usuarios externos también pueden obtener acceso mediante su pertenencia a grupos de Microsoft Entra.

Ejemplo:

```sql
CREATE USER [alice@fabrikam.com] FROM EXTERNAL PROVIDER;
```

Un usuario de dominio federado importado en un dominio administrado como usuario externo debe utilizar la identidad del dominio administrado.

## Consideraciones sobre nombres

Los caracteres especiales como `:` o `&` incluidos en los nombres de usuario de las instrucciones T-SQL `CREATE LOGIN` y `CREATE USER` no son compatibles.

Microsoft Entra ID y Azure SQL difieren en un aspecto del diseño de administración de usuarios:

* Microsoft Entra ID permite nombres para mostrar duplicados dentro de un tenant.
* Azure SQL requiere que todos los principales de servidor de un servidor o instancia y todos los principales de base de datos de una base de datos tengan un nombre único.

Azure SQL utiliza directamente el nombre para mostrar de la identidad de Microsoft Entra al crear los principales, lo que puede generar errores cuando se crean usuarios.

La mejora `WITH OBJECT_ID`, actualmente en versión preliminar, permite especificar el ID de objeto de Microsoft Entra de la identidad que se agrega al servidor o instancia.

## Permisos de Microsoft Graph

El comando:

```sql
CREATE USER ... FROM EXTERNAL PROVIDER
```

requiere que Azure SQL tenga acceso a Microsoft Entra ID, el proveedor externo, en nombre del usuario conectado.

Puede producirse el error SQL 33134, que contiene un mensaje específico de Microsoft Entra ID. El error puede indicar:

* Acceso denegado.
* El usuario debe inscribirse en MFA.
* El acceso entre aplicaciones de primera parte debe gestionarse mediante preautorización.

En los dos primeros casos, el problema suele estar causado por políticas de acceso condicional del tenant de Microsoft Entra que impiden al usuario acceder al proveedor externo.

Actualizar las políticas de acceso condicional para permitir el acceso a la aplicación `00000003-0000-0000-c000-000000000000`, correspondiente a Microsoft Graph API, debería resolver el problema.

Si el error indica que el acceso entre aplicaciones de primera parte debe gestionarse mediante preautorización, el problema se debe a que el usuario ha iniciado sesión como service principal. El comando debería funcionar si lo ejecuta un usuario.

Si aparece **Connection Timeout Expired**, puede ser necesario establecer el parámetro `TransparentNetworkIPResolution` de la cadena de conexión en `false`.

## Configurar autenticación multifactor

Para mejorar la seguridad del recurso de Azure SQL se puede configurar autenticación multifactor (MFA).

MFA solicita al usuario un segundo método alternativo para autenticarse en la base de datos, como una llamada telefónica o una aplicación autenticadora.

Para utilizar MFA:

1. Habilitar MFA.
2. Utilizar una política de acceso condicional para exigir MFA para el recurso de Azure SQL.

## Conectarse con Microsoft Entra

Después de configurar la autenticación de Microsoft Entra, puede utilizarse para conectarse al recurso SQL mediante herramientas de Microsoft como:

* SQL Server Management Studio (SSMS).
* SQL Server Data Tools (SSDT).
* Aplicaciones cliente configuradas para conectarse mediante identidades de Microsoft Entra.

## Solucionar problemas de autenticación de Microsoft Entra

Existe orientación específica para solucionar problemas relacionados con la autenticación de Microsoft Entra con Azure SQL Database y Azure Synapse.

---

# Portal de gobernanza de Microsoft Purview

Las soluciones de Microsoft Purview en el portal de gobernanza proporcionan un servicio unificado de gobernanza de datos que permite administrar datos locales, multicloud y de software como servicio (SaaS).

El portal de gobernanza de Microsoft Purview permite:

* Crear un mapa completo y actualizado del panorama de datos mediante descubrimiento automatizado de datos, clasificación de datos confidenciales y linaje de datos de extremo a extremo.
* Permitir que los curadores de datos y administradores de seguridad administren y mantengan seguro el patrimonio de datos.
* Permitir que los consumidores de datos encuentren datos valiosos y confiables.

## Prerrequisitos

* Debe existir una cuenta activa de Microsoft Purview creada en Azure Portal.
* El usuario debe tener permisos para acceder al portal de gobernanza de Microsoft Purview.

## Data Map

Microsoft Purview automatiza el descubrimiento de datos proporcionando escaneo y clasificación de datos para los activos del patrimonio de datos.

Los metadatos y descripciones de los activos descubiertos se integran en un mapa completo del patrimonio de datos.

Microsoft Purview Data Map proporciona la base para el descubrimiento y la gobernanza de datos.

Data Map es un servicio PaaS nativo de la nube que captura metadatos sobre datos empresariales presentes en sistemas analíticos y operativos locales y en la nube.

Data Map se mantiene actualizado automáticamente mediante sistemas integrados de escaneo y clasificación automatizados.

Los usuarios empresariales pueden configurar y utilizar Data Map mediante una interfaz de usuario, mientras que los desarrolladores pueden interactuar mediante programación utilizando las API de código abierto Apache Atlas 2.2.

Microsoft Purview Data Map proporciona la base para:

* Microsoft Purview Data Catalog.
* Microsoft Purview Data Estate Insights.
* Microsoft Purview Data Policy.

## Data Catalog app

Microsoft Purview Data Catalog permite a usuarios empresariales y técnicos encontrar rápidamente datos relevantes mediante una experiencia de búsqueda con filtros basados en:

* Términos del glosario.
* Clasificaciones.
* Etiquetas de sensibilidad.
* Otros criterios.

Para expertos en la materia, administradores de datos y responsables, Data Catalog proporciona funciones de curación como:

* Administración del glosario empresarial.
* Automatización del etiquetado de activos con términos del glosario.

Los consumidores y productores de datos también pueden visualizar el linaje de los activos de datos, por ejemplo, desde sistemas operativos locales, pasando por movimientos, transformaciones y enriquecimientos mediante sistemas de almacenamiento y procesamiento en la nube, hasta su consumo en sistemas analíticos como Power BI.

## Data Estate Insights app

Microsoft Purview Data Estate Insights permite a los responsables de datos y otras partes interesadas en la gobernanza obtener una visión general del patrimonio de datos y obtener información procesable sobre las brechas de gobernanza que pueden resolverse desde la propia experiencia.

## Data Sharing app

Microsoft Purview Data Sharing permite a las organizaciones compartir datos de forma segura:

* Dentro de la organización.
* Entre organizaciones con socios comerciales y clientes.

Los datos pueden compartirse o recibirse con unos pocos clics.

Los proveedores de datos pueden administrar y monitorear centralmente las relaciones de intercambio y revocar el acceso en cualquier momento.

Los consumidores de datos pueden acceder a los datos recibidos mediante sus propias herramientas analíticas y convertirlos en información útil.

## Data Policy app

Microsoft Purview Data Policy proporciona experiencias centralizadas y basadas en la nube para administrar de forma segura y a escala el acceso a fuentes y conjuntos de datos.

Permite:

* Administrar el acceso a fuentes de datos desde una experiencia centralizada basada en la nube.
* Aprovisionar acceso a escala.
* Introducir un modelo de permisos del plano de datos externo a las fuentes de datos.
* Integrarse con Microsoft Purview Data Map y Catalog.

La integración permite:

* Buscar activos de datos y conceder únicamente el acceso requerido mediante políticas detalladas.
* Avanzar hacia el soporte de fuentes de datos SaaS, locales y multicloud.
* Crear políticas que utilicen cualquier metadato asociado a los objetos de datos.
* Utilizar definiciones de roles simples y abstractas, como **Read** y **Modify**.

---

# Clasificación de información confidencial mediante Microsoft Purview

## Administración de fuentes de datos en Microsoft Purview

El proceso para administrar fuentes de datos comprende:

1. **Register**
2. **Scan and Ingest**
3. **Classify**

El etiquetado en Microsoft Purview Data Map está actualmente en **PREVIEW**.

## Registrar una nueva fuente

Para registrar una fuente y administrarla en el portal de gobernanza de Microsoft Purview, se necesita ser **Data Source Admin** y tener uno de los otros roles de Purview, por ejemplo:

* Data Reader.
* Data Share Contributor.

Después de registrar una fuente de datos, Microsoft Purview puede escanearla para:

* Capturar metadatos técnicos.
* Extraer el esquema.
* Aplicar clasificaciones a los datos.

### Prerrequisitos para escanear una fuente

Antes de escanear una fuente de datos:

1. Registrar la fuente de datos, proporcionando a Microsoft Purview la dirección de la fuente y asignándola a una colección en Microsoft Purview Data Map.
2. Considerar la red y elegir la configuración adecuada de Integration Runtime.
3. Considerar las credenciales que se utilizarán para conectarse a la fuente.

## Scans e ingestión en Microsoft Purview

Las funciones de escaneo e ingestión conectan la cuenta de Microsoft Purview con las fuentes para poblar Data Map y Data Catalog y permitir explorar y administrar los datos.

* **Scanning** captura metadatos de las fuentes de datos y los lleva a Microsoft Purview.
* **Ingestion** procesa los metadatos y los almacena en el catálogo de datos.

La ingestión puede provenir de:

* Escaneos de fuentes de datos: los metadatos escaneados se agregan a Microsoft Purview Data Map.
* Conexiones de linaje: los recursos de transformación agregan metadatos sobre sus fuentes, resultados y actividades a Microsoft Purview Data Map.

## Scanning

Después de registrar las fuentes de datos, el siguiente paso es escanearlas.

El proceso de escaneo:

* Establece una conexión con la fuente de datos.
* Captura metadatos técnicos como nombres, tamaños de archivos y columnas.
* Extrae el esquema de fuentes de datos estructuradas.
* Aplica clasificaciones a los esquemas.
* Aplica etiquetas de sensibilidad si Microsoft Purview Data Map está conectado a un portal de cumplimiento de Microsoft Purview.

El escaneo puede ejecutarse inmediatamente o programarse periódicamente para mantener actualizada la cuenta de Microsoft Purview.

Para cada escaneo pueden aplicarse personalizaciones para analizar únicamente la información necesaria y no toda la fuente.

## Crear un conjunto de reglas de escaneo

En un catálogo de Microsoft Purview se pueden crear **scan rule sets** para escanear rápidamente fuentes de datos de la organización.

Un scan rule set es un contenedor que agrupa un conjunto de reglas de escaneo para asociarlas a un escaneo.

Se pueden crear conjuntos predeterminados para cada tipo de fuente de datos y utilizarlos por defecto en los escaneos de la organización.

Los usuarios con los permisos adecuados también pueden crear otros conjuntos de reglas con diferentes configuraciones según las necesidades empresariales.

## System scan rule sets

Los system scan rule sets son conjuntos de reglas definidos por Microsoft y creados automáticamente para cada catálogo de Microsoft Purview.

Cada conjunto está asociado a un tipo específico de fuente de datos.

Al crear un escaneo se puede asociar un system scan rule set.

Cuando Microsoft actualiza estos conjuntos de reglas, se pueden actualizar en el catálogo y aplicar la actualización a todos los escaneos asociados.

---

# Clasificación de datos en el portal de gobernanza de Microsoft Purview

La clasificación de datos consiste en categorizar activos de datos asignándoles etiquetas o clases lógicas únicas.

La clasificación se basa en el contexto empresarial de los datos.

Ejemplos:

* Passport Number.
* Driver's License Number.
* Credit Card Number.
* SWIFT Code.
* Person’s Name.

Clasificar activos de datos permite:

* Comprenderlos mejor.
* Buscarlos más fácilmente.
* Gobernarlos.
* Comprender los riesgos asociados.
* Implementar medidas para proteger datos sensibles o importantes frente a proliferación no gobernada y acceso no autorizado.

Microsoft Purview Data Map proporciona una capacidad de clasificación automatizada durante el escaneo de las fuentes de datos.

Incluye más de **200 clasificaciones de sistema integradas** y permite crear clasificaciones personalizadas.

Los activos pueden clasificarse automáticamente durante la ingesta como parte de un escaneo configurado o editarse manualmente en el portal de gobernanza después de ser escaneados e ingeridos.

## Usos de la clasificación

La clasificación organiza los datos en categorías lógicas que facilitan recuperarlos, ordenarlos e identificarlos para usos futuros.

La clasificación ayuda a:

* Reducir la búsqueda a los activos de datos de interés.
* Organizar y comprender las diferentes clases de datos importantes para la organización y dónde se almacenan.
* Comprender los riesgos asociados a los activos de datos más importantes y tomar medidas apropiadas para mitigarlos.

## Tipos de clasificación

El portal de gobernanza de Microsoft Purview admite:

* **Clasificaciones de sistema**: más de 200 clasificaciones disponibles de forma integrada.
* **Clasificaciones personalizadas**: permiten clasificar activos según un patrón o nombre de columna específico que no está disponible como clasificación de sistema.

Las reglas de clasificación personalizada pueden basarse en:

* Patrones de expresiones regulares.
* Diccionarios.

Por ejemplo, si una columna Employee ID sigue el patrón `EMPLOYEE{GUID}`, se puede crear una clasificación personalizada mediante una expresión regular.

## Clasificaciones y etiquetas de sensibilidad

Las etiquetas de sensibilidad son diferentes de las clasificaciones.

Las etiquetas de sensibilidad categorizan los activos en el contexto de la seguridad y privacidad de los datos, por ejemplo:

* Highly Confidential.
* Restricted.
* Public.

Para utilizar etiquetas de sensibilidad en Microsoft Purview Data Map, se necesita al menos una licencia o cuenta de Microsoft 365 dentro del mismo tenant de Microsoft Entra que Microsoft Purview Data Map.

---
