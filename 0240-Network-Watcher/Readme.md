# Uso de controles de red sólidos

Puede conectar las máquinas virtuales y los dispositivos de Azure a otros dispositivos en red, colocándolos en redes virtuales de Azure. Esto permite conectar tarjetas de interfaz de red virtual a una red virtual para posibilitar las comunicaciones basadas en TCP/IP entre dispositivos habilitados para la red.

Las máquinas virtuales conectadas a una red virtual de Azure pueden conectarse a dispositivos:

* En la misma red virtual.
* En distintas redes virtuales.
* En Internet.
* En sus propias redes locales.

Al planear la red y la seguridad de la red, se recomienda centralizar:

* La administración de funciones de red centrales como ExpressRoute, el aprovisionamiento de redes virtuales y subredes, y la asignación de direcciones IP.
* El gobierno de elementos de seguridad de red.

Si se usa un conjunto común de herramientas de administración para supervisar la red y su seguridad, se obtiene una visibilidad clara de ambos aspectos. Una estrategia de seguridad sencilla y unificada reduce los errores, mejora la comprensión del lado humano y aumenta la confiabilidad de la automatización.

## Segmentación lógica de subredes

Las redes virtuales de Azure son similares a una LAN de red local. Una red virtual de Azure permite crear una red basada en un único espacio de direcciones IP privadas en la que pueden colocarse todas las máquinas virtuales de Azure.

Los espacios de direcciones IP privadas disponibles están en los intervalos:

* Clase A: `10.0.0.0/8`
* Clase B: `172.16.0.0/12`
* Clase C: `192.168.0.0/16`

### Procedimientos recomendados

**No asignar reglas de permiso con intervalos amplios.**

* Por ejemplo, permitir `0.0.0.0` a `255.255.255.255.255`.
* Los procedimientos de solución de problemas deben desalentar o prohibir este tipo de reglas.
* Estas reglas dan una falsa sensación de seguridad y pueden ser detectadas y explotadas por equipos de operaciones clandestinas.

**Segmentar el espacio de direcciones más grande en subredes.**

* Usar principios de subred basados en CIDR para crear las subredes.

**Crear controles de acceso de red entre subredes.**

* El enrutamiento entre subredes se realiza automáticamente y no es necesario configurar manualmente las tablas de enrutamiento.
* De manera predeterminada, no hay controles de acceso de red entre las subredes creadas en una red virtual de Azure.
* Usar un grupo de seguridad de red (NSG) para protegerse contra el tráfico no solicitado en subredes de Azure.
* Los NSG son dispositivos de inspección de paquetes simples y con estado.
* Los NSG utilizan el enfoque de 5 tuplas:

  * IP de origen.
  * Puerto de origen.
  * IP de destino.
  * Puerto de destino.
  * Protocolo.
* Las reglas permiten o deniegan tráfico hacia una sola dirección IP y desde esta, hacia varias direcciones IP y desde estas, o hacia subredes enteras y desde estas.
* Los NSG permiten controlar el acceso a la red entre subredes y establecer recursos que pertenezcan a la misma zona de seguridad o rol en sus propias subredes.

**Evitar pequeñas redes virtuales y subredes para garantizar simplicidad y flexibilidad.**

* La mayoría de las organizaciones agregan más recursos de los previstos inicialmente.
* La reasignación de direcciones requiere mucho trabajo.
* Si se usan subredes pequeñas, el valor de seguridad obtenido es limitado.
* Asignar un grupo de seguridad de red a cada subred supone una sobrecarga.
* Definir subredes amplias para disponer de flexibilidad para crecer.

**Simplificar la administración de reglas de grupo de seguridad de red mediante grupos de seguridad de aplicaciones.**

* Definir un grupo de seguridad de aplicaciones para las listas de direcciones IP que podrían cambiar en el futuro o utilizarse en muchos grupos de seguridad de red.
* Usar nombres claros para los grupos de seguridad de aplicaciones para que otros comprendan su contenido y finalidad.

## Adoptar un método de Confianza cero

Las redes basadas en el perímetro funcionan bajo el supuesto de que se puede confiar en todos los sistemas dentro de una red. Los empleados acceden actualmente a los recursos de la organización desde cualquier lugar y mediante varios dispositivos y aplicaciones, por lo que los controles de seguridad perimetral pueden resultar irrelevantes.

