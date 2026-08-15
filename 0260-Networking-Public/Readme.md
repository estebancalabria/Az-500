# Seguridad del acceso público a recursos de Azure

## Introducción

Este módulo proporciona conocimientos para diseñar, implementar y administrar una estrategia de seguridad integral para recursos de Azure accesibles públicamente, aplicando principios de **Zero Trust** y **defense-in-depth** para proteger aplicaciones web, APIs y tráfico de red frente a amenazas modernas, manteniendo la disponibilidad, escalabilidad y rendimiento.

---

## Transport Layer Security (TLS)

**TLS** es un protocolo criptográfico que proporciona **cifrado, autenticación e integridad** de las comunicaciones mediante un handshake seguro entre cliente y servidor, negociación de algoritmos y validación de certificados emitidos por autoridades certificadoras confiables.

* TLS 1.2 y TLS 1.3 ofrecen mayor seguridad, privacidad y rendimiento que versiones anteriores.
* TLS reemplazó al protocolo SSL.
* Azure requiere **TLS 1.2 o superior**; TLS 1.3 es recomendado.
* Protege los datos **en tránsito**, pero no los datos almacenados.
* Para datos en reposo deben utilizarse mecanismos específicos como cifrado de Azure Storage, Transparent Data Encryption o Azure Disk Encryption.
* Proporciona:

  * **Autenticación:** los certificados validan la identidad del servicio.
  * **Confidencialidad:** cifra los datos para evitar su lectura por terceros.
  * **Integridad:** detecta modificaciones durante la transmisión.
* Ayuda a prevenir ataques como **man-in-the-middle**, interceptación de credenciales, tokens, claves API y datos empresariales.
* También contribuye al cumplimiento de requisitos regulatorios y a la confianza de los usuarios.

Azure utiliza TLS en servicios como **App Service, API Management, Storage y SQL Database**, incorporando características como Perfect Forward Secrecy y authenticated encryption.

### Configurar TLS en Azure App Service

App Service proporciona terminación TLS administrada y renovación automática de certificados.

* La utilización de certificados desde el código de la aplicación requiere **Basic o superior**; en Free o Shared se puede incluir el certificado en el repositorio.
* Se puede separar la administración de certificados del código de la aplicación.

**Versión mínima de TLS:**

1. App Service → **Settings → Configuration**.
2. **General settings**.
3. Configurar **Minimum TLS version** en **1.2 o 1.3**; 1.3 es recomendado.
4. Guardar.

Los clientes que utilicen versiones antiguas recibirán un error de conexión.

**Forzar HTTPS:**

1. App Service → **Settings → Configuration → General settings**.
2. Establecer **HTTPS Only = On**.
3. Guardar.

Esto redirige las solicitudes HTTP a HTTPS.

**Certificado para dominio personalizado:**

1. Comprar o importar un certificado correspondiente al dominio.
2. App Service → **Settings → Certificates → Add certificate**.
3. Subir el certificado o crear un certificado administrado por App Service.
4. Asociarlo al dominio personalizado.

El certificado `*.azurewebsites.net` es gratuito. Los certificados administrados se renuevan automáticamente; los privados requieren renovación y carga manual.

### Configurar TLS en Azure API Management

API Management debe proteger tanto la conexión **cliente → gateway** como **gateway → backend**.

**Versión mínima del gateway:**

1. API Management → **Security → Protocols + ciphers**.
2. Deshabilitar **SSL 3.0, TLS 1.0 y TLS 1.1**.
3. Mantener **TLS 1.2** habilitado.
4. TLS 1.3 depende del nivel de servicio.
5. Guardar.

Debe verificarse previamente la compatibilidad de los consumidores de la API.

**Forzar HTTPS:**

1. API Management → **APIs** → seleccionar API.
2. **Settings → URL scheme**.
3. Seleccionar **HTTPS only**.
4. Guardar.

Las solicitudes HTTP son rechazadas con **403 Forbidden**.

**Validación TLS del backend:**

* API → **Design** → operación → **Backend**.
* Habilitar:

  * **Validate certificate chain**
  * **Validate certificate name**
* Cargar certificados raíz confiables si se utilizan certificados privados o autofirmados.

Esto permite mantener la validación de certificados y el cifrado extremo a extremo.

### Administración y planificación de TLS

Se recomienda:

* Utilizar **Azure Key Vault** para almacenar y rotar certificados.
* Auditar clientes que todavía utilizan TLS 1.0/1.1.
* Probar las renovaciones automáticas y documentar los procedimientos manuales.
* Configurar Azure Monitor para alertas de expiración de certificados y fallos de handshake TLS.
* Documentar la relación entre configuraciones TLS y requisitos de cumplimiento.
* Probar los cambios en staging antes de producción y realizar despliegues graduales para evitar problemas de compatibilidad.

---

# Azure Firewall

**Azure Firewall** es un firewall de red cloud-native, stateful y como servicio, con alta disponibilidad y escalabilidad sin restricciones. Permite inspección de tráfico:

* **North-south:** tráfico entre redes internas e Internet.
* **East-west:** tráfico entre redes y cargas internas.

Proporciona filtrado **L3-L7** y protección contra amenazas.

## SKUs

### Azure Firewall Standard

* Filtrado L3-L7.
* Threat Intelligence de Microsoft Cyber Security.
* Puede alertar y bloquear tráfico hacia/desde IP y dominios maliciosos.
* Las fuentes de amenazas se actualizan en tiempo real.

### Azure Firewall Premium

Incluye las capacidades de Standard y:

* **IDPS** basado en firmas.
* Más de **67.000 firmas** en más de 50 categorías.
* Actualización en tiempo real.
* Protección frente a malware, phishing, coin mining y troyanos.

### Azure Firewall Basic

Orientado a pequeñas y medianas empresas.

* Protección esencial.
* Similar a Standard, pero:

  * Solo **Threat Intelligence en modo Alert**.
  * Escalado fijo sobre dos instancias de máquinas virtuales.
  * Recomendado para entornos de aproximadamente **250 Mbps**.

---

# Azure Firewall Manager

**Azure Firewall Manager** proporciona administración centralizada de políticas de seguridad y rutas para perímetros de seguridad cloud.

Puede administrar:

### Secured virtual hub

Un **Azure Virtual WAN Hub** administrado por Microsoft que, al asociarse con políticas de seguridad y routing, se convierte en un secured virtual hub.

### Hub virtual network

Una VNet estándar administrada por el usuario. Actualmente admite Azure Firewall Policy.

Permite conectar VNets spoke que contienen cargas de trabajo y administrar firewalls en VNets independientes.

## Funcionalidades

### Implementación y configuración centralizada

Permite desplegar y configurar múltiples Azure Firewall en diferentes regiones y suscripciones.

### Políticas jerárquicas

Permite administrar políticas globales y locales:

* Políticas globales para aplicar reglas organizacionales.
* Políticas locales para proporcionar autonomía a equipos DevOps.

### Integración con proveedores SECaaS

En secured virtual hubs permite integrar proveedores externos de seguridad para:

* Virtual network → Internet.
* Branch → Internet.

También pueden utilizarse junto con Azure Firewall para escenarios Branch → VNet, VNet → VNet y VNet → Internet.

### Administración centralizada de rutas

Permite dirigir tráfico al hub protegido para filtrado y logging sin configurar manualmente UDR en las VNets spoke.

### DDoS Protection

Firewall Manager permite asociar VNets a un **DDoS Protection Plan**.

### WAF

Permite crear y asociar centralmente políticas **WAF** para plataformas de entrega de aplicaciones como:

* Azure Front Door.
* Azure Application Gateway.

Las Azure Firewall Policies pueden utilizarse entre regiones.

---

# Azure Application Gateway

**Azure Application Gateway** es un balanceador de tráfico web de **capa 7 (OSI)** que permite tomar decisiones de routing basadas en atributos HTTP.

A diferencia de los balanceadores tradicionales L4, que utilizan IP y puerto, Application Gateway puede utilizar:

* URI path.
* Host headers.
* URL.
* Otros atributos HTTP.

Ejemplo:

* `/images` → backend de imágenes.
* `/video` → backend optimizado para vídeos.

Funciona como punto único de contacto para los clientes y distribuye tráfico hacia pools backend que pueden contener:

* Azure VMs.
* Virtual Machine Scale Sets.
* Azure App Service.
* Servidores on-premises o externos.

## Infraestructura

Incluye:

* Virtual Network.
* Subnets.
* Network Security Groups.
* User Defined Routes.

## Frontend IP

Puede utilizar:

* IP pública.
* IP privada.
* Ambas.

La IP pública se utiliza para aplicaciones accesibles desde Internet. La privada resulta útil para aplicaciones internas y arquitecturas multicapa.

