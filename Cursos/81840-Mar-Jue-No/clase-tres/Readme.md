# Clase Tres - 18 de Junio 2026

# Repaso

* Seguridad en Redes
    * (VNET) Virtual Networks
      * Subnets
      * Segmentacion
    * (NSG) Network Security Grouos
      * Funciona a nivel capa 4 de OSI
      * Reglas
        * Ej Regla Inbound para RDP
* Seguridad en VMs
    * (VM) Virtual Machine
      * Recursos Relacionados
        * Disks
        * IP Publicas
        * NIC Address
      * Formas de Acceso
        * RDP
        * Bation as travez del Azure Portal (Un poco mas costosa pero mas segura)
        * JIT (Just In Time)
* Seguridad Operatorio
   * Hacer deploys mediante arm templates / bicep template

---

# Seguridad en redes

## Firewall

### Setup del Laboratorio

* Crear un resource group
   * rg-az500-clase-tres
* Crear una VNet
   * vnet-az500-clase-tres
   * Dejo todos los valores salvo el nombre por defecto
* Crear la subnet AzureFirewallSubnet
   * 10.0.2.0 / 26
* Crear la subnet Azure FirewallManagementSubnet
   * 10.0.3.0 / 26
* Crear un Network Security Group
   * nsg-az500-clase-tres
   * Agregar la regla de Inbound para permitir RDP desde mi IP
      * Allow_MyIp_To_Rdp_InBound
* Crear una Virtual Machine
   * vm-az500-clase-tres
   * IMPORTANTE: Asociar a la vm a la subnet default y al nsg recien creado (nsg-az500-clase-tres)
* Conectarnos a la VM por RDP
   * Verificar que la pc tenga salida a internet

### Creacion del FW

* Crear dos IP publicas
   * Firewall
      * pip-fw-main
   * Management del Firewall
      * pip-fw-mgmt
* Crear el firewall
   * fw-az500-clase-tres
   * SKU : Standard
   * Crear una policy nueva para el fw
      * fw-policy-az500-clase-tres
    * Asociar el firewall con
       * vnet : vnet-az500-clase-tres
       * ip del Firewall : pip-fw-main
       * IP para management : pip-fw-mgmt

> [!NOTE]
> Esperar que la creacion del Firewall lo cree

* Firewall
   * Anotar IP Privada (10.0.2.4)
* Crear una tabla de Ruteo
   *  rt-az500-clase-tres
   *  Propagate Gateway Rules : Disabled
      *  Agregar la ruta
         *  route-to-fw
            *  Adress prefix : 0.0.0.0/0 (todas)
            *  Next Hop Type : Virtual Appliance
            *  Next HOP : 10.0.2.4
      *  Asociar la ruta con la subnet de la VM (default)

* Configurar las reglas de firewall para permitir conectarse a la VM
   * Redirigir el trafico del fw en 3389 a la Virtual  (DNAT)
   * Permitir el trafico RDP interneto en la red (Network Rule)

* Desasociar la IP publica de la maquina virtual
   * Desasocio la IP publica de la NIC de la VM
   * Elimino la IP Publica
 
* Conectarnos a la VM mediante la IP publica de Firewall
   * Verificar que no podemos navegar porque el FW bloquea todo el trafico
 
* Agregar la regla de Applicacion
   * Source : 10.0.0.0/24 (Subnet)
   * Destination *
   * Prtocol : http:80, https:443
 
* Si verificamos ya podemos navegar por internet

## VNET peering

* Crear un Resource Group
* Crear dos Virtual Network
   * vnet-uno (10.0.0.0 / 24)
   * vnet-dos (10.1.0.0 / 24)
* Crear una maquina virtual en cada vnet
   * vm-uno (10.0.0.4)
   * vm-dos (10.1.0.4)
* Conectarse a la primera
* Tratar de hacer un RDP a la segunda
      * DENEGADO
* Vamos a establecer VNET peering entre las dos subnets
* Vamos a hacer un ping de una maquina a la otra
     * APROBADO
