# Planificación e Implementación de Seguridad para el Acceso Privado a Recursos de Azure

## Introducción

Este módulo aborda la planificación e implementación de medidas de seguridad para el acceso privado a recursos de Azure, con foco en proteger datos sensibles y la red mediante mecanismos como Service Endpoints, Private Endpoints y Private Link Services.

## Objetivos de aprendizaje

Al finalizar este módulo, se podrá:

- Planificar e implementar estrategias de seguridad para el acceso privado a recursos de Azure.
- Utilizar Virtual Network Service Endpoints para proteger el acceso a servicios de Azure.
- Implementar Private Endpoints para conectar redes privadas con servicios de Azure.
- Crear y administrar Private Link Services para exponer recursos propios de forma segura.
- Integrar Azure App Service y Azure Functions con redes virtuales.
- Configurar la seguridad de red de App Service Environment (ASE).
- Configurar la seguridad de red de Azure SQL Managed Instance.

## Virtual Network Service Endpoints

Los VNet Service Endpoints proporcionan conectividad directa y segura desde una VNet hacia servicios de Azure a través de la red troncal de Azure. Permiten restringir los recursos de Azure para que solo sean accesibles desde determinadas redes virtuales, utilizando las direcciones IP privadas de la VNet sin necesidad de una IP pública.

> **Nota:** Microsoft recomienda Azure Private Link y Private Endpoints como la opción preferida para el acceso privado y seguro a servicios de Azure.

### Servicios compatibles

- Azure Storage
- Azure SQL Database
- Azure Synapse Analytics
- Azure Database for PostgreSQL
- Azure Database for MySQL
- Azure Database for MariaDB
- Azure Cosmos DB
- Azure Key Vault
- Azure Service Bus
- Azure Event Hubs
- Azure App Service
- Azure Cognitive Services

### Beneficios

- **Mayor seguridad:** permite asociar los recursos de Azure a una VNet mediante reglas de red y eliminar el acceso público a los recursos.
- **Enrutamiento optimizado:** el tráfico hacia los servicios de Azure utiliza directamente la red troncal de Azure, sin verse afectado por rutas que fuerzan el tráfico de Internet hacia redes locales o dispositivos virtuales.
- **Configuración sencilla:** no requiere IP públicas reservadas, NAT ni dispositivos de gateway; se configura directamente sobre una subnet.

### Limitaciones

- Solo está disponible para VNets implementadas mediante Azure Resource Manager.
- Se configura en subnets y no permite tráfico desde servicios locales hacia Azure a través del endpoint.
- Para Azure SQL, el Service Endpoint solo aplica al tráfico dentro de la región de la VNet.
- Una VNet puede asociarse hasta con 200 combinaciones de suscripciones y regiones por cada servicio compatible con reglas VNet activas.

### Configuración y funcionamiento

- Los endpoints se configuran sobre una subnet y funcionan con cualquier instancia de cómputo dentro de ella.
- Una subnet puede tener múltiples Service Endpoints.
- Para Azure SQL Database, la VNet debe estar en la misma región que el servicio; para el resto de los servicios compatibles puede estar en otra región.
- La VNet y el recurso de Azure pueden pertenecer a la misma o a diferentes suscripciones.
- Al habilitar un Service Endpoint, el tráfico hacia el servicio cambia de utilizar una IP pública a utilizar la IP privada de la VNet como dirección de origen.
- Las reglas de firewall existentes basadas en IP públicas pueden dejar de funcionar tras el cambio.
- El cambio puede provocar una interrupción temporal y cerrar conexiones TCP existentes.

### Acceso desde redes locales

Los recursos de Azure protegidos mediante Service Endpoints no son accesibles desde redes locales de forma predeterminada. Para permitir ese acceso, se deben autorizar en el firewall del recurso las IP públicas (normalmente de NAT) utilizadas por la red local o por ExpressRoute.

### NSG y Service Endpoints

Los NSG permiten por defecto el tráfico saliente hacia Internet y el tráfico de la VNet hacia los servicios de Azure. Para restringir el tráfico saliente únicamente a determinados servicios, se pueden utilizar **Service Tags** en las reglas del NSG.

### Escenarios de uso

- Proteger servicios de Azure desde múltiples subnets o VNets conectadas mediante peering.
- Filtrar tráfico saliente hacia servicios de Azure mediante un Network Virtual Appliance.
- Proteger recursos de Azure desplegados directamente en subnets administradas.
- Restringir el acceso REST a determinados recursos de Azure Storage.

### Diagnóstico

El funcionamiento del Service Endpoint puede verificarse de dos formas:

- Comprobando la IP de origen de las solicitudes en los diagnósticos del servicio: con el endpoint habilitado se muestra la IP privada de la VNet.
- Consultando las rutas efectivas de una interfaz de red: la ruta hacia el servicio aparece con `nextHopType: VirtualNetworkServiceEndpoint`.

Las rutas de Service Endpoint tienen prioridad sobre las rutas BGP para el prefijo correspondiente al servicio.

### Permisos

