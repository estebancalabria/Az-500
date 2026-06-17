# Clase Dos - 16 de Junio 2026

# Repaso

* Roadmap
* Microsoft Entra
  * Gobernaza de Identidades
      * Mantenimientos de Grupos y Usuarios
      * PIM : Dar accesos temporales
      * Access Revies : Reveer accesos para eliminar usuarios fantasmas
      * Entitlement Management
      * Sign In Risks
      * Conditional Access

# Tennant   

## Licecias en mi Tennant

* Licencia E2 para todo lo que es ID Governance

## Creacion de Un tennant

* En el microsoft Entra puedo crear en el overview un tennant nuevo

# Seguridad en Red

> [!NOTE]
> El modelo de referencia en cuanto a segudiad de redes en Azure contempla armar una Arquitectura "Hub and Spoke"
> Donde existe una VNet que tiene acceso a internet y concentra recursos criticos de seguridad (Firewall, Bation, VpnGateway)
> La otras Vnets no deberian tener conexion directa con Internet

* Primero vamos a crear un Resource Group
  * Rg-Az500-Clase-Dos

* Crear una Virtual Network
  * vnet-Az500-Clase-Dos
  * 10.0.0.0/16
      * Los primeros 16 bits (2 octetos) quedan fijos
      * Esta seria la red 10.0 (O la red 0)

> [!NOTE]
> 10.0.0.0 / 16  -> 10.0.0.0 a 10.0.255.255

* Crear una subnet
  *   * vnet-Az500-Clase-Dos-Subnet-0
      *  10.0.0.0/24
   
* Crear un NSG (Networ Security Group)
    * Por ahora queda como esta...

* Creamos una Virtual Machine

* Me creo todo lo siguiente

