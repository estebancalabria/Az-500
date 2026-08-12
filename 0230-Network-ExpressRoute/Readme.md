# Azure ExpressRoute

ExpressRoute le permite ampliar las redes locales a la nube de Microsoft a través de una conexión privada con la ayuda de un proveedor de conectividad. Con ExpressRoute, puede establecer conexiones a servicios en la nube de Microsoft, como Microsoft Azure y Microsoft 365.

La conectividad puede ser desde una red de conectividad universal (IP VPN), una red Ethernet de punto a punto o una conexión cruzada virtual a través de un proveedor de conectividad en una instalación de ubicación compartida. Las conexiones ExpressRoute ofrecen más confiabilidad, velocidades más rápidas, latencias coherentes y mayor seguridad que las conexiones típicas a través de Internet, ya que no pasan por la red pública de Internet.

## Ventajas clave

* Conectividad de nivel 3 entre la red local y Microsoft Cloud a través de un proveedor de conectividad. La conectividad puede ser desde una red de cualquiera a cualquiera (IPVPN), una conexión Ethernet de punto a punto o mediante una conexión cruzada virtual por un intercambio Ethernet.
* Conectividad con los servicios en la nube de Microsoft en todas las regiones de la región geopolítica.
* Conectividad global con los servicios de Microsoft en todas las regiones con el complemento Premium de ExpressRoute.
* Enrutamiento dinámico entre la red y Microsoft a través del protocolo de puerta de enlace de borde (BGP).
* Redundancia integrada en cada ubicación de interconexión para una mayor fiabilidad.
* Contrato de nivel de servicio (SLA) de tiempo de actividad de conexión.
* Compatibilidad con calidad de servicio (QoS) para Skype Empresarial.

## Características

### Conectividad de nivel 3

Microsoft usa BGP, un protocolo de enrutamiento dinámico estándar del sector, para intercambiar rutas entre la red local, las instancias de Azure y las direcciones públicas de Microsoft. Establecemos varias sesiones BGP con tu red para diferentes perfiles de tráfico.

### Redundancia

Cada circuito ExpressRoute consta de dos conexiones a dos enrutadores perimetrales de Microsoft Enterprise (MSEE) en una ubicación de ExpressRoute proporcionada por el proveedor de conectividad o en el perímetro de su red.

Microsoft requiere conexiones BGP duales bien por parte del proveedor de conectividad o desde el perímetro de su red, una a cada MSEE.

Es posible que decida no implementar dispositivos redundantes o circuitos Ethernet al final. Sin embargo, los proveedores de conectividad usan dispositivos redundantes para asegurarse de que las conexiones se entregan a Microsoft de forma redundante.

### Conectividad con los servicios en la nube de Microsoft

Las conexiones de ExpressRoute permiten el acceso a los siguientes servicios:

* Servicios de Microsoft Azure.
* Servicios de Microsoft 365.

### Conectividad a todas las regiones dentro de una región geopolítica

Puede conectarse a Microsoft desde una de nuestras ubicaciones de emparejamiento y acceder a regiones dentro de la misma región geopolítica.

Por ejemplo, si se conecta a Microsoft en Ámsterdam a través de ExpressRoute, tiene acceso a todos los servicios en la nube de Microsoft hospedados en Norte y Oeste de Europa.

### Conectividad global con ExpressRoute Premium

Puede habilitar ExpressRoute Premium para ampliar la conectividad entre límites geopolíticas.

Si se conecta a Microsoft en Ámsterdam a través de ExpressRoute, tiene acceso a todos los servicios en la nube de Microsoft hospedados en todas las regiones de todo el mundo. Por ejemplo, tiene acceso a los servicios implementados en Oeste de EE. UU. o Este de Australia de la misma manera que accede a las regiones Norte y Oeste de Europa.

Se excluyen las nubes nacionales.

### Conectividad local con ExpressRoute Local

Puede transferir datos de forma rentable habilitando las unidades de almacenamiento de existencias locales (SKU).

Con la SKU local, puede traer los datos a una ubicación de ExpressRoute cerca de la región de Azure que desee. Con Local, la transferencia de datos se incluye en el cargo de puerto de ExpressRoute.

