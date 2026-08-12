# Cifrado de Azure

## Cifrado de datos en reposo

Los datos en reposo incluyen información que reside en almacenamiento persistente en medios físicos, en cualquier formato digital. Los medios pueden incluir archivos en medios magnéticos u ópticos, datos archivados y copias de seguridad.

Microsoft Azure ofrece soluciones de almacenamiento para archivos, discos, blobs y tablas, y proporciona cifrado para Azure SQL Database, Azure Cosmos DB y Azure Data Lake.

El cifrado de datos en reposo mediante AES 256 está disponible para servicios en modelos SaaS, PaaS e IaaS.

## Modelos de cifrado de Azure

Azure admite varios modelos de cifrado:

- **Cifrado del lado servidor** con claves administradas por el servicio.
- **Cifrado del lado servidor** con claves administradas por el cliente en Key Vault.
- **Cifrado del lado servidor** con claves administradas por el cliente en hardware controlado por el cliente.
- **Cifrado del lado cliente**, donde las claves se administran y almacenan localmente o en otra ubicación segura.

### Cifrado del lado cliente

El cifrado del lado cliente se realiza fuera de Azure. Incluye:

- Datos cifrados por una aplicación que se ejecuta en el centro de datos del cliente o mediante una aplicación de servicio.
- Datos que ya están cifrados cuando Azure los recibe.

Los proveedores de servicios en la nube no tienen acceso a las claves de cifrado y no pueden descifrar estos datos. El cliente mantiene el control total de las claves.

### Cifrado del lado servidor

Los tres modelos de cifrado del lado servidor ofrecen diferentes características de administración de claves:

- **Claves administradas por el servicio:** proporcionan una combinación de control y conveniencia con una sobrecarga baja.
- **Claves administradas por el cliente:** proporcionan control sobre las claves, incluida la compatibilidad con Bring Your Own Keys (BYOK) o la posibilidad de generar nuevas claves.
- **Claves administradas por el servicio en hardware controlado por el cliente:** permiten administrar claves en el repositorio propietario, fuera del control de Microsoft. Esta característica se denomina Host Your Own Key (HYOK). La configuración es compleja y la mayoría de los servicios de Azure no admiten este modelo.

### Cifrado de disco de Azure

Todos los discos, instantáneas e imágenes administrados están cifrados mediante Storage Service Encryption con una clave administrada por servicio.

Azure también ofrece opciones para proteger discos temporales, cachés y administrar claves en Azure Key Vault.

### Cifrado del servicio Azure Storage

Los datos en reposo en Azure Blob Storage y los recursos compartidos de archivos de Azure se pueden cifrar mediante escenarios del lado servidor y del lado cliente.

Azure Storage Service Encryption (SSE):

- Cifra automáticamente los datos antes de almacenarlos.
- Descifra automáticamente los datos al recuperarlos.
- Es completamente transparente para los usuarios.
- Utiliza AES de 256 bits.
- AES controla el cifrado, el descifrado y la administración de claves de forma transparente.

### Cifrado de cliente de blobs de Azure

Se puede realizar el cifrado del lado cliente de blobs de Azure mediante la biblioteca cliente de Azure Storage para .NET antes de cargar los datos en Azure Storage.

Cuando se usa el cifrado del lado cliente con Key Vault:

1. El SDK de cliente de Azure Storage genera una clave de cifrado de contenido simétrica (CEK) de un solo uso.
2. Los datos se cifran mediante la CEK.
3. La CEK se cifra mediante una clave de cifrado de claves (KEK).
4. La KEK puede ser una clave simétrica o un par de claves asimétricas.
5. La KEK puede administrarse localmente o almacenarse en Key Vault.
6. Los datos cifrados se cargan en Azure Storage.

También se puede utilizar la biblioteca cliente de Azure Storage para Java para cifrar datos antes de cargarlos y descifrarlos al descargarlos. Esta biblioteca admite la integración con Key Vault para la administración de claves.

## Cifrado de datos en reposo con Azure SQL Database

Azure SQL Database es un servicio de base de datos relacional de uso general en Azure que admite estructuras como datos relacionales, JSON, espacial y XML.

SQL Database admite:

- **Cifrado del lado servidor** mediante Transparent Data Encryption (TDE).
- **Cifrado del lado cliente** mediante Always Encrypted.

### Cifrado de datos transparente

TDE se utiliza para cifrar archivos de datos de SQL Server, Azure SQL Database y Azure Synapse Analytics en tiempo real mediante una clave de cifrado de base de datos (DEK).

La DEK se almacena en el registro de arranque de la base de datos para permitir su disponibilidad durante la recuperación.

TDE:

- Protege los archivos de datos y de registro.
- Utiliza algoritmos AES y Triple Data Encryption Standard (3DES).
- Realiza el cifrado de archivos de base de datos en el nivel de página.
- Cifra las páginas antes de escribirlas en disco.
- Descifra las páginas cuando se leen en memoria.
- Está habilitado de forma predeterminada en las bases de datos de Azure SQL recién creadas.

### Característica Always Encrypted

Always Encrypted permite cifrar los datos dentro de las aplicaciones cliente antes de almacenarlos en Azure SQL Database.

También permite:

- Delegar la administración de bases de datos a terceros.
- Mantener la separación entre quienes poseen y pueden ver los datos y quienes los administran pero no deben tener acceso a ellos.

### Cifrado de nivel de celda o de columna

Azure SQL Database permite aplicar cifrado simétrico a una columna mediante Transact-SQL.

Este enfoque se denomina:

- Cifrado de nivel de celda.
- Cifrado de nivel de columna (CLE).

Permite cifrar columnas específicas o incluso celdas específicas con claves de cifrado diferentes.

CLE proporciona una funcionalidad más granular que TDE, que cifra los datos en las páginas.

CLE permite cifrar datos mediante:

- Claves simétricas.
- Claves asimétricas.
- La clave pública de un certificado.
- Una frase de contraseña mediante 3DES.

## Cifrado de base de datos de Azure Cosmos DB

Azure Cosmos DB es la base de datos multimodelo distribuida globalmente de Microsoft.

Los datos de usuario almacenados en almacenamiento no volátil, como unidades de estado sólido, se cifran de forma predeterminada.

- No existen controles para activar o desactivar el cifrado en reposo.
- Se utilizan sistemas de almacenamiento de claves seguros, redes cifradas y API criptográficas.
- Microsoft administra las claves de cifrado.
- Las claves se rotan según las directrices internas de Microsoft.
- Se puede agregar una segunda capa de cifrado mediante claves administradas por el cliente (CMK).

## Cifrado en reposo en Data Lake

Azure Data Lake es un repositorio de toda la empresa para los diferentes tipos de datos recopilados en un único lugar antes de cualquier definición formal de requisitos o esquema.

Data Lake Store:

- Admite cifrado transparente de los datos en reposo de forma predeterminada.
- Configura el cifrado durante la creación de la cuenta.
- Administra las claves automáticamente de forma predeterminada.
- Permite administrar las propias claves.

Se utilizan tres tipos de claves:

- **MEK (Master Encryption Key):** se utiliza para cifrar la DEK.
- **DEK (Data Encryption Key):** se almacena en medios persistentes.
- **BEK (Block Encryption Key):** se deriva de la DEK y del bloque de datos.

Si se administran las propias claves, se puede rotar la MEK.

# Cifrado de datos en tránsito

Azure ofrece mecanismos para mantener los datos privados mientras se mueven de una ubicación a otra.

## Cifrado de capa de vínculo de datos en Azure

Cuando el tráfico del cliente de Azure se mueve entre centros de datos fuera de los límites físicos no controlados por Microsoft, se aplica cifrado de capa de vínculo de datos mediante los estándares de seguridad MAC IEEE 802.1AE, también conocidos como MACsec.

- Los paquetes se cifran en los dispositivos antes de ser enviados.
- Impide ataques físicos de intermediario o de espionaje/interceptación.
- Está integrado en el hardware de red.
- Proporciona cifrado a velocidad de línea.
- No genera un aumento de latencia de vínculo medible.
- Está activado de forma predeterminada para todo el tráfico de Azure que viaja dentro de una región o entre regiones.
- No requiere ninguna acción del cliente para habilitarlo.

