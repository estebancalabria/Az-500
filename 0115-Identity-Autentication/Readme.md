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


# Resumen – Autenticación e Identidad Híbrida en Microsoft Entra ID

## Sincronización de hash de contraseñas (Password Hash Synchronization - PHS)

La **Sincronización de Hash de Contraseñas (PHS)** es el método de autenticación híbrida más simple de Microsoft Entra. Microsoft Entra Connect sincroniza el **hash del hash** de la contraseña desde Active Directory local hacia Microsoft Entra ID; nunca se sincroniza la contraseña en texto plano.

### Funcionamiento

1. El usuario cambia la contraseña en Active Directory.
2. Microsoft Entra Connect calcula un hash adicional.
3. Ese hash se sincroniza con Microsoft Entra ID.
4. El usuario inicia sesión en Microsoft 365 o Azure usando la misma contraseña del dominio.

### Ventajas

* Un único usuario y contraseña para nube y entorno local (SSO parcial).
* Reduce llamadas al Help Desk.
* Fácil implementación.
* Compatible con Microsoft 365 y Azure.
* Permite detección de credenciales filtradas (Identity Protection).

### Credenciales filtradas

Microsoft compara credenciales expuestas en filtraciones públicas con las cuentas sincronizadas.

Si encuentra coincidencias:

* La cuenta pasa a estado **High Risk**.
* Puede requerirse MFA.
* Puede bloquearse mediante Identity Protection.

> Solo analiza filtraciones descubiertas después de habilitar PHS.

### También puede utilizarse como respaldo

Aunque la organización utilice **AD FS**, Microsoft recomienda habilitar PHS como mecanismo de recuperación si la infraestructura de federación falla.

### Requisitos

* Microsoft Entra Connect.
* Sincronización de directorios.
* Habilitar Password Hash Synchronization.

---

# Pass-through Authentication (PTA)

La **Autenticación de Paso a Través** valida la contraseña directamente contra el Active Directory local.

La contraseña:

* Nunca se almacena en la nube.
* Nunca se sincroniza.

Microsoft Entra únicamente reenvía la solicitud al agente instalado on-premises.

## Flujo

Usuario → Microsoft Entra → Agente PTA → Active Directory → Validación → Acceso

## Ventajas

### Excelente experiencia de usuario

* Una única contraseña.
* Menos llamadas al soporte.
* Compatible con SSPR.

### Fácil implementación

* Solo requiere instalar un agente.
* Sin abrir puertos entrantes.
* Solo conexiones salientes.

### Seguridad

* La contraseña nunca sale del entorno local.
* Compatible con:

  * MFA
  * Conditional Access
  * Password Protection
  * Bloqueo de autenticación heredada

### Alta disponibilidad

Puede instalar múltiples agentes PTA para evitar un punto único de falla.

### Características

* Compatible con Office moderno.
* Compatible con múltiples bosques.
* Compatible con Writeback de contraseñas.
* Gratuito.
* Configurable desde Microsoft Entra Connect.

---

# Federación con Microsoft Entra ID (AD FS)

La federación permite que **toda la autenticación ocurra en el entorno local**.

Microsoft Entra nunca valida la contraseña.

La validación la realiza AD FS u otro proveedor de identidad como PingFederate.

## Flujo

Usuario → Microsoft Entra → AD FS → Active Directory → Token → Microsoft Entra

## Cuándo utilizarlo

Cuando la organización necesita:

* Políticas de autenticación complejas.
* Smart Cards.
* Requisitos regulatorios.
* Métodos personalizados de autenticación.

## Ventajas

* Control total del proceso de autenticación.
* Inicio de sesión único.
* Compatible con múltiples dominios.
* Puede coexistir con Password Hash Sync como respaldo.

## Microsoft Entra Connect permite

* Instalar AD FS.
* Configurar granjas.
* Reparar relaciones de confianza.
* Agregar servidores.
* Agregar Web Application Proxy.
* Renovar certificados.
* Agregar dominios federados.
* Personalizar página de login.
* Modificar reglas de Claims.

---

# Comparación de métodos híbridos