### Conectividad local con Global Reach de ExpressRoute

Al activar Global Reach de ExpressRoute, puede intercambiar datos entre sus sitios locales a través de los circuitos de ExpressRoute.

Por ejemplo, supongamos que tiene dos centros de datos privados, uno en California y otro en Texas, cada uno conectado a un circuito ExpressRoute en sus respectivas regiones. Puede usar Global Reach de ExpressRoute para vincular los centros de datos con estos circuitos y el tráfico entre centros de datos usa la red de Microsoft.

### Ecosistema de asociados de conectividad enriquecido

ExpressRoute tiene un ecosistema constantemente creciente de proveedores de conectividad y asociados integradores de sistemas.

### Conectividad a nubes nacionales

Microsoft opera entornos de nube aislados para regiones geopolíticas especiales y segmentos de clientes.

### ExpressRoute Direct

ExpressRoute Direct proporciona a los clientes la oportunidad de conectarse directamente a la red global de Microsoft en ubicaciones de emparejamiento distribuidas estratégicamente en todo el mundo.

ExpressRoute Direct proporciona conectividad dual de 100 Gbps, que admite conectividad activa/activa a escala.

Entre las características clave que proporciona ExpressRoute Direct se incluyen, pero no se limitan a:

* Ingesta masiva de datos en servicios como Azure Storage y Azure Cosmos DB.
* Aislamiento físico para sectores regulados y que requieren conectividad dedicada y aislada. Por ejemplo: bancos, gobiernos y minoristas.
* Control pormenorizado de la distribución de circuitos en función de la unidad de negocio.

## Opciones de ancho de banda

Puede comprar circuitos ExpressRoute para una amplia gama de anchos de banda. Los anchos de banda admitidos se enumeran de la manera siguiente. Asegúrese de comprobar con el proveedor de conectividad para determinar los anchos de banda que admiten.

* 50 megabits por segundo (Mbps)
* 100 Mbps
* 200 Mbps
* 500 Mbps
* 1 gigabit por segundo (Gbps)
* 2 Gbps
* 5 Gbps
* 10 Gbps

La redundancia integrada de su circuito se configura utilizando conexiones primarias y secundarias, cada una de las cuales tiene el ancho de banda adquirido, a dos enrutadores Microsoft Enterprise Edge (MSEE).

El ancho de banda disponible a través de la conexión secundaria se puede usar para más tráfico si es necesario. Dado que la conexión secundaria está pensada para redundancia, no está garantizada y no debe usarse para el tráfico adicional durante un período de tiempo sostenido.

Si tiene previsto usar solo la conexión principal para transmitir tráfico, el ancho de banda de la conexión es fijo e intentar sobrescribirlo da como resultado un aumento de las caídas de paquetes.

## Escalado dinámico del ancho de banda

Puede aumentar el ancho de banda del circuito ExpressRoute sin tener que anular las conexiones.

## Modelos de facturación flexibles

Puede seleccionar un modelo de facturación que funcione mejor para usted:

* **Datos ilimitados:** la facturación se basa en una cuota mensual; todas las transferencias de datos entrantes y salientes se incluyen de forma gratuita.
* **Datos medidos:** la facturación se basa en una cuota mensual; todas las transferencias de datos entrantes son gratuitas. La transferencia de datos salientes se cobra por GB de transferencia de datos. Las tasas de transferencia de datos varían según la región.
* **Complemento Premium de ExpressRoute:** ExpressRoute Premium es un complemento para el circuito ExpressRoute. El complemento ExpressRoute Premium ofrece las siguientes capacidades:

  * Se han aumentado los límites de rutas para el emparejamiento público y privado de Azure, pasando de 4.000 rutas a 10.000 rutas.
  * Conectividad global para servicios. Un circuito ExpressRoute creado en cualquier región (excepto las nubes nacionales) tiene acceso a los recursos de todas las demás regiones del mundo. Por ejemplo, se puede acceder a una red virtual creada en Oeste de Europa a través de un circuito ExpressRoute aprovisionado en Silicon Valley.
  * Se ha incrementado el número de vínculos de red virtual por circuito ExpressRoute, de 10 a un límite superior, que depende del ancho de banda del circuito.

