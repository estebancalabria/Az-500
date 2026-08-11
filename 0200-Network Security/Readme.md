# Seguridad de redes virtuales de Azure

## Introducción

Este módulo está diseñado para proporcionar a los administradores los conocimientos y aptitudes necesarios para planear e implementar medidas de seguridad sólidas para redes virtuales de Azure, garantizando la confidencialidad, integridad y disponibilidad de los recursos de red.

### Escenario

Imagine que es un especialista en seguridad de red de Azure responsable de proteger las redes virtuales en el entorno en la nube de su organización. Su organización se basa en los servicios de Azure y necesita un conocimiento exhaustivo de la seguridad de red, desde los grupos de seguridad de red (NSG) hasta el cifrado a través de ExpressRoute.

### Objetivos de aprendizaje

Al final de este módulo, los participantes podrán:

* Planee e implemente medidas de seguridad para redes virtuales en Azure para proteger los datos y los recursos.
* Use eficazmente grupos de seguridad de red (NSG) y grupos de seguridad de aplicaciones (ASG) para proteger el tráfico de red.
* Cree y administre rutas definidas por el usuario (UDR) para optimizar el enrutamiento del tráfico de red.
* Establezca el emparejamiento de red virtual o las puertas de enlace de VPN para la conectividad de red segura.
* Planee e implemente Virtual WAN, incluidos los centros virtuales protegidos, para la administración de red optimizada.
* Proteja la conectividad VPN, incluidas las configuraciones de punto a sitio y de sitio a sitio, para el acceso remoto y la conectividad de rama.
* Implemente el cifrado a través de ExpressRoute para garantizar la privacidad y la integridad de los datos.
* Configure las opciones de firewall en los recursos de plataforma como servicio (PaaS) para controlar el tráfico entrante y saliente.
* Supervise la seguridad de red de forma eficaz mediante Network Watcher, incluido el registro de flujo de NSG para la detección y el análisis de amenazas.

### Objetivos

El módulo tiene como objetivo dotar a los participantes con los conocimientos y la experiencia necesarios para diseñar, implementar y mantener un entorno de red virtual altamente seguro en Azure. Los participantes podrán proteger los datos confidenciales, las aplicaciones y los recursos frente a posibles amenazas y vulnerabilidades, a la vez que mantienen la integridad y disponibilidad de la red.

---

## Prueba comparativa de seguridad en la nube de Microsoft: protección de datos, registro y detección de amenazas y seguridad de red

### Control de seguridad: protección de datos

La protección de datos cubre el control de la protección de datos en reposo, en tránsito y a través de mecanismos de acceso autorizados, como la detección, clasificación, protección y supervisión de recursos de datos confidenciales mediante el control de acceso, el cifrado, la administración de claves y la administración de certificados.

### DP-3: Cifrar datos confidenciales en tránsito

| **Identificadores de CIS Controls v8ID NIST SP 800-53 r4ID PCI-DSS v3.2.1** |      |               |
| --------------------------------------------------------------------------- | ---- | ------------- |
| 3.10                                                                        | SC-8 | 3.5, 3.6, 4.1 |

**Principio de seguridad:** proteja los datos en tránsito contra ataques "fuera de banda" (como la captura de tráfico) mediante cifrado para asegurarse de que los atacantes no puedan leer ni modificar fácilmente los datos.

Establezca el límite de red y el ámbito de servicio donde el cifrado de datos en tránsito es obligatorio tanto dentro como fuera de la red. Aunque esto es opcional para el tráfico en redes privadas, esto es fundamental para el tráfico en redes públicas y externas.

#### Guía de Azure

* Aplique el uso de transferencia segura en servicios como Azure Storage, donde se integra una funcionalidad nativa de cifrado de datos en tránsito.
* Aplique HTTPS para cargas de trabajo y servicios de aplicaciones web asegurándose de que los clientes que se conectan a los recursos de Azure usan TLS v1.2 o posterior.
* Para la administración remota de máquinas virtuales, use SSH (para Linux) o RDP/TLS (para Windows) en lugar de un protocolo sin cifrar.
* Para la transferencia de archivos segura, use el servicio SFTP/FTPS en blobs de Azure Storage, aplicaciones de App Service y aplicaciones de funciones, en lugar de usar el servicio FTP normal.

**Nota:** El cifrado de datos en tránsito está habilitado para todo el tráfico de Azure que viaja entre centros de datos de Azure. TLS v1.2 o posterior está habilitado en la mayoría de los servicios de Azure de forma predeterminada. Además, algunos servicios como Azure Storage y Application Gateway pueden aplicar TLS v1.2 o posterior en el lado servidor.

**Implementación de Azure y contexto adicional:**

* Cifrado doble para datos de Azure en tránsito
* Entender el cifrado en tránsito con Azure
* Información sobre la seguridad de TLS
* Aplicación de una transferencia segura en Azure Storage

#### Guía de AWS

* Aplique la transferencia segura en servicios como Amazon S3, RDS y CloudFront, donde se ha integrado una característica nativa de cifrado de datos en tránsito.
* Aplique HTTPS para servicios y aplicaciones web de carga de trabajo asegurándose de que los clientes que se conectan a los recursos de AWS usen TLS v1.2 o posterior.
* Para la administración remota de instancias EC2, use SSH (para Linux) o RDP/TLS (para Windows) en lugar de un protocolo sin cifrar.
* Para la transferencia segura de archivos, use el servicio SFTP o FTPS de AWS Transfer en lugar de un servicio FTP normal.