Los Service Endpoints pueden configurarse sobre una VNet con permisos de escritura. Para proteger recursos de Azure mediante una VNet se requiere el permiso:

```
Microsoft.Network/virtualNetworks/subnets/joinViaServiceEndpoint/action
```

Las redes virtuales y los recursos de Azure pueden pertenecer a diferentes suscripciones y, en determinados servicios, incluso a diferentes tenants de Microsoft Entra ID.

### Costos y límites

- Los Service Endpoints no tienen cargos adicionales.
- No existe un límite total de Service Endpoints por VNet.
- Algunos servicios, como Azure Storage, pueden establecer límites sobre la cantidad de subnets utilizadas para proteger el recurso.

### VNet Service Endpoint Policies

Las VNet Service Endpoint Policies permiten filtrar el tráfico de una VNet hacia servicios de Azure para autorizar únicamente recursos específicos, proporcionando un control de acceso más granular.

## Private Endpoints

Un **Private Endpoint** es una interfaz de red con una dirección IP privada de una VNet que permite conectarse de forma privada y segura a un servicio respaldado por Azure Private Link, incorporando ese servicio a la red virtual. Puede utilizarse con Azure Storage, Azure Cosmos DB, Azure SQL Database y servicios propios expuestos mediante Private Link Service.

### Propiedades

Un Private Endpoint define:

- **Nombre:** único dentro del Resource Group.
- **Subnet:** donde se implementa y desde la que obtiene la IP privada.
- **Private-link resource:** recurso al que se conecta, mediante Resource ID o alias.
- **Target subresource:** subrecurso específico al que se conecta.
- **Connection approval method:** aprobación automática o manual.
- **Request message:** mensaje opcional para solicitudes de aprobación manual.
- **Connection status:** estado de la conexión (Approved, Pending, Rejected o Disconnected). Solo los endpoints aprobados pueden enviar tráfico.

### Consideraciones generales

- Permiten conexiones desde la misma VNet, VNets con peering regional o global, entornos on-premises mediante VPN o ExpressRoute, y servicios respaldados por Private Link.
- Las conexiones solo pueden iniciarse desde los clientes hacia el Private Endpoint.
- Se crea automáticamente una interfaz de red de solo lectura con una IP privada dinámica de la subnet, que permanece durante todo el ciclo de vida del endpoint.
- El Private Endpoint debe estar en la misma región y suscripción que la VNet; el recurso Private Link puede estar en otra región.
- Se pueden crear múltiples Private Endpoints para un mismo recurso, aunque se recomienda uno por recurso cuando existe una configuración DNS común, para evitar conflictos.
- Los Private Endpoints pueden estar en la misma subnet o en subnets diferentes dentro de una VNet.

### Seguridad

Los Private Endpoints permiten proteger el tráfico hacia un recurso Private Link y validar que las conexiones lleguen al recurso especificado. Para acceder a diferentes subrecursos de un mismo servicio pueden ser necesarios distintos Private Endpoints; por ejemplo, Azure Storage requiere endpoints separados para file y blob.

El Private Endpoint proporciona una IP privada para acceder al servicio, pero **no deshabilita necesariamente su acceso público**, por lo que pueden requerirse controles adicionales. También admite políticas de red mediante NSG, UDR y ASG.

### Aprobación de conexiones

Existen dos métodos:

- **Automática:** cuando el usuario tiene permisos sobre el recurso Private Link.
- **Manual:** cuando no tiene esos permisos; la solicitud queda en estado Pending hasta que el propietario del recurso la aprueba.

El propietario puede revisar, aprobar, rechazar o eliminar conexiones. Solo una conexión en estado Approved puede enviar tráfico.

### Alias

Un alias es un identificador único generado por el propietario de un Private Link Service. Los consumidores pueden utilizar el URI del recurso o el alias para solicitar una conexión. Al usar alias se aplica el método de aprobación manual, aunque la solicitud puede aprobarse automáticamente si la suscripción del consumidor está permitida por el proveedor.

### DNS

Para acceder a un servicio mediante Private Endpoint se requieren configuraciones DNS específicas, normalmente mediante **Private DNS Zones**. El FQDN utilizado para conectarse debe resolver hacia la IP privada del Private Endpoint. La interfaz de red del endpoint contiene el FQDN y la IP privada necesarios para configurar el DNS.

### Limitaciones

- La configuración de IP estática no está disponible actualmente para determinados servicios.
- Las rutas y reglas de seguridad efectivas no se muestran para la NIC del Private Endpoint en Azure Portal.
- Los NSG Flow Logs no están disponibles para tráfico entrante hacia un Private Endpoint.
- Un Application Security Group asociado a un Private Endpoint tiene un máximo de 50 miembros.
- Existen límites para las combinaciones de rangos de puertos y direcciones en reglas NSG.
- El filtrado por puerto de origen no es un escenario válido de filtrado para tráfico dirigido a un Private Endpoint.
- Algunas funcionalidades no están disponibles en determinadas regiones.

### NSG, UDR y ASG

