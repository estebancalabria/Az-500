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
