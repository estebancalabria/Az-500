# Protección de usuarios en Microsoft Entra ID

Microsoft Entra ID permite administrar distintos tipos de usuarios dentro del inquilino, proporcionando flexibilidad para administrar empleados, colaboradores externos e identidades de organizaciones asociadas de forma segura. :contentReference[oaicite:0]{index=0}

## Roles mínimos para administrar usuarios

Aplicando el principio de **mínimo privilegio**, cada tarea requiere un rol específico:

| Tarea | Rol requerido |
|--------|---------------|
| Crear usuarios | Administrador de usuarios |
| Invitar usuarios externos | Invitador de usuarios |
| Asignar roles de Microsoft Entra | Administrador de roles con privilegios |

Siempre que sea posible, se recomienda evitar utilizar el rol de **Administrador Global** para tareas cotidianas. :contentReference[oaicite:1]{index=1}

---

# Tipos de usuarios

Microsoft Entra ID admite diferentes tipos de usuarios según su origen y nivel de acceso.

| Tipo | Descripción |
|------|-------------|
| **Miembro interno** | Empleado o usuario interno de la organización. |
| **Invitado interno** | Usuario creado en el tenant con permisos de invitado. |
| **Miembro externo** | Usuario autenticado en otro tenant de Microsoft Entra pero con permisos de miembro en el tenant actual. |
| **Invitado externo** | Colaborador externo autenticado mediante un proveedor externo con permisos de invitado. |

## Métodos de autenticación

Dependiendo del tipo de usuario:

- Los **miembros** e **invitados internos** utilizan credenciales administradas por el tenant.
- Los **miembros externos** se autentican en su tenant de origen mediante federación.
- Los **invitados externos** configuran su contraseña utilizando el enlace recibido durante la invitación. :contentReference[oaicite:2]{index=2}

---

# Creación de usuarios

La creación de usuarios se realiza desde:

**Identity → Users → All Users → New User**

Durante el asistente de creación se configuran los siguientes elementos.

## Información básica

- User Principal Name (UPN)
- Dominio
- Alias de correo
- Nombre para mostrar
- Contraseña inicial
- Estado de la cuenta (habilitada o deshabilitada)

## Propiedades

Se pueden definir propiedades adicionales como:

- Nombre y apellido
- Tipo de usuario
- Información laboral
- Datos de contacto
- Grupo de edad (cuando corresponda)
- Ubicación geográfica

## Asignaciones

Durante la creación del usuario es posible asignarlo directamente a:

- Grupos
- Roles de Microsoft Entra
- Unidades administrativas

Estas asignaciones también pueden modificarse posteriormente. :contentReference[oaicite:3]{index=3}

---

# Protección de grupos

Los grupos permiten administrar permisos de forma centralizada, evitando asignar permisos individualmente a cada usuario y facilitando la implementación del principio de **Zero Trust**.

Los grupos pueden utilizarse para otorgar acceso a:

- Recursos de Microsoft Entra ID.
- Aplicaciones SaaS.
- Servicios de Azure.
- Sitios de SharePoint.
- Recursos locales. :contentReference[oaicite:4]{index=4}

## Tipos de grupos

### Grupo de Seguridad

Se utiliza para administrar permisos sobre recursos.

Características:

- Usuarios
- Dispositivos
- Service Principals
- Grupos anidados
- Control de acceso

### Grupo de Microsoft 365

Diseñado para la colaboración.

Incluye recursos compartidos como:

- Outlook
- Calendario
- SharePoint
- Archivos
- Microsoft Teams

Puede incluir usuarios internos y externos. :contentReference[oaicite:5]{index=5}

---

# Tipos de pertenencia

Microsoft Entra ID admite tres modelos de pertenencia:

## Asignada

Los administradores agregan manualmente los miembros del grupo.

## Usuario dinámico

Los usuarios se agregan o eliminan automáticamente mediante reglas basadas en atributos, por ejemplo:

- Departamento
- Cargo
- Ubicación
- Empresa

## Dispositivo dinámico

Los dispositivos se agregan automáticamente según atributos como:

- Sistema operativo
- Propiedad
- Estado
- Modelo

Las reglas dinámicas reducen la administración manual y mantienen los grupos siempre actualizados. :contentReference[oaicite:6]{index=6}

---

# Administración del acceso mediante grupos

Una vez creado un grupo, el acceso debe asignarse siguiendo el principio de **mínimo privilegio**.

Microsoft Entra permite diferentes estrategias:

- **Asignación directa:** el recurso se asigna directamente al usuario.
- **Asignación por grupo:** el recurso se asigna al grupo y todos sus miembros heredan el acceso.
- **Asignación basada en reglas:** los miembros se incorporan automáticamente mediante reglas dinámicas.
- **Asignación desde un origen externo:** la pertenencia proviene de Active Directory local u otra plataforma externa. :contentReference[oaicite:7]{index=7}

---

# Autoinscripción en grupos

Los propietarios de grupos pueden permitir que los usuarios:

- Busquen grupos disponibles.
- Soliciten unirse.
- Se unan automáticamente.
- Esperen aprobación de un propietario.

Cuando la aprobación es obligatoria, la solicitud se envía a los propietarios del grupo, quienes deciden si conceden o rechazan la incorporación del usuario. :contentReference[oaicite:8]{index=8}

---

# Identidades Externas (Microsoft Entra External ID)

**Microsoft Entra External ID** permite colaborar de forma segura con usuarios externos, clientes y socios comerciales utilizando sus propias identidades, sin necesidad de administrar cuentas ni contraseñas externas. La solución cubre tanto escenarios **B2B (Business-to-Business)** como **CIAM (Customer Identity and Access Management)** para aplicaciones orientadas a consumidores. :contentReference[oaicite:0]{index=0}

