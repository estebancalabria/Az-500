# Planeamiento e implementación de las rutas definidas por el usuario

## Rutas definidas por el usuario

Puede crear rutas personalizadas o definidas por el usuario (estáticas) en Azure para invalidar las rutas predeterminadas del sistema de Azure o para agregar más rutas a la tabla de rutas de una subred. En Azure, creará una tabla de rutas y, a continuación, asociará la tabla de rutas a cero o más subredes de red virtual. Cada subred puede tener cero o una tabla de rutas asociada. Al crear una tabla de rutas y asociarla a una subred, las rutas de la tabla se combinan con las rutas predeterminadas de la subred. Si hay asignaciones de rutas en conflicto, las rutas definidas por el usuario invalidan las rutas predeterminadas.

### Tipos de próximo salto

- **Aplicación virtual:** una aplicación virtual es una máquina virtual que normalmente ejecuta una aplicación de red, como un firewall. Al crear una ruta con el tipo de salto de aplicación virtual, también se especifica una dirección IP del próximo salto.
  - Puede ser la dirección IP privada de una interfaz de red conectada a una máquina virtual.
  - La interfaz debe tener habilitado el reenvío IP de Azure si reenvía tráfico a una dirección distinta de la suya.
  - También puede ser la dirección IP privada de un equilibrador de carga interno de Azure.
- **Puerta de enlace de red virtual:** permite enrutar tráfico destinado a prefijos de dirección específicos a una puerta de enlace de red virtual.
  - La puerta de enlace debe crearse con el tipo VPN.
  - No se puede especificar una puerta de enlace ExpressRoute en una ruta definida por el usuario porque con ExpressRoute se debe usar BGP para rutas personalizadas.
  - Puede definirse una ruta con `0.0.0.0/0` como destino hacia una puerta de enlace de red virtual basada en rutas.
- **Ninguna:** se utiliza cuando se desea colocar tráfico en un prefijo de dirección en lugar de reenviarlo a un destino.
- **Red virtual:** permite invalidar el enrutamiento predeterminado dentro de una red virtual.
- **Internet:** permite enrutar explícitamente tráfico destinado a un prefijo de dirección a Internet o mantener dentro de la red troncal de Azure el tráfico destinado a servicios de Azure con direcciones IP públicas.

No se puede especificar **Emparejamiento de red virtual** o **VirtualNetworkServiceEndpoint** como tipo de próximo salto en una ruta definida por el usuario. Estas rutas solo las crea Azure al configurar un emparejamiento de red virtual o un punto de conexión de servicio.

## Etiquetas de servicio para rutas definidas por el usuario

Una ruta definida por el usuario puede utilizar una etiqueta de servicio como prefijo de dirección en lugar de un intervalo IP explícito.

Una etiqueta de servicio representa un grupo de prefijos de direcciones IP de un servicio de Azure determinado. Microsoft administra los prefijos de direcciones incluidos y actualiza automáticamente la etiqueta cuando las direcciones cambian.

Actualmente, se pueden crear 25 o menos rutas con etiquetas de servicio en cada tabla de rutas. También se admite el uso de etiquetas de servicio en escenarios de enrutamiento para contenedores.

### Coincidencia exacta

Cuando existe una coincidencia exacta de prefijo entre una ruta con un prefijo IP explícito y una ruta con una etiqueta de servicio, el sistema da preferencia a la ruta con el prefijo explícito.

Cuando varias rutas con etiquetas de servicio tienen prefijos IP coincidentes, se evalúan en este orden:

1. Etiquetas regionales, por ejemplo `Storage.EastUS` o `AppService.AustraliaCentral`.
2. Etiquetas de nivel superior, por ejemplo `Storage` o `AppService`.
3. Etiquetas regionales de AzureCloud, por ejemplo `AzureCloud.canadacentral` o `AzureCloud.eastasia`.
4. La etiqueta `AzureCloud`.

### Ejemplo con PowerShell

```powershell
$param = @{
    Name = 'StorageRoute'
    AddressPrefix = 'Storage'
    NextHopType = 'VirtualAppliance'
    NextHopIpAddress = '10.0.100.4'
}
New-AzRouteConfig @param
````

### Ejemplo con Azure CLI

```azurecli
az network route-table route create \
    --resource-group MyResourceGroup \
    --route-table-name MyRouteTable \
    --name StorageRoute \
    --address-prefix Storage \
    --next-hop-type VirtualAppliance \
    --next-hop-ip-address 10.0.100.4
