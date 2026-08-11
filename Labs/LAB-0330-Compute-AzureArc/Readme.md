# Lab: Onboarding de servidores on-premises a Azure Arc + Vulnerability Scanning con Defender for Cloud

## Contexto
10 servidores Windows Server 2019 on-premises. Se quiere habilitar vulnerability scanning vía Microsoft Defender for Cloud. Paso previo obligatorio: proyectarlos como recursos Azure Arc.

## Objetivo
Conectar un servidor on-premises (o VM simulando on-prem) a Azure Arc y habilitar Defender for Servers Plan 2 con vulnerability assessment.

## Prerrequisitos
- Suscripción Azure con permisos de Contributor sobre el Resource Group.
- Resource Group creado.
- Service Principal o permisos para generar token de onboarding.
- Windows Server 2019 con salida a internet (o Private Link/Proxy configurado).

## Paso 1 — Crear Resource Group y Service Principal

```powershell
az login
az group create --name rg-arc-lab --location eastus

az ad sp create-for-rbac --name "sp-arc-onboarding" --role "Azure Connected Machine Onboarding" `
  --scopes /subscriptions/<SUBSCRIPTION_ID>/resourceGroups/rg-arc-lab
```

**Output esperado:**
```json
{
  "appId": "xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx",
  "displayName": "sp-arc-onboarding",
  "password": "xxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxx",
  "tenant": "xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx"
}
```

**Explicación:** el rol "Azure Connected Machine Onboarding" es de mínimo privilegio, solo permite registrar el servidor como recurso Arc, no administrar Arc en general.

## Paso 2 — Generar script de instalación desde el portal o CLI

```powershell
az connectedmachine list --resource-group rg-arc-lab -o table
```

Desde el portal: **Azure Arc > Servers > Add > Add a single server (interactive)**, o generar script de onboarding masivo si son 10 servidores:

```powershell
$env:SUBSCRIPTION_ID = "<SUBSCRIPTION_ID>"
$env:RESOURCE_GROUP = "rg-arc-lab"
$env:TENANT_ID = "<TENANT_ID>"
$env:LOCATION = "eastus"
$env:AUTH_TYPE = "principal"
$env:CORRELATION_ID = [guid]::NewGuid().ToString()
$env:CLOUD = "AzureCloud"

Invoke-WebRequest -Uri "https://aka.ms/AzureConnectedMachineAgent" -OutFile AzureConnectedMachineAgent.msi
msiexec /i AzureConnectedMachineAgent.msi /l*v installationlog.txt /qn
```

## Paso 3 — Ejecutar el connect en cada servidor

```powershell
& "$env:ProgramW6432\AzureConnectedMachineAgent\azcmagent.exe" connect `
  --service-principal-id $env:appId `
  --service-principal-secret $env:password `
  --resource-group $env:RESOURCE_GROUP `
  --tenant-id $env:TENANT_ID `
  --location $env:LOCATION `
  --subscription-id $env:SUBSCRIPTION_ID `
  --cloud $env:CLOUD
```

**Output esperado:**
```
Executing pre-checks...
Connecting machine to Azure...
This can take a few minutes...
Successfully connected to Azure.
```

**Explicación:** el agente `azcmagent` es distinto del agente de Log Analytics o del MMA/AMA; su única función es establecer identidad y conectividad de gestión (Azure Resource Manager), no telemetría de seguridad.

## Paso 4 — Verificar el recurso Arc

```powershell
az connectedmachine show --name <NOMBRE_SERVIDOR> --resource-group rg-arc-lab -o table
```

**Output esperado:**
```
Name              Location    Status    OSName
----------------  ----------  --------  --------
SRV-ONPREM-01     eastus      Connected Windows
```

## Paso 5 — Habilitar Defender for Servers Plan 2

```powershell
az security pricing create --name VirtualMachines --tier Standard
```

Luego, en el portal: **Defender for Cloud > Environment Settings > rg-arc-lab > Defender plans > Servers > Plan 2 > Settings > Vulnerability assessment for machines = Microsoft Defender vulnerability management (built-in)**.

## Paso 6 — Verificar el vulnerability assessment

```powershell
az security assessment list --query "[?contains(resourceDetails.Id, 'SRV-ONPREM-01')]" -o table
```

**Output esperado:** aparece el assessment `Machines should have a vulnerability assessment solution` con estado **Healthy** una vez que el agente MDE se despliega automáticamente sobre el recurso Arc.

## Consigna
Repetí el proceso para 3 servidores simulados (podés usar 3 VMs Azure con `az vm run-command` deshabilitado para simular "on-premises" o VMs en un Resource Group separado sin Arc auto-habilitado). Confirmá que los 3 aparecen como **Connected** en Azure Arc y que el assessment de vulnerabilidades pasa a **Healthy** dentro de las 24 hs siguientes al onboarding de MDE.

## Nota conceptual
El onboarding a Arc no otorga por sí solo capacidades de seguridad: solo habilita la identidad de recurso. El vulnerability scanning real lo provee Microsoft Defender for Endpoint, que Defender for Cloud despliega automáticamente sobre el recurso Arc-enabled una vez habilitado el plan.