## ¿Cuándo utilizar Microsoft Entra External ID?

Microsoft recomienda utilizar External ID en dos escenarios principales:

### Colaboración B2B (Business-to-Business)

Cuando una organización necesita permitir que socios, proveedores o colaboradores externos accedan a recursos corporativos.

Ejemplos:

- Compartir aplicaciones empresariales.
- Acceso a Microsoft 365.
- SharePoint.
- Microsoft Teams.
- Aplicaciones SaaS.
- Aplicaciones internas.

Los usuarios mantienen sus propias credenciales y se autentican contra su proveedor de identidad habitual. :contentReference[oaicite:1]{index=1}

### CIAM (Customer Identity and Access Management)

Cuando una organización desarrolla aplicaciones dirigidas a clientes o consumidores.

Permite:

- Registro de usuarios.
- Inicio de sesión.
- Administración de identidades.
- Personalización completa de la experiencia de autenticación.

En este escenario se utiliza un **tenant externo** independiente del tenant corporativo. :contentReference[oaicite:2]{index=2}

---

# Colaboración B2B

La colaboración B2B permite invitar usuarios externos para acceder a recursos de la organización sin crear cuentas internas.

### Beneficios

- Los usuarios utilizan sus propias credenciales.
- No es necesario administrar contraseñas externas.
- No se sincronizan cuentas entre organizaciones.
- Se mantiene el control sobre los recursos corporativos.
- Compatible con cuentas Microsoft, Microsoft Entra y proveedores sociales como Google o Facebook. :contentReference[oaicite:3]{index=3}

## Métodos para incorporar invitados

Microsoft Entra External ID ofrece varias opciones:

- Invitación desde el portal de Microsoft Entra.
- Invitación mediante PowerShell.
- Registro de autoservicio.
- Microsoft Graph API.
- Microsoft Entra Entitlement Management. :contentReference[oaicite:4]{index=4}

Una vez aceptada la invitación:

- Se crea un objeto de usuario.
- Puede agregarse a grupos.
- Puede recibir roles.
- Puede asignarse a aplicaciones y recursos.

El usuario continúa autenticándose contra su organización de origen. :contentReference[oaicite:5]{index=5}

---

# Registro de autoservicio

Microsoft Entra External ID permite que usuarios externos creen sus propias cuentas mediante flujos de registro configurables.

Durante el registro es posible:

- Elegir proveedores de identidad.
- Utilizar cuentas sociales.
- Utilizar cuentas corporativas.
- Solicitar atributos personalizados.
- Ejecutar validaciones mediante API.
- Integrar procesos de aprobación personalizados. :contentReference[oaicite:6]{index=6}

---

# Proveedores de identidad compatibles

Los usuarios externos pueden autenticarse utilizando identidades existentes, por ejemplo:

- Microsoft Entra ID
- Cuenta Microsoft
- Google
- Facebook
- Otros proveedores empresariales federados

Esto evita la creación de nuevas credenciales específicas para la aplicación. 

---

# Administración de la colaboración externa

Microsoft Entra proporciona múltiples mecanismos para controlar la colaboración con organizaciones externas.

## Acceso entre tenants

Permite configurar:

- Colaboración entrante.
- Colaboración saliente.
- Usuarios autorizados.
- Aplicaciones autorizadas.
- Confianza entre tenants.
- Reutilización de MFA.
- Confianza en dispositivos administrados. 

## Configuración de colaboración externa

Permite definir:

- Quién puede invitar usuarios.
- Dominios permitidos.
- Dominios bloqueados.
- Restricciones para usuarios invitados. :contentReference[oaicite:9]{index=9}

---

# Tenant de recursos vs Tenant externo

Microsoft Entra External ID distingue dos modelos de implementación.

## Tenant de recursos (Workforce)

Contiene:

- Empleados.
- Aplicaciones internas.
- Recursos corporativos.

Permite colaborar con invitados B2B. :contentReference[oaicite:10]{index=10}

## Tenant externo

Se utiliza para:

- Clientes.
- Consumidores.
- Aplicaciones públicas.

Mantiene completamente separado el directorio de clientes del directorio corporativo. :contentReference[oaicite:11]{index=11}

---

# Comparación: Workforce vs External Tenant

| Workforce (B2B) | External Tenant (CIAM) |
|-----------------|------------------------|
| Colaboración con socios comerciales | Aplicaciones para clientes |
| Usuarios invitados | Usuarios consumidores |
| Comparte recursos corporativos | Administra cuentas de clientes |
| Compatible con Microsoft 365 | No orientado a Microsoft 365 |
| Soporta Entitlement Management | Diseñado para experiencias de autenticación personalizadas | :contentReference[oaicite:12]{index=12}

---

# Integración con Microsoft 365

Microsoft Entra External ID puede integrarse con:

- Microsoft Teams
- SharePoint Online
- OneDrive
- Aplicaciones Microsoft 365

Los invitados acceden utilizando sus propias credenciales mientras la organización mantiene el control sobre permisos y acceso. :contentReference[oaicite:13]{index=13}

---

# Conexión directa B2B

La característica **B2B Direct Connect** establece relaciones de confianza entre dos organizaciones Microsoft Entra para habilitar escenarios como **Teams Shared Channels**.

Características:

- No crea usuarios invitados.
- Cada usuario permanece en su tenant.
- Acceso transparente entre organizaciones.
- Los usuarios no cambian de tenant para colaborar.
- Basado en relaciones de confianza entre organizaciones. :contentReference[oaicite:14]{index=14}
