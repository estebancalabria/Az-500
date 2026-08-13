# Seguridad de Azure Storage

## Introducción

Azure Storage proporciona mecanismos para **proteger los datos, controlar el acceso, administrar claves, cifrar información y recuperar datos ante incidentes**.

La estrategia de seguridad debe aplicar **Zero Trust, mínimo privilegio y defensa en profundidad**, considerando amenazas como accesos no autorizados, eliminación accidental, modificaciones maliciosas, ransomware, errores operativos y fallos de infraestructura.

Los principales mecanismos tratados son:

* Autenticación y autorización mediante **Microsoft Entra ID y Azure RBAC**.
* **Managed identities** para evitar credenciales almacenadas.
* Cifrado y administración de claves.
* **BYOK** para mantener control sobre claves generadas externamente.
* Protección de datos mediante soft delete, versioning, snapshots e inmutabilidad.
* **Double encryption** para escenarios que requieren capas adicionales de cifrado.
* Redundancia y recuperación ante desastres.
* Monitoreo y aplicación de políticas de seguridad.

---

## Autorización de acceso a Azure Storage

Microsoft recomienda utilizar **Microsoft Entra ID** para autorizar el acceso a los datos en lugar de **Shared Key**.

El proceso con Microsoft Entra ID tiene dos pasos:

1. **Autenticación**: se autentica la identidad y se obtiene un token OAuth 2.0.
2. **Autorización**: el token se utiliza para acceder al recurso según los permisos asignados mediante **Azure RBAC**.

Las identidades pueden ser:

* Usuarios.
* Grupos.
* Service principals.
* Managed identities.

Para aplicaciones ejecutándose en Azure, se recomienda utilizar **managed identities**, evitando almacenar credenciales en el código.

### Azure Identity

La biblioteca **Azure Identity** es la opción recomendada para la mayoría de los escenarios de desarrollo.

`DefaultAzureCredential` permite obtener credenciales de forma consistente tanto durante el desarrollo como en Azure.

**MSAL** puede utilizarse en escenarios avanzados.

### Shared Key

Shared Key proporciona acceso amplio mediante las claves de la cuenta y no permite aprovechar mecanismos avanzados como Conditional Access y MFA.

Buenas prácticas:

* Preferir Microsoft Entra ID.
* Limitar el uso de access keys y connection strings a prototipos o escenarios iniciales.
* Deshabilitar Shared Key cuando sea posible.
* Para acceso delegado temporal a Blob Storage, preferir **user delegation SAS**.

---

## Azure Blobs

Para Blob Storage, Microsoft Entra ID utiliza Azure RBAC para autorizar el acceso a los datos.

Roles principales:

* **Storage Blob Data Owner**: acceso completo a blobs y contenedores.
* **Storage Blob Data Contributor**: lectura, escritura y eliminación.
* **Storage Blob Data Reader**: acceso de solo lectura.
* **Storage Blob Delegator**: permite obtener una user delegation key para crear una SAS basada en credenciales de Microsoft Entra.

### Alcance de RBAC

Aplicar siempre el alcance más restrictivo posible:

1. Contenedor.
2. Storage account.
3. Resource group.
4. Subscription.
5. Management group.

Los roles **Owner**, **Contributor** y **Storage Account Contributor** permiten administrar la cuenta, pero no proporcionan por sí mismos acceso a los datos mediante Microsoft Entra ID.

Los roles que incluyen `Microsoft.Storage/storageAccounts/listKeys/action` pueden permitir acceso mediante Shared Key.

También puede utilizarse **Azure ABAC** para agregar condiciones más específicas a las asignaciones de roles.

---

## Azure Tables

Azure Table Storage admite Microsoft Entra ID y Azure RBAC para autorizar el acceso a los datos.

Roles principales:

* **Storage Table Data Contributor**: lectura, escritura y eliminación.
* **Storage Table Data Reader**: acceso de solo lectura.

El alcance puede establecerse en:

* Tabla.
* Storage account.
* Resource group.
* Subscription.
* Management group.

Buenas prácticas:

* Utilizar managed identities.
* Aplicar mínimo privilegio.
* Asignar roles al nivel de tabla cuando sea posible.
* Evitar Shared Key.
* Utilizar roles personalizados cuando sea necesario.
* Supervisar el acceso.
* Aplicar Conditional Access.
* Revisar periódicamente las asignaciones.
* Separar entornos de desarrollo y producción.

---

## Azure Queues

Azure Queue Storage también admite Microsoft Entra ID y Azure RBAC.

Roles principales:

* **Storage Queue Data Reader**: lectura.
* **Storage Queue Data Message Sender**: agregar mensajes.
* **Storage Queue Data Message Processor**: consultar, recuperar y eliminar mensajes.
* **Storage Queue Data Contributor**: acceso completo a los datos de las colas.

El alcance puede establecerse desde una cola individual hasta un management group.

Buenas prácticas:

* Utilizar managed identities en aplicaciones de Azure.
* Aplicar mínimo privilegio.
* Asignar roles al nivel de cola cuando sea posible.
* Evitar Shared Key.
* Utilizar roles personalizados para permisos específicos.
* Supervisar el acceso y revisar periódicamente las asignaciones.
* Separar entornos de desarrollo, pruebas y producción.
* Implementar retry logic con exponential backoff para aplicaciones basadas en colas.

---

# Cifrado de Azure Storage

Azure Storage cifra automáticamente todos los datos de una storage account mediante **AES de 256 bits** a nivel de servicio. Este cifrado es compatible con **FIPS 140-2**.

El cifrado a nivel de servicio puede utilizar:

* Claves administradas por Microsoft.
* **Customer-managed keys (CMK)** almacenadas en Azure Key Vault o Key Vault Managed HSM.

---

## Double encryption: cifrado a nivel de infraestructura

Para escenarios que requieren una capa adicional de protección, Azure Storage permite habilitar **infrastructure encryption**.

Con esta configuración, los datos se cifran dos veces:

1. Cifrado a nivel de servicio.
2. Cifrado adicional a nivel de infraestructura.

Cada capa utiliza **una clave diferente y un algoritmo diferente**, proporcionando defensa en profundidad frente a un posible compromiso de una de las capas criptográficas.

El cifrado de infraestructura utiliza **claves administradas por Microsoft**, independientes de las claves utilizadas por el cifrado a nivel de servicio.

### Cuándo utilizarlo

Está principalmente destinado a escenarios donde el **cumplimiento normativo o requisitos específicos exigen doble cifrado**.

Para la mayoría de los escenarios, el cifrado estándar de Azure Storage proporciona protección suficiente y el doble cifrado puede no aportar un beneficio significativo de seguridad o rendimiento.

### Configuración

Infrastructure encryption puede habilitarse:

* Para toda la **storage account**.
* Para un **encryption scope** específico.

En una storage account debe habilitarse **durante la creación** y no puede activarse ni desactivarse posteriormente.

La cuenta debe ser:

* **General-purpose v2**, o
* **Premium block blob**.

Si la infraestructura no está habilitada para toda la cuenta, puede habilitarse al crear un encryption scope. Esta configuración tampoco puede modificarse después de crear el scope.

### Consideraciones

* Evaluar primero los requisitos de cumplimiento.
* Planificar la configuración antes de crear la cuenta.
* Si posteriormente se necesita cambiar la configuración de infraestructura, será necesario crear otra storage account y migrar los datos.
* El impacto de rendimiento suele ser pequeño, pero debe evaluarse en cargas de alto throughput.
* Puede combinarse con **customer-managed keys** a nivel de servicio para obtener mayor control sobre el cifrado.
* **Azure Policy** dispone de una política integrada para exigir infrastructure encryption.
* No implica un costo adicional de almacenamiento, aunque introduce cierto overhead computacional.
* Debe contemplarse en los procedimientos de backup, recuperación y migración.

---

# Bring Your Own Key (BYOK)

**BYOK** permite importar a Azure Key Vault una clave generada en un **HSM externo**, manteniendo el control sobre su ciclo de vida.

Es especialmente útil cuando una organización necesita controlar sus claves por requisitos de seguridad, cumplimiento o regulación.

Requisitos fundamentales:

* La clave transferida **nunca existe fuera de un HSM en texto plano**.
* Fuera del HSM, la clave está protegida mediante una clave almacenada en el HSM de Azure Key Vault.

### Elementos del proceso

* **Key Exchange Key (KEK)**: clave RSA-HSM generada en Azure Key Vault para proteger la importación.
* **Wrapping Key**: clave AES temporal generada por el HSM de origen.
* **Target Key**: clave que se importa a Azure Key Vault.

La KEK:

* Debe ser una clave **RSA-HSM**.
* Se recomienda **4096 bits**; también se admiten 3072 y 2048 bits.
* Debe tener únicamente la operación **`import`**.
* Debe estar en el mismo vault donde se importará la clave.

El tamaño de la KEK debe además cumplir los requisitos del servicio que utilizará la clave.

### Flujo BYOK

1. Generar la **KEK** en Azure Key Vault.
2. Obtener su clave pública.
3. Utilizar la herramienta BYOK del fabricante del HSM para proteger la Target Key.
4. Generar un **Key Transfer Blob (`.byok`)**.
5. Importar la clave protegida en Azure Key Vault mediante Azure CLI, PowerShell o Azure Portal.

El material criptográfico permanece protegido durante la transferencia mediante mecanismos de wrapping entre los HSM.

### Consideraciones del HSM

El HSM de origen debe permitir el mecanismo de exportación necesario.

La Target Key puede requerir estar marcada como **`CKA_EXTRACTABLE`**, y determinadas configuraciones pueden requerir que la KEK y la wrapping key estén marcadas como **`CKA_TRUSTED`**.

La configuración concreta depende del fabricante del HSM y de su herramienta BYOK.

### Buenas prácticas BYOK