# Implementación del cifrado a través de ExpressRoute

Implemente Azure Virtual WAN para establecer una conexión VPN de IPsec/IKE desde la red local a Azure por medio del emparejamiento privado de un circuito Azure ExpressRoute.

Esta técnica puede proporcionar un tránsito cifrado entre las redes locales y las redes virtuales de Azure a través de ExpressRoute sin necesidad de pasar por la red pública de Internet ni utilizar direcciones IP públicas.

## Topología y enrutamiento

El diagrama muestra una red dentro de la red local conectada a la puerta de enlace de VPN del centro de conectividad de Azure a través del emparejamiento privado de ExpressRoute.

El establecimiento de la conectividad es sencillo:

1. Establezca la conectividad de ExpressRoute con un circuito ExpressRoute y emparejamiento privado.
2. Establezca la conectividad VPN como se describe en este artículo.

Un aspecto importante de esta configuración es el enrutamiento entre las redes locales y Azure a través de las rutas de acceso de ExpressRoute y VPN.

## Tráfico desde redes locales a Azure

En el caso del tráfico entre las redes locales y Azure, los prefijos de Azure, lo que incluye el centro de conectividad virtual y todas las redes virtuales de radio conectadas a ese centro, se anuncian a través de los protocolos de puerta de enlace de borde del emparejamiento privado de ExpressRoute y de la red privada virtual.

El resultado son dos rutas de red (rutas de acceso) hacia Azure desde las redes locales:

* Una sobre la ruta de acceso protegida mediante IPsec.
* Otra directamente sobre ExpressRoute, sin protección de IPsec.

Para aplicar el cifrado a la comunicación, debe asegurarse de que para la red conectada a VPN del diagrama, se prefieren las rutas de Azure a través de la puerta de enlace de VPN local a la ruta de acceso directa de ExpressRoute.

## Tráfico desde Azure a las redes locales

El mismo requisito se aplica al tráfico de Azure a las redes locales.

Para asegurarse de que la ruta de acceso de IPsec se elige antes que la ruta de acceso directa de ExpressRoute (sin IPsec), tiene dos opciones:

* **Anuncie prefijos más específicos en la sesión BGP de VPN para la red conectada a VPN.** Puede anunciar un rango mayor que abarque la red conectada a VPN a través del emparejamiento privado de ExpressRoute y, después, rangos más específicos en la sesión del protocolo de puerta de enlace de borde de VPN. Por ejemplo, anuncie `10.0.0.0/16` a través de ExpressRoute y `10.0.1.0/24` a través de VPN.
* **Anuncie prefijos disjuntos para VPN y ExpressRoute.** Si los rangos de redes conectadas a VPN no están unidos a otras redes conectadas de ExpressRoute, puede anunciar los prefijos en las sesiones de los protocolos de puerta de enlace de borde de ExpressRoute y VPN, respectivamente. Por ejemplo, anuncie `10.0.0.0/24` a través de ExpressRoute y `10.0.1.0/24` a través de VPN.

En ambos ejemplos, Azure enviará tráfico a `10.0.1.0/24` a través de la conexión VPN en lugar de hacerlo directamente a través de ExpressRoute sin protección VPN.

## Antes de empezar

Antes de iniciar la configuración, compruebe que cumple los siguientes criterios:

* Si ya tiene una red virtual a la que quiere conectarse, compruebe que ninguna de las subredes de la red local se superponga con ella. La red virtual no requiere una subred de puerta de enlace y no puede tener ninguna puerta de enlace de red virtual. Si no tiene una red virtual, siga los pasos de este artículo para crear una.
* Obtenga un intervalo de direcciones IP para la región del concentrador. El centro de conectividad es una red virtual, y el intervalo de direcciones que especifique para la región del concentrador no se puede superponer con ninguna de las redes virtuales existentes a las que esté conectado. Tampoco se puede superponer con los intervalos de direcciones a los que se conecte en el entorno local. Si no está familiarizado con los intervalos de direcciones IP ubicados en la configuración de red local, consulte a alguien que pueda proporcionarle estos detalles.
* Si no tiene una suscripción a Azure, cree una cuenta gratuita antes de empezar.

