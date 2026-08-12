# Resumen: Planifique e implemente una Red de Área Amplia Virtual, incluido el concentrador virtual protegido

## Virtual WAN y conexión VPN

* Usar **Virtual WAN** para conectarse a los recursos de Azure mediante una conexión VPN de **IPsec/IKE (IKEv1 e IKEv2)**.
* Este tipo de conexión requiere un **dispositivo VPN local con una dirección IP pública asignada**.

## Prerrequisitos

* Comprobar que se tiene una suscripción a Azure.
* Decidir el intervalo de direcciones IP para el espacio de direcciones privadas del centro virtual.
* Un **centro de conectividad virtual** es una red virtual que Virtual WAN crea y usa. Es el núcleo de la red Virtual WAN en una región.
* El intervalo de direcciones del centro:

  * No se puede superponer con ninguna red virtual existente a la que se conecta.
  * No se puede superponer con los intervalos de direcciones locales a los que se conecta.
* Si no se conocen los intervalos de direcciones IP de la red local, se debe consultar a alguien que pueda proporcionar estos detalles.

## Azure Portal o Azure PowerShell

* Se puede usar **Azure Portal** o **Azure PowerShell** para crear una conexión de sitio a sitio para Azure Virtual WAN.
* **Cloud Shell** es un shell interactivo gratuito con herramientas comunes de Azure preinstaladas y configuradas para usar con la cuenta.
* Para abrir Cloud Shell:

  * Seleccionar **Abrir Cloud Shell** en la esquina superior derecha de un bloque de código.
  * También se puede abrir en una pestaña independiente del explorador mediante `https://shell.azure.com/powershell`.
* Se pueden copiar los bloques de código, pegarlos en Cloud Shell y ejecutarlos.
* También se pueden instalar y ejecutar los cmdlets de Azure PowerShell localmente.
* Los cmdlets de PowerShell se actualizan con frecuencia. Si no está instalada la versión más reciente, los valores de las instrucciones pueden producir errores.
* Para buscar las versiones de Azure PowerShell instaladas:

  ```powershell
  Get-Module -ListAvailable Az
  ```

## Iniciar sesión

### Azure Cloud Shell

* Al abrir Azure Cloud Shell, se dirige automáticamente al inicio de sesión.
* No es necesario ejecutar `Connect-AzAccount`.
* Se pueden cambiar las suscripciones mediante:

  ```powershell
  Get-AzSubscription
  Select-AzSubscription
  ```

### PowerShell local

* Abrir la consola de PowerShell con privilegios elevados.
* Conectarse a la cuenta de Azure mediante:

  ```powershell
  Connect-AzAccount
  ```
* El cmdlet solicita las credenciales.
* Después de autenticarse, se descarga la configuración de la cuenta para que esté disponible para Azure PowerShell.
* Se puede cambiar la suscripción mediante:

  ```powershell
  Get-AzSubscription
  Select-AzSubscription -SubscriptionName "Name of subscription"
  ```

## Creación de una instancia de Virtual WAN

* Antes de crear una WAN virtual, se debe crear un grupo de recursos para hospedarla o utilizar uno existente.
* Ejemplo para crear un grupo de recursos denominado `TestRG` en Este de EE. UU.:

  ```powershell
  New-AzResourceGroup -Location "East US" -Name "TestRG"
  ```
* Crear la WAN virtual mediante `New-AzVirtualWan`:

  ```powershell
  $virtualWan = New-AzVirtualWan -ResourceGroupName TestRG -Name TestVWAN1 -Location "East US"
  ```

## Creación del centro y configuración de sus valores

* Un **centro de conectividad** es una red virtual que puede contener puertas de enlace para funcionalidades de:

  * Sitio a sitio.
  * ExpressRoute.
  * Punto a sitio.
* Crear un centro virtual mediante `New-AzVirtualHub`.
* Ejemplo:

  ```powershell
  $virtualHub = New-AzVirtualHub -VirtualWan $virtualWan -ResourceGroupName "TestRG" -Name "Hub1" -AddressPrefix "10.1.0.0/16" -Location "westus"
  ```

