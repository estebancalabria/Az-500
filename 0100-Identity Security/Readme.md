# Administrar controles de seguridad en Microsoft Entra ID

## Descripción

Este módulo se centra en la administración de los controles de seguridad en **Microsoft Entra ID**, con el objetivo de proteger identidades, autenticación y autorización para garantizar un acceso seguro a los recursos de la organización.

Se abordan las mejores prácticas para proteger:

- Identidades de usuarios.
- Grupos y privilegios administrativos.
- Identidades externas.
- Procesos de autenticación.
- Acceso a recursos mediante políticas basadas en riesgo.

El enfoque combina controles preventivos, detección de amenazas y automatización de decisiones de acceso para fortalecer la postura de seguridad de la organización.

---

# Escenario

Imagine que es un **Ingeniero de Seguridad de Azure** responsable de proteger las identidades y administrar los controles de acceso en **Microsoft Entra ID**.

Su responsabilidad consiste en proteger:

- Usuarios internos.
- Grupos.
- Identidades externas (B2B/B2C).
- Recursos corporativos.

Para ello deberá implementar diferentes mecanismos de seguridad, entre ellos:

- Microsoft Entra ID Protection.
- Autenticación Multifactor (MFA).
- Acceso Condicional (Conditional Access).
- Controles de autenticación.
- Controles de autorización.

El objetivo es impedir accesos no autorizados mientras se mantiene una experiencia de usuario segura y transparente.

---

# Objetivos de aprendizaje

Al finalizar este módulo podrá:

- Proteger identidades de usuario mediante mecanismos seguros de autenticación y administración de acceso.
- Proteger grupos y privilegios administrativos evitando modificaciones no autorizadas.
- Administrar identidades externas aplicando políticas de seguridad adecuadas.
- Implementar **Microsoft Entra ID Protection** para detectar, investigar y responder a amenazas relacionadas con identidades.
- Configurar **Conditional Access** para aplicar controles de acceso basados en:
  - Riesgo del usuario.
  - Riesgo de inicio de sesión.
  - Estado del dispositivo.
  - Ubicación.
  - Aplicaciones utilizadas.
  - Otros factores contextuales.

---

# Objetivo del módulo

El propósito del módulo es desarrollar los conocimientos necesarios para administrar de forma segura las identidades dentro de Microsoft Entra ID.

Durante el recorrido se aprenderá a:

- Proteger identidades de usuario.
- Asegurar grupos y privilegios administrativos.
- Administrar identidades externas.
- Detectar amenazas relacionadas con identidades.
- Mitigar riesgos utilizando Microsoft Entra ID Protection.
- Aplicar políticas de Conditional Access.
- Mantener la integridad, confidencialidad y cumplimiento del acceso a los recursos corporativos.

---

# Administración de identidades y acceso con privilegios

La administración de identidades es uno de los pilares de la seguridad en la nube. Su objetivo es garantizar que únicamente las identidades autorizadas puedan acceder a los recursos, aplicando mecanismos de autenticación sólidos, autorización basada en privilegios mínimos y supervisión continua de actividades sospechosas. :contentReference[oaicite:0]{index=0}

## IM-1: Utilizar un sistema centralizado de identidad

La recomendación es centralizar toda la autenticación de la organización mediante **Microsoft Entra ID**, evitando múltiples almacenes de identidad y reduciendo la administración de cuentas duplicadas.

### Beneficios

- Administración centralizada de usuarios.
- Inicio de sesión único (SSO).
- Integración con recursos locales y en la nube.
- Sincronización con Active Directory.
- Integración con aplicaciones SaaS.
- Administración de identidades híbridas.

Siempre que sea posible, las aplicaciones deben autenticarse mediante Microsoft Entra ID en lugar de mecanismos de autenticación locales. :contentReference[oaicite:1]{index=1}

---

## IM-2: Proteger los sistemas de identidad

La identidad constituye uno de los objetivos principales de los ataques modernos, por lo que debe protegerse mediante controles específicos.

Entre las recomendaciones se encuentran:

- Aplicar el principio de mínimo privilegio.
- Limitar el número de administradores globales.
- Requerir MFA para administradores.
- Bloquear la autenticación heredada.
- Habilitar Self-Service Password Reset (SSPR).
- Supervisar actividades privilegiadas.
- Implementar Microsoft Entra ID Identity Protection.
- Utilizar Microsoft Defender for Identity para Active Directory local.

Microsoft recomienda utilizar la **Identity Secure Score** para evaluar continuamente la postura de seguridad de Microsoft Entra ID y detectar configuraciones inseguras. :contentReference[oaicite:2]{index=2}

---

## IM-3: Administrar identidades de aplicaciones

Las aplicaciones no deberían utilizar cuentas de usuario para autenticarse.

Se recomienda utilizar:

- Managed Identities.
- Service Principals.
- Certificados en lugar de secretos.
- Rotación automática de credenciales.

Las identidades administradas eliminan la necesidad de almacenar credenciales dentro del código fuente o archivos de configuración. :contentReference[oaicite:3]{index=3}

---

## IM-4: Autenticación entre servidores y servicios

Toda comunicación entre aplicaciones y servicios debe autenticarse utilizando **TLS**.

Buenas prácticas:

- Exigir TLS en todos los servicios.
- Validar certificados emitidos por entidades certificadoras confiables.
- Utilizar Mutual TLS (mTLS) cuando ambas partes deban autenticarse.

Esto garantiza la autenticidad del servidor y protege la información durante el tránsito. :contentReference[oaicite:4]{index=4}

---

## IM-5: Implementar Single Sign-On (SSO)

El inicio de sesión único mejora tanto la experiencia del usuario como la seguridad.

Ventajas:

- Menor cantidad de credenciales.
- Administración centralizada.
- Menor riesgo de reutilización de contraseñas.
- Acceso unificado a aplicaciones locales y en la nube.
- Compatibilidad con usuarios internos y externos.

Microsoft Entra ID proporciona SSO para recursos de Azure, aplicaciones SaaS y aplicaciones locales. :contentReference[oaicite:5]{index=5}

---

## IM-6: Aplicar autenticación segura

Las contraseñas por sí solas ya no son suficientes.