La configuración tradicional admite una IP pública y una privada. El frontend también soporta dual-stack IPv4/IPv6 en public preview, con hasta cuatro frontend IP.

La IP frontend se asocia a un **listener**.

Cuando se utilizan listeners públicos y privados en el mismo puerto, deben considerarse las reglas NSG de la subnet.

## Listeners

Un listener recibe solicitudes según:

* Protocolo.
* Puerto.
* Hostname.
* IP.

Debe existir al menos un listener y pueden existir múltiples listeners.

Protocolos:

* HTTP.
* HTTPS.
* HTTP/2.
* WebSocket.

HTTP/2 se admite para conexiones cliente → Application Gateway; la comunicación con backend utiliza HTTP/1.1. WebSocket está habilitado de forma predeterminada.

Un listener HTTPS permite **TLS termination**, descargando del backend el procesamiento de cifrado y descifrado.

## Puertos

* V2: puertos 1–64999, excepto 22.
* V1: puertos 1–65502, excepto 3389.

## Request routing rules

Las reglas vinculan:

* Listener.
* Backend pool.
* Backend HTTP settings.

Tipos:

### Basic

Todas las solicitudes del listener se envían a un único backend pool.

### Path-based

Las solicitudes se distribuyen según rutas URL a diferentes backend pools, con un pool y configuración predeterminados para las rutas que no coincidan.

## Redirecciones

Permiten redirigir:

* Entre listeners, por ejemplo HTTP → HTTPS.
* Hacia sitios externos.

En reglas path-based, la redirección puede aplicarse solamente a determinadas rutas.

## Rewrite HTTP headers and URL

Las reglas de rewrite permiten:

* Agregar, eliminar o modificar headers HTTP(S).
* Modificar rutas URL.
* Modificar query strings.

Esto puede utilizarse para seguridad, compatibilidad, extracción de IP de clientes y ocultación de información del backend.

## HTTP settings

Definen cómo Application Gateway se comunica con los backends:

* Puerto.
* Protocolo.
* Timeout.
* Certificados.
* Hostname.
* Health probes.
* Affinity.
* Connection draining.

### Cookie-based affinity

Application Gateway utiliza cookies administradas por el gateway para mantener una sesión asociada al mismo backend.

Es útil cuando el estado de sesión se mantiene localmente en el servidor.

### Connection draining

Permite retirar servidores del backend de forma gradual:

* No reciben nuevas conexiones.
* Las conexiones existentes continúan hasta el timeout.
* Se aplica durante eliminación del pool, scale-in o detección de unhealthy.

Valores:

* Predeterminado sin habilitar: **30 segundos**.
* Configurable: **1–3.600 segundos**.

## TLS hacia el backend

Application Gateway puede utilizar HTTP o HTTPS.

* HTTP → tráfico backend sin cifrar.
* HTTPS → tráfico backend cifrado.

HTTPS en listener + HTTPS en backend permite **end-to-end TLS**.

Cuando se utiliza HTTPS hacia el backend, se requiere un certificado raíz confiable:

* Certificado público confiable → puede utilizarse la opción de CA conocida.
* Certificado autofirmado o CA interna → se debe cargar el certificado público correspondiente en formato `.CER`.

## Request timeout

Define cuánto espera Application Gateway una respuesta del backend.

## Override backend path

Permite definir una ruta personalizada al reenviar solicitudes al backend, útil para reescrituras y compatibilidad con rutas diferentes.

## Custom probe

Permite controlar específicamente el monitoreo de salud del backend.

Puede definir:

* Hostname.
* URL path.
* Puerto.
* Intervalo.
* Timeout.
* Umbral de fallos.

El probe debe estar asociado explícitamente al HTTP setting correspondiente.

## Hostname

Application Gateway puede utilizar un hostname diferente hacia el backend.

Opciones:

### Pick host name from backend address

Establece dinámicamente el header `Host` utilizando el hostname del backend.

Es útil cuando el backend utiliza un dominio diferente al de Application Gateway, por ejemplo determinados escenarios con App Service.

### Host name override

Reemplaza el header `Host` original por el hostname especificado.

En producción se recomienda mantener, cuando sea posible, el mismo hostname entre cliente, Application Gateway y backend para evitar problemas con URLs absolutas, redirects y cookies vinculadas al host.

## Backend pool

Puede contener:

* VM.
* VM Scale Set.
* IP/FQDN.
* App Service.