Las directivas de control de acceso que se centran únicamente en quién puede acceder a un recurso no son suficientes. También debe tenerse en cuenta el modo en que se accede a los recursos.

Las redes deben evolucionar de las defensas tradicionales porque pueden ser vulnerables a diversas infracciones. Un atacante puede poner en peligro un único punto de conexión dentro del límite de confianza y expandir rápidamente su presencia en toda la red.

Las redes de Confianza cero eliminan el concepto de confianza según la ubicación de red dentro de un perímetro. Las arquitecturas de Confianza cero utilizan notificaciones de confianza de usuario y dispositivo para obtener acceso a los datos y recursos de la organización.

En nuevas iniciativas, se recomienda adoptar métodos de Confianza cero que validen la confianza en el momento del acceso.

### Procedimientos recomendados

**Conceder acceso condicional a los recursos en función del dispositivo, la identidad, la garantía, la ubicación de red, etc.**

* El acceso condicional de Microsoft Entra permite aplicar los controles de acceso adecuados mediante decisiones de control de acceso automatizadas basadas en las condiciones necesarias.

**Habilitar el acceso al puerto solo después de la aprobación del flujo de trabajo.**

* El acceso a máquinas virtuales Just-In-Time en Microsoft Defender for Cloud permite bloquear el tráfico entrante a las máquinas virtuales de Azure.
* Esto reduce la exposición a ataques y proporciona acceso sencillo para conectarse a las máquinas virtuales cuando sea necesario.

**Conceder permisos temporales para realizar tareas con privilegios.**

* Impide que usuarios malintencionados o no autorizados obtengan acceso después de que los permisos hayan expirado.
* El acceso se concede solo cuando los usuarios lo necesitan.
* Usar el acceso Just-In-Time en Microsoft Entra Privileged Identity Management o en una solución de terceros para conceder permisos para realizar tareas con privilegios.

Confianza cero es la próxima evolución en seguridad de red. El estado de los ataques cibernéticos condiciona a las organizaciones a adquirir una mentalidad de "presunción de infracción". Las redes de Confianza cero protegen los recursos y datos corporativos y permiten crear un área de trabajo moderna mediante tecnologías que permiten a los empleados ser productivos en cualquier momento, lugar y modo.

## Control del comportamiento de enrutamiento

Cuando se coloca una máquina virtual en una red virtual de Azure, puede conectarse a cualquier otra máquina virtual de la misma red virtual, incluso si las otras máquinas virtuales están en subredes diferentes.

Esto es posible porque existen rutas del sistema habilitadas de manera predeterminada que permiten este tipo de comunicación.

Las rutas predeterminadas permiten que las máquinas virtuales de la misma red virtual:

* Inicien conexiones entre sí.
* Inicien conexiones con Internet, únicamente para comunicaciones salientes a Internet.

Las rutas del sistema predeterminadas son útiles para muchos escenarios, pero en algunos casos puede ser necesario personalizar la configuración de enrutamiento.

Se puede configurar la dirección del próximo salto para acceder a destinos específicos.

También se recomienda configurar rutas definidas por el usuario al implementar un dispositivo de seguridad para una red virtual.

**Nota:** las rutas definidas por el usuario no son necesarias y las rutas del sistema predeterminadas funcionan en la mayoría de los casos.

## Uso de aplicaciones de red virtual

Los grupos de seguridad de red y el enrutamiento definido por el usuario pueden proporcionar cierto grado de seguridad de red en las capas de red y transporte del modelo OSI.

En algunas situaciones puede ser necesario habilitar la seguridad en niveles superiores de la pila. En esos casos, se recomienda implementar aplicaciones de seguridad de la red virtual proporcionadas por asociados de Azure.

Las aplicaciones de seguridad de la red de Azure pueden proporcionar niveles de seguridad mejorados respecto de los controles de nivel de red.

Las funcionalidades de seguridad de los dispositivos de seguridad de la red virtual incluyen:

* Firewalls.
* Detección y prevención de intrusiones.
* Administración de vulnerabilidades.
* Control de la aplicación.
* Detección de anomalías basadas en la red.
* Filtrado de web.
* Antivirus.
* Protección de redes de robots (botnets).