## Crear una puerta de enlace de VPN de sitio a sitio

* Se crea una puerta de enlace de VPN de sitio a sitio en la misma ubicación del centro virtual.
* Al crear la puerta de enlace se especifican las unidades de escalado.
* La puerta de enlace tarda aproximadamente **30 minutos** en crearse.

1. Si se cerró Azure Cloud Shell o expiró la conexión, puede ser necesario volver a declarar `$virtualHub`:

   ```powershell
   $virtualHub = Get-AzVirtualHub -ResourceGroupName "TestRG" -Name "Hub1"
   ```

2. Crear la puerta de enlace mediante `New-AzVpnGateway`:

   ```powershell
   New-AzVpnGateway -ResourceGroupName "TestRG" -Name "vpngw1" -VirtualHubId $virtualHub.Id -VpnGatewayScaleUnit 2
   ```

3. Ver la puerta de enlace creada:

   ```powershell
   Get-AzVpnGateway -ResourceGroupName "TestRG" -Name "vpngw1"
   ```

## Creación de un sitio y conexiones

* Los sitios se corresponden con las ubicaciones físicas y las conexiones.
* Los sitios contienen los puntos de conexión de los dispositivos VPN locales.
* Se pueden crear hasta **1000 sitios por centro virtual** en una WAN virtual.
* Si hay varios centros de conectividad, se pueden crear **1000 sitios en cada uno**.

### Configuración del sitio

1. Establecer la variable para VPN Gateway y para el espacio de direcciones IP del sitio local.

* El tráfico destinado a este espacio de direcciones se enruta al sitio local.
* Esto es necesario cuando no se habilita BGP para el sitio.

```powershell
$vpnGateway = Get-AzVpnGateway -ResourceGroupName "TestRG" -Name "vpngw1"
$vpnSiteAddressSpaces = New-Object string[] 2
$vpnSiteAddressSpaces[0] = "192.168.2.0/24"
$vpnSiteAddressSpaces[1] = "192.168.3.0/24"
```

2. Crear vínculos con información sobre los vínculos físicos de la rama, incluyendo:

* Velocidad del vínculo.
* Nombre del proveedor.
* Dirección IP pública del dispositivo local.

```powershell
$vpnSiteLink1 = New-AzVpnSiteLink -Name "TestSite1Link1" -IpAddress "15.25.35.45" -LinkProviderName "SomeTelecomProvider" -LinkSpeedInMbps "10"

$vpnSiteLink2 = New-AzVpnSiteLink -Name "TestSite1Link2" -IpAddress "15.25.35.55" -LinkProviderName "SomeTelecomProvider2" -LinkSpeedInMbps "100"
```

3. Crear el sitio VPN mediante `New-AzVpnSite`.

Si se cerró Azure Cloud Shell o expiró la conexión, volver a declarar la variable de Virtual WAN:

```powershell
$virtualWan = Get-AzVirtualWAN -ResourceGroupName "TestRG" -Name "TestVWAN1"
```

Crear el sitio:

```powershell
$vpnSite = New-AzVpnSite -ResourceGroupName "TestRG" -Name "TestSite1" -Location "westus" -VirtualWan $virtualWan -AddressSpace $vpnSiteAddressSpaces -DeviceModel "SomeDevice" -DeviceVendor "SomeDeviceVendor" -VpnSiteLink @($vpnSiteLink1, $vpnSiteLink2)
```

4. Crear las conexiones de los vínculos del sitio.

* La conexión está compuesta por **dos túneles activo-activo** desde una rama o sitio hacia la puerta de enlace escalable.

```powershell
$vpnSiteLinkConnection1 = New-AzVpnSiteLinkConnection -Name "TestLinkConnection1" -VpnSiteLink $vpnSite.VpnSiteLinks[0] -ConnectionBandwidth 100

$vpnSiteLinkConnection2 = New-AzVpnSiteLinkConnection -Name "testLinkConnection2" -VpnSiteLink $vpnSite.VpnSiteLinks[1] -ConnectionBandwidth 10
```