Debe asociarse a reglas de routing y utilizar health probes.

## Health probes

Application Gateway supervisa continuamente los backends y deja de enviar tráfico a servidores unhealthy. Cuando vuelven a responder correctamente, recuperan el tráfico.

### Default probe

Utiliza el protocolo y puerto del backend HTTP setting.

Valores predeterminados:

* Intervalo: **30 segundos**.
* Timeout: **30 segundos**.
* Umbral unhealthy: **3 fallos consecutivos**.
* HTTP 200–399 se considera saludable.

La URL predeterminada utiliza:

`<protocol>://127.0.0.1:<port>/`

### Custom probe

Permite configurar:

* Name.
* Protocol.
* Host.
* Path.
* Port.
* Interval.
* Timeout.
* Unhealthy threshold.

También permite definir criterios de coincidencia:

* Códigos HTTP específicos o rangos.
* Contenido específico del body.

Por defecto, las respuestas 200–399 se consideran saludables.

## Casos de uso de custom probes

* Backends que requieren autenticación y responden 403.
* Backends con certificados wildcard que requieren un hostname específico para SNI.

## NSG

La subnet de Application Gateway tiene requisitos específicos de comunicación con Azure:

* V1: permitir TCP **65503–65534** desde `GatewayManager`.
* V2: permitir TCP **65200–65535** desde `GatewayManager`.
* No bloquear conectividad saliente a Internet.
* Permitir tráfico entrante desde `AzureLoadBalancer`.

## Flujo de solicitudes

1. **DNS resolution:** se resuelve el dominio y se obtiene la IP frontend.
2. **Listener:** recibe la solicitud.
3. **WAF:** si está habilitado, inspecciona headers y body.
4. **Request routing:** determina el backend pool.
5. **Backend selection:** selecciona un backend saludable.
6. **TLS:** según HTTP settings, la comunicación backend puede estar cifrada o no.

Application Gateway puede funcionar como:

* Balanceador público mediante IP pública.
* Balanceador interno mediante IP privada.

## DNS de backend

Cuando el backend utiliza FQDN, Application Gateway realiza una consulta DNS y almacena el resultado según el TTL. Los servidores DNS personalizados deben proporcionar valores consistentes.

## Headers agregados

Application Gateway agrega headers como:

* `x-forwarded-for`
* `x-forwarded-port`
* `x-forwarded-proto`
* `x-original-host`
* `x-original-url`
* `x-appgw-trace-id`

Estos proporcionan información sobre la solicitud original y facilitan logging y tracing.

---

# Web Application Firewall (WAF)

**WAF** proporciona protección centralizada de aplicaciones web contra vulnerabilidades y ataques comunes, evitando tener que implementar toda la protección directamente en el código backend.

Protege especialmente contra:

* SQL Injection.
* Cross-site scripting (XSS).
* API abuse.
* Ataques de bots.
* Command injection.
* HTTP request smuggling.
* HTTP response splitting.
* Remote file inclusion.
* Violaciones y anomalías del protocolo HTTP.
* Crawlers y scanners.
* Configuraciones incorrectas comunes.

Puede desplegarse con:

* Azure Application Gateway.
* Azure Front Door.
* Azure CDN de Microsoft.

## WAF con Application Gateway

Está basado en los **Core Rule Sets (CRS) de OWASP**.

Una **WAF Policy** contiene:

* Managed rules.
* Custom rules.
* Exclusions.
* Límites de tamaño.
* Otras personalizaciones.

Una política puede asociarse:

* Globalmente.
* A un Application Gateway.
* A listeners.
* A reglas path-based.
* A sitios individuales.

Una instancia de Application Gateway puede proteger hasta **40 sitios web**.

## Protección y personalización

Permite:

* Proteger aplicaciones sin modificar el código backend.
* Aplicar diferentes políticas a diferentes sitios.
* Protección mediante IP Reputation.
* Protección contra bots.
* Geo-filtering.
* Inspección de JSON y XML.
* Exclusiones para determinados atributos de solicitudes.
* Reglas personalizadas.
* Límites de tamaño de solicitudes.

## Core Rule Sets

Application Gateway admite:

* CRS 3.2 — recomendado.
* CRS 3.1.
* CRS 3.0.

CRS 3.2 proporciona mayor precisión, rendimiento y protección frente a amenazas modernas.

## Custom rules

Las reglas personalizadas tienen mayor prioridad que las reglas administradas y permiten implementar requisitos específicos de una aplicación o reducir falsos positivos.