## 1. Creación de una red virtual WAN y un concentrador con puertas de enlace

Antes de continuar deben estar en vigor los siguientes recursos de Azure y las configuraciones locales correspondientes:

* Una Azure Virtual WAN.
* Un centro de Virtual WAN con ExpressRoute y una puerta de enlace de red privada virtual.

## 2. Creación de un sitio para la red local

El recurso del sitio es el mismo que el de los sitios VPN que no son de ExpressRoute para una red de área extensa virtual.

La dirección IP del dispositivo VPN local ahora puede ser una dirección IP privada o una dirección IP pública en la red local accesible a través de la configuración de emparejamiento privado de ExpressRoute creada anteriormente.

1. Vaya a la **red de Virtual WAN**, a los sitios **VPN** y cree un sitio para la red local. Tenga en cuenta los siguientes valores de configuración:

   * **Protocolo de puerta de enlace de borde:** seleccione "Habilitar" si la red local usa el protocolo de puerta de enlace de borde.
   * **Espacio de direcciones privadas:** escriba el espacio de direcciones IP que se encuentra en el sitio local. El tráfico destinado a este espacio de direcciones se enruta a la red local a través de la puerta de enlace de VPN.
2. Seleccione **Vínculos** para agregar información sobre los vínculos físicos. Tenga en cuenta la siguiente información de configuración:

   * **Nombre de proveedor:** el nombre del proveedor de acceso a Internet de este sitio. En el caso de la red local de ExpressRoute, es el nombre del proveedor de servicios de ExpressRoute.
   * **Velocidad:** la velocidad del vínculo de servicio de Internet o del circuito de ExpressRoute.
   * **Dirección IP:** la dirección IP pública del dispositivo VPN que reside en el sitio local. O bien, en el entorno local de ExpressRoute, es la dirección IP privada del dispositivo VPN a través de ExpressRoute.
   * Si BGP está habilitado, se aplica a todas las conexiones creadas para este sitio en Azure. La configuración del protocolo de puerta de enlace de borde en Virtual WAN es igual a su configuración en Azure VPN Gateway.
   * La dirección de su par BGP local **no debe** ser la misma que la dirección IP de la VPN al dispositivo o al espacio de direcciones de la red virtual del sitio VPN. Use otra dirección IP en el dispositivo VPN para la dirección IP del par BGP. Puede ser una dirección asignada a la interfaz de bucle invertido en el dispositivo. Sin embargo, **no puede** ser una dirección IP privada automática (`169.254.x.x`). Especifique esta dirección en el sitio VPN correspondiente que representa la ubicación.
3. Seleccione **Siguiente: Revisar y Crear** para comprobar los valores de configuración y crear el sitio VPN y, a continuación, **Crear** el sitio.
4. A continuación, conecte el sitio al centro. La actualización de la puerta de enlace puede tardar hasta **30 minutos**.

## 3. Actualización de la configuración de conexión VPN para usar ExpressRoute

Después de crear el sitio VPN y conectarlo al centro de conectividad, use estos pasos para configurar la conexión para que use el emparejamiento privado de ExpressRoute:

1. Vaya al centro de conectividad virtual. Para ello, vaya a Virtual WAN y seleccione el centro para abrir la página del centro, o bien puede ir al centro virtual conectado desde el sitio VPN.
2. En **Conectividad**, seleccione **VPN (de sitio a sitio)**.
3. Seleccione los puntos suspensivos (**...**) o haga clic con el botón derecho en el sitio VPN sobre ExpressRoute y seleccione **Editar la conexión de VPN a este concentrador**.
4. En la página **Aspectos básicos**, deje los valores predeterminados.
5. En la página **Vincular conexión 1**, configure las siguientes opciones:

   * En **Usar dirección IP privada de Azure**, seleccione **Sí**. El ajuste configura la puerta de enlace de VPN de concentrador para que use direcciones IP privadas dentro del intervalo de direcciones del concentrador en la puerta de enlace para esta conexión, en lugar de las direcciones IP públicas. Esto garantiza que el tráfico de la red local atraviesa las rutas de acceso del emparejamiento privado de ExpressRoute, en lugar de usar la red pública de Internet para esta conexión VPN.