**Nota:** Todo el tráfico de red entre los centros de datos de AWS se cifra de forma transparente en la capa física. Todo el tráfico dentro de una VPC y entre las VPN emparejadas entre regiones se cifra de forma transparente en la capa de red cuando se usan tipos de instancia de Amazon EC2 compatibles. TLS v1.2 o posterior está habilitado en la mayoría de los servicios de AWS de forma predeterminada. Además, algunos servicios como AWS Load Balancer pueden aplicar TLS v1.2 o posterior en el lado servidor.

**Implementación de AWS y contexto adicional:**

* Políticas de seguridad TLS en Elastic Load Balancer
* AWS Transfer SFTP y FTPS

#### Guía de GCP

* Haga cumplir la transferencia segura en servicios como Google Cloud Storage, donde se incorpora una característica nativa de cifrado de datos en tránsito.
* Aplique HTTPS para cargas de trabajo y servicios de aplicaciones web, lo que garantiza que los clientes que se conectan a los recursos de GCP usen TLS v1.2 o posterior.
* Para la administración remota, Google Cloud Compute Engine usa SSH (para Linux) o RDP/TLS (para Windows) en lugar de un protocolo sin cifrar.
* Para la transferencia segura de archivos, use el servicio SFTP/FTPS en servicios como Google Cloud Big Query o Cloud App Engine en lugar de un servicio FTP normal.

**Implementación de GCP y contexto adicional:**

* Cifrado en tránsito
* Cifrado en tránsito en Google Cloud

### Control de seguridad: registro y detección de amenazas

El registro y la detección de amenazas abarcan los controles para detectar amenazas en la nube y habilitar, recopilar y almacenar registros de auditoría para servicios en la nube, incluida la habilitación de procesos de detección, investigación y corrección con controles para generar alertas de alta calidad con detección de amenazas nativa en servicios en la nube; también incluye la recopilación de registros con un servicio de supervisión en la nube, la centralización del análisis de seguridad con un SIEM, la sincronización de tiempo y la retención de registros.

### LT-4: Habilitación del registro de red para la investigación de seguridad

| **Identificadores de CIS Controls v8ID NIST SP 800-53 r4ID PCI-DSS v3.2.1** |                         |      |
| --------------------------------------------------------------------------- | ----------------------- | ---- |
| 8.2, 8.5, 8.6, 8.7, 13.6                                                    | AU-3, AU-6, AU-12, SI-4 | 10.8 |

**Principio de seguridad:** habilite el registro de los servicios de red para admitir investigaciones de incidentes relacionadas con la red, la búsqueda de amenazas y la generación de alertas de seguridad. Los registros de red pueden incluir registros de servicios de red, como el filtrado de IP, el firewall de aplicaciones y redes, DNS y la supervisión de flujos, entre otros.

#### Guía de Azure

* Habilite y recopile registros de recursos del grupo de seguridad de red (NSG), registros de flujo de NSG, registros de Azure Firewall y registros de Web Application Firewall (WAF) y registros de máquinas virtuales a través del agente de recopilación de datos de tráfico de red para el análisis de seguridad para admitir investigaciones de incidentes de soporte técnico y la generación de alertas de seguridad.
* Puede enviar los registros de flujo a un área de trabajo de Log Analytics de Azure Monitor y, a continuación, usar Análisis de tráfico para proporcionar información.
* Recopile registros de consultas DNS para ayudar a correlacionar otros datos de red.

#### Guía de AWS

* Habilite y recopile registros de red, como registros de flujo de VPC, registros de WAF y registros de consultas del resolutor de Route53 para el análisis de seguridad.
* Los registros se pueden exportar a CloudWatch para la supervisión o un cubo de almacenamiento S3 para la ingesta en la solución de Microsoft Sentinel para el análisis centralizado.

#### Guía de GCP

* La mayoría de los registros de actividades de red están disponibles a través de los registros de flujo de VPC, que registran una muestra de flujos de red enviados y recibidos por recursos.
* Estos registros se pueden usar para la supervisión de red, los análisis forenses, el análisis de seguridad en tiempo real y la optimización de gastos.
* Puede ver los registros de flujo en Registro en la nube y exportar registros al destino que admite la exportación de registro en la nube.
* Los registros de flujo se agregan por conexión desde las VMs de Compute Engine y se exportan en tiempo real.
* Al suscribirse a Pub/Sub, puede analizar los registros de flujo mediante las API de streaming en tiempo real.

**Nota:** También puede utilizar Packet Mirroring, que clona el tráfico de las instancias especificadas en su red de nube privada virtual (VPC) y lo reenvía para su examen. El reflejo de paquetes captura todo el tráfico y los datos de paquetes, incluida la carga útil y los encabezados.

### Control de seguridad: Seguridad de red

La seguridad de red cubre los controles para proteger y proteger las redes, incluida la protección de redes virtuales, el establecimiento de conexiones privadas, la prevención y la mitigación de ataques externos y la protección de DNS.

### NS-1: Establecer límites de segmentación de red

| **Identificadores de CIS Controls v8ID NIST SP 800-53 r4ID PCI-DSS v3.2.1** |                  |               |
| --------------------------------------------------------------------------- | ---------------- | ------------- |
| 3.12, 13.4, 4.4                                                             | AC-4, SC-2, SC-7 | 1.1, 1.2, 1.3 |

**Principio de seguridad:** asegúrese de que la implementación de la red virtual se alinea con la estrategia de segmentación empresarial definida en el control de seguridad GS-2. Toda carga de trabajo que pueda incurrir en un mayor riesgo para la organización debe estar en redes virtuales aisladas.

Ejemplos de carga de trabajo de alto riesgo:

* Una aplicación que almacena o procesa datos sumamente confidenciales.
* Una aplicación orientada a una red externa, a la que pueden acceder los usuarios públicos o ajenos a la organización.
* Una aplicación que usa una arquitectura no segura o que contiene vulnerabilidades que no se pueden corregir fácilmente.