| Característica          | PHS               | PTA                    | Federación                |
| ----------------------- | ----------------- | ---------------------- | ------------------------- |
| Contraseña en nube      | Hash sincronizado | No                     | No                        |
| Validación              | Microsoft Entra   | Active Directory       | AD FS                     |
| Requiere servidores     | No                | Agente ligero          | Sí                        |
| Fácil implementación    | ⭐⭐⭐⭐⭐             | ⭐⭐⭐⭐                   | ⭐⭐                        |
| Alta disponibilidad     | Sí                | Sí (múltiples agentes) | Depende de AD FS          |
| Compatible con MFA      | Sí                | Sí                     | Sí                        |
| Recomendación Microsoft | ✅ Sí              | Sí                     | Solo cuando sea necesario |

---

# Autenticación en Microsoft Entra ID

La autenticación en Microsoft Entra va mucho más allá de usuario y contraseña. Incluye mecanismos modernos para mejorar la seguridad y simplificar la experiencia del usuario.

## Componentes principales

* Self-Service Password Reset (SSPR).
* Microsoft Entra MFA.
* Password Writeback.
* Password Protection para Active Directory local.
* Autenticación sin contraseña.

## Beneficios

* Reduce llamadas al Help Desk.
* Mejora la experiencia del usuario.
* Permite autenticación moderna.
* Mayor seguridad frente a ataques de contraseñas.

---

# Self-Service Password Reset (SSPR)

Permite que el usuario administre su propia contraseña sin intervención del administrador.

## Escenarios

### Cambio de contraseña

El usuario conoce la contraseña y desea modificarla.

### Restablecimiento

Olvidó la contraseña.

### Desbloqueo de cuenta

La cuenta quedó bloqueada.

## Password Writeback

Cuando existe integración híbrida:

Microsoft Entra escribe la nueva contraseña nuevamente hacia Active Directory local.

Esto mantiene sincronizados ambos entornos.

Beneficios:

* Una sola contraseña.
* Sincronización inmediata.
* Compatible con aplicaciones locales.

---

# Microsoft Entra Multi-Factor Authentication (MFA)

MFA requiere **dos o más factores** de autenticación.

## Factores posibles

### Algo que sabes

* Contraseña.

### Algo que tienes

* Celular.
* Token.
* Llave FIDO2.

### Algo que eres

* Huella.
* Reconocimiento facial.
* Iris.

Mientras más factores se utilizan, menor es la posibilidad de compromiso de la cuenta.

---

# Conditional Access + MFA

La mejor práctica es exigir MFA mediante **Conditional Access**.

Las políticas pueden aplicarse según:

* Usuarios.
* Grupos.
* Aplicaciones.
* Ubicación.
* Riesgo.
* Dispositivo.

## Laboratorio básico

Crear política:

Protection → Conditional Access → New Policy

Nombre:

Piloto MFA

Asignar:

Grupo MFA-Test-Group

Cloud Apps:

Azure Service Management API

Grant:

Require Multi-Factor Authentication

Enable:

On

Al iniciar sesión:

* Aplicaciones comunes → sin MFA.
* Aplicación protegida → solicita MFA.

---

# Configuración de MFA

Desde:

Microsoft Entra ID

→ Security

→ Multi-Factor Authentication

Se pueden configurar:

* Bloqueo de cuentas.
* Bloqueo manual de usuarios.
* Alertas de fraude.
* Actividad sospechosa.
* Notificaciones por correo.
* Tokens OATH.
* Configuración telefónica.

---

# Bloqueo de cuentas

Configurable:

* Intentos fallidos permitidos.
* Tiempo para desbloqueo automático.
* Reinicio del contador.

---

# Bloquear usuarios

Si un dispositivo fue robado:

Security

→ MFA

→ Block Users

El usuario no podrá autenticarse mediante MFA hasta ser desbloqueado.

---

# Reportar actividad sospechosa

Si un usuario recibe una solicitud MFA que no inició:

Puede reportarla desde Microsoft Authenticator.

Consecuencias:

* Usuario marcado como **High Risk**.
* Identity Protection genera alertas.
* Puede activarse una política basada en riesgo.

Los eventos aparecen en:

* Sign-in Logs.
* Audit Logs.
* Identity Protection.

---

# Tokens OATH

Microsoft Entra soporta tokens físicos OATH TOTP.

Formato CSV:

* UPN.
* Número de serie.
* Clave secreta.
* Intervalo.
* Fabricante.
* Modelo.

El administrador:

1. Importa el CSV.
2. Activa el token.
3. Valida OTP.

