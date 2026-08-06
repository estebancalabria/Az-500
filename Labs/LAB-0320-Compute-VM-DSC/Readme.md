# Lab: Deshabilitar Windows Features automáticamente con DSC en ARM Templates

**Escenario:** Tenés ARM templates para desplegar VMs de Azure. Necesitás deshabilitar Windows Features no usadas automáticamente cuando se provisionan instancias de la VM.

## ¿Qué son los Windows Features?

Son roles y componentes opcionales del sistema operativo Windows Server (o Windows desktop) que no vienen activados por defecto y se habilitan/deshabilitan según lo que necesite el servidor. Se gestionan con el cmdlet `Get-WindowsFeature` / `Install-WindowsFeature` (o desde "Server Manager" en la UI). Cada uno tiene un nombre interno (el que usa DSC en `Name`) distinto del nombre visible.

Algunos de los más comunes:

| Nombre visible | Nombre interno (DSC/PowerShell) | Para qué sirve |
|---|---|---|
| Web Server (IIS) | `Web-Server` | Servidor web / hosting de aplicaciones |
| .NET Framework 4.x | `NET-Framework-45-Core` | Runtime para apps .NET |
| Telnet Client | `Telnet-Client` | Cliente Telnet (legacy, suele deshabilitarse por seguridad) |
| DNS Server | `DNS` | Rol de servidor DNS |
| DHCP Server | `DHCP` | Rol de servidor DHCP |
| Active Directory Domain Services | `AD-Domain-Services` | Rol de Domain Controller |
| File Server | `FS-FileServer` | Compartir archivos en red |
| Hyper-V | `Hyper-V` | Virtualización |
| Print and Document Services | `Print-Services` | Servidor de impresión |
| Windows PowerShell ISE | `PowerShell-ISE` | Editor gráfico de PowerShell (a veces se deshabilita en servers hardened) |
| SNMP Service | `SNMP-Service` | Monitoreo vía SNMP (target típico de hardening por vulnerabilidades) |
| SMB 1.0/CIFS File Sharing Support | `FS-SMB1` | Protocolo SMB1 legacy (se recomienda deshabilitar, tiene vulnerabilidades conocidas) |

Para ver todos los disponibles en una VM: `Get-WindowsFeature` (server) o `Get-WindowsOptionalFeature -Online` (desktop).

---

## Objetivo del lab

Crear una DSC configuration simple, con el feature hardcodeado en el script (sin parámetros), y desplegarla vía ARM template como extensión de una VM.

> **Nota:** para habilitar un feature en vez de deshabilitarlo (por ejemplo IIS con `Web-Server`), simplemente cambiás a mano `Name` y `Ensure = 'Present'` en el script del Paso 1.

## Prerrequisitos

- Suscripción de Azure activa
- Azure PowerShell o Azure CLI instalado
- Una VM Windows ya desplegada (o lista para desplegar) en un Resource Group

---

## Paso 1: Crear la configuración DSC

Archivo `DisableFeatures.ps1`:

```powershell
Configuration DisableFeatures
{
    Import-DscResource -ModuleName PSDesiredStateConfiguration

    Node localhost
    {
        WindowsFeature DisableTelnetClient
        {
            Name   = 'Telnet-Client'
            Ensure = 'Absent'
        }
    }
}
```

## Paso 2: Empaquetar la configuración

```powershell
# Compilar el MOF
DisableFeatures -OutputPath .\DSCConfig

# Comprimir en zip (requerido por el extension handler)
Compress-Archive -Path .\DSCConfig\*, .\DisableFeatures.ps1 -DestinationPath .\DisableFeatures.zip
```

## Paso 3: Subir el paquete a un Storage Account

```powershell
$storageAccount = Get-AzStorageAccount -ResourceGroupName "rg-lab" -Name "stlabdsc"
$ctx = $storageAccount.Context

New-AzStorageContainer -Name "dsc" -Context $ctx -Permission Blob

Set-AzStorageBlobContent -File ".\DisableFeatures.zip" `
    -Container "dsc" -Blob "DisableFeatures.zip" -Context $ctx
```

## Paso 4: Definir el ARM template con la DSC extension

Template completo (`azuredeploy.json`). Asume que la VM ya existe en el mismo Resource Group:

```json
{
  "$schema": "https://schema.management.azure.com/schemas/2019-04-01/deploymentTemplate.json#",
  "contentVersion": "1.0.0.0",
  "parameters": {
    "vmName": {
      "type": "string",
      "metadata": {
        "description": "Nombre de la VM existente donde se aplica la DSC extension"
      }
    }
  },
  "resources": [
    {
      "type": "Microsoft.Compute/virtualMachines/extensions",
      "apiVersion": "2023-03-01",
      "name": "[concat(parameters('vmName'), '/DSCExtension')]",
      "location": "[resourceGroup().location]",
      "properties": {
        "publisher": "Microsoft.Powershell",
        "type": "DSC",
        "typeHandlerVersion": "2.83",
        "autoUpgradeMinorVersion": true,
        "settings": {
          "configuration": {
            "url": "https://stlabdsc.blob.core.windows.net/dsc/DisableFeatures.zip",
            "script": "DisableFeatures.ps1",
            "function": "DisableFeatures"
          }
        },
        "protectedSettings": {}
      }
    }
  ]
}
```

Archivo de parámetros de ejemplo (`azuredeploy.parameters.json`):

```json
{
  "$schema": "https://schema.management.azure.com/schemas/2019-04-01/deploymentParameters.json#",
  "contentVersion": "1.0.0.0",
  "parameters": {
    "vmName": { "value": "vm-lab-01" }
  }
}
```

## Paso 5: Desplegar el template

```powershell
New-AzResourceGroupDeployment `
  -ResourceGroupName "rg-lab" `
  -TemplateFile ".\azuredeploy.json" `
  -TemplateParameterFile ".\azuredeploy.parameters.json"
```

## Paso 6: Validar

Conectate a la VM por RDP o Remote PowerShell y verificá:

```powershell
Get-WindowsFeature -Name Telnet-Client
```

El estado de `Telnet-Client` debe figurar como `Removed`/`Absent`.