Para mejorar la estrategia de segmentación empresarial, restrinja o supervise el tráfico entre los recursos internos mediante controles de red. Para aplicaciones específicas bien definidas, puede tratarse de un enfoque altamente seguro de "denegar de manera predeterminada, permitir por excepción", mediante la restricción de los puertos, protocolos, direcciones IP de origen y destino del tráfico de red. Si tiene muchas aplicaciones y puntos de conexión que interactúan entre sí, es posible que el bloqueo del tráfico no se escale bien y que solo pueda supervisar el tráfico.

#### Guía de Azure

* Cree una red virtual (VNet) como un enfoque de segmentación fundamental en la red de Azure.
* Cree subredes dentro de la red virtual para subredes más pequeñas.
* Use grupos de seguridad de red (NSG) como control de la capa de red para restringir o supervisar el tráfico por puerto, protocolo, dirección IP de origen o dirección IP de destino.
* Use el Fortalecimiento Adaptativo de la Red para recomendar reglas de endurecimiento del NSG basadas en la inteligencia sobre amenazas y el resultado del análisis del tráfico.
* Use grupos de seguridad de aplicaciones (ASG) para simplificar una configuración compleja y agrupar máquinas virtuales y directivas de seguridad de red basadas en esos grupos.

#### Guía de AWS

* Cree una nube privada virtual (VPC) como un enfoque fundamental de segmentación.
* Cree subredes dentro de VPC para subredes más pequeñas.
* Use los Grupos de Seguridad como un cortafuegos con estado para restringir el tráfico por puerto, protocolo, dirección IP de origen o dirección IP de destino.
* En el nivel de subred de VPC, use la lista de control de acceso de red (NACL) como firewall sin estado.
* Para controlar el tráfico de VPC, se debe configurar Internet y NAT Gateway para asegurarse de que el tráfico desde o hacia Internet está restringido.

#### Guía de GCP

* Cree una red de nube privada virtual (VPC) como un enfoque de segmentación fundamental.
* Cree subredes dentro de la VPC para subredes más pequeñas.
* Use reglas de firewall de VPC como control de capa de red distribuida para permitir o denegar conexiones a o desde sus instancias de destino.
* Configure reglas de firewall de VPC para que tengan como destino todas las instancias de la red de VPC, las instancias con una etiqueta de red coincidente o instancias que usen una cuenta de servicio específica.

### NS-2: Protección de servicios nativos en la nube con controles de red

**Principio de seguridad:** proteja los servicios en la nube mediante el establecimiento de un punto de acceso privado para los recursos. También debe deshabilitar o restringir el acceso desde redes públicas siempre que sea posible.

#### Guía de Azure

* Implemente puntos de conexión privados para todos los recursos de Azure que admiten la característica Private Link para establecer un punto de acceso privado.
* El uso de Private Link impide que la conexión privada se enrute a través de la red pública.
* Algunos servicios de Azure también pueden permitir la comunicación privada mediante puntos de conexión de servicio, aunque se recomienda usar Azure Private Link para el acceso seguro y privado.
* Puede implementar la integración de red virtual para el servicio.
* Configure las reglas de ACL de red nativa del servicio o deshabilite el acceso a la red pública.
* En las máquinas virtuales de Azure, salvo que haya un caso de uso seguro, debe evitar asignar direcciones IP o subredes públicas directamente a la interfaz de máquina virtual y usar servicios de puerta de enlace o equilibrador de carga como front-end.

#### Guía de AWS

* Implemente VPC PrivateLink para los recursos que lo admitan.
* El uso de PrivateLink impide que la conexión privada se enrute a través de la red pública.
* Puede implementar la instancia de servicio en su propia VPC para aislar el tráfico.
* Configure reglas de ACL nativas del servicio para bloquear el acceso desde la red pública.
* En los recursos de servicio en la VPC, salvo que haya un caso de uso seguro, debe evitar asignar direcciones IP o subredes públicas directamente y usar direcciones IP o subredes privadas.

#### Guía de GCP

* Implemente Google Access privado de VPC para los recursos que lo admitan.
* Estas opciones de acceso privado impiden que la conexión privada se enrute a través de la red pública.
* Puede implementar la instancia de servicio en su propia VPC para aislar el tráfico.
* Configure reglas de ACL nativas del servicio para bloquear el acceso desde la red pública.
* En las máquinas virtuales del motor de proceso de GCP, salvo que haya un caso de uso seguro, debe evitar asignar direcciones IP o subredes públicas directamente a la interfaz de máquina virtual y usar servicios de puerta de enlace o equilibrador de carga como front-end.

### NS-3: Implementación de un firewall en el perímetro de la red empresarial

**Principio de seguridad:** implemente un firewall para realizar un filtrado avanzado en el tráfico de red hacia y desde redes externas. También puede usar firewalls entre segmentos internos como refuerzo de una estrategia de segmentación. Si es necesario, use rutas personalizadas para la subred para invalidar la ruta del sistema cuando necesite forzar que el tráfico de red pase por un dispositivo de red con fines de control de seguridad.

Como mínimo, bloquee las direcciones IP incorrectas conocidas y los protocolos de alto riesgo, como la administración remota (RDP y SSH) y los protocolos de intranet (SMB y Kerberos).

#### Guía de Azure

* Use Azure Firewall para proporcionar una restricción de tráfico de capa de aplicación totalmente con estado, como el filtrado de direcciones URL, y/o una administración central en un gran número de segmentos o radiales empresariales.
* En una topología de red compleja, como una configuración en estrella tipo hub-and-spoke, puede crear rutas definidas por el usuario (UDR) para asegurarse de que el tráfico pase por la ruta deseada.
* Puede usar una UDR para redirigir el tráfico de Internet de salida a través de Azure Firewall o una aplicación virtual de red específica.