## Cifrado TLS en Azure

Microsoft permite utilizar TLS (Transport Layer Security) para proteger los datos cuando viajan entre los servicios en la nube y los clientes.

TLS proporciona:

- Autenticación sólida.
- Privacidad de mensajes.
- Integridad.
- Detección de manipulación, interceptación y falsificación de mensajes.
- Interoperabilidad.
- Flexibilidad de algoritmos.
- Facilidad de implementación y uso.

La confidencialidad directa perfecta (PFS) protege las conexiones mediante claves únicas.

Las conexiones admiten:

- Longitudes de clave RSA de 2048 bits.
- Longitudes de clave ECC de 256 bits.
- Autenticación de mensajes SHA-384.
- Cifrado de datos AES-256.

## Transacciones de Azure Storage

Al interactuar con Azure Storage mediante Azure Portal, todas las transacciones se realizan mediante HTTPS.

También se puede utilizar la API REST de Storage mediante HTTPS.

Se puede exigir HTTPS para las API REST que acceden a objetos de cuentas de almacenamiento habilitando la transferencia segura necesaria para la cuenta.

Las firmas de acceso compartido (SAS):

- Permiten delegar el acceso a objetos de Azure Storage.
- Incluyen una opción para especificar que solo se puede utilizar HTTPS.
- Garantizan que los vínculos con tokens SAS utilicen el protocolo adecuado.

SMB 3.0, utilizado para acceder a recursos compartidos de Azure Files:

- Admite cifrado.
- Está disponible en Windows Server 2012 R2, Windows 8, Windows 8.1 y Windows 10.
- Permite acceso entre regiones e incluso acceso en el escritorio.

El cifrado del lado cliente cifra los datos antes de enviarlos a Azure Storage, por lo que los datos viajan cifrados a través de la red.

## Cifrado de SMB a través de redes virtuales de Azure

Mediante SMB 3.0 en máquinas virtuales que ejecutan Windows Server 2012 o posterior, se pueden proteger las transferencias de datos mediante cifrado de datos en tránsito a través de Redes virtuales de Azure.

El cifrado ayuda a protegerse contra:

- Ataques de manipulación.
- Ataques de interceptación.

Los administradores pueden habilitar el cifrado SMB:

- Para todo el servidor.
- Para recursos compartidos específicos.

Después de activar el cifrado SMB para un recurso compartido o servidor, de forma predeterminada solo los clientes SMB 3.0 pueden acceder a los recursos compartidos cifrados.

# Cifrado en tránsito en máquinas virtuales

Los datos en tránsito hacia, desde y entre máquinas virtuales que ejecutan Windows se pueden cifrar de varias maneras según la naturaleza de la conexión.

## Sesiones RDP

Se puede conectar e iniciar sesión en una máquina virtual mediante el Protocolo de escritorio remoto (RDP) desde:

- Un equipo cliente de Windows.
- Un equipo Mac con un cliente RDP instalado.

Los datos en tránsito por la red en sesiones RDP pueden protegerse mediante TLS.

También se puede utilizar Escritorio remoto para conectarse a una máquina virtual Linux en Azure.

## Acceso seguro a las VM de Linux con SSH

SSH (Secure Shell) permite conectarse a máquinas virtuales Linux que se ejecutan en Azure para la administración remota.

SSH:

- Es un protocolo de conexión cifrado.
- Permite inicios de sesión seguros mediante conexiones no seguras.
- Es el protocolo de conexión predeterminado para las máquinas virtuales Linux hospedadas en Azure.
- Puede utilizar claves SSH para la autenticación.
- Elimina la necesidad de contraseñas para iniciar sesión.
- Utiliza un par de claves pública y privada mediante cifrado asimétrico para la autenticación.

# Cifrado de VPN de Azure

Se puede conectar a Azure mediante una red privada virtual que crea un túnel seguro para proteger la privacidad de los datos enviados a través de la red.

## Puertas de enlace de VPN de Azure

Una puerta de enlace de VPN de Azure permite:

- Enviar tráfico cifrado entre una red virtual y una ubicación local mediante una conexión pública.
- Enviar tráfico entre redes virtuales.

