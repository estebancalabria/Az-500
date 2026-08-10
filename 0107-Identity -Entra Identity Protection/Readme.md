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