Cada usuario puede tener hasta cinco tokens OATH o aplicaciones Authenticator.

---

# Kerberos

Kerberos es el protocolo de autenticación predeterminado en entornos **Active Directory**.

Implementa Kerberos v5 y utiliza el **Key Distribution Center (KDC)** integrado en los controladores de dominio, usando Active Directory como base de datos de seguridad.

## Ventajas

### Autenticación delegada

Permite que un servicio actúe en nombre del usuario para acceder a otros recursos (delegación).

### Single Sign-On (SSO)

Tras iniciar sesión una vez, el usuario accede a múltiples recursos sin volver a autenticarse.

### Interoperabilidad

Basado en estándares IETF, permitiendo interoperar con otros sistemas Kerberos.

### Mayor eficiencia

Usa tickets reutilizables, evitando consultar constantemente al controlador de dominio.

### Autenticación mutua

Cliente y servidor verifican mutuamente su identidad, reduciendo ataques de suplantación.

---

# NTLM (NT LAN Manager)

NTLM es un protocolo de autenticación más antiguo basado en un mecanismo de **desafío-respuesta (Challenge/Response)**.

Cada vez que un usuario necesita acceder a un recurso, el servidor debe validar la identidad consultando:

* El controlador de dominio (cuentas de dominio), o
* La base de cuentas local (cuentas locales).

## Uso actual

Aunque Kerberos es el protocolo recomendado para Active Directory, NTLM sigue utilizándose en:

* Equipos en grupos de trabajo (Workgroup).
* Inicio de sesión local.
* Aplicaciones heredadas que no soportan Kerberos.

## Administración

No se configura desde Server Manager.

Se administra mediante:

* Políticas de seguridad.
* Directivas de Grupo (GPO).

Microsoft recomienda auditar y reducir progresivamente el uso de NTLM.

---

# Comparación Kerberos vs NTLM

| Característica            | Kerberos | NTLM              |
| ------------------------- | -------- | ----------------- |
| Protocolo moderno         | Sí       | No                |
| Basado en tickets         | Sí       | No                |
| Challenge/Response        | No       | Sí                |
| Single Sign-On            | Sí       | Limitado          |
| Autenticación mutua       | Sí       | No                |
| Delegación                | Sí       | No                |
| Requiere Active Directory | Sí       | No necesariamente |
| Rendimiento               | Mejor    | Menor             |

---

# Autenticación sin contraseña (Passwordless)

Elimina el uso de contraseñas y las reemplaza por una combinación de:

* Algo que tienes.
* Algo que eres (biometría) o sabes (PIN).

## Beneficios

* Reduce ataques de phishing.
* Elimina la gestión de contraseñas.
* Mejora la experiencia del usuario.
* Compatible con SSO y Acceso Condicional.

## Métodos soportados por Microsoft Entra

### Windows Hello for Business

Ideal para equipos Windows personales.

Características:

* Usa biometría o PIN.
* Clave privada protegida por TPM.
* Compatible con SSO.
* Obtiene un Primary Refresh Token (PRT) tras autenticarse.

Flujo simplificado:

1. Usuario inicia sesión con biometría/PIN.
2. Se desbloquea la clave privada.
3. Microsoft Entra envía un nonce.
4. El dispositivo firma el nonce.
5. Microsoft Entra valida la firma.
6. Devuelve un PRT para acceso a recursos.

### Microsoft Authenticator (Passwordless)

El teléfono móvil se convierte en la credencial principal.

Flujo:

1. Usuario ingresa su nombre de usuario.
2. Microsoft Entra envía una notificación push.
3. El usuario aprueba con biometría o PIN.
4. La aplicación firma el desafío (nonce).
5. Microsoft Entra valida la firma y entrega el token.

Ventajas:

* Compatible con iOS y Android.
* No requiere contraseña.
* Funciona desde cualquier dispositivo.

### Claves de seguridad FIDO2 (Passkeys)

Basadas en el estándar abierto **FIDO2/WebAuthn**, resistente al phishing.

Características:

* Dispositivos USB, NFC o Bluetooth.
* No utilizan contraseñas.
* Protegen la clave privada en hardware seguro.
* Compatibles con Microsoft Entra y Windows.

Flujo:

1. El usuario conecta la llave FIDO2.
2. Windows la detecta.
3. Microsoft Entra envía un nonce.
4. El usuario desbloquea la llave con PIN o biometría.
5. La llave firma el nonce.
6. Microsoft Entra valida la firma y entrega el PRT.