Las VPN de sitio a sitio utilizan IPsec para el cifrado de transporte.

Las puertas de enlace de VPN de Azure utilizan un conjunto de propuestas predeterminadas, pero pueden configurarse con una directiva IPsec/IKE personalizada con algoritmos criptográficos específicos y distintos puntos fuertes de clave.

## VPN de punto a sitio

Las VPN de punto a sitio permiten que equipos cliente individuales accedan a una red virtual de Azure.

- Se utiliza SSTP (Secure Socket Tunneling Protocol) para crear el túnel VPN.
- Puede atravesar firewalls porque el túnel aparece como una conexión HTTPS.
- Se puede utilizar una autoridad certificadora raíz (CA) propia de una infraestructura interna de clave pública (PKI).
- La conexión puede configurarse mediante Azure Portal con autenticación de certificados o mediante PowerShell.

## VPN de sitio a sitio

Una conexión de puerta de enlace VPN de sitio a sitio permite conectar una red local con una red virtual de Azure mediante un túnel VPN IPsec/IKE:

- IKEv1.
- IKEv2.

Este tipo de conexión requiere un dispositivo VPN local con una dirección IP pública externa.

La conexión VPN de sitio a sitio puede configurarse mediante:

- Azure Portal.
- PowerShell.
- CLI de Azure.

# Cifrado en tránsito en el Data Lake

Los datos en tránsito, también conocidos como datos en movimiento, siempre se cifran en Data Lake Store.

Además de cifrar los datos antes de almacenarlos en medios persistentes:

- Los datos siempre se protegen en tránsito mediante HTTPS.
- HTTPS es el único protocolo admitido para las interfaces REST de Data Lake Store.

# Administración de claves con Key Vault

Sin la protección y administración adecuadas de las claves, el cifrado se hace inútil.

Key Vault es la solución recomendada por Microsoft para:

- Administrar las claves de cifrado utilizadas por los servicios en la nube.
- Controlar el acceso a las claves de cifrado.

Los permisos para acceder a las claves se pueden asignar a servicios o usuarios mediante cuentas de Microsoft Entra.

Key Vault evita que las organizaciones tengan que:

- Configurar módulos de seguridad de hardware (HSM).
- Aplicar revisiones a los HSM.
- Mantener HSM.
- Mantener software de administración de claves.

Al utilizar Key Vault:

- El cliente mantiene el control.
- Microsoft nunca ve las claves.
- Las aplicaciones no tienen acceso directo a las claves.
- Se pueden importar o generar claves en HSM.

# Cifrado de Azure Virtual Network

El cifrado de Azure Virtual Network es una característica de las instancias de Azure Virtual Network.

Permite cifrar y descifrar el tráfico entre Azure Virtual Machines mediante la creación de un túnel de seguridad de la capa de transporte de datagramas (DTLS).

Permite:

- Cifrar el tráfico entre Virtual Machines y Virtual Machine Scale Sets dentro de la misma red virtual.
- Cifrar el tráfico entre redes virtuales emparejadas de forma regional.
- Cifrar el tráfico entre redes virtuales emparejadas de forma global.
- Mejorar el cifrado existente en las funcionalidades de tránsito de Azure.

## Requisitos del cifrado de Azure Virtual Network

El cifrado de Virtual Network se admite en los siguientes tamaños de instancia de máquina virtual:

### Cargas de trabajo de uso general

- Serie D V4:
  - Serie Dv4 y Dsv4.
  - Series Ddv4 y Ddsv4.
  - Serie Dav4 y Dasv4.
- Serie D V5:
  - Series Dv5 y Dsv5.
  - Series Ddv5 y Ddsv5.
  - Series Dlsv5 y Dldsv5.
  - Series Dasv5 y Dadsv5.
- Serie D V6:
  - Series Dasv6 y Dadsv6.
  - Series Dalsv6 y Daldsv6.
  - Serie Dsv6.

### Cargas de trabajo de uso intensivo de memoria

- Serie E V4:
  - Serie Ev4 y Esv4.
  - Series Edv4 y Edsv4.
  - Serie Eav4 y Easv4.
- Serie E V5:
  - Series Ev5 y Esv5.
  - Series Edv5 y Edsv5.
  - Series Easv5 y Eadsv5.