Los dispositivos de seguridad de red virtual de Azure se pueden encontrar en Azure Marketplace mediante las palabras clave "seguridad" y "seguridad de red".

## Implementar redes perimetrales para las zonas de seguridad

Una red perimetral, también conocida como DMZ, es un segmento de red físico o lógico diseñado para proporcionar un nivel de seguridad adicional entre los recursos e Internet.

Los dispositivos de control de acceso de red especializados situados en el borde de una red perimetral solo permiten el tráfico deseado en la red virtual.

Las redes perimetrales permiten centralizar la administración, supervisión, registro y generación de informes sobre los dispositivos de control de acceso a la red en el borde de la instancia de red virtual de Azure.

En una red perimetral normalmente se habilitan:

* Protección contra denegación de servicio distribuido (DDoS).
* Sistemas de detección y prevención de intrusiones (IDS/IPS).
* Reglas y directivas de firewall.
* Filtrado web.
* Antimalware de red.

Los dispositivos de seguridad de la red se sitúan entre Internet y la red virtual de Azure y tienen una interfaz en ambas redes.

Existen distintos diseños de redes perimetrales, como:

* Configuración opuesta.
* Triple alojamiento.
* Múltiple alojamiento.

Según el concepto de Confianza cero, se recomienda considerar el uso de una red perimetral en todas las implementaciones de alta seguridad para mejorar el nivel de control de acceso y seguridad de red de los recursos de Azure.

Se puede utilizar Azure o una solución de terceros para proporcionar una capa adicional de seguridad entre los recursos e Internet.

### Controles nativos de Azure

Azure Firewall y Azure Web Application Firewall ofrecen ventajas básicas de seguridad:

* Firewall como servicio con estado completo.
* Alta disponibilidad integrada.
* Escalabilidad de nube sin restricciones.
* Filtrado de FQDN.
* Compatibilidad con conjuntos de reglas principales de OWASP.
* Configuración sencilla.

### Ofertas de terceros

Azure Marketplace ofrece firewalls de próxima generación (NGFW) y otras ofertas de terceros que proporcionan herramientas de seguridad conocidas y niveles de seguridad de red mejorados.

La configuración puede ser más compleja, pero una oferta de terceros puede permitir utilizar los conjuntos de habilidades y capacidades existentes.

## Uso de vínculos WAN dedicados para evitar la exposición en Internet

Muchas organizaciones han elegido una ruta de TI híbrida. Algunos recursos de información de la compañía están en Azure, mientras que otros siguen siendo locales.

En muchos casos, algunos componentes de un servicio se ejecutan en Azure y otros siguen siendo locales.

En un escenario de TI híbrida suele existir algún tipo de conectividad entre locales. Esta conectividad permite conectar las redes locales con las redes virtuales de Azure.

Hay dos soluciones de conectividad entre locales:

### VPN de sitio a sitio

Es una tecnología de confianza y bien establecida, pero realiza la conexión a través de Internet.

* El ancho de banda está limitado a un máximo de aproximadamente 1,25 Gbps.
* Es una opción conveniente en algunos escenarios.

### Azure ExpressRoute

Se recomienda usar ExpressRoute para la conectividad entre locales.

ExpressRoute permite ampliar las redes locales en la nube de Microsoft mediante una conexión privada facilitada por un proveedor de conectividad.

Mediante ExpressRoute se pueden establecer conexiones con servicios en la nube de Microsoft, como:

* Azure.
* Microsoft 365.
* Dynamics 365.

ExpressRoute es un vínculo de WAN dedicada entre la ubicación local o un proveedor de hospedaje de Microsoft Exchange.

Al tratarse de una conexión de telecomunicaciones, los datos no viajan a través de Internet y no quedan expuestos a los posibles riesgos inherentes a este tipo de comunicaciones.

### Ubicación de la conexión de ExpressRoute

La ubicación donde termina ExpressRoute puede afectar a:

* Capacidad del firewall.
* Escalabilidad.
* Confiabilidad.
* Visibilidad del tráfico de red.

Es necesario identificar dónde debe terminar ExpressRoute en las redes existentes locales.

**Terminarlo fuera del firewall — paradigma de red perimetral**

* Usar esta recomendación si se necesita visibilidad del tráfico.
* Si se necesita continuar con una práctica existente de aislar los centros de datos.
* Si solo se colocan recursos de extranet en Azure.