#### Guía de AWS

* Use Firewall de red de AWS para proporcionar una restricción de tráfico de capa de aplicación totalmente con estado y/o una administración central.
* En una topología de hub y radial, puede crear tablas de rutas de VPC personalizadas.
* Puede usar una ruta personalizada para redirigir el tráfico de Internet de salida a través de un firewall de AWS específico o una aplicación virtual de red.

#### Guía de GCP

* Use las directivas de seguridad de Google Cloud Armor para proporcionar filtrado de nivel 7 y protección de ataques web comunes.
* Use reglas de firewall de VPC para proporcionar restricciones de tráfico de capa de red distribuidas, totalmente con estado.
* En una topología tipo hub/spoke, cree políticas de firewall que agrupen reglas de firewall y que sean jerárquicas para que se puedan aplicar a varias redes VPC.

### NS-5: Desplegar protección contra DDOS

**Principio de seguridad:** implemente la protección contra denegación de servicio distribuido (DDoS) para proteger la red y las aplicaciones frente a ataques.

#### Guía de Azure

* DDoS Protection Basic se habilita automáticamente para proteger la infraestructura de la plataforma subyacente de Azure y no requiere configuración de los usuarios.
* Para mayores niveles de protección de la capa de aplicación (nivel 7), como inundaciones HTTP e inundaciones de DNS, habilite el plan de protección estándar de DDoS en la red virtual para proteger los recursos expuestos a las redes públicas.

#### Guía de AWS

* AWS Shield Standard se habilita automáticamente con mitigaciones estándar para proteger la carga de trabajo frente a ataques DDoS comunes de la capa de red y transporte (nivel 3 y 4).
* Para mayores niveles de protección de las aplicaciones frente a ataques de capa de aplicación (nivel 7), como inundaciones HTTPS e inundaciones de DNS, habilite la protección avanzada de AWS Shield.

#### Guía de GCP

Google Cloud Armor ofrece:

* Protección contra DDoS de red estándar: protección básica siempre activa para equilibradores de carga de red, reenvío de protocolos o máquinas virtuales con direcciones IP públicas.
* Protección contra DDoS de red avanzada: protecciones adicionales para suscriptores de Managed Protection Plus.
* La protección contra DDoS de red estándar siempre está habilitada.
* Puede configurar la protección contra DDoS de red avanzada por región.

### NS-6: Implementación de un firewall de aplicaciones web

**Principio de seguridad:** implemente un firewall de aplicaciones web (WAF) y configure las reglas adecuadas para proteger las aplicaciones web y las API frente a ataques específicos de la aplicación.

#### Guía de Azure

* Use funcionalidades de WAF en Azure Application Gateway, Azure Front Door y Azure Content Delivery Network (CDN).
* Establezca el WAF en modo "detección" o "prevención", según las necesidades y el panorama de amenazas.
* Elija un conjunto de reglas integrado, como vulnerabilidades principales de OWASP 10, y afínelo a las necesidades de la aplicación.

#### Guía de AWS

* Use AWS Web Application Firewall (WAF) en Amazon CloudFront, Amazon API Gateway, Application Load Balancer o AWS AppSync.
* Use las reglas administradas de AWS para WAF para implementar grupos de línea de base integrados y personalizarlos.
* También puede usar AWS WAF Security Automations para implementar automáticamente reglas de AWS WAF predefinidas.

#### Guía de GCP

* Use Google Cloud Armor para ayudar a proteger aplicaciones y sitios web frente a ataques por denegación de servicio y web.
* Use las reglas integradas de Google Cloud Armor para mitigar vulnerabilidades comunes de aplicaciones web y ayudar a proporcionar protección contra OWASP Top 10.
* Configure las reglas de WAF preconfiguradas basadas en ModSecurity Core Rules (CRS).
* Cloud Armor funciona junto con equilibradores de carga externos y protege contra DDoS y otros ataques basados en web.
* La protección adaptable en Cloud Armor ayuda a evitar, detectar y proteger aplicaciones y servicios frente a ataques distribuidos L7 mediante el análisis de patrones de tráfico, detección y alerta de ataques sospechosos y generación de reglas WAF sugeridas.

### NS-8: Detección y deshabilitación de protocolos y servicios no seguros

**Principio de seguridad:** detecte y deshabilite los servicios y protocolos no seguros en el sistema operativo, la aplicación o el nivel de paquete de software. Implemente controles de compensación si no es posible deshabilitar los servicios y protocolos no seguros.

#### Guía de Azure

Use el libro de protocolo inseguro integrado de Microsoft Sentinel para detectar el uso de servicios y protocolos no seguros, como:

* SSL/TLSv1
* SSHv1
* SMBv1
* LM/NTLMv1
* wDigest
* Cifrados débiles en Kerberos
* Enlaces LDAP sin firmar

Deshabilite los servicios y protocolos no seguros que no cumplan el estándar de seguridad adecuado.

**Nota:** Si no es posible deshabilitar los servicios o protocolos no seguros, use controles de compensación, como bloquear el acceso a los recursos a través del grupo de seguridad de red, Azure Firewall o Azure Web Application Firewall para reducir la superficie expuesta a ataques.

#### Guía de AWS

* Habilite los registros de flujo de VPC y use GuardDuty para analizar los registros de flujo de VPC para identificar posibles servicios y protocolos no seguros.
* Si los registros del entorno de AWS se pueden reenviar a Microsoft Sentinel, también puede usar el libro de protocolo no seguro integrado de Microsoft Sentinel.

