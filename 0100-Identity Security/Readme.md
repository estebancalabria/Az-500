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