**Terminarlo dentro del firewall — paradigma de extensión de red**

* Es la recomendación predeterminada.
* En los demás casos, se recomienda tratar Azure como un centro de datos más.

## Optimización del rendimiento y el tiempo de actividad

Si un servicio está inactivo, no se puede acceder a la información. Si el rendimiento es tan bajo que no se pueden utilizar los datos, se puede considerar que los datos son inaccesibles.

Desde una perspectiva de seguridad, se debe hacer todo lo posible para garantizar que los servicios tengan un rendimiento y un tiempo de actividad óptimos.

Un método popular y eficaz para mejorar la disponibilidad y el rendimiento es utilizar el equilibrio de carga.

El equilibrio de carga distribuye el tráfico de red entre los servidores que forman parte de un servicio.

Por ejemplo, si existen servidores web front-end que forman parte de un servicio, el equilibrio de carga puede distribuir el tráfico entre ellos.

Esta distribución:

* Aumenta la disponibilidad porque, si uno de los servidores deja de estar disponible, el equilibrio de carga deja de enviarle tráfico y lo redirige a los servidores que siguen en línea.
* Mejora el rendimiento porque la sobrecarga del procesador, la red y la memoria se distribuye entre todos los servidores con carga equilibrada.

Se recomienda utilizar el equilibrio de carga siempre que sea posible y según corresponda a los servicios.

### Aplicaciones web con requisitos específicos

Escenario:

* Las solicitudes de la misma sesión de usuario o cliente deben llegar a la misma máquina virtual de back-end.
* Ejemplos: aplicaciones de carro de la compra y servidores de correo web.
* Solo se acepta una conexión segura.
* La comunicación sin cifrar con los servidores no es aceptable.
* Es necesario que varias solicitudes HTTP en la misma conexión TCP de ejecución prolongada se enruten a servidores de back-end diferentes o se equilibre su carga entre ellos.

Opción:

* Usar Azure Application Gateway.
* Es un equilibrador de carga de tráfico web HTTP.
* Admite cifrado TLS de un extremo a otro.
* Admite terminación TLS en la puerta de enlace.
* Los servidores web pueden liberarse de la sobrecarga de cifrado y descifrado y del tráfico que fluye sin encriptar a los servidores de back-end.

### Conexiones entrantes desde Internet

Escenario:

* Equilibrar la carga de conexiones entrantes desde Internet entre servidores ubicados en una red virtual de Azure.
* Aplicaciones sin estado que aceptan solicitudes entrantes de Internet.
* No se requieren sesiones permanentes ni descargas de TLS.

Opción:

* Crear un equilibrador de carga externo.
* Distribuye las solicitudes entrantes entre varias máquinas virtuales para proporcionar un mayor nivel de disponibilidad.

### Conexiones internas

Escenario:

* Equilibrar la carga de conexiones de máquinas virtuales que no están en Internet.
* Los dispositivos inician en una red virtual de Azure las conexiones que se aceptan para el equilibrio de carga.
* Ejemplos: instancias de SQL Server o servidores web internos.

Opción:

* Crear un equilibrador de carga interno.
* Distribuye las solicitudes entrantes entre varias máquinas virtuales para proporcionar un mayor nivel de disponibilidad.

### Balanceo de carga global

Escenario:

* Solución en la nube distribuida ampliamente en varias regiones que requiere el nivel más alto de tiempo de actividad o disponibilidad posible.
* Necesidad del nivel más alto de tiempo de actividad para garantizar que el servicio esté disponible incluso si todo un centro de datos deja de funcionar.

Opción:

* Usar Azure Traffic Manager.
* Permite equilibrar la carga de las conexiones a los servicios en función de la ubicación del usuario.

Por ejemplo, si el usuario realiza una solicitud desde la Unión Europea, la conexión se dirige a los servicios situados en un centro de datos de la Unión Europea.

Esto ayuda a mejorar el rendimiento porque la conexión al centro de datos más cercano es más rápida que a los centros de datos que están lejos.

## Deshabilitar el acceso RDP/SSH a las máquinas virtuales

Es posible acceder a las máquinas virtuales de Azure mediante:

* Protocolo de escritorio remoto (RDP).
* Secure Shell (SSH).