**Nota:** Si no es posible deshabilitar los servicios o protocolos no seguros, use controles de compensación como bloquear el acceso a los recursos a través de grupos de seguridad, AWS Network Firewall o AWS Web Application Firewall.

#### Guía de GCP

* Habilite los registros de flujo de VPC y use BigQuery o Security Command Center para analizar los registros de flujo de VPC.
* Si los registros del entorno de GCP se pueden reenviar a Microsoft Sentinel, también puede usar el libro de protocolos no seguros integrado de Microsoft Sentinel.
* Puede reenviar registros a Google Cloud Chronicle SIEM y SOAR y crear reglas personalizadas para el mismo propósito.

**Nota:** Si no es posible deshabilitar los servicios o protocolos no seguros, use controles de compensación, como bloquear el acceso a los recursos a través de las directivas y reglas de firewall de VPC, o Cloud Armor.

### NS-9: Conexión privada a una red local o en la nube

**Principio de seguridad:** use conexiones privadas para la comunicación segura entre diferentes redes, como centros de datos del proveedor de servicios en la nube y la infraestructura local en un entorno de coubicación.

#### Guía de Azure

* Para la conectividad ligera de sitio a sitio o de punto a sitio, use la red privada virtual (VPN) de Azure.
* Para conexiones de alto rendimiento de nivel empresarial, use Azure ExpressRoute o Virtual WAN para conectar centros de datos de Azure e infraestructura local.
* Al conectar dos o más redes virtuales de Azure entre sí, use el emparejamiento de red virtual.
* El tráfico de red entre redes virtuales emparejadas es privado y se mantiene en la red troncal de Azure.

#### Guía de AWS

* Para conectividad de sitio a sitio o de punto a sitio, use AWS VPN.
* Para conexiones de alto rendimiento de nivel empresarial, use AWS Direct Connect.
* Puede usar el emparejamiento de VPC o la puerta de enlace de tránsito para establecer conectividad entre dos o más VPN dentro o entre regiones.
* El tráfico de red entre VPC emparejadas es privado y se mantiene en la red troncal de AWS.
* Cuando necesite unir varias VPN para crear una subred plana grande, también tiene la opción de usar el uso compartido de VPC.

#### Guía de GCP

* Para conectividad ligera de sitio a sitio o de punto a sitio, use la VPN de Google Cloud.
* Para conexiones de alto rendimiento de nivel empresarial, use Google Cloud Interconnect o Partner Interconnect.
* Puede usar el emparejamiento de red de VPC o el Centro de conectividad de red para establecer conectividad entre dos o más VPN.
* El tráfico de red entre las VPN emparejadas es privado y se mantiene en la red troncal de GCP.
* Cuando necesite unir varias VPC para crear una subred plana grande, también tiene la opción de usar Shared VPC.

---

## ¿Qué es una Azure Virtual Network?

Azure Virtual Network es un servicio que proporciona el bloque de construcción fundamental para su red privada en Azure. Una instancia del servicio (una red virtual) permite que muchos tipos de recursos de Azure se comuniquen de forma segura entre sí, Internet y redes locales. Estos recursos de Azure incluyen máquinas virtuales (VM).

Una red virtual es similar a una red tradicional que operaría en su propio centro de datos, pero aporta ventajas adicionales de la infraestructura Azure, como escala, disponibilidad y aislamiento.

### ¿Por qué usar una red virtual Azure?

Entre los escenarios clave que puede lograr con una red virtual se incluyen:

* Comunicación de los recursos Azure con Internet.
* Comunicación entre los recursos de Azure.
* Comunicación con recursos locales.
* Filtrado del tráfico de red.
* Enrutamiento del tráfico de red.
* Integración con los servicios de Azure.

### Comunicarse con Internet

De manera predeterminada, todos los recursos de una red virtual pueden comunicarse con Internet.

También puede utilizar:

* Una dirección IP pública.
* Una puerta de enlace NAT.
* Un equilibrador de carga pública.

Puede comunicarse de entrada con un recurso asignándole una dirección IP pública o un equilibrador de carga pública.

Cuando se usa solo una instancia interna del equilibrador de carga estándar, la conectividad de salida no está disponible hasta que define cómo desea que las conexiones salientes trabajen con una dirección IP pública o un equilibrador de carga público en el nivel de instancia.

### Comunicación entre recursos de Azure

Los recursos de Azure se comunican de forma segura entre sí mediante:

* **Red virtual:** puede implementar máquinas virtuales y otros tipos de recursos de Azure en una red virtual. Algunos ejemplos son App Service Environments, Azure Kubernetes Service (AKS) y Azure Virtual Machine Scale Sets.
* **Punto de conexión de servicio de red virtual:** puede ampliar el espacio de direcciones privadas de la red virtual y la identidad de la red virtual a los recursos de servicio de Azure a través de una conexión directa. Algunos ejemplos son las cuentas Azure Storage y Azure SQL Database.
* **Emparejamiento de redes virtuales:** puede conectar redes virtuales entre sí mediante el emparejamiento virtual. Los recursos de ambas redes virtuales pueden comunicarse entre sí. Las redes virtuales pueden estar en la misma región de Azure o en regiones diferentes.

### Comunicación con recursos localizados en las instalaciones

Puede conectar los equipos y redes locales a una red virtual mediante:

* **VPN de punto a sitio:** se establece entre una red virtual y un único equipo de la red. Cada equipo que desea establecer conectividad con una red virtual debe configurar su conexión. Es útil si está empezando a utilizar Azure o para desarrolladores, dado que requiere pocos o ningún cambio en una red existente. La comunicación se envía mediante un túnel cifrado a través de Internet.
* **VPN de sitio a sitio:** se establece entre el dispositivo VPN local y una puerta de enlace de VPN de Azure implementada en una red virtual. Habilita que cualquier recurso local autorizado acceda a una red virtual. La comunicación se envía mediante un túnel cifrado a través de Internet.
* **Azure ExpressRoute:** se establece entre la red y Azure a través de un asociado de ExpressRoute. Esta conexión es privada y el tráfico no pasa por Internet.

### Filtrado del tráfico de red

Puede filtrar el tráfico de red entre subredes mediante:

* **Grupos de seguridad de red:** los grupos de seguridad de red y los grupos de seguridad de aplicaciones pueden contener varias reglas de seguridad entrantes y salientes. Estas reglas permiten filtrar el tráfico desde y hacia los recursos por dirección IP de origen y destino, puerto y protocolo.
* **Aplicaciones virtuales de red:** una aplicación virtual de red es una máquina virtual que realiza una función de red, como un firewall o una optimización WAN.

### Enrutar tráfico de red

De forma predeterminada Azure enruta el tráfico entre subredes, redes virtuales conectadas, redes locales e Internet.

Puede invalidar las rutas predeterminadas mediante:

* **Tablas de rutas:** puede crear tablas de rutas personalizadas que controle a dónde se enruta el tráfico para cada subred.
* **Rutas BGP:** si conecta la red virtual a la red local mediante una puerta de enlace de VPN de Azure o una conexión ExpressRoute, puede propagar las rutas BGP de la red local a las redes virtuales.

### Integración con servicios de Azure

La integración de servicios de Azure en una red virtual de Azure permite el acceso privado al servicio desde las máquinas virtuales o los recursos de proceso de la red virtual.

Puede:

* Implementar instancias dedicadas del servicio en una red virtual.
* Usar Azure Private Link para acceder de forma privada a una instancia específica del servicio desde su red virtual y desde redes locales.
* Acceder al servicio a través de puntos de conexión públicos extendiendo una red virtual al servicio, a través de puntos de conexión de servicio.

### Límites

Hay ciertos límites en torno al número de recursos de Azure que puede implementar. La mayoría de los límites de red de Azure están en los valores máximos. Sin embargo, puede aumentar determinados límites de red.

### Redes virtuales y zonas de disponibilidad

Las redes virtuales y subredes abarcan todas las zonas de disponibilidad de una región. No es necesario dividirlos por zonas de disponibilidad para dar cabida a los recursos zonales.

### Precios

El uso de Azure Virtual Network es gratuito. No tiene coste alguno. Se aplican tarifas estándar para recursos como máquinas virtuales y otros productos.

---

## Azure Virtual Network Manager

Azure Virtual Network Manager es un servicio de administración que permite agrupar, configurar, implementar y administrar redes virtuales globalmente entre suscripciones.

Con Virtual Network Manager se pueden definir grupos de redes para identificar y segmentar lógicamente las redes virtuales. Después, se pueden determinar las configuraciones de conectividad y seguridad deseadas y aplicarlas en todas las redes virtuales seleccionadas de los grupos de red a la vez.

### ¿Cómo funciona Azure Virtual Network Manager?

Durante el proceso de creación, se puede definir el ámbito de lo que Azure Virtual Network Manager administra. El administrador de redes solo tiene el acceso delegado para aplicar configuraciones dentro de este límite de ámbito.

La definición de un ámbito se puede realizar directamente en una lista de suscripciones. Sin embargo, se recomienda usar grupos de administración para definir el ámbito. Los grupos de administración proporcionan una organización jerárquica a las suscripciones.

Después de definir el ámbito, se implementan tipos de configuración, incluidas las reglas **Connectivity** y **SecurityAdmin** para Virtual Network Manager.

Después de implementar la instancia de Virtual Network Manager, se crea un **grupo de red**, que actúa como contenedor lógico de recursos de red para aplicar configuraciones a escala.

Se pueden seleccionar manualmente las redes virtuales individuales que se van a agregar al grupo de red, lo que se conoce como **pertenencia estática**.

También se puede usar Azure Policy para definir condiciones que controlan la pertenencia a los grupos de manera dinámica, lo que se conoce como **pertenencia dinámica**.

Después, se crean configuraciones de seguridad o conectividad aplicadas a esos grupos de red en función de las necesidades de topología y seguridad.

* Una **configuración de conectividad** permite crear una red con una topología de malla o tipo hub-and-spoke.
* Una **configuración de seguridad** permite definir una colección de reglas que se puede aplicar a uno o varios grupos de red en el nivel global.

Una vez creados los grupos y configuraciones de red deseados, se pueden implementar las configuraciones en cualquier región de su elección.

Azure Virtual Network Manager se puede implementar y administrar mediante:

* Azure Portal.
* CLI de Azure.
* Azure PowerShell.
* Terraform.

### Ventajas clave

* Administre de forma centralizada las directivas de conectividad y seguridad globalmente en regiones y suscripciones.
* Habilite la conexión directa entre radios en una configuración radial sin la complejidad de administrar una red de malla.
* Servicio altamente escalable y de alta disponibilidad con redundancia y replicación en todo el mundo.
* Capacidad para crear reglas de seguridad de red que invaliden las reglas del grupo de seguridad de red.
* Baja latencia y ancho de banda alto entre los recursos de diferentes redes virtuales mediante el emparejamiento de redes virtuales.
* Implemente los cambios de red a través de una secuencia de región específica y la frecuencia que elija.

### Precios

Para más información, consulte los precios de Azure Virtual Network Manager.

### Preguntas más frecuentes

Para preguntas más frecuentes, consulte las preguntas más frecuentes sobre Azure Virtual Network Manager.

### Límites