Cada regla contiene:

* Condición de coincidencia.
* Prioridad.
* Acción.

Acciones:

* **ALLOW**
* **BLOCK**
* **LOG**

Las reglas se procesan por prioridad; un número menor tiene mayor prioridad.

## Bot protection

El Bot Manager permite clasificar bots en:

* **Bad:** bots maliciosos o procedentes de IP maliciosas.
* **Good:** bots validados, como motores de búsqueda confiables.
* **Unknown:** bots identificados mediante user agents sin validación adicional.

Las firmas de bots se administran y actualizan dinámicamente.

Se pueden configurar acciones para permitir, bloquear o registrar las solicitudes.

Los logs pueden enviarse a:

* Storage Account.
* Event Hub.
* Log Analytics.
* Soluciones de partners.

## Modos WAF

### Detection

* Detecta y registra amenazas.
* No bloquea solicitudes.
* Recomendado durante la implementación y ajuste inicial para detectar falsos positivos.

### Prevention

* Bloquea las amenazas detectadas.
* Devuelve **403** al atacante.
* Registra los eventos.
* Recomendado para producción después del ajuste de las reglas.

## WAF Engine

CRS 3.2 o posterior utiliza el nuevo WAF engine, que proporciona mayor rendimiento y funcionalidades adicionales.

## Anomaly Scoring

OWASP admite:

* Traditional mode.
* Anomaly Scoring mode.

En **Anomaly Scoring**, las coincidencias acumulan una puntuación según severidad:

| Severidad | Puntuación |
| --------- | ---------: |
| Critical  |          5 |
| Error     |          4 |
| Warning   |          3 |
| Notice    |          2 |

El umbral de bloqueo es **5**.

Por ejemplo:

* Una regla Critical → 5 → puede bloquear.
* Una regla Warning → 3 → no bloquea por sí sola.

## Configuración y monitoreo

Las WAF policies pueden configurarse mediante:

* Azure Portal.
* REST API.
* ARM templates.
* Azure PowerShell.

WAF se integra con:

* Azure Monitor.
* Azure Monitor Logs.
* Microsoft Defender for Cloud.

Los logs permiten analizar ataques, alertas y tendencias.

---

# Azure Front Door y CDN

**Azure Front Door** es la CDN moderna de Microsoft para entregar contenido y aplicaciones globalmente con:

* Alta disponibilidad.
* Baja latencia.
* Escalabilidad.
* Seguridad.

Utiliza la red global de Microsoft con numerosos **Points of Presence (PoPs)** cercanos a los usuarios.

## Beneficios

### Escala y rendimiento global

* Red global de Microsoft.
* Edge locations conectadas mediante WAN privada.
* Anycast.
* Split TCP.
* SSL/TLS offload en el edge.
* Administración integrada de certificados.
* IPv6.
* HTTP/2.

### Aplicaciones modernas

Permite:

* Integración con CLI, SDK, Bicep, ARM y PowerShell.
* Dominios personalizados.
* Balanceo y routing entre origins.
* Health probes.
* Integración con DNS, Web Apps y Storage.
* Reglas en el edge.
* Regex y server variables.
* URL redirect/rewrite.
* Reports integrados.
* Monitorización en tiempo real.
* Alertas mediante Azure Monitor.
* Logs de solicitudes y health probes.

### Seguridad

Incluye:

* Protección DDoS L3-L4.
* Integración con WAF.
* Protección L7 mediante WAF.
* Bot Manager basado en Microsoft Threat Intelligence.
* Geo-filtering.
* Private Link hacia backend.
* Azure Policy.
* Azure Advisor.
* Modelo Zero Trust.

### Costos y administración

* Entrega estática y dinámica en un único servicio.
* Caching.
* SSL offload.
* Certificados administrados con autorrotación.
* Modelo de costos simplificado.
* Integración de costos de egress entre Azure y Front Door.

## Front Door vs Azure CDN

Ambos permiten:

* Entrega global de contenido.
* Caching.
* Routing inteligente.
* Optimización del rendimiento.
* Red global de PoPs.
* Funciones de seguridad y monitoreo.

Front Door añade capacidades orientadas a entrega global de aplicaciones, routing y seguridad.

Para cambiar de tier en Front Door se debe recrear el perfil; existe capacidad de migración desde Front Door Classic al nuevo tier.

### Capacidades destacadas de Front Door Standard/Premium