## Conexión del sitio VPN a un centro

1. Antes de ejecutar el comando, puede ser necesario volver a declarar las variables:

```powershell
$virtualWan = Get-AzVirtualWAN -ResourceGroupName "TestRG" -Name "TestVWAN1"
$vpnGateway = Get-AzVpnGateway -ResourceGroupName "TestRG" -Name "vpngw1"
$vpnSite = Get-AzVpnSite -ResourceGroupName "TestRG" -Name "TestSite1"
```

2. Conectar el sitio VPN con el centro de conectividad:

```powershell
New-AzVpnConnection -ResourceGroupName $vpnGateway.ResourceGroupName -ParentResourceName $vpnGateway.Name -Name "testConnection" -VpnSite $vpnSite -VpnSiteLinkConnection @($vpnSiteLinkConnection1, $vpnSiteLinkConnection2)
```

## Conexión de una red virtual al centro

* El siguiente paso es conectar el centro a la red virtual.
* Si se creó un nuevo grupo de recursos para el ejercicio, normalmente no habrá una red virtual en el grupo de recursos.
* Se puede crear una red virtual y después crear una conexión entre el centro de conectividad y la red virtual.

### Creación de una red virtual

1. Crear una red virtual:

```powershell
$vnet = @{ Name = 'VNet1' ResourceGroupName = 'TestRG' Location = 'eastus' AddressPrefix = '10.21.0.0/16' } $virtualNetwork = New-AzVirtualNetwork @vnet
```

2. Especificar la configuración de subred:

```powershell
$subnet = @{ Name = 'Subnet-1' VirtualNetwork = $virtualNetwork AddressPrefix = '10.21.0.0/24' } $subnetConfig = Add-AzVirtualNetworkSubnetConfig @subnet
```

3. Establecer la red virtual:

```powershell
$virtualNetwork | Set-AzVirtualNetwork
```

### Conectar una red virtual a un concentrador

* Se puede conectar la red virtual al centro virtual mediante PowerShell o Azure Portal.
* Se deben repetir estos pasos para cada red virtual que se quiera conectar.

Antes de crear una conexión:

* Una red virtual solo se puede conectar a **un centro virtual a la vez**.
* Para conectarla a un centro virtual, la red virtual remota **no debe tener ninguna puerta de enlace**.
* Algunas opciones de configuración, como **Propagar ruta estática**, solo se pueden configurar actualmente en Azure Portal.
* Si existen puertas de enlace de VPN en el centro virtual, esta operación y cualquier otra operación de escritura en la red virtual conectada pueden provocar:

  * Desconexión de clientes de punto a sitio.
  * Reconexión de túneles de sitio a sitio.
  * Reconexión de sesiones de **protocolo de puerta de enlace de borde (BGP)**.

### Agregar una conexión

1. Declarar las variables de los recursos existentes, incluida la red virtual:

```powershell
$resourceGroup = Get-AzResourceGroup -ResourceGroupName "TestRG" $virtualWan = Get-AzVirtualWan -ResourceGroupName "TestRG" -Name "TestVWAN1" $virtualHub = Get-AzVirtualHub -ResourceGroupName "TestRG" -Name "Hub1" $remoteVirtualNetwork = Get-AzVirtualNetwork -Name "VNet1" -ResourceGroupName "TestRG"
```

2. Crear una conexión para emparejar la red virtual con el centro de conectividad virtual:

```powershell
New-AzVirtualHubVnetConnection -ResourceGroupName "TestRG" -VirtualHubName "Hub1" -Name "VNet1-connection" -RemoteVirtualNetwork $remoteVirtualNetwork
```

## Configurar dispositivo VPN

### Descargar configuración VPN

* Usar el archivo de configuración de dispositivos VPN para configurar el dispositivo VPN local.

Pasos básicos:

1. En la página de Virtual WAN, ir a **Centros de conectividad → Centro de conectividad virtual → VPN (de sitio a sitio)**.
2. En la parte superior de la página **VPN (de sitio a sitio)**, seleccionar **Descargar la configuración de VPN**.
3. Azure crea una cuenta de almacenamiento en el grupo de recursos `microsoft-network-[ubicación]`, donde `ubicación` corresponde a la ubicación de la WAN.
4. También se puede agregar una cuenta de almacenamiento existente mediante **Usar existente** y proporcionando una URL de SAS válida con permisos de escritura habilitados.
5. Una vez creado el archivo, seleccionar el vínculo para descargarlo.
6. El archivo se crea con la configuración de VPN en la ubicación de URL de SAS proporcionada.
7. Aplicar la configuración al dispositivo VPN local.
8. Después de aplicar la configuración a los dispositivos VPN, no es necesario mantener la cuenta de almacenamiento creada.

La configuración contiene:

* **Espacio de direcciones de la red virtual del centro o centros de conectividad virtual**.

  * Ejemplo:

    ```text
    "AddressSpace":"10.1.0.0/24"
    ```
* **Espacio de direcciones de las redes virtuales conectadas al centro de conectividad virtual**.

  * Ejemplo:

    ```text
    "ConnectedSubnets":["10.2.0.0/16","10.3.0.0/16"]
    ```
* **Espacio de direcciones IP del centro de conectividad virtual VPN Gateway**.

  * Cada conexión VPN Gateway se compone de dos túneles en configuración activo-activo.
  * Se muestran ambas direcciones IP.
  * Ejemplos:

    ```text
    "Instance0":"nnn.nn.nn.nnn"
    "Instance1":"nnn.nn.nn.nnn"
    ```
* **Dirección IP pública:** asignada por Azure.
* **Dirección IP privada:** asignada por Azure.
* **Dirección IP BGP predeterminada:** asignada por Azure.
* **Dirección IP BGP personalizada:** campo reservado para APIPA (direccionamiento IP privado automático).

  * Azure admite IP de BGP en los intervalos `169.254.21.*` y `169.254.22.*`.
  * Azure acepta conexiones BGP en estos intervalos, pero marca la conexión con la IP de BGP predeterminada.
  * Se pueden especificar varias direcciones IP BGP personalizadas para cada instancia.
  * No se debe usar la misma dirección IP BGP personalizada para ambas instancias.

## Archivo de configuración del dispositivo VPN

* El archivo de configuración del dispositivo contiene la configuración que se debe utilizar para configurar el dispositivo VPN local.

### `vpnSiteConfiguration`

* Indica los detalles del dispositivo configurado como un sitio que se conecta a la WAN virtual.
* Incluye el nombre y la dirección IP pública del dispositivo de rama.

### `vpnSiteConnections`

Proporciona información sobre la configuración, incluyendo:

* Detalles de configuración de conexión de VPN Gateway.
* BGP.
* Clave precompartida.
* **PSK:** clave precompartida generada automáticamente.
* La PSK se puede editar desde la página **Información general** para establecer una clave previamente compartida personalizada.

## Archivo de configuración de dispositivo de ejemplo