Estos protocolos permiten administrar máquinas virtuales desde ubicaciones remotas y son protocolos estándar utilizados en la computación de centros de datos.

El problema de seguridad al utilizar estos protocolos a través de Internet es que los atacantes pueden utilizar diversas técnicas de fuerza bruta para obtener acceso a las máquinas virtuales de Azure.

Una vez que los atacantes obtienen acceso, pueden utilizar la máquina virtual como punto de inicio para poner en peligro otros equipos de la red virtual o atacar dispositivos en red fuera de Azure.

Se recomienda deshabilitar el acceso directo de RDP y SSH a las máquinas virtuales de Azure desde Internet.

### Acceso mediante VPN de punto a sitio

Escenario:

* Habilitar a un único usuario para conectarse a una red virtual de Azure a través de Internet.

Opción:

* Utilizar VPN de punto a sitio.
* Es otro término para una conexión cliente/servidor de VPN con acceso remoto.
* Una vez establecida la conexión, el usuario puede utilizar RDP o SSH para conectarse a cualquier máquina virtual situada en la red virtual de Azure a la que se conectó mediante la VPN de punto a sitio.
* El usuario debe tener permiso para acceder a dichas máquinas virtuales.

La VPN de punto a sitio es más segura que las conexiones RDP o SSH directas porque el usuario tiene que autenticarse dos veces:

1. Debe autenticarse y tener autorización para establecer la conexión VPN de punto a sitio.
2. Debe autenticarse y tener autorización para establecer la sesión RDP o SSH.

### Acceso mediante VPN de sitio a sitio

Escenario:

* Habilitar a los usuarios de la red local para conectarse a las máquinas virtuales de la red virtual de Azure.

Opción:

* Una VPN de sitio a sitio conecta una red completa con otra red a través de Internet.
* Se puede utilizar para conectar la red local a una red virtual de Azure.
* Los usuarios de la red local pueden conectarse mediante RDP o SSH a través de la conexión VPN de sitio a sitio.
* No se debe permitir el acceso directo de RDP o SSH a través de Internet.

### Acceso mediante ExpressRoute

Escenario:

* Utilizar un vínculo WAN dedicado para proporcionar una funcionalidad similar a la VPN de sitio a sitio.

Opción:

* Usar ExpressRoute.

ExpressRoute proporciona funcionalidades similares a la VPN de sitio a sitio. Las principales diferencias son:

* El vínculo WAN dedicado no recorre Internet.
* Los vínculos WAN dedicados suelen ser más estables y eficaces.

## Proteja los recursos críticos del servicio de Azure solo en las redes virtuales

Use Azure Private Link para acceder a los servicios PaaS de Azure, por ejemplo:

* Azure Storage.
* SQL Database.

El acceso se realiza mediante un punto de conexión privado de la red virtual.

Los puntos de conexión privados permiten proteger los recursos de servicio de Azure únicamente para las redes virtuales.

El tráfico desde la red virtual al servicio de Azure permanece siempre en la red troncal de Microsoft Azure.

Ya no es necesario exponer la red virtual a la red pública de Internet para consumir servicios PaaS de Azure.

### Ventajas de Azure Private Link

**Seguridad mejorada para los recursos de servicio de Azure**

* Los recursos de servicio de Azure se pueden proteger en la red virtual mediante el punto de conexión privado.
* La protección de los recursos de servicio para un punto de conexión privado mejora la seguridad.
* El acceso a los recursos a través de la red pública de Internet se elimina completamente.
* El tráfico solo se permite desde el punto de conexión privado en la red virtual.

**Acceso privado a los recursos de servicio de Azure en la plataforma Azure**

* Conecta la red virtual a los servicios de Azure mediante puntos de conexión privados.
* No es necesaria una dirección IP pública.
* Private Link administra la conectividad entre el consumidor y los servicios mediante la red troncal de Azure.

**Acceso desde redes locales y emparejadas**

* Permite acceder a servicios que se ejecutan en Azure desde el entorno local mediante:

  * Emparejamiento privado de ExpressRoute.
  * Túneles VPN.
  * Redes virtuales emparejadas mediante puntos de conexión privados.
* No es necesario configurar el emparejamiento de Microsoft para ExpressRoute.
* No es necesario atravesar Internet para llegar al servicio.
* Private Link proporciona una manera segura de migrar cargas de trabajo a Azure.