Para conocer los límites, consulte los límites de Azure Virtual Network Manager.

### Acuerdo de Nivel de Servicio

Para el Acuerdo de Nivel de Servicio, consulte el Acuerdo de Nivel de Servicio para Azure Virtual Network Manager.

---

## Planear e implementar grupos de seguridad de red (NSG) y grupos de seguridad de aplicaciones (ASG)

Puede usar el grupo de seguridad de red de Azure para filtrar el tráfico de red entre los recursos de Azure de una red virtual de Azure.

Un grupo de seguridad de red contiene reglas de seguridad que permiten o deniegan el tráfico de red entrante o el tráfico de red saliente de varios tipos de recursos de Azure.

Para cada regla, puede especificar:

* El origen y el destino.
* El puerto.
* El protocolo.

### Grupos de seguridad de red (NSG)

Un grupo de seguridad de red contiene tantas reglas como desee, dentro de los límites de suscripción de Azure.

Cada regla especifica las siguientes propiedades:

| Propiedad            | Explicación                                                                                                                                                                                                                                                                                                                                                                        |
| -------------------- | ---------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------- |
| Nombre               | Un nombre único dentro del grupo de seguridad de red. Puede tener hasta 80 caracteres. Debe comenzar con un carácter de palabra y terminar con un carácter alfabético o `_`. Puede contener caracteres alfabéticos o `.`, `-`, `_`.                                                                                                                                                |
| Prioridad            | Número entre 100 y 4096. Las reglas se procesan en orden de prioridad. Primero se procesan las reglas con números más bajos. Cuando el tráfico coincide con una regla, el procesamiento se detiene. Las reglas de seguridad predeterminadas de Azure reciben el número más alto con la prioridad más baja para asegurar que las reglas personalizadas siempre se procesen primero. |
| Origen o destino     | Puede ser cualquiera, una dirección IP individual, un bloque CIDR, una etiqueta de servicio o un grupo de seguridad de aplicaciones.                                                                                                                                                                                                                                               |
| Protocolo            | TCP, UDP, ICMP, ESP, AH o cualquiera. ESP y AH no están disponibles actualmente a través de Azure Portal, pero se pueden usar mediante plantillas de Azure Resource Manager.                                                                                                                                                                                                       |
| Dirección            | Indica si la regla se aplica al tráfico entrante o al saliente.                                                                                                                                                                                                                                                                                                                    |
| Intervalo de puertos | Puede especificar un puerto individual o un intervalo de puertos, por ejemplo `80` o `10000-10005`.                                                                                                                                                                                                                                                                                |
| Acción               | Permitir o denegar.                                                                                                                                                                                                                                                                                                                                                                |

Las reglas de seguridad se evalúan y aplican en función de la información de la quintuple:

1. Origen.
2. Puerto de origen.
3. Destino.
4. Puerto de destino.
5. Protocolo.

No puede crear dos reglas de seguridad con la misma prioridad y dirección.

Se crea un registro de flujo para las conexiones existentes. Se permite o deniega la comunicación en función del estado de conexión del registro de flujo. El registro de flujo permite que un grupo de seguridad de red sea con estado.

Por ejemplo, si especifica una regla de seguridad de salida para cualquier dirección a través del puerto 80, no será necesario especificar una regla de seguridad de entrada para la respuesta al tráfico saliente. Solo debe especificar una regla de seguridad de entrada si la comunicación se inicia de forma externa. Lo contrario también es cierto.

Las conexiones existentes no se interrumpen necesariamente cuando se elimina una regla de seguridad que permitía la conexión. Al modificar las reglas de grupo de seguridad de red, solo se verán afectadas las nuevas conexiones. Las conexiones existentes no se reevaluarán con las nuevas reglas.

### Cómo filtran el tráfico de red los grupos de seguridad de red

Puede implementar recursos de varios servicios de Azure en una red virtual de Azure.

Puede asociar cero o un grupo de seguridad de red a cada subred de red virtual e interfaz de red en una máquina virtual. El mismo grupo de seguridad de red se puede asociar a tantas interfaces de red y subredes como se desee.

### Tráfico entrante

Para el tráfico entrante, Azure procesa:

1. Las reglas de un grupo de seguridad de red asociadas a una subred, si existe.
2. Las reglas de un grupo de seguridad de red asociadas a la interfaz de red, si existe.

Este proceso incluye también el tráfico dentro de la subred.

* **VM1:** las reglas de NSG1 se procesan porque está asociada a la subred 1. A menos que exista una regla que permita el puerto 80 entrante, `DenyAllInbound` deniega el tráfico. Si NSG1 permite el puerto 80, NSG2 procesa el tráfico. Para permitir el puerto 80 a la máquina virtual, NSG1 y NSG2 deben tener una regla que permita el puerto 80 desde Internet.
* **VM2:** las reglas de NSG1 se procesan porque VM2 está en la subred 1. Como VM2 no tiene un NSG asociado a su interfaz, recibe todo el tráfico permitido o denegado por NSG1.
* **VM3:** como no hay un NSG asociado a la subred 2, el tráfico se permite en la subred y se procesa mediante NSG2, asociado a la interfaz de red.
* **VM4:** el tráfico se permite porque no hay un NSG asociado a la subred 3 ni a la interfaz de red de la máquina virtual.

### Tráfico saliente

Para el tráfico saliente, Azure procesa:

1. Las reglas de un grupo de seguridad de red asociadas a una interfaz de red, si existe.
2. Las reglas de un grupo de seguridad de red asociadas a la subred, si existe.

Este proceso incluye también el tráfico dentro de la subred.