- El tráfico saliente denegado desde un Private Endpoint no es un escenario válido, ya que el proveedor del servicio no puede iniciar tráfico.
- Algunos servicios pueden requerir que todos los puertos de destino estén abiertos cuando se utilizan Private Endpoints junto con filtros NSG.
- Para UDR, se recomienda utilizar SNAT para garantizar que el tráfico de retorno sea correctamente encaminado.
- Existen limitaciones regionales para UDR y Application Security Groups.

## Private Link Services

Un **Azure Private Link Service** permite exponer un servicio propio —ejecutándose detrás de un Azure Standard Load Balancer— para que los consumidores puedan acceder a él de forma privada desde sus propias VNets mediante Private Endpoints.

### Creación y uso

1. La aplicación debe ejecutarse detrás de un Standard Load Balancer.
2. Se crea el Private Link Service asociado al frontend del Load Balancer.
3. Se selecciona una subnet para las IP de NAT; se recomienda disponer de al menos ocho IP de NAT.
4. El tráfico de los consumidores aparecerá ante el proveedor como proveniente de las IP privadas del pool de NAT.
5. Azure genera un alias globalmente único, que puede compartirse con los consumidores junto con el URI del recurso.
6. Los consumidores utilizan el alias o el URI para iniciar una conexión Private Link.
7. El proveedor puede aceptar o rechazar las solicitudes de conexión, administradas mediante la propiedad `privateEndpointConnections`.

### Propiedades

Un Private Link Service incluye:

- **Provisioning State:** estado de aprovisionamiento.
- **Alias:** identificador globalmente único para compartir el servicio.
- **Visibility:** controla quién puede solicitar acceso.
- **Auto Approval:** permite aprobar automáticamente conexiones de suscripciones autorizadas.
- **Load Balancer frontend IP configuration:** frontend del Standard Load Balancer que recibe el tráfico.
- **NAT IP configuration:** IP utilizada para realizar NAT del tráfico.
- **Private endpoint connections:** conexiones de Private Endpoints asociadas.
- **TCP Proxy V2:** permite obtener información de conexión del consumidor mediante el protocolo Proxy v2.

### Conectividad

Un Private Link Service puede ser accedido por Private Endpoints aprobados desde:

- La misma VNet.
- VNets con peering regional o global.
- Entornos on-premises mediante VPN privada o ExpressRoute.

El Private Link Service debe estar en la misma región que la VNet y el Standard Load Balancer. Un mismo servicio puede ser utilizado por múltiples Private Endpoints pertenecientes a diferentes VNets, suscripciones o tenants.

Puede utilizar hasta ocho IP de NAT para escalar las conexiones. Una vez agregadas, las IP de NAT no pueden eliminarse, para evitar afectar conexiones activas.

### Estructura del alias

El alias tiene la estructura:

```
Prefix.GUID.Suffix
```

- **Prefix:** nombre elegido para el servicio; no puede modificarse después de crear el alias.
- **GUID:** identificador global generado por Azure.
- **Suffix:** agregado automáticamente por Azure, con la región y `azure.privatelinkservice`.

### Control de exposición (Visibility)

La propiedad **Visibility** ofrece tres opciones:

- **Role-based access control only:** acceso mediante permisos RBAC, incluyendo escenarios entre tenants.
- **Restricted by subscription:** acceso limitado a determinadas suscripciones.
- **Anyone with your alias:** cualquier consumidor que disponga del alias puede solicitar una conexión.

### Control de acceso y aprobación

Los consumidores crean un Private Endpoint y solicitan una conexión. La solicitud queda en estado Pending hasta que el proveedor la aprueba, rechaza o elimina. Solo las conexiones aprobadas pueden enviar tráfico.

**Visibility** determina quién puede solicitar acceso, mientras que **Auto Approval** determina qué suscripciones tienen aprobación automática.

### TCP Proxy V2

El tráfico recibido por el servicio utiliza NAT, por lo que la aplicación normalmente recibe la IP NAT del proveedor en lugar de la IP real del consumidor. Si se necesita identificar al consumidor, puede habilitarse **TCP Proxy V2**, que proporciona información como la IP de origen y el LinkID del Private Endpoint.

El proveedor debe configurar correctamente la aplicación para interpretar el encabezado Proxy Protocol V2. Si la configuración del servicio y de la aplicación no coinciden, las solicitudes fallarán.

### Limitaciones

- Solo funciona con Standard Load Balancer, no con Basic Load Balancer.
- El backend del Standard Load Balancer debe estar configurado mediante NIC, no mediante IP.
- Solo admite tráfico IPv4.
- Solo admite tráfico TCP y UDP.
- Tiene un tiempo de inactividad aproximado de 5 minutos (300 segundos); las aplicaciones deben utilizar TCP Keepalives con un intervalo inferior a ese tiempo.

## Conclusión

Este módulo cubrió las principales estrategias para asegurar el acceso privado a recursos de Azure: Service Endpoints para conectividad optimizada sobre la red troncal, Private Endpoints para incorporar servicios de Azure a la red virtual mediante IP privada, y Private Link Services para exponer aplicaciones propias de forma privada y controlada a consumidores externos.