Microsoft recomienda implementar:

- Autenticación Multifactor (MFA).
- Autenticación sin contraseña.
- Windows Hello for Business.
- Microsoft Authenticator.
- Claves FIDO2.
- Smart Cards.

El despliegue debe comenzar por los administradores y usuarios privilegiados, extendiéndose posteriormente al resto de la organización.

También se recomienda:

- Bloquear autenticación heredada.
- Eliminar contraseñas débiles.
- Cambiar credenciales predeterminadas de aplicaciones y dispositivos. :contentReference[oaicite:6]{index=6}

---

## IM-7: Restringir el acceso mediante condiciones

El acceso a los recursos debe decidirse dinámicamente utilizando el modelo **Zero Trust**, evaluando múltiples señales antes de permitir el acceso.

Microsoft Entra Conditional Access permite crear políticas basadas en:

- Riesgo del usuario.
- Riesgo del inicio de sesión.
- Rol del usuario.
- Aplicación utilizada.
- Dirección IP.
- Ubicación geográfica.
- Estado del dispositivo.
- Dispositivo administrado.
- Uso de protocolos heredados.
- Frecuencia de inicio de sesión.
- Persistencia de sesión.

Casos comunes de implementación:

- Exigir MFA para administradores.
- Bloquear autenticación heredada.
- Permitir acceso únicamente desde ubicaciones confiables.
- Bloquear inicios de sesión de alto riesgo.
- Exigir dispositivos corporativos para determinadas aplicaciones.

---

# ¿Qué es Microsoft Entra ID?

**Microsoft Entra ID** es el servicio de administración de identidades y acceso (IAM) basado en la nube de Microsoft. Permite autenticar y autorizar usuarios para acceder de forma segura tanto a recursos internos como externos desde una única plataforma de identidad. :contentReference[oaicite:0]{index=0}

## ¿Qué recursos protege?

Microsoft Entra ID proporciona acceso seguro a:

- Microsoft 365
- Azure Portal
- Aplicaciones SaaS
- Aplicaciones corporativas internas (Intranet)
- Aplicaciones desarrolladas por la organización
- Recursos híbridos (locales y nube) :contentReference[oaicite:1]{index=1}

---

# ¿Quién utiliza Microsoft Entra ID?

## Administradores de TI

Los administradores utilizan Microsoft Entra ID para:

- Administrar el acceso a aplicaciones y recursos.
- Implementar Autenticación Multifactor (MFA).
- Automatizar el aprovisionamiento de usuarios.
- Proteger identidades y credenciales.
- Implementar controles de gobernanza de acceso. :contentReference[oaicite:2]{index=2}

## Desarrolladores

Los desarrolladores pueden utilizar Microsoft Entra ID como proveedor de identidad para:

- Implementar autenticación basada en estándares.
- Agregar Single Sign-On (SSO).
- Consumir Microsoft Graph.
- Integrar aplicaciones con Microsoft Entra mediante APIs. :contentReference[oaicite:3]{index=3}

## Organizaciones

Toda organización que utilice alguno de estos servicios ya dispone automáticamente de un inquilino de Microsoft Entra ID:

- Microsoft 365
- Office 365
- Azure
- Dynamics 365 :contentReference[oaicite:4]{index=4}

---

# Licencias de Microsoft Entra ID

Microsoft Entra ID ofrece distintos niveles de funcionalidad según la licencia.

| Licencia | Características principales |
|----------|-----------------------------|
| **Free** | Administración de usuarios y grupos, sincronización de directorios, SSO, cambio de contraseña de autoservicio y administración básica. |
| **P1** | Incluye todo lo anterior más grupos dinámicos, identidad híbrida, autoservicio avanzado y funcionalidades empresariales. |
| **P2** | Agrega Identity Protection, Privileged Identity Management (PIM) y Acceso Condicional basado en riesgo. |
| **Pago por uso** | Características específicas como Microsoft Entra B2C para aplicaciones orientadas a clientes. | :contentReference[oaicite:5]{index=5}

---

# Principales funcionalidades

Microsoft Entra ID proporciona una plataforma completa de administración de identidades que incluye:

## Administración de acceso

- Single Sign-On (SSO)
- Acceso Condicional
- Administración de aplicaciones
- Administración de dispositivos
- Identity Governance

## Seguridad

- Autenticación Multifactor (MFA)
- Passwordless Authentication
- Identity Protection
- Smart Lockout
- Self-Service Password Reset (SSPR)

## Administración de identidades

- Usuarios y grupos
- Roles administrativos
- Identidades híbridas
- Identidades administradas (Managed Identities)
- Identidades de carga de trabajo

## Administración privilegiada

- Privileged Identity Management (PIM)
- Revisiones de acceso
- Supervisión y mantenimiento
- Auditoría

## Escenarios empresariales

- Azure
- Microsoft 365
- Aplicaciones SaaS
- Aplicaciones locales
- B2B (Business to Business)
- B2C (Business to Consumer) :contentReference[oaicite:6]{index=6}

---

# Conceptos fundamentales

| Concepto | Descripción |
|----------|-------------|
| **Identidad** | Entidad que puede autenticarse (usuario, aplicación o servicio). |
| **Cuenta** | Identidad con información asociada. |
| **Tenant (Inquilino)** | Instancia dedicada de Microsoft Entra ID que representa una organización. |
| **Directorio** | Contenedor donde se almacenan usuarios, grupos y aplicaciones del tenant. |
| **Cuenta Microsoft Entra** | Cuenta profesional o educativa utilizada para acceder a recursos empresariales. |
| **Administrador Global** | Rol con privilegios máximos sobre el tenant. |
| **Dominio personalizado** | Dominio propio de la organización asociado al tenant (por ejemplo, contoso.com). |
| **Cuenta Microsoft (MSA)** | Cuenta personal utilizada para servicios de consumo como Outlook.com, OneDrive o Xbox. | :contentReference[oaicite:7]{index=7}
Conditional Access constituye uno de los principales mecanismos para implementar una arquitectura **Zero Trust** en Microsoft Entra ID. :contentReference[oaicite:7]{index=7}

---

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

---

# Administración de acceso para invitados

Microsoft recomienda utilizar **Microsoft Entra Entitlement Management** para automatizar:

- Solicitudes de acceso.
- Aprobaciones.
- Asignación a grupos.
- Asignación a aplicaciones.
- Revisiones periódicas.
- Expiración automática del acceso.

Esto simplifica la gobernanza de identidades externas y reduce el riesgo asociado a cuentas de invitados olvidadas. 

---

# Microsoft Graph para External ID

Microsoft Graph permite automatizar la administración de identidades externas mediante API.

Entre las capacidades disponibles se incluyen:

- Envío de invitaciones.
- Creación de experiencias personalizadas de incorporación.
- Configuración de políticas entre tenants.
- Administración de colaboración B2B.
- Automatización de flujos de acceso. :contentReference[oaicite:16]{index=16}

---

# Acceso Condicional para usuarios externos

Las políticas de **Conditional Access** también pueden aplicarse a usuarios B2B.

Es posible:

- Requerir MFA.
- Exigir dispositivos compatibles.
- Confiar en la autenticación realizada por el tenant de origen.
- Reutilizar notificaciones de cumplimiento entre organizaciones.

Esto mejora la experiencia del usuario sin reducir el nivel de seguridad. :contentReference[oaicite:17]{index=17}

---

# Organizaciones multiinquilino

Las organizaciones con múltiples tenants pueden utilizar **Cross-Tenant Synchronization** para sincronizar usuarios entre tenants sin necesidad de invitaciones individuales.

Beneficios:

- Mejor experiencia de usuario.
- Menor administración manual.
- Colaboración transparente entre tenants.
- Sincronización automática de identidades. :contentReference[oaicite:18]{index=18}

  
---

---

# Microsoft Entra Identity Protection

**Microsoft Entra Identity Protection** es el servicio de detección y respuesta ante riesgos de identidad de Microsoft Entra ID. Analiza continuamente miles de millones de señales de autenticación para identificar credenciales comprometidas, comportamientos anómalos y posibles ataques, permitiendo responder automáticamente o mediante acciones administrativas. :contentReference[oaicite:0]{index=0}

## Objetivos

Identity Protection permite:

- Detectar riesgos relacionados con identidades.
- Investigar actividades sospechosas.
- Corregir automáticamente riesgos cuando sea posible.
- Integrar el riesgo con Conditional Access.
- Exportar información a soluciones SIEM para investigaciones avanzadas.

---

# Funcionamiento de Identity Protection

El proceso puede resumirse en cinco etapas:

```text
Detección
      ↓
Evaluación del riesgo
      ↓
Investigación
      ↓
Corrección
      ↓
Monitoreo continuo
```

Durante cada inicio de sesión Microsoft Entra evalúa el riesgo del usuario y del inicio de sesión antes de conceder acceso.

---

# Detección de riesgos

Identity Protection utiliza inteligencia de Microsoft basada en el análisis de **billones de señales diarias** provenientes de Microsoft Entra ID, cuentas Microsoft y otros servicios para detectar amenazas relacionadas con identidades.

Algunos ejemplos de detecciones son:

- Uso de direcciones IP anónimas.
- Ataques de Password Spray.
- Credenciales filtradas o comprometidas.

Cada autenticación genera un **nivel de riesgo de inicio de sesión**, que posteriormente puede utilizarse en políticas de acceso condicional. :contentReference[oaicite:1]{index=1}

---

# Informes de Identity Protection

Identity Protection proporciona tres informes principales para investigar incidentes de seguridad.

## Risk Detections

Muestra cada detección individual realizada por el servicio.

Ejemplos:

- Credenciales filtradas.
- IP anónima.
- Password Spray.
- Actividad sospechosa.

---

## Risky Sign-ins

Registra los inicios de sesión considerados riesgosos debido a una o varias detecciones.

Permite conocer:

- Fecha y hora.
- Ubicación.
- Dirección IP.
- Nivel de riesgo.
- Tipo de detección.

---

## Risky Users

Identifica usuarios cuya cuenta presenta evidencia suficiente de compromiso.

Un usuario aparece en este informe cuando:

- Tiene uno o más inicios de sesión riesgosos.
- Existen una o más detecciones de riesgo asociadas. :contentReference[oaicite:2]{index=2}

---

# Corrección automática

Identity Protection puede integrarse con **Conditional Access** para responder automáticamente frente a un riesgo detectado.

Según el nivel de riesgo, Microsoft Entra puede exigir:

- Autenticación Multifactor (MFA).
- Restablecimiento seguro de contraseña.
- Métodos de autenticación más seguros.
- Controles adicionales antes de conceder acceso.

Si el usuario supera correctamente el desafío de seguridad, el riesgo puede marcarse automáticamente como corregido.

---

# Corrección manual

Cuando la remediación automática no está habilitada, un administrador debe revisar los incidentes manualmente.

Las acciones disponibles incluyen:

- Confirmar que el usuario es seguro.
- Confirmar el riesgo.
- Descartar la detección.

Estas acciones pueden realizarse desde:

- Microsoft Entra Admin Center.
- Microsoft Graph API.
- Microsoft 365 Defender. :contentReference[oaicite:3]{index=3}

---

# Integración con otras soluciones

Los datos de Identity Protection pueden exportarse para enriquecer otros sistemas de seguridad.

## Microsoft Graph

Permite consultar mediante API:

- Usuarios en riesgo.
- Inicios de sesión riesgosos.
- Detecciones de riesgo.

Ideal para automatizaciones y desarrollos personalizados.

## Microsoft Sentinel

Los eventos pueden enviarse a Microsoft Sentinel para:

- Correlación de eventos.
- Investigación avanzada.
- Automatización mediante Playbooks.

## Azure Monitor y Log Analytics

También es posible exportar los registros hacia:

- Log Analytics Workspace.
- Azure Storage.
- Event Hubs.
- Plataformas SIEM de terceros.

Esto permite conservar el historial durante períodos prolongados y realizar análisis avanzados. :contentReference[oaicite:4]{index=4}

---

# Roles necesarios

El acceso a Identity Protection depende del rol asignado.