* **VM1:** se procesan las reglas de seguridad de NSG2. Si NSG2 deniega el puerto 80, deniega el tráfico y NSG1 nunca lo evalúa.
* **VM2:** como la interfaz de red no tiene un NSG asociado, se procesan las reglas de NSG1.
* **VM3:** si NSG2 deniega el puerto 80, deniega el tráfico. Si no lo deniega, la regla `AllowInternetOutbound` permite el tráfico porque no hay un NSG asociado a la subred 2.
* **VM4:** todo el tráfico de red se permite porque no hay un NSG asociado a la interfaz ni a la subred 3.

### Tráfico dentro de la subred

Las reglas de seguridad de un NSG asociado a una subred pueden afectar la conectividad entre las máquinas virtuales dentro de ella.

De manera predeterminada, las máquinas virtuales de la misma subred pueden comunicarse en función de una regla de NSG predeterminada que permita el tráfico dentro de la subred.

Si se agrega una regla a NSG1 que deniega todo el tráfico entrante y saliente, VM1 y VM2 no podrán comunicarse entre sí.

Puede ver las reglas de agregado aplicadas a una interfaz de red viendo las **reglas de seguridad eficaces** de una interfaz de red.

También puede usar la **comprobación del flujo de IP** en Azure Network Watcher para determinar si se permite o se deniega una comunicación y exponer la identidad de la regla de seguridad de red responsable.

Los grupos de seguridad de red están asociados a:

* Subredes o máquinas virtuales y servicios en la nube implementados en el modelo de implementación clásico.
* Subredes o interfaces de red en el modelo de implementación de Resource Manager.

A menos que tenga un motivo concreto, se recomienda asociar un grupo de seguridad de red a una subred o a una interfaz de red, pero no a ambas, ya que las reglas pueden entrar en conflicto y provocar problemas de comunicación inesperados.

### Grupos de seguridad de aplicaciones (ASG)

Los grupos de seguridad de aplicaciones permiten configurar la seguridad de red como una extensión natural de la estructura de una aplicación, agrupando máquinas virtuales y directivas de seguridad de red basadas en esos grupos.

Puede reutilizar la directiva de seguridad a escala sin mantenimiento manual de direcciones IP explícitas. La plataforma controla la complejidad de las direcciones IP explícitas y varios conjuntos de reglas.

Una interfaz de red puede ser miembro de varios grupos de seguridad de aplicaciones, hasta los límites de Azure.

### Ejemplo de reglas con ASG

#### Allow-HTTP-Inbound-Internet

Esta regla es necesaria para permitir el tráfico de Internet a los servidores web. Dado que la regla de seguridad predeterminada `DenyAllInbound` deniega el tráfico entrante de Internet, no se necesita ninguna regla adicional para los grupos de seguridad de aplicaciones `AsgLogic` o `AsgDb`.

| Prioridad | Fuente   | Puertos de origen | Destino | Puertos de destino | Protocolo | Acceso   |
| --------- | -------- | ----------------- | ------- | ------------------ | --------- | -------- |
| 100       | Internet | *                 | AsgWeb  | 80                 | TCP       | Permitir |

#### Deny-Database-All

Dado que la regla de seguridad predeterminada `AllowVNetInBound` permite toda la comunicación entre los recursos de la misma red virtual, esta regla es necesaria para denegar el tráfico de todos los recursos.

| Prioridad | Fuente | Puertos de origen | Destino | Puertos de destino | Protocolo  | Acceso  |
| --------- | ------ | ----------------- | ------- | ------------------ | ---------- | ------- |
| 120       | *      | *                 | AsgDb   | 1433               | Cualquiera | Denegar |

#### Allow-Database-BusinessLogic

Esta regla permite el tráfico desde el grupo de seguridad de aplicaciones `asgLogic` al grupo de seguridad de aplicaciones `asgDb`.

La prioridad de esta regla es mayor que la prioridad de la regla `Deny-Database-All`. Como resultado, esta regla se procesa antes de `Deny-Database-All`, por lo que se permite el tráfico del grupo de seguridad de la aplicación `AsgLogic`, mientras que el resto del tráfico está bloqueado.

| Prioridad | Fuente   | Puertos de origen | Destino | Puertos de destino | Protocolo | Acceso   |
| --------- | -------- | ----------------- | ------- | ------------------ | --------- | -------- |
| 110       | AsgLogic | *                 | AsgDb   | 1433               | TCP       | Permitir |

Las interfaces de red que son miembros del grupo de seguridad de aplicaciones aplican las reglas que las especifican como origen o destino.

Las reglas no afectan a otras interfaces de red. Si la interfaz de red no es miembro de un grupo de seguridad de aplicaciones, la regla no se aplica a la interfaz de red aunque el grupo de seguridad de red esté asociado a la subred.

### Restricciones de los grupos de seguridad de aplicaciones

* Hay límites para el número de grupos de seguridad de aplicaciones que puede tener en una suscripción y otros límites relacionados con los grupos de seguridad de aplicaciones.
* Todas las interfaces de red asignadas a un grupo de seguridad de aplicaciones deben existir en la misma red virtual en la que se encuentra la primera interfaz de red asignada al grupo.
* No se pueden agregar interfaces de red de diferentes redes virtuales al mismo grupo de seguridad de aplicaciones.
* Si especifica un grupo de seguridad de aplicaciones como origen y destino en una regla de seguridad, las interfaces de red en ambos grupos de seguridad de aplicaciones deben existir en la misma red virtual.

Para minimizar el número de reglas de seguridad necesarias y la necesidad de cambiar las reglas, planee los grupos de seguridad de aplicaciones y cree reglas mediante etiquetas de servicio o grupos de seguridad de aplicaciones en lugar de direcciones IP individuales o intervalos de direcciones IP siempre que sea posible.

---