```powershell
{ "configurationVersion":{ "LastUpdatedTime":"2018-07-03T18:29:49.8405161Z", "Version":"r403583d-9c82-4cb8-8570-1cbbcd9983b5" }, "vpnSiteConfiguration":{ "Name":"testsite1", "IPAddress":"73.239.3.208" }, "vpnSiteConnections":[ { "hubConfiguration":{ "AddressSpace":"10.1.0.0/24", "Region":"West Europe", "ConnectedSubnets":[ "10.2.0.0/16", "10.3.0.0/16" ] }, "gatewayConfiguration":{ "IpAddresses":{ "Instance0":"104.45.18.186", "Instance1":"104.45.13.195" } }, "connectionConfiguration":{ "IsBgpEnabled":false, "PSK":"bkOWe5dPPqkx0DfFE3tyuP7y3oYqAEbI", "IPsecParameters":{ "SADataSizeInKilobytes":102400000, "SALifeTimeInSeconds":3600 } } } ] }, { "configurationVersion":{ "LastUpdatedTime":"2018-07-03T18:29:49.8405161Z", "Version":"1f33f891-e1ab-42b8-8d8c-c024d337bcac" }, "vpnSiteConfiguration":{ "Name":" testsite2", "IPAddress":"66.193.205.122" }, "vpnSiteConnections":[ { "hubConfiguration":{ "AddressSpace":"10.1.0.0/24", "Region":"West Europe" }, "gatewayConfiguration":{ "IpAddresses":{ "Instance0":"104.45.18.187", "Instance1":"104.45.13.195" } }, "connectionConfiguration":{ "IsBgpEnabled":false, "PSK":"XzODPyAYQqFs4ai9WzrJour0qLzeg7Qg", "IPsecParameters":{ "SADataSizeInKilobytes":102400000, "SALifeTimeInSeconds":3600 } } } ] }, { "configurationVersion":{ "LastUpdatedTime":"2018-07-03T18:29:49.8405161Z", "Version":"cd1e4a23-96bd-43a9-93b5-b51c2a945c7" }, "vpnSiteConfiguration":{ "Name":" testsite3", "IPAddress":"182.71.123.228" }, "vpnSiteConnections":[ { "hubConfiguration":{ "AddressSpace":"10.1.0.0/24", "Region":"West Europe" }, "gatewayConfiguration":{ "IpAddresses":{ "Instance0":"104.45.18.187", "Instance1":"104.45.13.195" } }, "connectionConfiguration":{ "IsBgpEnabled":false, "PSK":"YLkSdSYd4wjjEThR3aIxaXaqNdxUwSo9", "IPsecParameters":{ "SADataSizeInKilobytes":102400000, "SALifeTimeInSeconds":3600 } } } ] }
```

## Configuración del dispositivo VPN

* Si se trabaja con una **solución de asociado de WAN virtual**, la configuración de los dispositivos VPN se produce automáticamente.
* El controlador de dispositivos obtiene el archivo de configuración de Azure y lo aplica al dispositivo para configurar la conexión con Azure.
* No es necesario saber cómo configurar manualmente el dispositivo VPN.

## Visualización o edición de la configuración de la puerta de enlace

* Se puede ver y editar la configuración de la instancia de VPN Gateway en cualquier momento.
* Ruta:
  **Centro de conectividad virtual → VPN (de sitio a sitio) → Ver/Configurar**.
* En la página **Editar VPN Gateway** se pueden ver los valores de configuración.

---

# Conectividad VPN segura, incluyendo de punto a sitio y de sitio a sitio

* Existen diferentes configuraciones disponibles para las conexiones de **VPN Gateway**.
* Se debe determinar qué configuración se adapta mejor a las necesidades.
* Los diagramas y descripciones de las conexiones de VPN Gateway ayudan a seleccionar la topología de conexión adecuada.
* Los diagramas muestran las principales topologías de línea base, pero se pueden crear configuraciones más complejas utilizándolos como directrices.

## VPN de sitio a sitio

* Una conexión de gateway VPN de **sitio a sitio (S2S)** se establece mediante un túnel VPN **IPsec/IKE (IKEv1 o IKEv2)**.
* Las conexiones S2S se pueden utilizar para:

  * Configuraciones híbridas.
  * Configuraciones entre locales.
* Una conexión S2S requiere un **dispositivo VPN ubicado en el entorno local con una dirección IP pública asignada**.

### Modos de VPN Gateway

VPN Gateway se puede configurar:

* **Modo activo-en espera**, mediante una dirección IP pública.

  * Un túnel IPsec está activo y el otro está en espera.
  * El tráfico fluye por el túnel activo.
  * Si se produce un problema con el túnel activo, el tráfico pasa al túnel en espera.
* **Modo activo-activo**, mediante dos direcciones IP públicas.

  * Ambos túneles IPsec están activos simultáneamente.
  * Los datos fluyen por ambos túneles al mismo tiempo.
  * Se recomienda configurar VPN Gateway en modo activo-activo.
  * Una ventaja adicional es que los clientes experimentan mayores rendimientos.