* Utilizar el mayor tamaño de clave compatible.
* Limitar la KEK a la operación `import`.
* Proteger el archivo `.byok` durante su transferencia.
* Probar el proceso con claves de prueba antes de utilizar claves de producción.
* Documentar las claves importadas y su propósito.
* Planificar la rotación de claves mediante nuevas versiones.
* Utilizar **Managed HSM** para escenarios que requieran aislamiento HSM de tenant único.
* Aplicar Azure RBAC para separar permisos de creación de KEK e importación.
* Habilitar auditoría y monitoreo de las operaciones BYOK.
* Verificar que el fabricante del HSM proporcione una herramienta BYOK compatible con Azure Key Vault.

---

# Protección de datos en Azure Storage

Azure Storage ofrece mecanismos para recuperar datos eliminados o sobrescritos y proteger información crítica.

La estrategia debe utilizar **defensa en profundidad**, combinando mecanismos según las necesidades de recuperación, seguridad y cumplimiento.

### Protección básica recomendada

* Configurar un **Azure Resource Manager lock** para evitar la eliminación o modificación de la cuenta.
* Habilitar **container soft delete**.
* Para Blob Storage, habilitar **blob versioning**.
* Para Data Lake Storage, utilizar **snapshots** cuando corresponda.

### Principales mecanismos

| Mecanismo                        | Protección principal                                               |
| -------------------------------- | ------------------------------------------------------------------ |
| **Azure Resource Manager lock**  | Protege la cuenta contra eliminación o cambios de configuración    |
| **Container soft delete**        | Permite recuperar contenedores eliminados                          |
| **Blob soft delete**             | Permite recuperar blobs o versiones eliminadas                     |
| **Blob versioning**              | Conserva versiones anteriores de los blobs                         |
| **Point-in-time restore**        | Permite recuperar un conjunto de block blobs a un momento anterior |
| **Blob snapshots**               | Conservan el estado de un blob en un momento determinado           |
| **Immutability policy**          | Impide eliminar o sobrescribir datos durante el período definido   |
| **Copia a otra storage account** | Permite recuperar datos si la cuenta principal se ve comprometida  |

### Inmutabilidad

Las políticas de **immutable storage** protegen datos críticos frente a eliminación y sobrescritura.

Pueden aplicarse:

* A una versión de blob.
* A un contenedor.

Son especialmente útiles para requisitos legales y regulatorios de tipo **WORM (Write Once, Read Many)**.

### Recuperación

Según la protección habilitada, es posible recuperar:

* Una cuenta eliminada, bajo determinadas condiciones.
* Un contenedor mediante container soft delete.
* Un blob desde una versión anterior.
* Un blob mediante blob soft delete.
* Un blob desde un snapshot.
* Un conjunto de block blobs mediante point-in-time restore.
* Datos desde una segunda storage account.

### Costos

Los mecanismos de protección pueden incrementar los costos de almacenamiento porque mantienen versiones, snapshots o datos eliminados durante períodos de retención.

En particular:

* **Blob versioning** puede aumentar el consumo de capacidad.
* **Soft delete** mantiene datos eliminados facturables hasta su eliminación definitiva.
* **Snapshots** pueden incrementar el almacenamiento.
* Una segunda storage account implica costos adicionales de capacidad y transacciones.
* Las copias entre regiones pueden generar costos de salida.

Se puede utilizar **lifecycle management** para eliminar versiones y snapshots antiguos y controlar costos.

---

# Redundancia y recuperación ante desastres

Azure Storage mantiene múltiples copias de los datos para protegerlos frente a fallos de hardware, red, energía y otros eventos.

Las cuentas con redundancia geográfica pueden mantener datos en una región secundaria y permitir un **failover** ante una interrupción de la región primaria.

Para datos críticos se recomienda considerar mecanismos de **geo-redundancia** y procedimientos de recuperación.

La protección de datos debe complementarse con:

* Pruebas periódicas de recuperación.
* Retenciones adecuadas.
* Backups cuando sean necesarios.
* Procedimientos documentados de recuperación.

---

# Principios fundamentales de seguridad

* **Preferir Microsoft Entra ID** sobre Shared Key.
* **Usar managed identities** para aplicaciones de Azure.
* **Aplicar mínimo privilegio** mediante Azure RBAC.
* Asignar permisos al **alcance más reducido posible**.
* Utilizar **ABAC** o roles personalizados cuando se necesite mayor granularidad.
* Deshabilitar Shared Key cuando sea posible.
* Combinar **soft delete, versioning, snapshots e inmutabilidad** según las necesidades.
* Utilizar **BYOK** cuando sea necesario mantener control sobre claves generadas externamente.
* Utilizar **double encryption** cuando existan requisitos de cumplimiento que justifiquen una segunda capa de cifrado.
* Supervisar accesos y operaciones mediante Azure Monitor y registros de auditoría.
* Utilizar Azure Policy para imponer requisitos de seguridad.
* Probar periódicamente los procedimientos de recuperación.
* Equilibrar protección, retención, rendimiento y costos.
* Documentar la arquitectura de seguridad y los requisitos de cumplimiento.

