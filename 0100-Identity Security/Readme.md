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

Conditional Access constituye uno de los principales mecanismos para implementar una arquitectura **Zero Trust** en Microsoft Entra ID. :contentReference[oaicite:7]{index=7}