### Varias conexiones VPN

* Se puede crear más de una conexión VPN desde la puerta de enlace de red virtual, normalmente conectándose a varios sitios locales.
* Al trabajar con varias conexiones, se debe utilizar un tipo de VPN **RouteBased**, conocido como puerta de enlace dinámica al trabajar con VNets clásicas.
* Cada red virtual solo puede tener una puerta de enlace de VPN.
* Todas las conexiones a través de la puerta de enlace comparten el ancho de banda disponible.
* Este tipo de conexión también se conoce como conexión **multisitio**.

## Red privada virtual de punto a sitio

* Una conexión de VPN Gateway de **punto a sitio (P2S)** permite crear una conexión segura a la red virtual desde un equipo cliente individual.
* La conexión P2S se establece iniciándola desde el equipo cliente.
* Es útil para:

  * Teletrabajadores que necesitan conectarse a redes virtuales de Azure desde una ubicación remota, como casa o un congreso.
  * Sustituir una VPN S2S cuando solo hay algunos clientes que necesitan conectarse a una red virtual.
* A diferencia de las conexiones S2S, las conexiones P2S:

  * No requieren una dirección IP local orientada al público.
  * No requieren un dispositivo VPN.
* Las conexiones P2S se pueden utilizar con conexiones S2S mediante la misma puerta de enlace de VPN, siempre que todos los requisitos de configuración de ambas conexiones sean compatibles.

## Conexiones de red virtual a red virtual

* Una conexión de una red virtual a otra red virtual (**VNet a VNet**) es similar a la conexión de una red virtual a un sitio local.
* Ambos tipos de conectividad utilizan una puerta de enlace de VPN para proporcionar un túnel seguro mediante **IPsec/IKE**.
* La comunicación entre redes virtuales se puede combinar con configuraciones de conexión multisitio.
* Esto permite establecer topologías que combinen:

  * Conectividad entre entornos.
  * Conectividad entre redes virtuales.

Las VNets que se conectan pueden estar:

* En las mismas regiones o en regiones diferentes.
* En la misma suscripción o en suscripciones diferentes.
* En los mismos modelos de implementación o en modelos diferentes.

## Conexiones entre modelos de implementación

* Azure tiene actualmente dos modelos de implementación:

  * **Clásico**.
  * **Resource Manager**.
* Las máquinas virtuales de Azure y los roles de instancia más antiguos pueden ejecutarse en una red virtual clásica.
* Las máquinas virtuales y las instancias de rol más recientes pueden ejecutarse en una red virtual creada en Resource Manager.
* Se puede crear una conexión entre las redes virtuales para permitir que los recursos de una red virtual se comuniquen directamente con los recursos de otra.

## Emparejamiento de red virtual

* Se puede utilizar el **emparejamiento de redes virtuales (emparejamiento de VNet)** para crear la conexión, siempre que la red virtual cumpla determinados requisitos.
* El emparejamiento de VNet **no utiliza una puerta de enlace de red virtual**.

## Conexiones coexistentes de sitio a sitio y ExpressRoute

* **ExpressRoute** es una conexión directa y privada desde la WAN, no a través de la red pública de Internet, a los servicios de Microsoft, incluido Azure.
* El tráfico VPN de sitio a sitio viaja cifrado a través de la red pública de Internet.
* Se pueden configurar conexiones VPN de sitio a sitio y ExpressRoute para la misma red virtual.
* Esto proporciona varias ventajas:

  * Se puede configurar una VPN de sitio a sitio como **ruta de conmutación por error segura para ExpressRoute**.
  * Se puede utilizar VPN de sitio a sitio para conectarse a sitios que no forman parte de la red, pero que están conectados mediante ExpressRoute.
* Esta configuración requiere **dos puertas de enlace de red virtual para la misma red virtual**:

  * Una con el tipo de puerta de enlace **Vpn**.
  * Otra con el tipo de puerta de enlace **ExpressRoute**.