```

## Tipos de próximo salto en las herramientas de Azure

El nombre utilizado para los tipos de próximo salto es diferente entre Azure Portal, las herramientas de línea de comandos y los modelos de implementación clásico y Resource Manager.

| Tipo de próximo salto                         | CLI de Azure y PowerShell (Resource Manager) | CLI clásica de Azure y PowerShell (clásico) |
| --------------------------------------------- | -------------------------------------------- | ------------------------------------------- |
| Puerta de enlace de red virtual               | `VirtualNetworkGateway`                      | Puerta de enlace VPN                        |
| Red de área virtual                           | `VNetLocal`                                  | `VNETLocal`                                 |
| Internet                                      | `Internet`                                   | `Internet`                                  |
| Aplicación virtual                            | `VirtualAppliance`                           | `VirtualAppliance`                          |
| Ninguno                                       | `Ninguno`                                    | `Null`                                      |
| Emparejamiento de redes virtuales de Azure    | Emparejamiento de redes virtuales de Azure   | No aplicable                                |
| Puntos de conexión de servicio de red virtual | `VirtualNetworkServiceEndpoint`              | No aplicable                                |

## Protocolo de puerta de enlace de frontera

Una puerta de enlace de red local puede intercambiar rutas con una puerta de enlace de red virtual de Azure mediante BGP.

El uso de BGP con una puerta de enlace de red virtual de Azure depende del tipo seleccionado:

* **ExpressRoute:** debe usar BGP para anunciar rutas locales para el enrutador perimetral de Microsoft Edge. No puede crear UDR para forzar el tráfico a una puerta de enlace de red virtual de ExpressRoute si implementa una puerta de enlace de tipo ExpressRoute. Puede usar UDR para forzar el tráfico desde ExpressRoute a una aplicación virtual de red.
* **VPN:** opcionalmente, puede usar BGP.

Al intercambiar rutas con Azure mediante BGP, se agrega una ruta independiente a la tabla de rutas de todas las subredes de una red virtual para cada prefijo anunciado. La ruta se agrega con **Puerta de enlace de red virtual** como origen y tipo de próximo salto.

Puede deshabilitar la propagación de rutas de ExpressRoute y Azure VPN Gateway en una subred mediante una propiedad en una tabla de rutas. Al deshabilitar la propagación de rutas, el sistema no agrega rutas a la tabla de rutas de las subredes que tengan deshabilitada la propagación de rutas de puerta de enlace de red virtual.

La propagación de rutas no debe deshabilitarse en `GatewaySubnet`, porque la puerta de enlace no funcionará si esta configuración está deshabilitada.

## Selección de rutas por parte de Azure

Cuando se envía tráfico saliente desde una subred, Azure selecciona una ruta en función de la dirección IP de destino utilizando el algoritmo de **coincidencia de prefijo más largo**.

Por ejemplo, si una tabla de rutas contiene:

* `10.0.0.0/24`
* `10.0.0.0/16`

El tráfico destinado a `10.0.0.5` utiliza la ruta `10.0.0.0/24`, porque es un prefijo más largo.

El tráfico destinado a `10.0.1.5` utiliza la ruta `10.0.0.0/16`, porque `10.0.1.5` no está incluido en `10.0.0.0/24`.

Si varias rutas contienen el mismo prefijo de dirección, Azure selecciona la ruta según esta prioridad:

1. Ruta definida por el usuario.
2. Ruta BGP.
3. Ruta del sistema.

Las rutas de sistema para el tráfico relacionado con la red virtual, los emparejamientos de la red virtual o los puntos de conexión de servicio de red virtual son las rutas preferidas. Se prefieren incluso si las rutas BGP son más específicas.

Las rutas con un punto de conexión de servicio de red virtual como tipo de próximo salto no se pueden invalidar, incluso cuando se utiliza una tabla de rutas.

Por ejemplo:

| Fuente         | Prefijos de dirección | Tipo del próximo salto          |
| -------------- | --------------------- | ------------------------------- |
| Predeterminado | `0.0.0.0/0`           | Internet                        |
| Usuario        | `0.0.0.0/0`           | Puerta de enlace de red virtual |

Cuando el tráfico está destinado a una dirección IP fuera de los prefijos de dirección de cualquier otra ruta de la tabla, Azure selecciona la ruta con origen de usuario porque las UDR tienen mayor prioridad que las rutas predeterminadas del sistema.

## Prefijo de dirección `0.0.0.0/0`

Una ruta con el prefijo de dirección `0.0.0.0/0` proporciona instrucciones a Azure para enrutar el tráfico destinado a una dirección IP que no está dentro del prefijo de dirección de ninguna otra ruta de una tabla de rutas de una subred.

Cuando se crea una subred, Azure crea una ruta predeterminada para `0.0.0.0/0` con el tipo de próximo salto **Internet**.

Si esta ruta no se reemplaza, Azure enruta a Internet todo el tráfico destinado a direcciones IP no incluidas en el prefijo de dirección de otra ruta.

La excepción es que el tráfico dirigido a las direcciones IP públicas de los servicios de Azure permanece en la red troncal de Azure y no se enruta a Internet.

Al invalidar esta ruta con una ruta personalizada, el tráfico destinado a direcciones que no están dentro de los prefijos de dirección de cualquier otra ruta de la tabla se dirige al destino especificado en la ruta personalizada, que puede ser una aplicación virtual de red o una puerta de enlace de red virtual.

Al invalidar el prefijo `0.0.0.0/0`, el tráfico saliente de la subred fluye a través de la puerta de enlace de red virtual o la aplicación virtual.

### Cambios en el enrutamiento predeterminado

* Azure envía todo el tráfico al tipo de próximo salto especificado en la ruta, incluido el tráfico destinado a direcciones IP públicas de los servicios de Azure.
* Cuando el tipo de próximo salto es **Internet**, el tráfico destinado a direcciones IP públicas de servicios de Azure permanece en la red troncal de Azure.
* Al crear una ruta UDR o BGP con una puerta de enlace de red virtual o una aplicación virtual como próximo salto, todo el tráfico se envía al tipo de próximo salto especificado.
* Al habilitar un punto de conexión de servicio para un servicio, Azure crea una ruta con prefijos de dirección para el servicio. Estos prefijos son más largos que `0.0.0.0/0`, por lo que el tráfico al servicio no se enruta al próximo salto definido por la ruta `0.0.0.0/0`.

### Acceso desde Internet

Al invalidar `0.0.0.0/0`, ya no se puede acceder directamente a los recursos de la subred desde Internet. El acceso puede realizarse indirectamente mediante el dispositivo especificado como próximo salto.

Si el próximo salto es una **Aplicación virtual**, el dispositivo debe:

* Ser accesible desde Internet.
* Tener una dirección IP pública asignada.
* No tener una regla del grupo de seguridad de red que impida la comunicación con el dispositivo.
* No denegar la comunicación.
* Poder traducir y reenviar direcciones de red, o desviar el tráfico mediante un servidor proxy al recurso de destino y devolver el tráfico a Internet.

Si el próximo salto es una **Puerta de enlace de red virtual** y la puerta de enlace es una puerta de enlace ExpressRoute, un dispositivo local conectado a Internet puede realizar traducción de dirección de red y reenviar, o actuar como proxy para el tráfico hacia el recurso de destino mediante el emparejamiento privado de ExpressRoute.

Si la red virtual está conectada a una instancia de Azure VPN Gateway, no se debe asociar una tabla de rutas a la subred de puerta de enlace que incluya una ruta con destino `0.0.0.0/0`, ya que la puerta de enlace puede no funcionar correctamente.

## Ejemplo de enrutamiento

El ejemplo de enrutamiento describe:

* Un escenario con requisitos.
* Las rutas personalizadas necesarias para cumplir los requisitos.
* Las tablas de rutas de una subred que incluyen las rutas predeterminadas y personalizadas necesarias.

Este ejemplo no pretende ser una implementación aconsejable ni unos procedimientos recomendados. Se proporciona únicamente para ilustrar los conceptos.

### Requisitos

1. Implementar dos redes virtuales en la misma región de Azure y habilitar los recursos necesarios para la comunicación entre ellas.
2. Habilitar una red local para comunicarse de forma segura con ambas redes virtuales mediante un túnel VPN a través de Internet. Alternativamente, se puede utilizar ExpressRoute, pero en este ejemplo se utiliza una conexión VPN.
3. Para una subred de una red virtual:

   * Enrutar todo el tráfico saliente desde la subred a través de una aplicación virtual de red para inspección y registro, excluyendo el tráfico a Azure Storage y dentro de la subred.
   * No inspeccionar el tráfico entre direcciones IP privadas dentro de la subred.
   * Permitir que el tráfico fluya directamente entre todos los recursos.
   * Eliminar todo el tráfico saliente destinado a la otra red virtual.
   * Permitir que el tráfico saliente a Azure Storage fluya directamente al almacenamiento, sin pasar por una aplicación virtual de red.
4. Permitir todo el tráfico entre las restantes subredes y las redes virtuales.

## Tablas de rutas

### Subnet1

La tabla de rutas de `Subnet1` contiene:

| ID | Fuente         | Estado    | Prefijos de dirección | Tipo del próximo salto                     | Dirección IP del próximo salto | Nombre UDR     |
| -- | -------------- | --------- | --------------------- | ------------------------------------------ | ------------------------------ | -------------- |
| 1  | Predeterminado | No válido | `10.0.0.0/16`         | Red de área virtual                        |                                |                |
| 2  | Usuario        | Activo    | `10.0.0.0/16`         | Aplicación virtual                         | `10.0.100.4`                   | Within-VNet1   |
| 3  | Usuario        | Activo    | `10.0.0.0/24`         | Red de área virtual                        |                                | Within-Subnet1 |
| 4  | Predeterminado | No válido | `10.1.0.0/16`         | Emparejamiento de redes virtuales de Azure |                                |                |
| 5  | Predeterminado | No válido | `10.2.0.0/16`         | Emparejamiento de redes virtuales de Azure |                                |                |
| 6  | Usuario        | Activo    | `10.1.0.0/16`         | Ninguno                                    |                                | ToVNet2-1-Drop |
| 7  | Usuario        | Activo    | `10.2.0.0/16`         | Ninguno                                    |                                | ToVNet2-2-Drop |
| 8  | Predeterminado | No válido | `10.10.0.0/16`        | Puerta de enlace de red virtual            | `[X.X.X.X]`                    |                |
| 9  | Usuario        | Activo    | `10.10.0.0/16`        | Aplicación virtual                         | `10.0.100.4`                   | To-On-Prem     |
| 10 | Predeterminado | Activo    | `[X.X.X.X]`           | VirtualNetworkServiceEndpoint              |                                |                |
| 11 | Predeterminado | No válido | `0.0.0.0/0`           | Internet                                   |                                |                |
| 12 | Usuario        | Activo    | `0.0.0.0/0`           | Aplicación virtual                         | `10.0.100.4`                   | Default-NVA    |

### Explicación de las rutas de Subnet1

* **ID1:** Azure agregó automáticamente esta ruta para todas las subredes de `Virtual-network-1` porque `10.0.0.0/16` es el único intervalo de direcciones definido en el espacio de direcciones de la red virtual. Sin la UDR de ID2, el tráfico destinado a cualquier dirección entre `10.0.0.1` y `10.0.255.254` se enruta dentro de la red virtual.
* **ID2:** Azure agregó esta ruta cuando una UDR para `10.0.0.0/16` se asoció a `Subnet1`. La UDR especifica `10.0.100.4` como dirección IP de la aplicación virtual. Reemplaza la ruta predeterminada ID1.
* **ID3:** Azure agregó esta ruta cuando una UDR para `10.0.0.0/24` se asoció a `Subnet1`. El tráfico destinado a direcciones entre `10.0.0.1` y `10.0.0.254` permanece dentro de la subred. No se enruta a la aplicación virtual de ID2 porque ID3 tiene un prefijo más largo.
* **ID4:** Azure agregó esta ruta automáticamente para todas las subredes de `Virtual-network-1` cuando la red virtual se emparejó con `Virtual-network-2`. `Virtual-network-2` tiene los intervalos `10.1.0.0/16` y `10.2.0.0/16`.
* **ID5:** misma explicación que ID4.
* **ID6:** Azure agregó esta ruta cuando una UDR para `10.1.0.0/16` se asoció a `Subnet1`. El tráfico destinado a ese intervalo se elimina en lugar de enviarse a la red virtual emparejada.
* **ID7:** misma explicación que ID6.
* **ID8:** Azure agregó automáticamente esta ruta cuando se creó una puerta de enlace de red virtual de tipo VPN. El tráfico destinado a `10.10.0.0/16` se enruta a la puerta de enlace de red virtual.
* **ID9:** Azure agregó esta ruta cuando se agregó una UDR para `10.10.0.0/16`. Reemplaza ID8 y envía el tráfico destinado a la red local a una aplicación virtual de red para su inspección.
* **ID10:** Azure agregó automáticamente esta ruta cuando se habilitó un punto de conexión de servicio para un servicio de Azure en la subred. El tráfico se dirige a una dirección IP pública del servicio a través de la infraestructura de Azure.
* **ID11:** Azure agregó automáticamente esta ruta a las tablas de rutas de todas las subredes de `Virtual-network-1` y `Virtual-network-2`. `0.0.0.0/0` es el prefijo más corto y funciona como ruta predeterminada.
* **ID12:** Azure agregó esta ruta cuando una UDR para `0.0.0.0/0` se asoció a `Subnet1`. Especifica `10.0.100.4` como dirección IP de la aplicación virtual. Todo el tráfico que no coincida con otros prefijos se envía a la aplicación virtual.

### Subnet2

La tabla de rutas de `Subnet2` contiene:

| Fuente         | Estado | Prefijos de dirección | Tipo del próximo salto                     | Dirección IP del próximo salto |
| -------------- | ------ | --------------------- | ------------------------------------------ | ------------------------------ |
| Predeterminado | Activo | `10.0.0.0/16`         | Red de área virtual                        |                                |
| Predeterminado | Activo | `10.1.0.0/16`         | Emparejamiento de redes virtuales de Azure |                                |
| Predeterminado | Activo | `10.2.0.0/16`         | Emparejamiento de redes virtuales de Azure |                                |
| Predeterminado | Activo | `10.10.0.0/16`        | Puerta de enlace de red virtual            | `[X.X.X.X]`                    |
| Predeterminado | Activo | `0.0.0.0/0`           | Internet                                   |                                |
| Predeterminado | Activo | `10.0.0.0/8`          | Ninguno                                    |                                |
| Predeterminado | Activo | `100.64.0.0/10`       | Ninguno                                    |                                |
| Predeterminado | Activo | `192.168.0.0/16`      | Ninguno                                    |                                |

La tabla de rutas de `Subnet2` contiene todas las rutas predeterminadas creadas por Azure, además de las rutas opcionales de emparejamiento de red virtual y de puerta de enlace de red virtual.

Azure agregó las rutas opcionales a todas las subredes de la red virtual cuando se agregaron la puerta de enlace y el emparejamiento a la red virtual.

Azure quitó las rutas de los prefijos `10.0.0.0/8`, `192.168.0.0/16` y `100.64.0.0/10` de la tabla de rutas de `Subnet1` cuando se agregó a `Subnet1` la UDR del prefijo `0.0.0.0/0`.

# Planeamiento e implementación del emparejamiento de Red Virtual o puerta de enlace de Red Virtual

Una red virtual es una parte virtual y aislada de la red pública de Azure. De forma predeterminada, el tráfico no se puede enrutar entre dos redes virtuales. Sin embargo, es posible conectar redes virtuales, ya sea dentro de una sola región o entre dos regiones, para que el tráfico se pueda enrutar entre ellas.

## Tipos de conexión de red virtual

### Emparejamiento de red virtual

El emparejamiento de redes virtuales conecta dos redes virtuales de Azure. Una vez emparejadas, las redes virtuales aparecen como una sola a efectos de conectividad.

El tráfico entre las máquinas virtuales en las redes virtuales emparejadas se enruta a través de la infraestructura de red troncal de Microsoft, únicamente a través de direcciones IP privadas. No implica la red pública de Internet.

También puede emparejar redes virtuales entre regiones de Azure mediante **emparejamiento global**.

### Puertas de enlace de VPN

Una puerta de enlace de VPN es un tipo específico de puerta de enlace de red virtual que se usa para enviar tráfico entre una red virtual de Azure y una ubicación local a través de la red pública de Internet.

También puede utilizar una puerta de enlace de VPN para enviar tráfico entre redes virtuales de Azure.

Cada red virtual puede tener como máximo una puerta de enlace de VPN.

Debe habilitar el estándar de protección contra denegación de servicio distribuido (DDoS) de Azure en cualquier red virtual perimetral.

### Comparación general

El emparejamiento de red virtual proporciona una conexión de ancho de banda alto y de baja latencia. No hay ninguna puerta de enlace en la ruta de acceso, por lo que no hay saltos adicionales, lo que garantiza conexiones de baja latencia.

Resulta útil en escenarios como:

* Replicación de datos entre regiones.
* Conmutación por error de la base de datos.

Dado que el tráfico es privado y permanece en la red troncal de Microsoft, también se puede considerar el emparejamiento de redes virtuales si existen directivas de datos estrictas y se quiere evitar el envío de tráfico a través de Internet.

Las puertas de enlace de VPN proporcionan una conexión de ancho de banda limitada y son útiles en escenarios en los que se necesita cifrado, pero se pueden tolerar restricciones de ancho de banda. En estos escenarios, los clientes tampoco son tan sensibles a la latencia.

## Tránsito de puerta de enlace

El emparejamiento de redes virtuales y las puertas de enlace de VPN también pueden coexistir a través del **tránsito de puerta de enlace**.

El tránsito de puerta de enlace permite usar la puerta de enlace de una red virtual emparejada para conectarse al entorno local, en lugar de crear una nueva puerta de enlace para la conectividad.

A medida que aumentan las cargas de trabajo en Azure, es necesario escalar las redes entre regiones y redes virtuales para mantenerse al día con el crecimiento.

El tránsito de puerta de enlace permite compartir una puerta de enlace de ExpressRoute o VPN con todas las redes virtuales emparejadas y administrar la conectividad en un solo lugar.

El uso compartido permite:

* Ahorrar costos.
* Reducir la sobrecarga de administración.

Con el tránsito de puerta de enlace habilitado en el emparejamiento de red virtual, se puede crear una **red virtual de tránsito** que contenga:

* La puerta de enlace de VPN.
* La aplicación virtual de red.
* Otros servicios compartidos.

A medida que la organización crece con nuevas aplicaciones o unidades de negocio y pone en marcha nuevas redes virtuales, puede conectarse a la red virtual de tránsito mediante el emparejamiento.

Esto evita agregar complejidad a la red y reduce la sobrecarga de administrar varias puertas de enlace y otros dispositivos.

## Configuración de conexiones

El emparejamiento de redes virtuales y las puertas de enlace de VPN admiten los siguientes tipos de conexión:

* Redes virtuales en diferentes regiones.
* Redes virtuales en distintos inquilinos de Microsoft Entra.
* Redes virtuales en distintas suscripciones de Azure.
* Redes virtuales que usan una combinación de modelos de implementación de Azure, Resource Manager y clásico.

## Comparación del emparejamiento de redes virtuales y VPN Gateway

| Elemento                           | Interconexión de redes virtuales                                                                                                                                | VPN Gateway                                                                                                                                                    |
| ---------------------------------- | --------------------------------------------------------------------------------------------------------------------------------------------------------------- | -------------------------------------------------------------------------------------------------------------------------------------------------------------- |
| **Límites**                        | Hasta 500 emparejamientos de red virtual por red virtual                                                                                                        | Una puerta de enlace de VPN por red virtual. El número máximo de túneles por puerta de enlace depende de la SKU de puerta de enlace                            |
| **Modelo de precios**              | Entrada/salida                                                                                                                                                  | Salida por hora y salida                                                                                                                                       |
| **Encriptación**                   | Se recomienda el cifrado de nivel de software                                                                                                                   | La directiva IPsec/IKE personalizada se puede aplicar a conexiones nuevas o existentes                                                                         |
| **Limitaciones de ancho de banda** | Sin limitaciones de ancho de banda                                                                                                                              | Varía en función de la SKU                                                                                                                                     |
| **¿Privado?**                      | Sí. Enrutado a través de la red troncal de Microsoft y privada. No hay Internet público implicado                                                               | IP pública implicada, pero enrutada a través de la red troncal de Microsoft si está habilitada la red global de Microsoft                                      |
| **Relación transitiva**            | Las conexiones de emparejamiento no son transitivas. Las redes transitivas se pueden lograr mediante NVA o puertas de enlace en la red virtual del concentrador | Si las redes virtuales están conectadas a través de puertas de enlace de VPN y BGP está habilitada en las conexiones de red virtual, la transitividad funciona |
| **Hora de configuración inicial**  | Rápido                                                                                                                                                          | ~30 minutos                                                                                                                                                    |
| **Escenarios típicos**             | Replicación de datos, conmutación por error de base de datos y otros escenarios que necesitan copias de seguridad frecuentes de datos grandes                   | Escenarios específicos del cifrado que no son sensibles a la latencia y que no necesitan un alto a lo largo de todo                                            |


```