| Rol | Capacidades principales |
|------|-------------------------|
| **Security Administrator** | Administración completa de Identity Protection. |
| **Security Operator** | Investigar riesgos, confirmar o descartar incidentes. |
| **Security Reader** | Acceso de solo lectura a informes. |
| **Global Reader** | Consulta general de la información. |
| **User Administrator** | Restablecimiento de contraseñas de usuarios. |

Los administradores de **Conditional Access** pueden utilizar el riesgo del usuario y del inicio de sesión como condiciones dentro de las políticas de acceso. :contentReference[oaicite:5]{index=5}

---

# Requisitos de licencia

La mayoría de las funcionalidades de Identity Protection requieren **Microsoft Entra ID P2**.

Entre las características exclusivas de P2 se encuentran:

- Directivas basadas en riesgo.
- Informes completos de usuarios en riesgo.
- Informes completos de inicios de sesión riesgosos.
- Detecciones de riesgo detalladas.
- Alertas automáticas.
- Resúmenes semanales.
- Políticas de registro de MFA.

Las licencias **Free** y **P1** proporcionan únicamente información limitada sobre los riesgos detectados. :contentReference[oaicite:6]{index=6}

---

# Flujo de trabajo de Identity Protection

```text
Inicio de sesión
        │
        ▼
Análisis de señales de riesgo
        │
        ▼
Cálculo del nivel de riesgo
        │
        ▼
Generación de:
• Risk Detection
• Risky Sign-in
• Risky User
        │
        ▼
Conditional Access
        │
 ┌──────┴────────┐
 │               │
Permitir     Solicitar MFA
 │               │
 │        Restablecer contraseña
 │               │
 └──────┬────────┘
        ▼
Corrección automática o manual
```

---

# Microsoft Entra Connect

**Microsoft Entra Connect** es la solución de Microsoft para implementar una **identidad híbrida**, sincronizando usuarios, grupos y credenciales entre **Active Directory local** y **Microsoft Entra ID**, permitiendo que los usuarios utilicen una única identidad tanto para recursos locales como en la nube.

## Características principales

Microsoft Entra Connect integra varias funcionalidades en una única solución:

### Password Hash Synchronization (PHS)

Sincroniza el **hash** de la contraseña del usuario desde Active Directory hacia Microsoft Entra ID.

**Ventajas**

- Inicio de sesión con la misma contraseña.
- No requiere infraestructura adicional.
- Método recomendado para la mayoría de las organizaciones.

---

### Pass-Through Authentication (PTA)

Permite validar la contraseña directamente contra Active Directory local.

Características:

- La contraseña nunca se almacena en Microsoft Entra ID.
- No requiere implementar una infraestructura de federación.
- Proporciona una experiencia de inicio de sesión unificada.

---

### Integración con AD FS (Federación)

Microsoft Entra Connect puede configurar automáticamente un entorno federado utilizando **Active Directory Federation Services (AD FS)**.

Incluye:

- Configuración inicial.
- Administración de certificados.
- Implementación de nuevos servidores AD FS.
- Administración del entorno federado.

---

### Sincronización de directorios

Permite sincronizar automáticamente:

- Usuarios
- Grupos
- Contactos
- Objetos del directorio
- Hashes de contraseñas

Garantizando que la información permanezca consistente entre Active Directory y Microsoft Entra ID.

---

### Microsoft Entra Connect Health

Incluye monitoreo continuo del entorno híbrido.

Permite supervisar:

- Estado de sincronización.
- Estado de AD FS.
- Rendimiento.
- Disponibilidad.
- Alertas.
- Tendencias de uso.

Toda la información se centraliza en el **Microsoft Entra Admin Center**.

---

# ¿Por qué utilizar Microsoft Entra Connect?

Implementar una identidad híbrida ofrece numerosos beneficios:

- Una única identidad para recursos locales y en la nube.
- Inicio de sesión único (SSO).
- Integración transparente con Microsoft 365.
- Administración centralizada de identidades.
- Sincronización automática de usuarios y grupos.
- Compatibilidad con múltiples escenarios híbridos.

---

# Microsoft Entra Connect Health

Microsoft Entra Connect Health proporciona una supervisión continua de la infraestructura de identidad local para garantizar la disponibilidad de los servicios de autenticación.

## Funcionalidades

- Supervisión de AD FS.
- Supervisión de Azure AD Connect.
- Supervisión de Domain Services.
- Panel centralizado de salud.
- Alertas automáticas.
- Métricas de rendimiento.
- Estadísticas de utilización.

---

## Beneficios

### Seguridad

- Detección de bloqueos de extranet.
- Informes de errores de autenticación.
- Cumplimiento de requisitos de privacidad.

### Monitoreo

- Estado de servidores.
- Disponibilidad.
- Conectividad.
- Rendimiento.
- Mantenimiento preventivo.

### Facilidad de administración

- Instalación rápida mediante agentes.
- Actualización automática.
- Datos disponibles pocos minutos después de instalar el agente.

### Analítica

Permite conocer:

- Aplicaciones más utilizadas.
- Solicitudes de tokens.
- Ubicaciones de conexión.
- Estadísticas de autenticación.

---

# Microsoft Entra Cloud Sync

**Microsoft Entra Cloud Sync** es la evolución de la sincronización híbrida basada en agentes ligeros administrados desde la nube.

En lugar de depender completamente de Microsoft Entra Connect, utiliza un **Cloud Provisioning Agent** que simplifica la implementación y la administración.

---

## Ventajas de Cloud Sync

### Agentes ligeros

No requiere instalar un servidor completo de sincronización.

Los agentes únicamente actúan como puente entre:

```text
Active Directory
        │
Provisioning Agent
        │
Microsoft Entra ID
```

---

### Alta disponibilidad

Es posible instalar varios agentes activos simultáneamente para ofrecer:

- Alta disponibilidad.
- Balanceo.
- Mayor tolerancia a fallos.

---

### Soporte para múltiples bosques

Cloud Sync permite escenarios que anteriormente eran complejos, como:

- Múltiples bosques.
- Bosques desconectados.
- Fusiones y adquisiciones.
- Organizaciones con infraestructuras independientes.

---

### Grupos grandes

Admite grupos con hasta **50.000 miembros** utilizando reglas de sincronización optimizadas.

---

# ¿Cómo funciona Cloud Sync?