**Protección contra la pérdida de datos**

* Se asigna un punto de conexión privado a una instancia de un recurso de PaaS en lugar de a todo el servicio.
* Los consumidores solo pueden conectarse al recurso específico.
* Se bloquea el acceso a cualquier otro recurso del servicio.
* Este mecanismo proporciona protección contra riesgos de pérdida de datos.

**Alcance global**

* Permite conectarse de forma privada a servicios que se ejecutan en otras regiones.
* La red virtual del consumidor puede estar en una región y conectarse a servicios de otra región.

**Fácil de configurar y administrar**

* Ya no se necesitan direcciones IP públicas reservadas y públicas en las redes virtuales para proteger los recursos de Azure mediante un firewall de IP.
* No se necesita ningún dispositivo NAT o de puerta de enlace para configurar los puntos de conexión privados.
* Los puntos de conexión privados se configuran mediante un flujo de trabajo sencillo.
* En el lado del servicio, las solicitudes de conexión se pueden administrar fácilmente en el recurso de servicio de Azure.
* Azure Private Link funciona para consumidores y servicios que pertenecen a diferentes inquilinos de Microsoft Entra.

# Supervisión de la seguridad de red con Network Watcher

Azure Network Watcher proporciona un conjunto de herramientas para supervisar, diagnosticar, ver métricas y habilitar o deshabilitar registros para recursos de IaaS de Azure (infraestructura como servicio).

Network Watcher permite supervisar y reparar el estado de red de productos IaaS como:

* Máquinas virtuales (VM).
* Redes virtuales (VNet).
* Puertas de enlace de aplicaciones.
* Equilibradores de carga.
* Otros recursos de IaaS.

Network Watcher no está diseñado para la supervisión de PaaS ni para el análisis web.

Network Watcher consta de tres conjuntos principales de herramientas y funcionalidades:

* Monitorización.
* Herramientas de diagnóstico de red.
* Tráfico.

**Nota:**

* Al crear o actualizar una red virtual en la suscripción, Network Watcher se habilita automáticamente en la región de la red virtual.
* No hay ningún impacto en los recursos ni en el cargo asociado para habilitar automáticamente Network Watcher.

## Monitorización

Network Watcher ofrece dos herramientas de supervisión:

* Topología.
* Monitor de conexión.

### Topología

La topología proporciona una visualización de toda la red para comprender la configuración de red.

Proporciona una interfaz interactiva para ver los recursos y sus relaciones en Azure que abarcan:

* Varias suscripciones.
* Grupos de recursos.
* Ubicaciones.

### Monitor de conexión

El monitor de conexión proporciona supervisión de conexión de un extremo a otro para los puntos de conexión híbridos y de Azure.

Ayuda a comprender el rendimiento de la red entre varios puntos de conexión de la infraestructura de red.

## Herramientas de diagnóstico de red

Network Watcher ofrece siete herramientas de diagnóstico de red:

* Verificación del flujo de IP.
* Diagnósticos NSG.
* Próximo salto.
* Reglas de seguridad eficaces.
* Solución de problemas de conexión.
* Captura de paquetes.
* Solución de problemas de VPN.

### Verificación del flujo de IP

La comprobación del flujo de IP permite detectar problemas de filtrado de tráfico a nivel de máquina virtual.

Comprueba si se permite o deniega un paquete hacia o desde una dirección IP, ya sea IPv4 o IPv6.

También indica qué regla de seguridad permitió o denegó el tráfico.

### Diagnósticos del grupo de seguridad de red (NSG)

Los diagnósticos de NSG permiten detectar problemas de filtrado de tráfico a nivel de:

* Máquina virtual.
* Conjunto de escalado de máquinas virtuales.
* Puerta de enlace de aplicaciones.

Comprueba si se permite o deniega un paquete hacia o desde:

* Una dirección IP.
* Un prefijo IP.
* Una etiqueta de servicio.

Indica qué regla de seguridad permitió o denegó el tráfico.

También permite agregar una nueva regla de seguridad con una prioridad más alta para permitir o denegar el tráfico.

### Próximo salto

El siguiente salto permite detectar problemas de enrutamiento.

Comprueba si el tráfico se enruta correctamente al destino previsto.

Proporciona información sobre:

* Tipo de próximo salto.
* Dirección IP.
* Identificador de la tabla de rutas.