```
{
    "$schema": "https://schema.management.azure.com/schemas/2019-04-01/deploymentTemplate.json#",
    "contentVersion": "1.0.0.0",
    "parameters": {
        "virtualMachines_vm_az500_clase_dos_name": {
            "defaultValue": "vm-az500-clase-dos",
            "type": "String"
        },
        "virtualNetworks_vnet_Az500_Clase_Dos_name": {
            "defaultValue": "vnet-Az500-Clase-Dos",
            "type": "String"
        },
        "networkInterfaces_vm_az500_clase_dos815_name": {
            "defaultValue": "vm-az500-clase-dos815",
            "type": "String"
        },
        "publicIPAddresses_vm_az500_clase_dos_ip_name": {
            "defaultValue": "vm-az500-clase-dos-ip",
            "type": "String"
        },
        "virtualNetworks_vm_az500_clase_dos_vnet_name": {
            "defaultValue": "vm-az500-clase-dos-vnet",
            "type": "String"
        },
        "networkSecurityGroups_nsg_az500_clase_dos_name": {
            "defaultValue": "nsg-az500-clase-dos",
            "type": "String"
        },
        "networkSecurityGroups_vm_az500_clase_dos_nsg_name": {
            "defaultValue": "vm-az500-clase-dos-nsg",
            "type": "String"
        }
    },
    "variables": {},
    "resources": [
        {
            "type": "Microsoft.Network/networkSecurityGroups",
            "apiVersion": "2025-05-01",
            "name": "[parameters('networkSecurityGroups_nsg_az500_clase_dos_name')]",
            "location": "westus",
            "properties": {
                "securityRules": [
                    {
                        "name": "AllowRDP",
                        "id": "[resourceId('Microsoft.Network/networkSecurityGroups/securityRules', parameters('networkSecurityGroups_nsg_az500_clase_dos_name'), 'AllowRDP')]",
                        "properties": {
                            "protocol": "TCP",
                            "sourcePortRange": "*",
                            "destinationPortRange": "3389",
                            "sourceAddressPrefix": "181.238.134.205",
                            "destinationAddressPrefix": "*",
                            "access": "Allow",
                            "priority": 100,
                            "direction": "Inbound",
                            "sourcePortRanges": [],
                            "destinationPortRanges": [],
                            "sourceAddressPrefixes": [],
                            "destinationAddressPrefixes": []
                        }
                    }
                ]
            }
        },
        {
            "type": "Microsoft.Network/networkSecurityGroups",
            "apiVersion": "2025-05-01",
            "name": "[parameters('networkSecurityGroups_vm_az500_clase_dos_nsg_name')]",
            "location": "westus",
            "properties": {
                "securityRules": [
                    {
                        "name": "default-allow-rdp",
                        "id": "[resourceId('Microsoft.Network/networkSecurityGroups/securityRules', parameters('networkSecurityGroups_vm_az500_clase_dos_nsg_name'), 'default-allow-rdp')]",
                        "properties": {
                            "protocol": "TCP",
                            "sourcePortRange": "*",
                            "destinationPortRange": "3389",
                            "sourceAddressPrefix": "*",
                            "destinationAddressPrefix": "*",
                            "access": "Allow",
                            "priority": 1000,
                            "direction": "Inbound",
                            "sourcePortRanges": [],
                            "destinationPortRanges": [],
                            "sourceAddressPrefixes": [],
                            "destinationAddressPrefixes": []
                        }
                    }
                ]
            }
        },
        {
            "type": "Microsoft.Network/publicIPAddresses",
            "apiVersion": "2025-05-01",
            "name": "[parameters('publicIPAddresses_vm_az500_clase_dos_ip_name')]",
            "location": "westus",
            "sku": {
                "name": "Standard",
                "tier": "Regional"
            },
            "properties": {
                "ipAddress": "104.210.49.44",
                "publicIPAddressVersion": "IPv4",
                "publicIPAllocationMethod": "Static",
                "idleTimeoutInMinutes": 4,
                "ipTags": [],
                "ddosSettings": {
                    "protectionMode": "VirtualNetworkInherited"
                }
            }
        },
        {
            "type": "Microsoft.Network/virtualNetworks",
            "apiVersion": "2025-05-01",
            "name": "[parameters('virtualNetworks_vm_az500_clase_dos_vnet_name')]",
            "location": "westus",
            "properties": {
                "addressSpace": {
                    "addressPrefixes": [
                        "10.0.0.0/16"
                    ]
                },
                "privateEndpointVNetPolicies": "Disabled",
                "subnets": [
                    {
                        "name": "default",
                        "id": "[resourceId('Microsoft.Network/virtualNetworks/subnets', parameters('virtualNetworks_vm_az500_clase_dos_vnet_name'), 'default')]",
                        "properties": {
                            "addressPrefix": "10.0.0.0/24",
                            "delegations": [],
                            "privateEndpointNetworkPolicies": "Disabled",
                            "privateLinkServiceNetworkPolicies": "Enabled"
                        }
                    }
                ],
                "virtualNetworkPeerings": [],
                "enableDdosProtection": false
            }
        },
        {
            "type": "Microsoft.Network/virtualNetworks",
            "apiVersion": "2025-05-01",
            "name": "[parameters('virtualNetworks_vnet_Az500_Clase_Dos_name')]",
            "location": "westus",
            "properties": {
                "addressSpace": {
                    "addressPrefixes": [
                        "10.0.0.0/16"
                    ]
                },
                "encryption": {
                    "enabled": false,
                    "enforcement": "AllowUnencrypted"
                },
                "privateEndpointVNetPolicies": "Disabled",
                "subnets": [
                    {
                        "name": "[concat(parameters('virtualNetworks_vnet_Az500_Clase_Dos_name'), '-Subnet-0')]",
                        "id": "[resourceId('Microsoft.Network/virtualNetworks/subnets', parameters('virtualNetworks_vnet_Az500_Clase_Dos_name'), concat(parameters('virtualNetworks_vnet_Az500_Clase_Dos_name'), '-Subnet-0'))]",
                        "properties": {
                            "addressPrefixes": [
                                "10.0.0.0/24"
                            ],
                            "delegations": [],
                            "privateEndpointNetworkPolicies": "Disabled",
                            "privateLinkServiceNetworkPolicies": "Enabled",
                            "defaultOutboundAccess": false
                        }
                    }
                ],
                "virtualNetworkPeerings": [],
                "enableDdosProtection": false
            }
        },
        {
            "type": "Microsoft.Compute/virtualMachines",
            "apiVersion": "2025-11-01",
            "name": "[parameters('virtualMachines_vm_az500_clase_dos_name')]",
            "location": "westus",
            "dependsOn": [
                "[resourceId('Microsoft.Network/networkInterfaces', parameters('networkInterfaces_vm_az500_clase_dos815_name'))]"
            ],
            "properties": {
                "hardwareProfile": {
                    "vmSize": "Standard_DS2_v2"
                },
                "additionalCapabilities": {
                    "hibernationEnabled": false
                },
                "storageProfile": {
                    "imageReference": {
                        "publisher": "MicrosoftWindowsServer",
                        "offer": "WindowsServer",
                        "sku": "2025-datacenter-g2",
                        "version": "latest"
                    },
                    "osDisk": {
                        "osType": "Windows",
                        "name": "[concat(parameters('virtualMachines_vm_az500_clase_dos_name'), '_OsDisk_1_91aa4370f4d94290a1c2d4ca292ca7bf')]",
                        "createOption": "FromImage",
                        "caching": "ReadWrite",
                        "managedDisk": {
                            "storageAccountType": "Premium_LRS",
                            "id": "[resourceId('Microsoft.Compute/disks', concat(parameters('virtualMachines_vm_az500_clase_dos_name'), '_OsDisk_1_91aa4370f4d94290a1c2d4ca292ca7bf'))]"
                        },
                        "deleteOption": "Delete",
                        "diskSizeGB": 127
                    },
                    "dataDisks": [],
                    "diskControllerType": "SCSI"
                },
                "osProfile": {
                    "computerName": "vm-az500-clase-",
                    "windowsConfiguration": {
                        "provisionVMAgent": true,
                        "enableAutomaticUpdates": true,
                        "patchSettings": {
                            "patchMode": "AutomaticByOS",
                            "assessmentMode": "ImageDefault",
                            "enableHotpatching": false
                        }
                    },
                    "secrets": [],
                    "allowExtensionOperations": true,
                    "requireGuestProvisionSignal": true,
                    "adminUsername": "AzureUser"
                },
                "securityProfile": {
                    "uefiSettings": {
                        "secureBootEnabled": true,
                        "vTpmEnabled": true
                    },
                    "securityType": "TrustedLaunch"
                },
                "networkProfile": {
                    "networkInterfaces": [
                        {
                            "id": "[resourceId('Microsoft.Network/networkInterfaces', parameters('networkInterfaces_vm_az500_clase_dos815_name'))]",
                            "properties": {
                                "deleteOption": "Delete"
                            }
                        }
                    ]
                }
            }
        },
        {
            "type": "Microsoft.Network/networkSecurityGroups/securityRules",
            "apiVersion": "2025-05-01",
            "name": "[concat(parameters('networkSecurityGroups_nsg_az500_clase_dos_name'), '/AllowRDP')]",
            "dependsOn": [
                "[resourceId('Microsoft.Network/networkSecurityGroups', parameters('networkSecurityGroups_nsg_az500_clase_dos_name'))]"
            ],
            "properties": {
                "protocol": "TCP",
                "sourcePortRange": "*",
                "destinationPortRange": "3389",
                "sourceAddressPrefix": "181.238.134.205",
                "destinationAddressPrefix": "*",
                "access": "Allow",
                "priority": 100,
                "direction": "Inbound",
                "sourcePortRanges": [],
                "destinationPortRanges": [],
                "sourceAddressPrefixes": [],
                "destinationAddressPrefixes": []
            }
        },
        {
            "type": "Microsoft.Network/networkSecurityGroups/securityRules",
            "apiVersion": "2025-05-01",
            "name": "[concat(parameters('networkSecurityGroups_vm_az500_clase_dos_nsg_name'), '/default-allow-rdp')]",
            "dependsOn": [
                "[resourceId('Microsoft.Network/networkSecurityGroups', parameters('networkSecurityGroups_vm_az500_clase_dos_nsg_name'))]"
            ],
            "properties": {
                "protocol": "TCP",
                "sourcePortRange": "*",
                "destinationPortRange": "3389",
                "sourceAddressPrefix": "*",
                "destinationAddressPrefix": "*",
                "access": "Allow",
                "priority": 1000,
                "direction": "Inbound",
                "sourcePortRanges": [],
                "destinationPortRanges": [],
                "sourceAddressPrefixes": [],
                "destinationAddressPrefixes": []
            }
        },
        {
            "type": "Microsoft.Network/virtualNetworks/subnets",
            "apiVersion": "2025-05-01",
            "name": "[concat(parameters('virtualNetworks_vm_az500_clase_dos_vnet_name'), '/default')]",
            "dependsOn": [
                "[resourceId('Microsoft.Network/virtualNetworks', parameters('virtualNetworks_vm_az500_clase_dos_vnet_name'))]"
            ],
            "properties": {
                "addressPrefix": "10.0.0.0/24",
                "delegations": [],
                "privateEndpointNetworkPolicies": "Disabled",
                "privateLinkServiceNetworkPolicies": "Enabled"
            }
        },
        {
            "type": "Microsoft.Network/virtualNetworks/subnets",
            "apiVersion": "2025-05-01",
            "name": "[concat(parameters('virtualNetworks_vnet_Az500_Clase_Dos_name'), '/', parameters('virtualNetworks_vnet_Az500_Clase_Dos_name'), '-Subnet-0')]",
            "dependsOn": [
                "[resourceId('Microsoft.Network/virtualNetworks', parameters('virtualNetworks_vnet_Az500_Clase_Dos_name'))]"
            ],
            "properties": {
                "addressPrefixes": [
                    "10.0.0.0/24"
                ],
                "delegations": [],
                "privateEndpointNetworkPolicies": "Disabled",
                "privateLinkServiceNetworkPolicies": "Enabled",
                "defaultOutboundAccess": false
            }
        },
        {
            "type": "Microsoft.Network/networkInterfaces",
            "apiVersion": "2025-05-01",
            "name": "[parameters('networkInterfaces_vm_az500_clase_dos815_name')]",
            "location": "westus",
            "dependsOn": [
                "[resourceId('Microsoft.Network/publicIPAddresses', parameters('publicIPAddresses_vm_az500_clase_dos_ip_name'))]",
                "[resourceId('Microsoft.Network/virtualNetworks/subnets', parameters('virtualNetworks_vm_az500_clase_dos_vnet_name'), 'default')]",
                "[resourceId('Microsoft.Network/networkSecurityGroups', parameters('networkSecurityGroups_vm_az500_clase_dos_nsg_name'))]"
            ],
            "kind": "Regular",
            "properties": {
                "ipConfigurations": [
                    {
                        "name": "ipconfig1",
                        "id": "[concat(resourceId('Microsoft.Network/networkInterfaces', parameters('networkInterfaces_vm_az500_clase_dos815_name')), '/ipConfigurations/ipconfig1')]",
                        "properties": {
                            "privateIPAddress": "10.0.0.4",
                            "privateIPAllocationMethod": "Dynamic",
                            "publicIPAddress": {
                                "id": "[resourceId('Microsoft.Network/publicIPAddresses', parameters('publicIPAddresses_vm_az500_clase_dos_ip_name'))]",
                                "properties": {
                                    "deleteOption": "Delete"
                                }
                            },
                            "subnet": {
                                "id": "[resourceId('Microsoft.Network/virtualNetworks/subnets', parameters('virtualNetworks_vm_az500_clase_dos_vnet_name'), 'default')]"
                            },
                            "primary": true,
                            "privateIPAddressVersion": "IPv4"
                        }
                    }
                ],
                "dnsSettings": {
                    "dnsServers": []
                },
                "enableAcceleratedNetworking": true,
                "enableIPForwarding": false,
                "disableTcpStateTracking": false,
                "networkSecurityGroup": {
                    "id": "[resourceId('Microsoft.Network/networkSecurityGroups', parameters('networkSecurityGroups_vm_az500_clase_dos_nsg_name'))]"
                },
                "nicType": "Standard",
                "auxiliaryMode": "None",
                "auxiliarySku": "None"
            }
        }
    ]
}
```

* Borramos Todo

* Hacemos un Deploy Custom Template con el ARM template de arriba

## Azure Bastion

* Es un servicio de Azure para que nos podamos conectar a las maquinas virtuales de forma segura sin exponer el puerto RDP en internet
* Solo permite conexiones mediante el portal de Azure
* Es la forma recomendada por Microsoft para conectarnos las VM
* Contra: Tiene un costo adicional el recurso de Bastion


## JIT

* Just In Time
* Es para no dejar el puerto RDP abierto todo el tiempo
* Solamente das acceso cuando lo piden (Just in Time) por un tiempo determinado