Escenarios ideales:

* Equipos compartidos.
* Administradores.
* Organizaciones con altos requisitos de seguridad.
* Usuarios sin teléfono móvil.

### Autenticación basada en certificados (CBA)

Permite autenticarse directamente con certificados **X.509** emitidos por una infraestructura PKI.

Ventajas:

* Resistente al phishing.
* No requiere AD FS.
* Compatible con Acceso Condicional y MFA.
* Gratuita en Microsoft Entra ID.
* Permite definir reglas según emisor, OID o atributos del certificado.

Escenarios compatibles:

* Aplicaciones web.
* Aplicaciones móviles de Office.
* Outlook, OneDrive y demás aplicaciones nativas.
* Navegadores móviles.

---

# Comparación de métodos Passwordless

| Método                     | Requisitos              | Factor principal             | Escenario recomendado                            |
| -------------------------- | ----------------------- | ---------------------------- | ------------------------------------------------ |
| Windows Hello for Business | Windows 10/11 + TPM     | Biometría o PIN              | PC corporativa dedicada                          |
| Microsoft Authenticator    | iOS o Android           | Biometría o PIN del teléfono | Trabajo desde cualquier dispositivo              |
| Claves FIDO2               | Llave USB/NFC/Bluetooth | PIN o biometría en la llave  | Equipos compartidos o alta seguridad             |
| CBA (Certificados X.509)   | PKI y certificados      | Certificado digital          | Organizaciones con PKI y requisitos regulatorios |

## Recomendaciones según el perfil

| Usuario                              | Escenario              | Método recomendado                 |
| ------------------------------------ | ---------------------- | ---------------------------------- |
| Administrador                        | PC Windows dedicada    | Windows Hello for Business o FIDO2 |
| Administrador                        | Dispositivo no Windows | Microsoft Authenticator            |
| Trabajador de oficina                | PC Windows             | Windows Hello for Business o FIDO2 |
| Trabajador móvil                     | Cualquier dispositivo  | Microsoft Authenticator            |
| Primera línea / Kioscos / Hospitales | Equipos compartidos    | Claves de seguridad FIDO2          |

---

# Autenticación Kerberos

## ¿Qué es?

Kerberos es el protocolo de autenticación predeterminado en entornos **Active Directory Domain Services (AD DS)**. Implementa **Kerberos V5** junto con extensiones de Microsoft para autenticación con claves públicas, delegación y transporte de datos de autorización.

### Componentes principales

* **Cliente Kerberos (SSP)**: integrado con Winlogon y SSPI para proporcionar inicio de sesión único (SSO).
* **KDC (Key Distribution Center)**: se ejecuta en el controlador de dominio y utiliza Active Directory como base de datos de identidades.
* **Active Directory**: almacena cuentas, claves y permisos utilizados durante la autenticación.

---

## Ventajas de Kerberos

### Autenticación delegada

Permite que un servicio actúe en nombre del usuario para acceder a recursos de otros servidores.

**Ejemplo:**

* Usuario → IIS
* IIS → SQL Server usando la identidad del usuario

Esto es posible mediante **Delegation**, algo que NTLM no soporta adecuadamente.

---

### Inicio de sesión único (SSO)

Después del primer inicio de sesión:

* El usuario obtiene un Ticket Granting Ticket (TGT).
* Puede acceder a múltiples recursos sin volver a introducir credenciales.

---

### Interoperabilidad

Kerberos sigue los estándares del **IETF**, permitiendo interoperar con sistemas Linux, UNIX y otros productos compatibles con Kerberos.

---

### Mejor rendimiento

Con NTLM:

Cliente → Servidor → Controlador de Dominio (cada autenticación)

Con Kerberos:

Cliente obtiene Ticket → reutiliza el ticket durante la sesión.

Reduce tráfico hacia los controladores de dominio.

---

### Autenticación mutua

No solo el servidor verifica al cliente.

También el cliente verifica que realmente está hablando con el servidor correcto.

Protege frente a ataques de suplantación (spoofing).

---

## Flujo simplificado de Kerberos

1. Usuario inicia sesión.
2. El KDC entrega un **TGT**.
3. El usuario solicita un ticket para un servicio.
4. El KDC entrega un **Service Ticket**.
5. El usuario presenta el ticket al servidor.
6. El servidor valida el ticket y concede acceso.