La información corresponde a una dirección IP de destino específica.

### Reglas de seguridad eficaces

Las reglas de seguridad eficaces permiten ver las reglas de seguridad vigentes aplicadas a una interfaz de red.

Muestran todas las reglas de seguridad aplicadas a:

* La interfaz de red.
* La subred en la que se encuentra la interfaz de red.
* El agregado de ambos.

### Solución de problemas de conexión

La solución de problemas de conexión permite probar una conexión entre:

* Una máquina virtual.
* Un conjunto de escalado de máquinas virtuales.
* Una puerta de enlace de aplicaciones.
* Un host de Bastion.

Y:

* Una máquina virtual.
* Un FQDN.
* Un URI.
* Una dirección IPv4.

La prueba devuelve información similar a la funcionalidad del monitor de conexión, pero prueba la conexión en un momento dado en lugar de supervisarla con el tiempo.

### Captura de paquetes

La captura de paquetes permite crear sesiones de captura de paquetes de forma remota para realizar un seguimiento del tráfico hacia y desde:

* Una máquina virtual (VM).
* Un conjunto de escalado de máquinas virtuales.

### Solución de problemas de VPN

La solución de problemas de VPN permite solucionar problemas de:

* Puertas de enlace de red virtual.
* Sus conexiones.

## Tráfico

Network Watcher ofrece dos herramientas de tráfico que permiten registrar y visualizar el tráfico de red:

* Registros de flujos.
* Análisis de tráfico.

### Registros de flujos

Los registros de flujo permiten registrar información sobre el tráfico IP de Azure y almacenar los datos en Azure Storage.

El registro de flujos de grupos de seguridad de red es una característica de Azure Network Watcher que permite registrar información sobre el tráfico IP que fluye a través de un grupo de seguridad de red.

Los datos de flujo se envían a Azure Storage, desde donde se puede acceder a ellos y exportarlos a cualquier:

* Herramienta de visualización.
* Solución de información de seguridad y administración de eventos (SIEM).
* Sistema de detección de intrusiones (IDS).

### Análisis de tráfico

El análisis de tráfico proporciona visualizaciones enriquecidas de los datos de registros de flujo.

## Uso y cuotas

La funcionalidad Uso y cuotas de Network Watcher proporciona un resumen de los recursos de red implementados dentro de una suscripción y región.

Incluye:

* Uso actual.
* Límites correspondientes para cada recurso.

Esta información es útil al planear futuras implementaciones de recursos, ya que no se podrán crear más recursos si se alcanzan los límites dentro de la suscripción o región.

## Límites de Network Watcher

| Recurso                                                     |                                                                            Límite |
| ----------------------------------------------------------- | --------------------------------------------------------------------------------: |
| Instancias de Network Watcher por región por suscripción    | 1 (una instancia de una región para habilitar el acceso al servicio en la región) |
| Monitores de conexión por región por suscripción            |                                                                               100 |
| Máximo de grupos de prueba por monitor de conexión          |                                                                                20 |
| Máximo de orígenes y destinos por monitor de conexión       |                                                                               100 |
| Máximo de configuraciones de prueba por monitor de conexión |                                                                                20 |
| Sesiones de captura de paquetes por región por suscripción  |                          10.000 (solo número de sesiones, sin capturas guardadas) |
| Operaciones de solución de problemas de VPN por suscripción |                                                1 (número de operaciones a la vez) |

## Precios

Para obtener más información sobre los precios, se debe consultar la información de precios de Network Watcher.

## Contrato de nivel de servicio (SLA)

Para obtener más información sobre el Acuerdo de Nivel de Servicio, se debe consultar el Contrato de Nivel de Servicio (SLA) para los servicios en línea.

## Preguntas más frecuentes (FAQ)

Para obtener respuestas a las preguntas más frecuentes sobre Network Watcher, se debe consultar la sección de Preguntas más frecuentes (FAQ) de Network Watcher.

## ¿Qué hay nuevo?

Para consultar las actualizaciones de características de Network Watcher más recientes, se deben consultar las actualizaciones de servicio.

## Contenido relacionado

* Inicio rápido: diagnóstico de un problema de filtro de tráfico de red de máquinas virtuales.
* Módulo de aprendizaje: Introducción a Azure Network Watcher.
