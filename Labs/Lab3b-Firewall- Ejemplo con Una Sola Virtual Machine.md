# Laboratorio 3B
Uso del Firewall con una sola VM

## Pasos
1. Crear el Resource Group
2. Crear la VNET
3. Crear las Subnets
      * Subnet principal
      * Subnet "AzureFirewallSubnet"
      * Subnet "AzureFirewallManagementSubnet"
4. Crear un NSG
     * Permitir todo el trafico RDP (3389) hacia la red
5. Crear a Virtual Machine
     * Asociarla a la subnet pricipal y al nsg creados anteriormente
6. Conectarnos a la VM por RDP y fijarse que navegamos por internet
7. Crear el Firewal
8. Crear la tabla re ruteo
    * De la IP de la VM que pase por la IP del Firewall
    * Asociar la tabla re ruteo a la subnet pruncipal
9. Me deberia desconectar de la VM
10. Agregar reglas en el firewal
    * Agregar una regla de NAT para que cuando te conectes a la IP publica del Firewall lo redirija a la IP privada de la VM en el 3389
    * Agregar una Network Rule para permitir el trafico al RDP 3389
11. Conectar a la Virtual
    * Vemos que no se puede navegar desde internet