---

## Comparación Kerberos vs NTLM

| Característica                         | Kerberos | NTLM         |
| -------------------------------------- | -------- | ------------ |
| Active Directory                       | Sí       | Sí           |
| Inicio de sesión único (SSO)           | ✔        | ✘            |
| Autenticación mutua                    | ✔        | ✘            |
| Delegación                             | ✔        | Muy limitada |
| Tickets reutilizables                  | ✔        | ✘            |
| Requiere contactar al DC continuamente | No       | Sí           |
| Rendimiento                            | Mejor    | Menor        |

---

# NTLM (NT LAN Manager)

## ¿Qué es?

Familia de protocolos de autenticación basada en desafío-respuesta (Challenge/Response).

Versiones:

* LAN Manager
* NTLMv1
* NTLMv2 (recomendado)

---

## Funcionamiento

Cada vez que un usuario necesita un nuevo token:

Si es cuenta de dominio:

Servidor → Controlador de Dominio

Si es cuenta local:

Servidor → Base SAM local

---

## Cuándo se usa actualmente

Aunque Kerberos es el protocolo recomendado, NTLM sigue utilizándose en:

* Equipos Workgroup
* Inicio de sesión local
* Equipos fuera del dominio
* Aplicaciones heredadas
* Sistemas antiguos que no soportan Kerberos

---

## Administración

No se configura desde Server Manager.

Se administra mediante:

* Group Policy
* Security Policy

Microsoft recomienda:

* Auditar el uso de NTLM
* Restringirlo progresivamente
* Migrar aplicaciones a Kerberos

---

# Autenticación sin contraseña (Passwordless)

## Objetivo

Eliminar el uso de contraseñas para reducir ataques como:

* Phishing
* Password Spraying
* Fuerza bruta
* Robo de credenciales

La autenticación combina:

* Algo que tienes
* Algo que eres o sabes

---

## Métodos disponibles en Microsoft Entra ID

### Windows Hello for Business

Ideal para equipos Windows personales.

Autenticación mediante:

* PIN
* Huella
* Reconocimiento facial

Características:

* Claves protegidas por TPM.
* SSO integrado.
* Compatible con recursos locales y nube.

### Flujo simplificado

1. Usuario desbloquea la clave privada con PIN o biometría.
2. Microsoft Entra envía un nonce.
3. El dispositivo firma el nonce.
4. Microsoft Entra valida la firma.
5. Devuelve un Primary Refresh Token (PRT).
6. El usuario obtiene acceso con SSO.

---

### Microsoft Authenticator

Convierte un teléfono Android o iPhone en una credencial sin contraseña.

Proceso:

1. Usuario escribe su nombre.
2. Microsoft Entra envía una notificación push.
3. Usuario aprueba.
4. Se verifica con PIN o biometría.
5. La aplicación firma el nonce.
6. Microsoft Entra devuelve el token.

---

### Claves FIDO2

Dispositivos físicos:

* USB
* NFC
* Bluetooth

Características:

* Sin contraseña.
* Resistentes al phishing.
* Compatibles con WebAuthn.
* Funcionan en Windows y navegadores compatibles.

Flujo:

1. Insertar llave.
2. Windows detecta la llave.
3. Microsoft Entra envía nonce.
4. Usuario valida con PIN o biometría.
5. La llave firma el nonce.
6. Microsoft Entra entrega el PRT.

---

### Autenticación basada en certificados (CBA)

Permite autenticarse mediante certificados **X.509** emitidos por una PKI.

Ventajas:

* Sin contraseñas.
* Resistente al phishing.
* Compatible con Acceso Condicional.
* No requiere AD FS.

Escenarios:

* Navegadores.
* Outlook.
* OneDrive.
* Aplicaciones Office.
* Dispositivos móviles.

---

## Comparación de métodos Passwordless

| Método                  | Requisitos               | Ideal para                             |
| ----------------------- | ------------------------ | -------------------------------------- |
| Windows Hello           | PC Windows con TPM       | Equipos dedicados                      |
| Microsoft Authenticator | Smartphone               | Usuarios móviles                       |
| FIDO2                   | Llave física             | Equipos compartidos y alta seguridad   |
| Certificados (CBA)      | PKI y certificados X.509 | Organizaciones con infraestructura PKI |