Toda la configuración de sincronización reside en Microsoft Entra ID.

Los agentes locales únicamente ejecutan las operaciones necesarias.

```text
Microsoft Entra ID
        │
Configuración de sincronización
        │
Provisioning Agent
        │
Active Directory
```

Esto reduce considerablemente la complejidad administrativa.

---

# Comparación: Microsoft Entra Connect vs Cloud Sync

| Característica | Entra Connect | Cloud Sync |
|----------------|---------------|------------|
| Sincronización de usuarios | ✅ | ✅ |
| Sincronización de grupos | ✅ | ✅ |
| Sincronización de contactos | ✅ | ✅ |
| Password Hash Sync | ✅ | ✅ |
| Pass-Through Authentication | ✅ | ❌ |
| Federación (AD FS) | ✅ | ✅ |
| Agente ligero | ❌ | ✅ |
| Múltiples agentes activos | ❌ | ✅ |
| Bosques desconectados | ❌ | ✅ |
| Aprovisionamiento bajo demanda | ❌ | ✅ |
| Sincronización de dispositivos | ✅ | ❌ |
| LDAP | ✅ | ❌ |
| Personalización avanzada de flujos | ✅ | Limitada |

---

# ¿Cuándo utilizar cada solución?

## Microsoft Entra Connect

Recomendado cuando:

- Se requiere **Pass-Through Authentication (PTA)**.
- Se utilizan directorios LDAP.
- Se necesitan sincronizaciones avanzadas.
- Se requiere una personalización compleja de atributos.
- Se sincronizan dispositivos.

---

## Microsoft Entra Cloud Sync

Recomendado cuando:

- Se desea una implementación sencilla.
- Se requieren múltiples agentes para alta disponibilidad.
- Existen varios bosques de Active Directory desconectados.
- Se busca minimizar la infraestructura local.
- Se prioriza una administración centralizada desde la nube.

---

# Resumen

| Microsoft Entra Connect | Microsoft Entra Cloud Sync |
|--------------------------|----------------------------|
| Solución híbrida tradicional | Solución híbrida moderna |
| Mayor cantidad de funcionalidades | Arquitectura simplificada |
| Más opciones de personalización | Menor complejidad |
| Requiere servidor de sincronización | Utiliza agentes ligeros |
| Ideal para escenarios complejos | Ideal para implementaciones modernas |

---
---

# Opciones de autenticación en Microsoft Entra ID

La autenticación es el componente más importante de una solución de identidad híbrida, ya que controla el acceso a aplicaciones, recursos y datos tanto locales como en la nube.

La elección del método de autenticación impacta directamente en:

- Seguridad.
- Experiencia del usuario.
- Complejidad de implementación.
- Infraestructura necesaria.
- Continuidad del negocio.
- Costos de operación.

Microsoft Entra Connect es la herramienta encargada de implementar el método de autenticación seleccionado.

---

# Métodos de autenticación

Microsoft Entra soporta tres modelos principales de autenticación:

## 1. Password Hash Synchronization (PHS)

La sincronización de hash de contraseña es el método recomendado para la mayoría de las organizaciones.

### ¿Cómo funciona?

- El hash de la contraseña del usuario se sincroniza desde Active Directory hacia Microsoft Entra ID.
- La autenticación ocurre completamente en la nube.
- El usuario mantiene la misma contraseña local.

```text
Usuario
    │
Microsoft Entra ID
    │
Hash sincronizado desde AD
```

### Ventajas

- Implementación muy sencilla.
- No requiere infraestructura adicional.
- Alta disponibilidad.
- Excelente recuperación ante desastres.
- Compatible con Single Sign-On.
- Compatible con Windows Hello for Business.
- Requerido por Microsoft Entra Identity Protection.
- Requerido por Microsoft Entra Domain Services.

### Consideraciones

- Los cambios de estado de una cuenta (deshabilitada, bloqueada, etc.) no son inmediatos.
- La sincronización ocurre aproximadamente cada dos minutos.
- Puede ejecutarse manualmente un ciclo de sincronización.

---

## 2. Pass-Through Authentication (PTA)

Permite validar la contraseña directamente contra Active Directory local sin almacenarla en la nube.

```text
Usuario
      │
Microsoft Entra ID
      │
Authentication Agent
      │
Active Directory
```

### Características

- La contraseña nunca se almacena en Microsoft Entra.
- Requiere agentes ligeros.
- Microsoft recomienda instalar al menos tres agentes.
- Toda la comunicación se encuentra cifrada.

### Ventajas

- Se aplican inmediatamente las políticas locales.
- Se respetan:

- Cuenta deshabilitada.
- Cuenta bloqueada.
- Contraseña expirada.
- Horarios de inicio de sesión.
- Directivas de Active Directory.

### Recomendaciones

- Implementar múltiples agentes para alta disponibilidad.
- Mantener Password Hash Synchronization como método de respaldo.

---

## 3. Autenticación Federada (AD FS)

En este modelo Microsoft Entra delega completamente la autenticación a un proveedor de identidad externo.

Generalmente:

- Active Directory Federation Services (AD FS)

```text
Usuario
      │
Microsoft Entra ID
      │
AD FS
      │
Active Directory
```

### Ventajas

- Máxima flexibilidad.
- Compatible con MFA externo.
- Compatible con proveedores de identidad personalizados.
- Permite autenticación mediante:

- DOMAIN\usuario
- sAMAccountName
- Certificados
- Smart Cards
- Métodos propietarios

### Escenarios donde se utiliza

- Organizaciones con infraestructura AD FS existente.
- Requerimientos regulatorios.
- MFA externo.
- Sistemas de autenticación heredados.
- Inicio de sesión personalizado.

### Consideraciones

- Mayor costo.
- Mayor complejidad.
- Requiere infraestructura redundante.
- Requiere servidores AD FS.
- Requiere Web Application Proxy (WAP).
- Requiere certificados TLS.
- Mayor mantenimiento.

---

# Comparación de los métodos

