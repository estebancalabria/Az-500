
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