---

# Implementación de autenticación sin contraseña

## Objetivo

Eliminar las contraseñas como principal vector de ataque mediante:

* Microsoft Authenticator
* Claves de seguridad FIDO2
* Windows Hello para empresas

---

## Asistente de métodos sin contraseña

Disponible en el **Centro de administración de Microsoft Entra**.

Permite seleccionar el método adecuado según el perfil de usuarios y generar un plan de implementación.

---

## Escenarios recomendados

| Dispositivo                   | Método recomendado         |
| ----------------------------- | -------------------------- |
| Windows dedicado              | Windows Hello for Business |
| Dispositivo no Windows        | Microsoft Authenticator    |
| Equipos compartidos           | FIDO2                      |
| Quioscos                      | FIDO2 o Authenticator      |
| Tablets y móviles compartidos | Authenticator              |

---

## Roles necesarios

| Rol                                          | Función                         |
| -------------------------------------------- | ------------------------------- |
| Administrador de usuarios                    | Registro combinado              |
| Administrador de autenticación               | Administrar métodos             |
| Administrador de directivas de autenticación | Configurar políticas            |
| Usuario                                      | Registrar Authenticator o FIDO2 |

---

## Prerrequisitos

### Microsoft Authenticator

* Registro combinado MFA + SSPR habilitado.
* Usuarios registrados para MFA.
* Dispositivo registrado en Microsoft Entra.

### FIDO2

* Registro combinado habilitado.
* Windows 10 1809 o superior (1903 recomendado).
* Navegadores compatibles (Edge, Firefox 67+).
* Claves FIDO2 certificadas.

### Windows Hello

Los requisitos dependen de:

* Solo nube.
* Híbrido.
* Local.

Microsoft proporciona un asistente que genera el plan de implementación.

---

## Planificación del proyecto

Antes de implementar:

* Definir responsables.
* Involucrar a las partes interesadas.
* Alinear expectativas.

---

## Plan piloto

Se recomienda:

* Crear grupos piloto.
* Habilitar Passwordless solo para esos grupos.
* Validar la experiencia antes del despliegue general.

---

## Comunicación a usuarios

Debe incluir:

* Registro combinado MFA y SSPR.
* Descarga de Microsoft Authenticator.
* Registro del método.
* Cómo iniciar sesión sin contraseña.

Microsoft proporciona plantillas de comunicación.

---

## Registro de usuarios

Los usuarios registran sus métodos en:

[https://aka.ms/mysecurityinfo](https://aka.ms/mysecurityinfo)

Desde allí pueden registrar:

* Microsoft Authenticator.
* Claves FIDO2.
* Otros métodos de autenticación.

---

## Temporary Access Pass (TAP)

Código temporal emitido por un administrador para:

* Registrar métodos sin contraseña por primera vez.
* Recuperar acceso si el usuario pierde su método de autenticación.

Es:

* Temporal.
* Individual.
* Seguro.

---

# Implementación de Microsoft Authenticator

## Consideraciones

* El dispositivo debe estar registrado en Microsoft Entra.
* No admite dispositivos compartidos.
* Solo una cuenta profesional por dispositivo para inicio de sesión telefónico.

---

## Integración con AD FS

Cuando el usuario utiliza Passwordless:

* Normalmente no pasa por AD FS.
* Se autentica directamente en Microsoft Entra.
* Algunas aplicaciones heredadas siguen utilizando AD FS.

---

## Casos de prueba

Verificar que:

* El usuario registra Authenticator.
* Puede activar el inicio de sesión telefónico.
* Puede iniciar sesión correctamente.
* Puede eliminar la credencial.
* La deshabilitación mediante políticas impide el acceso.

---

## Solución de problemas

Problemas comunes:

* Registro combinado deshabilitado.
* Usuario fuera del grupo habilitado.
* Política Passwordless no aplicada.

---

# Implementación de FIDO2

## Ciclo de vida

* Distribución de llaves.
* Registro inicial.
* Activación mediante PIN o biometría.
* Deshabilitación.
* Reemplazo por nuevas llaves.

---

## Requisitos

Para Windows:

* Windows 10 1809 o superior.
* Edge o Firefox compatibles.

Para dispositivos híbridos:

* Windows 10 2004+.
* Windows Server 2016 o 2019.
* Microsoft Entra Connect actualizado.

---

## Habilitación

Puede realizarse mediante:

* Microsoft Intune (recomendado).
* Paquetes de aprovisionamiento.
* Directivas de Grupo (solo híbrido).

---

## Restricción de fabricantes

Es posible permitir únicamente llaves FIDO2 de fabricantes aprobados utilizando el **AAGUID (Authenticator Attestation GUID)**.

---

## Casos de prueba

Comprobar:

* Registro de la llave.
* Restablecimiento.
* Inicio de sesión en Windows.
* Inicio de sesión en aplicaciones web.
* Funcionamiento tras cambios en las políticas.

---

## Problemas comunes

* Registro combinado deshabilitado.
* Claves FIDO2 no habilitadas.
* Windows demasiado antiguo.
* Navegador no compatible.
* Proveedor de credenciales no habilitado.

---

# Implementación de la Protección con contraseña de Microsoft Entra

## Objetivo

Bloquear:

* Contraseñas débiles.
* Contraseñas comunes.
* Variantes de contraseñas conocidas.
* Palabras personalizadas definidas por la organización.

La misma política se aplica tanto en Microsoft Entra ID como en Active Directory local.

---

## Principios de diseño

* Los controladores de dominio **no necesitan acceso directo a Internet**.
* No se abren nuevos puertos de red en los DC.
* No requiere cambios en el esquema de AD DS.
* Compatible con cualquier nivel funcional admitido.
* Las contraseñas nunca salen del controlador de dominio en texto plano.
* No depende de Password Hash Synchronization (PHS).
* Permite implementación incremental, aunque solo aplica en los DC con el agente instalado.

---

## Implementación incremental

Es posible instalar el agente solo en algunos controladores de dominio para pruebas.

**Importante:** esto **no es seguro para producción**, ya que solo los DC con el agente validarán las contraseñas. Microsoft recomienda instalar el agente en **todos** los controladores de dominio del dominio.

---

## Componentes de la solución

### Servicio Proxy de Protección con contraseña

* Se instala en un equipo unido al dominio.
* Descarga las políticas desde Microsoft Entra.
* Las reenvía a los controladores de dominio.
* No almacena políticas (es stateless).

### Agente del Controlador de Dominio (DC Agent)

* Recibe solicitudes de validación desde el filtro de contraseñas.
* Descarga y almacena localmente la política.
* Valida cada cambio o restablecimiento de contraseña.

### DLL del filtro de contraseñas

* Intercepta los cambios de contraseña del sistema operativo.
* Envía la contraseña al servicio DC Agent para validación.
* Devuelve **Aprobada** o **Rechazada**.

---

## Flujo de funcionamiento

1. El servicio Proxy se registra mediante objetos **serviceConnectionPoint** en Active Directory.
2. El DC Agent localiza un Proxy disponible.
3. Solicita la política de contraseñas a Microsoft Entra a través del Proxy.
4. El Proxy descarga la política y la devuelve al DC Agent.
5. El DC Agent almacena la política en **SYSVOL**.
6. Otros DC la reciben mediante la replicación de SYSVOL.
7. El DC Agent comprueba cada hora si existe una política más reciente.
8. Ante un cambio de contraseña, el DC utiliza la política almacenada localmente para aceptar o rechazar la nueva contraseña.

---

## Características importantes

* Cada política es específica del **tenant** de Microsoft Entra.
* El Proxy se comunica con el DC Agent mediante **RPC sobre TCP**.
* El DC Agent nunca escucha conexiones de red.
* El Proxy no almacena políticas.
* Si un DC no dispone de ninguna política descargada, **acepta automáticamente la contraseña** y registra un evento de advertencia.
* La sincronización de cambios de política **no es inmediata**; puede existir un retraso hasta que todos los DC reciban la actualización.
* La Protección con contraseña **complementa** las políticas de contraseña existentes y otros filtros de terceros; todos deben aprobar una contraseña para que sea aceptada.

---

## Registro del bosque (Forest Registration)

Para utilizar Protección con contraseña:

* El bosque de AD DS debe registrarse en un único tenant de Microsoft Entra.
* Todos los servicios Proxy del bosque deben registrarse en **ese mismo tenant**.

**No se admite** registrar un mismo bosque o sus proxies en distintos tenants de Microsoft Entra, ya que impediría la descarga de las políticas de contraseña.