| Característica | Password Hash Sync | Pass-Through | Federación AD FS |
|---------------|-------------------|--------------|------------------|
| Autenticación | Nube | AD local mediante agente | AD FS |
| Contraseña almacenada en la nube | Hash | No | No |
| Infraestructura adicional | No | Agentes | AD FS + WAP |
| Alta disponibilidad | Muy alta | Alta | Depende de la infraestructura |
| Complejidad | Baja | Media | Alta |
| Costo | Bajo | Medio | Alto |
| Recomendado por Microsoft | ✅ | ✅ | Solo casos especiales |

---

# Comparación técnica

| Característica | Password Hash Sync | Pass-Through | Federación |
|---------------|-------------------|--------------|------------|
| Password Hash Sync | ✅ | Opcional (recomendado) | Opcional (recomendado) |
| Single Sign-On | ✅ | ✅ | ✅ |
| MFA Microsoft Entra | ✅ | ✅ | ✅ |
| MFA externo | ❌ | ❌ | ✅ |
| Smart Lockout | ✅ | ✅ | Mediante AD FS |
| Identity Protection | ✅ | ✅ (requiere PHS) | ✅ (requiere PHS) |
| Windows Hello for Business | ✅ | ✅ | ✅ |
| Certificados / Smart Cards | ✅ | ✅ | ✅ |
| sAMAccountName | ❌ | ❌ | ✅ |

---

# Arquitectura requerida

## Password Hash Synchronization

La arquitectura más simple.

```text
Active Directory
        │
Entra Connect
        │
Microsoft Entra ID
```

No requiere servidores adicionales.

---

## Pass-Through Authentication

```text
Active Directory
        │
Authentication Agent
        │
Microsoft Entra ID
```

Microsoft recomienda instalar varios agentes para redundancia.

---

## Federación

Arquitectura más compleja.

```text
Usuario

        │

Internet

        │

Web Application Proxy

        │

AD FS

        │

Active Directory
```

Requiere:

- AD FS.
- WAP.
- Balanceadores.
- Certificados.
- DNS.
- Infraestructura redundante.

---

# Continuidad del negocio

Microsoft recomienda mantener habilitada **Password Hash Synchronization** incluso cuando se utilice Pass-Through Authentication o AD FS.

Beneficios:

- Recuperación ante desastres.
- Conmutación manual al método de respaldo.
- Mayor disponibilidad.
- Protección frente a fallos de la infraestructura local.

---

# Identity Protection y Password Hash Sync

Microsoft recomienda habilitar siempre Password Hash Synchronization porque Identity Protection utiliza esa información para:

- Detectar credenciales filtradas.
- Detectar cuentas comprometidas.
- Comparar contraseñas con bases de datos de credenciales expuestas.
- Generar informes de riesgo.

Esta funcionalidad requiere Microsoft Entra ID P2.

---

# Recomendaciones de Microsoft

En la mayoría de los escenarios Microsoft recomienda utilizar **Password Hash Synchronization (PHS)** debido a:

- Menor complejidad.
- Mayor disponibilidad.
- Mejor recuperación ante desastres.
- Integración completa con Identity Protection.
- Compatibilidad con Microsoft Entra Domain Services.
- Implementación rápida.
- Menor costo operativo.

**Pass-Through Authentication (PTA)** es recomendable cuando se requiere aplicar inmediatamente las políticas de Active Directory sin almacenar hashes de contraseña en la nube.

**Federación con AD FS** debe reservarse para escenarios específicos donde existan requisitos avanzados, como integración con proveedores de autenticación externos, autenticación mediante `DOMAIN\usuario`, soluciones heredadas o necesidades regulatorias que no pueden resolverse con los métodos de autenticación en la nube.


Este flujo muestra cómo **Microsoft Entra Identity Protection** trabaja junto con **Conditional Access** para implementar un modelo de seguridad **Zero Trust**, evaluando continuamente el riesgo antes de conceder acceso a los recursos corporativos.

---

# Implementación de métodos de autenticación en Microsoft Entra ID

Microsoft Entra ID ofrece tres métodos principales para autenticar usuarios en entornos híbridos. Cada uno responde a diferentes necesidades de seguridad, infraestructura y experiencia de usuario.

## Password Hash Synchronization (PHS)

La **Sincronización de Hash de Contraseñas (PHS)** es el método de autenticación recomendado por Microsoft para la mayoría de las organizaciones.

Microsoft Entra Connect sincroniza el **hash criptográfico** de la contraseña desde Active Directory hacia Microsoft Entra ID, permitiendo que los usuarios utilicen las mismas credenciales tanto en entornos locales como en la nube.

### Beneficios

- Un único usuario y contraseña para recursos locales y cloud.
- Reduce llamadas al Service Desk relacionadas con contraseñas.
- Mejora la experiencia del usuario.
- No requiere infraestructura adicional.
- Alta disponibilidad como servicio cloud.
- Compatible con Single Sign-On (SSO).
- Compatible con Windows Hello for Business.
- Requisito para Microsoft Entra Domain Services.
- Requisito para Microsoft Entra Identity Protection.

### Seguridad

PHS permite que **Identity Protection** detecte credenciales comprometidas.

Microsoft compara continuamente las credenciales sincronizadas con bases de datos de credenciales filtradas obtenidas de:

- Dark Web.
- Investigadores de seguridad.
- Organismos gubernamentales.
- Agencias de aplicación de la ley.

Si encuentra coincidencias, el usuario se marca automáticamente como **High Risk User**.

> **Importante:** solo se analizan credenciales filtradas detectadas después de habilitar Password Hash Synchronization.

### Requisitos

Para utilizar Password Hash Synchronization se debe:

1. Instalar Microsoft Entra Connect.
2. Configurar la sincronización entre Active Directory y Microsoft Entra ID.
3. Habilitar Password Hash Synchronization.

### Casos de uso

Ideal cuando:

- Se desea la implementación más sencilla.
- No existen requisitos regulatorios especiales.
- Se busca minimizar la infraestructura local.
- Se desea aprovechar Identity Protection y Azure AD Domain Services.

---

# Pass-Through Authentication (PTA)

La **Autenticación de Paso a Través (PTA)** permite validar las credenciales directamente contra Active Directory local sin almacenar la contraseña en la nube.

```text
Usuario
      │
Microsoft Entra ID
      │
PTA Agent
      │
Active Directory
```

Durante el proceso:

1. El usuario intenta autenticarse.
2. Microsoft Entra envía la solicitud al agente.
3. El agente valida la contraseña contra Active Directory.
4. Devuelve únicamente el resultado de la autenticación.

La contraseña nunca abandona el entorno local.

---

## Ventajas

### Mejor experiencia de usuario

- Misma contraseña para aplicaciones locales y cloud.
- Menos llamadas al soporte técnico.
- Compatible con Self-Service Password Reset (SSPR).

### Fácil implementación

- Solo requiere instalar un agente ligero.
- No necesita AD FS.
- No necesita infraestructura DMZ.
- Actualizaciones automáticas del agente.

### Seguridad

- Las contraseñas nunca se almacenan en Microsoft Entra.
- Compatible con:
  - Conditional Access.
  - Multi-Factor Authentication (MFA).
  - Smart Lockout.
  - Bloqueo de autenticación heredada.
- Comunicación protegida mediante certificados.
- El agente solo realiza conexiones salientes hacia Microsoft Entra ID.

### Alta disponibilidad

Es posible instalar múltiples agentes para garantizar la continuidad del servicio.

Microsoft recomienda instalar **tres agentes** para alta disponibilidad.

---

## Características compatibles

- Aplicaciones web.
- Microsoft 365.
- Microsoft Office con autenticación moderna.
- User Principal Name (UPN).
- Login ID alternativo.
- Single Sign-On.
- Self-Service Password Reset.
- Password Writeback.
- Password Protection.
- Ambientes con múltiples bosques de Active Directory.
- Conditional Access.
- MFA.
- Sincronización mediante Microsoft Entra Connect.

---

## Casos de uso

PTA resulta ideal cuando la organización necesita:

- Mantener las contraseñas únicamente en Active Directory.
- Aplicar inmediatamente las políticas locales.
- Evitar implementar una infraestructura federada.
- Reducir costos operativos.

---

# Federación con Microsoft Entra ID

La **Federación** delega completamente el proceso de autenticación a un proveedor de identidad externo.

Generalmente se implementa mediante:

- Active Directory Federation Services (AD FS).
- PingFederate.

```text
Usuario
      │
Microsoft Entra ID
      │
AD FS
      │
Active Directory
```

Microsoft Entra únicamente confía en el resultado de autenticación devuelto por AD FS.

---

## Características

- Toda la autenticación permanece on-premises.
- Soporta autenticación y autorización.
- Compatible con MFA externo.
- Compatible con Smart Cards.
- Compatible con certificados.
- Compatible con Login ID alternativo.
- Compatible con sAMAccountName.
- Compatible con proveedores de identidad externos.

---

## Funcionalidades de Microsoft Entra Connect para AD FS

Microsoft Entra Connect simplifica la administración de AD FS permitiendo:

### Implementación

- Crear una nueva granja AD FS.
- Utilizar una granja existente.
- Configurar dominios federados.

### Administración

- Reparar relaciones de confianza.
- Agregar nuevos servidores AD FS.
- Incorporar servidores Web Application Proxy (WAP).
- Agregar nuevos dominios federados.
- Renovar certificados TLS/SSL.
- Renovar certificados de federación.

### Personalización

- Agregar logotipo corporativo.
- Personalizar la página de inicio de sesión.
- Modificar mensajes.
- Configurar Login ID alternativo.
- Editar reglas de Claims (Claims Rules).

### Escenarios avanzados

- Federar múltiples tenants de Microsoft Entra con una única granja AD FS.
- Integración con proveedores de autenticación externos.
- Compatibilidad con infraestructuras híbridas complejas.

---

# Comparación de los métodos de autenticación

| Característica | Password Hash Sync | Pass-Through Authentication | Federación (AD FS) |
|----------------|-------------------|-----------------------------|--------------------|
| Autenticación | En la nube | Validación en Active Directory mediante agente | Totalmente local |
| Contraseña almacenada en la nube | Hash | No | No |
| Infraestructura adicional | Ninguna | Agentes ligeros | AD FS + WAP + Balanceadores |
| Complejidad | Baja | Media | Alta |
| Coste operativo | Bajo | Medio | Alto |
| Alta disponibilidad | Muy alta | Alta | Depende de la infraestructura |
| MFA Microsoft Entra | Sí | Sí | Sí |
| MFA externo | No | No | Sí |
| Identity Protection | Sí | Sí (requiere PHS) | Sí (requiere PHS) |
| Single Sign-On | Sí | Sí | Sí |
| Windows Hello for Business | Sí | Sí | Sí |
| sAMAccountName | No | No | Sí |

---

# Recomendaciones de Microsoft

Microsoft recomienda habilitar siempre **Password Hash Synchronization**, incluso cuando se utilice **Pass-Through Authentication** o **Federación**, por los siguientes motivos:

- Actúa como mecanismo de recuperación ante desastres.
- Permite cambiar rápidamente al inicio de sesión en la nube si falla la infraestructura local.
- Es un requisito para Microsoft Entra Identity Protection.
- Habilita la detección de credenciales filtradas.
- Reduce el impacto de incidentes como ransomware o caídas de Active Directory.

En la mayoría de los escenarios empresariales, **Password Hash Synchronization** constituye la opción preferida por su simplicidad, resiliencia y menor costo operativo. **Pass-Through Authentication** es adecuada cuando se necesita aplicar inmediatamente las políticas de Active Directory sin almacenar hashes en la nube, mientras que **Federación con AD FS** debe reservarse para organizaciones con requisitos avanzados de autenticación o inversiones existentes en infraestructura federada.

---

# Autenticación en Microsoft Entra ID

La autenticación es el componente central de Microsoft Entra ID para validar la identidad de los usuarios cuando acceden a dispositivos, aplicaciones y servicios. Más allá de validar usuario y contraseña, Microsoft Entra incorpora múltiples mecanismos para mejorar la seguridad, reducir la dependencia del soporte técnico y ofrecer una mejor experiencia al usuario.

## Componentes principales

* **Self-Service Password Reset (SSPR)** para restablecimiento y cambio de contraseñas.
* **Microsoft Entra Multi-Factor Authentication (MFA)**.
* **Password Writeback** para sincronizar cambios de contraseña hacia Active Directory local.
* **Password Protection** para aplicar políticas de contraseñas también en entornos híbridos.
* **Autenticación sin contraseña (Passwordless)** mediante tecnologías modernas.