* Contenido estático y dinámico.
* Dominios personalizados.
* HTTPS y certificados propios.
* TLS 1.3/TLS 1.2.
* Caching y query string caching.
* Purge y compresión.
* Origin load balancing.
* Path-based routing.
* Rules engine.
* Server variables.
* Regex en reglas.
* URL rewrite/redirect.
* IPv4/IPv6.
* HTTP/2.
* WAF.
* Geo-filtering.
* DDoS protection.
* Domain Fronting Block.
* Monitoring y logs.
* Health probes.
* Integración con Storage y Web Apps.
* Administración mediante REST API, SDKs y PowerShell.
* Azure Key Vault y Managed Identities.
* Azure Advisor.
* Private Link y protección mediante reglas administradas en Premium.

---

# Azure DDoS Protection

Los ataques **DDoS** buscan agotar los recursos de una aplicación para impedir el acceso de usuarios legítimos. Pueden dirigirse contra cualquier endpoint públicamente accesible mediante Internet.

**Azure DDoS Protection**, junto con buenas prácticas de diseño, proporciona mitigación avanzada sin requerir cambios en las aplicaciones o recursos protegidos.

Protege principalmente las capas:

* **L3:** red.
* **L4:** transporte.

Para protección de aplicaciones web en **L7** se debe complementar con **WAF**.

## Tiers

### DDoS Network Protection

* Se configura sobre una virtual network.
* Se adapta automáticamente a los recursos protegidos.
* Protege los recursos de la VNet.

### DDoS IP Protection

* Modelo de pago por IP pública protegida.
* Incluye las capacidades principales de Network Protection.
* Se diferencia por servicios adicionales como:

  * DDoS Rapid Response.
  * Cost protection.
  * Descuentos en WAF.

## Funcionalidades

### Monitoreo permanente

Supervisa el tráfico **24x7** y detecta automáticamente indicadores de ataques DDoS.

### Ajuste adaptativo

Aprende los patrones de tráfico de la aplicación y ajusta dinámicamente los perfiles de protección.

### Analytics, métricas y alertas

Aplica políticas de mitigación autotuned para:

* TCP SYN.
* TCP.
* UDP.

Los umbrales se configuran mediante perfiles de tráfico basados en machine learning.

La mitigación se activa cuando el tráfico supera el umbral correspondiente.

Durante los ataques permite:

* Informes detallados en intervalos de cinco minutos.
* Resumen posterior al ataque.
* Streaming de mitigation flow logs hacia Microsoft Sentinel o SIEM.
* Métricas en Azure Monitor.
* Alertas al inicio, durante y final del ataque.

Las alertas pueden integrarse con:

* Azure Monitor Logs.
* Splunk.
* Azure Storage.
* Email.
* Azure Portal.

### DDoS Rapid Response

Durante un ataque activo se puede acceder al equipo **DDoS Rapid Response (DRR)** para investigación y análisis posterior.

### Integración nativa

Está integrado con Azure y puede configurarse desde Azure Portal.

### Turnkey protection

Una vez habilitado:

* DDoS Network Protection protege automáticamente los recursos de la VNet.
* DDoS IP Protection protege automáticamente la IP pública seleccionada.

No requiere intervención adicional ni definición manual de protección.

### Protección multicapa

Combinando:

* **Azure DDoS Protection → L3/L4**
* **WAF → L7**

se obtiene protección en múltiples capas.

### Escala de mitigación

Puede mitigar vectores de ataque L3/L4 a gran escala utilizando capacidad global.

### Cost protection

Proporciona créditos de servicio relacionados con transferencia de datos y escalado de aplicaciones cuando existen costos derivados de ataques DDoS documentados.

## Arquitectura

Azure DDoS Protection está diseñado para servicios desplegados en una **VNet**.

Para otros servicios se aplica la protección DDoS de infraestructura predeterminada de Azure frente a ataques comunes de red.

## Pricing

Para **DDoS Network Protection**, un único DDoS Protection Plan puede utilizarse entre múltiples suscripciones dentro de un tenant.

Para **DDoS IP Protection** no es necesario crear un plan; puede habilitarse directamente sobre una IP pública.

## Buenas prácticas

* Diseñar aplicaciones e infraestructura con redundancia y resiliencia.
* Implementar seguridad multicapa para red, aplicación y datos.
* Preparar un plan de respuesta a incidentes para coordinar la actuación frente a ataques DDoS.