6. Haga clic en **Crear** para actualizar la configuración. Una vez creada la configuración, la puerta de enlace de VPN de concentrador usará las direcciones IP privadas en la puerta de enlace de VPN para establecer las conexiones de IPsec/IKE con el dispositivo VPN local a través de ExpressRoute.

## 4. Obtención de las direcciones IP privadas de puerta de enlace de VPN del centro de conectividad

Descargue la configuración del dispositivo VPN para obtener las direcciones IP privadas de la puerta de enlace de VPN del centro de conectividad. Estas direcciones son necesarias para configurar el dispositivo VPN local.

1. En la página del centro de conectividad, seleccione **VPN (sitio a sitio)** en **Conectividad**.
2. En la parte superior de la página **Información general**, seleccione **Descargar configuración de VPN**. Azure crea una cuenta de almacenamiento en el grupo de recursos `microsoft-network-[location]`, donde `location` es la ubicación de la red WAN. Después de aplicar la configuración a los dispositivos VPN, puede eliminar esta cuenta de almacenamiento.
3. Después de que se cree el archivo, seleccione el vínculo para descargarlo.
4. Aplique la configuración al dispositivo VPN.

### Archivo de configuración del dispositivo VPN

El archivo de configuración del dispositivo contiene la configuración que se debe usar al configurar el dispositivo VPN local. Cuando visualice este archivo, tenga en cuenta la siguiente información:

* **vpnSiteConfiguration:** en esta sección se indica la configuración de los detalles del dispositivo como un sitio que se conecta a la WAN virtual. Incluye el nombre y la dirección IP pública del dispositivo de rama.
* **vpnSiteConnections:** en esta sección se proporciona información sobre la siguiente configuración:

  * Espacio de direcciones de la red virtual del centro de conectividad virtual.
    Ejemplo: `"AddressSpace":"10.51.230.0/24"`
  * Espacio de direcciones de las redes virtuales que están conectadas al centro de conectividad.
    Ejemplo: `"ConnectedSubnets":["10.51.231.0/24"]`
  * Direcciones IP de la puerta de enlace de VPN del centro de conectividad virtual. Dado que cada conexión de la puerta de enlace de VPN consta de dos túneles en una configuración activo-activo, verá ambas direcciones IP en este archivo. En este ejemplo, verá Instance0 e Instance1 para cada sitio, son direcciones IP privadas en lugar de direcciones IP públicas.
    Ejemplo: `"Instance0":"10.51.230.4" "Instance1":"10.51.230.5"`
  * Detalles de la configuración de la conexión de la puerta de enlace de VPN, como BGP y clave precompartida. La clave precompartida se genera automáticamente. La conexión se puede modificar en cualquier momento en la página **Información general** de una clave precompartida personalizada.

### Configuración del dispositivo VPN

Si necesita instrucciones para configurar el dispositivo, puede utilizar las que se proporcionan en la página de scripts de configuración de dispositivo VPN con las siguientes advertencias:

* Las instrucciones de la página del dispositivo VPN no están escritas para una red de área extensa virtual. Sin embargo, puede usar los valores de la red de área extensa virtual del archivo de configuración para configurar manualmente el dispositivo VPN.
* Los scripts de configuración de dispositivo descargables que sean para la puerta de enlace de VPN no funcionan para la red de área extensa virtual, ya que la configuración es diferente.
* Una red de área extensa virtual nueva puede admitir IKEv1 e IKEv2.
* Una red de área extensa virtual solo puede usar dispositivos VPN basados en rutas e instrucciones de los dispositivos.

## 5. Visualizar la instancia de Virtual WAN

1. Vaya a la red de área extensa virtual.
2. En la página **Información general**, cada punto del mapa representa un centro de conectividad.
3. En la sección **Centros y conexiones**, puede ver el centro de conectividad, sitio, región y el estado de la conexión VPN. También puede ver los bytes que entran y salen.

## 6. Supervisar una conexión

Cree una conexión para supervisar la comunicación entre una máquina virtual de Azure y un sitio remoto.