---

# Self-Service Password Reset (SSPR)

El autoservicio de restablecimiento de contraseña permite que los usuarios administren sus propias credenciales sin depender del departamento de TI.

## Escenarios soportados

* Cambio voluntario de contraseña.
* Restablecimiento cuando el usuario olvidó su contraseña.
* Desbloqueo de cuentas bloqueadas.

## Beneficios

* Reduce llamadas al Service Desk.
* Disminuye tiempos de inactividad.
* Permite Password Writeback para sincronizar automáticamente la nueva contraseña hacia Active Directory local.
* Mejora la continuidad operativa.

---

# Microsoft Entra Multi-Factor Authentication (MFA)

La autenticación multifactor incrementa la seguridad solicitando dos o más factores de autenticación.

## Factores soportados

### Algo que sabes

* Contraseña
* PIN

### Algo que tienes

* Microsoft Authenticator
* Código SMS
* Llamada telefónica
* Token OATH
* Llave FIDO2

### Algo que eres

* Huella digital
* Reconocimiento facial
* Biometría

---

## Beneficios

* Reduce el riesgo ante robo de credenciales.
* Compatible con Conditional Access.
* Compatible con Passwordless Authentication.
* Compatible con Self-Service Password Reset.
* Permite múltiples métodos de autenticación de respaldo.

---

# Implementación de MFA mediante Conditional Access

Microsoft recomienda habilitar MFA utilizando **Conditional Access** en lugar de habilitar MFA usuario por usuario.

## Flujo de implementación

1. Crear una nueva política de Conditional Access.
2. Seleccionar usuarios o grupos (ejemplo: MFA-Test-Group).
3. Seleccionar las aplicaciones protegidas.
4. Configurar los controles de acceso.
5. Requerir Multi-Factor Authentication.
6. Habilitar la política.
7. Probar el inicio de sesión con un usuario de prueba.

---

## Componentes de una política

### Asignaciones

* Usuarios
* Grupos
* Aplicaciones
* Workload Identities

### Condiciones

* Aplicaciones en la nube
* Riesgo
* Plataforma
* Ubicación
* Cliente

### Controles

* Requerir MFA
* Dispositivo conforme
* Aplicación aprobada
* Password Change
* Session Controls

---

# Configuración avanzada de MFA

Microsoft Entra permite personalizar múltiples aspectos de MFA.

## Bloqueo de cuentas

Permite bloquear temporalmente usuarios tras múltiples intentos fallidos.

Configuraciones disponibles:

* Número máximo de intentos.
* Tiempo hasta reiniciar contador.
* Tiempo de desbloqueo automático.

---

## Bloqueo manual de usuarios

Un administrador puede:

* Bloquear usuarios.
* Desbloquear usuarios.
* Registrar el motivo del bloqueo.

Muy útil cuando un dispositivo fue robado o comprometido.

---

## Reportar actividad sospechosa

Los usuarios pueden indicar que una solicitud MFA fue fraudulenta utilizando Microsoft Authenticator.

Cuando esto ocurre:

* Identity Protection marca al usuario como **High Risk**.
* Se generan eventos de auditoría.
* Aparecen registros en:

  * Risk Detections
  * Sign-in Logs
  * Audit Logs

Posteriormente un administrador puede investigar y remediar el incidente.

---

## Alertas y notificaciones

Microsoft Entra puede enviar correos electrónicos automáticos cuando:

* Un usuario reporta fraude.
* Se detectan actividades sospechosas.
* Se producen eventos de riesgo.

---

# Tokens OATH

Microsoft Entra soporta tokens hardware basados en:

* OATH TOTP
* SHA-1

Características:

* Código renovado cada 30 o 60 segundos.
* Importación masiva mediante CSV.
* Activación desde Azure Portal.
* Compatible con hasta cinco métodos MFA por usuario.

Formato del archivo CSV:

```
UPN
Número de serie
Clave secreta
Intervalo
Fabricante
Modelo
```

---

# Passwordless Authentication

Microsoft Entra permite eliminar completamente el uso de contraseñas.

Métodos soportados:

* Windows Hello for Business
* FIDO2 Security Keys
* Microsoft Authenticator

## Beneficios

* Mayor seguridad.
* Eliminación de ataques de phishing basados en contraseña.
* Mejor experiencia de usuario.
* Reducción del soporte técnico.

---

# Integración híbrida de autenticación

Microsoft Entra permite mantener sincronizados los entornos híbridos mediante:

* Password Writeback
* Password Protection
* Password Hash Synchronization
* Pass-through Authentication
* Federation con AD FS

Esto permite que los usuarios mantengan una identidad unificada tanto para recursos locales como para recursos en la nube.

---

# Buenas prácticas recomendadas

Microsoft recomienda:

* Implementar MFA mediante Conditional Access.
* Habilitar Self-Service Password Reset.
* Configurar Password Writeback.
* Habilitar Passwordless Authentication cuando sea posible.
* Permitir múltiples métodos MFA por usuario.
* Supervisar continuamente los eventos de riesgo mediante Identity Protection.
* Investigar reportes de fraude generados por los usuarios.
* Utilizar grupos piloto antes de desplegar MFA a toda la organización.
* Integrar Identity Protection con Microsoft Sentinel o un SIEM para automatizar la respuesta a incidentes.

---

## Relación con otros componentes de Microsoft Entra

La autenticación se integra directamente con:

* Microsoft Entra Identity Protection
* Conditional Access
* Microsoft Entra Connect
* Microsoft Entra Cloud Sync
* Password Hash Synchronization
* Pass-through Authentication
* Federation (AD FS)
* External Identities (B2B)
* Windows Hello for Business
* FIDO2 Security Keys
* Microsoft Authenticator
* Microsoft Sentinel
* Microsoft Graph API

Este conjunto de funcionalidades constituye la base del modelo de **Zero Trust**, donde cada autenticación es evaluada dinámicamente considerando la identidad, el riesgo, el dispositivo, la ubicación y el contexto antes de conceder acceso a los recursos corporativos.



