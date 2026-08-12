# Microsoft Defender for Cloud – Planes de Pricing

> Nota histórica: este servicio se llamaba **Azure Security Center**. El nombre actual es **Microsoft Defender for Cloud**. El viejo modelo de "Free tier / Standard tier" único hoy se reemplazó por **planes de Defender independientes por tipo de recurso**, que se habilitan uno por uno.

## Equivalencia de terminología (Free/Standard vs. modelo actual)

El examen AZ-500 y muchos dumps de práctica todavía usan la terminología vieja de **Free tier / Standard tier**, porque fueron escritos cuando el producto se llamaba Azure Security Center. Esa terminología ya no existe tal cual en el portal actual, pero equivale a lo siguiente:

| Terminología vieja (la que usa el examen) | Terminología actual en el portal |
|---|---|
| Free tier | Foundational CSPM (gratis) |
| Standard tier | Cualquier plan de pago "Defender for..." (Servers, Storage, SQL, Containers, etc.) |

Si una pregunta de examen ofrece como opciones Free / Standard / Basic / Premium y pide "qué tier necesitás" para algo avanzado (custom alerts, threat protection, hardening de SO, etc.), la respuesta correcta es **Standard** — que en el portal actual se traduce en tener habilitado el plan de pago correspondiente de Defender for el recurso en cuestión.

## 1. Foundational CSPM (Free)

El nivel gratuito, equivalente conceptual al viejo "Free tier".

Incluye:
- Secure Score
- Evaluación continua de postura de seguridad
- Recomendaciones básicas basadas en Microsoft Cloud Security Benchmark
- Inventario de assets

No incluye:
- Alertas de amenazas (custom alert rules)
- Threat protection activo
- Vulnerability scanning
- Attack path analysis

## 2. Defender CSPM (pago)

Extiende el CSPM gratuito con capacidades avanzadas de postura de seguridad.

Incluye:
- Agentless vulnerability scanning
- Attack path analysis
- Cloud security graph
- Data-aware security posture
- DevOps security (multi-pipeline)

Facturación: por cantidad de recursos facturables (servers, storage accounts, databases, serverless containers/functions), no por suscripción completa.

## 3. Planes de Cloud Workload Protection (CWP)

Estos son los planes de pago (equivalente al viejo "Standard tier") que habilitan **threat protection real**. Cada uno de estos planes incluye:

- Detección de amenazas basada en analytics e inteligencia de Microsoft
- **Capacidad de crear custom alert rules** sobre los datos recolectados del recurso protegido
- Recomendaciones de hardening específicas del recurso (por ejemplo, configuración de seguridad del sistema operativo en Defender for Servers)
- Alertas de seguridad avanzadas (vulnerability to SQL injection, brute force, potential SQL injection, etc., según el recurso)

Se activan de forma independiente según qué recursos querés proteger — no es un switch único para toda la suscripción.

| Recurso | Plan |
|---|---|
| Servidores | Defender for Servers Plan 1 |
| Servidores | Defender for Servers Plan 2 |
| Containers | Defender for Containers |
| Bases de datos | Defender for SQL (Azure-connected / Arc-enabled) |
| Bases de datos | Defender for SQL Servers on machines (fuera de Azure, sin Arc) |
| Bases de datos | Defender for MySQL |
| Bases de datos | Defender for PostgreSQL |
| Bases de datos | Defender for Azure Cosmos DB |
| Storage | Defender for Storage |
| Storage | Malware Scanning (add-on de Defender for Storage) |
| AI | Defender for AI Services |
| APIs | Defender for APIs (Plan 1 a 5, según volumen) |
| App Service | Defender for App Service |
| Key Vault | Defender for Key Vault |
| Resource Manager | Defender for Resource Manager |

## Dónde entran las "custom alert rules"

Las reglas de alerta personalizadas (custom alert rules) requieren tener habilitado **al menos un plan de pago de Cloud Workload Protection** sobre el recurso correspondiente (por ejemplo, Defender for Servers si querés alertas sobre VMs, o Defender for Storage si querés alertas sobre un storage account). Con el tier Free (Foundational CSPM) **no es posible crear alertas personalizadas** — solo se obtienen recomendaciones de postura, sin capacidad de detección de amenazas ni generación de alertas propias.

## Otros datos relevantes

- **Prueba gratuita**: los planes pagos incluyen 30 días gratis; a partir de ahí se factura según el plan.
- **Auto-enrollment**: al habilitar Defender for Cloud, Azure inscribe automáticamente todos los recursos elegibles salvo que se haga opt-out explícito.
- **Pre-purchase (Commit Units)**: se puede pre-comprar consumo con descuentos de hasta 22% sobre pay-as-you-go, válido por 12 meses, aplicable a cualquier plan de Defender for Cloud.
- **Defender Experts**: servicio adicional de MDR (Managed Detection and Response) 24/7 para servidores, facturado por servidor/mes según horas de actividad.

## Punto clave para el examen AZ-500

Cuando una pregunta menciona "custom alert rules", "advanced threat detection" o "threat protection" en el contexto de Security Center / Defender for Cloud, la respuesta casi siempre pasa por confirmar que el **plan de pago correspondiente** (no el Free/Foundational CSPM) esté habilitado sobre el tipo de recurso en cuestión.

Fuente: [Azure – Pricing Defender for Cloud](https://azure.microsoft.com/en-us/pricing/details/defender-for-cloud/)