- Serie E V6:
  - Series Easv6 y Eadsv6.
- Serie M V2:
  - Serie Mv2.
  - Serie msv2 y mdsv2 de memoria media.
- Serie M V3:
  - Serie msv3 y mdsv3 de memoria media.

### Cargas de trabajo de uso intensivo de almacenamiento

- Serie L V3:
  - Serie LSv3.

### Optimización informática

- Serie F V6:
  - Serie Falsv6.
  - Serie Famsv6.
  - Serie Fasv6.

Además:

- Las redes aceleradas deben estar habilitadas en la interfaz de red de la máquina virtual.
- El cifrado solo se aplica al tráfico entre máquinas virtuales de una red virtual.
- El tráfico se cifra desde una dirección IP privada a una dirección IP privada.
- El tráfico hacia máquinas virtuales no admitidas no está cifrado.
- Los registros de flujo de Virtual Network permiten confirmar el cifrado de flujo entre máquinas virtuales.
- Es necesario iniciar o detener las máquinas virtuales existentes después de habilitar el cifrado en una red virtual.

## Disponibilidad

El cifrado de Azure Virtual Network está disponible con carácter general en todas las regiones públicas de Azure.

Actualmente está en versión preliminar pública en:

- Azure Government.
- Microsoft Azure operado por 21Vianet.

## Limitaciones

El cifrado de Azure Virtual Network tiene las siguientes limitaciones:

- En escenarios en los que interviene PaaS, la máquina virtual en la que se hospeda PaaS determina si se admite el cifrado de red virtual. La máquina virtual debe cumplir los requisitos.
- Para el equilibrador de carga interno, todas las máquinas virtuales detrás del equilibrador de carga deben ser un SKU de máquina virtual compatible.
- **AllowUnencrypted** es la única aplicación admitida en disponibilidad general. La aplicación de **DropUnencrypted** se admitirá en el futuro.
- Las redes virtuales con cifrado habilitado no admiten Azure DNS Private Resolver.
- Las redes virtuales configuradas con el servicio Azure Private Link no admiten el cifrado de red virtual. Por lo tanto, el cifrado de red virtual no debe estar habilitado en estas redes virtuales.
- El cifrado de red virtual no debe estar habilitado en redes virtuales que tengan SKU de máquina virtual de informática confidencial de Azure.

Para utilizar máquinas virtuales de informática confidencial de Azure en redes virtuales donde está habilitado el cifrado de red virtual:

- Habilitar Redes aceleradas en el NIC de la máquina virtual si se admite.
- Si no se admiten Redes aceleradas, cambiar la SKU de máquina virtual a una que admita Redes aceleradas o cifrado de red virtual.

No se debe habilitar el cifrado de red virtual si la SKU de máquina virtual no admite Redes aceleradas ni cifrado de red virtual.

## Escenarios admitidos

### Máquinas virtuales de la misma red virtual

Se admite el tráfico entre máquinas virtuales de las SKU compatibles.

Esto incluye:

- Máquinas virtuales.
- Conjuntos de escalado de máquinas virtuales.
- Su equilibrador de carga interno.

### Emparejamiento de redes virtuales de Azure

Se admite el tráfico entre máquinas virtuales a través del emparejamiento regional.

### Emparejamiento global de redes virtuales

Se admite el tráfico entre máquinas virtuales mediante el emparejamiento global.

### Azure Kubernetes Service (AKS)

El cifrado de red virtual es:

- **Compatible con AKS** mediante la interfaz de red de contenedor de Azure (modo normal o de superposición), Kubenet o BYOCNI:
  - El tráfico de nodos y pods está cifrado.
- **Parcialmente compatible con AKS** mediante la Asignación Dinámica de IP para Pods de Azure CNI (`podSubnetId` especificado):
  - El tráfico del nodo está cifrado.
  - El tráfico del pod no está cifrado.
- El tráfico dirigido al plano de control administrado de AKS sale de la red virtual y, por lo tanto, no está dentro del alcance del cifrado de la red virtual.
- El tráfico hacia el plano de control administrado de AKS siempre se cifra mediante TLS.
