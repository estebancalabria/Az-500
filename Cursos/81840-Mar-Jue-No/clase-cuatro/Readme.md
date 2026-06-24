# Clase Cuatro - 23 de Junio 2026

# Repaso

* Seguridad en Red
  * NSG (Network Security Group)
    * Funciona a nivel capa 4 (Puerto e IP)
  * Firewall para proteger los recursos publicos
    * DNAT 
    * Aplicacion Rules
    * Network Rules
  * Implentamos la arquitectura Hub and Spoke
  * VNet Peering

# Setup

* Crear el Resource Group

# Despliegue aplicaciones

* Opcion de despliegye de aplicaciones
  * Virtual Machine
  * App Services
  * Contenedores
      * App Services
      * Container Instances
      * Container Apps
      * Kubernetes
    
* Pagina de registro de  contenedores publica
  * https://hub.docker.com/

# Desplegando contenedores

* ACR : Azure Contanier Registry
* ACI : Azure Container Instance

* Creamos un ACI con la imagen de nginx
  * Nos Da una ip publica a la cual puedo acceder y ver que el contenedor esta desplegado
  * Usamos la imagen "nginx" que la descarga de dockerhub
 
* Crear un Container Registry

* Subir una imagen del contenedor publico de Microsoft a mi propio contenedor (Solamente por cloud shell)

```
az acr import --name acraz500casecatroesteban --source mcr.microsoft.com/azuredocs/aci-helloworld --image nginx
```

# Kubernetees

* Crear el resource group
 * rg-az500-clase-cuatro

* Crear el ACR por Linea de comando

```
az acr create --resource-group rg-az500-clase-cuatro --name acraz500esteban --sku basic
```

* Vamos a crear un archivo dockerfile

> [!NOTE]
> El archivo Dockerfile es un archivo que contiene la imagen base que vamos a usar y una serie de comandos que dicen como instalar la aplicacion en nuestro contenedor

```
echo "FROM nginx" > Dockerfile
```

* Luego hacemos build del dockerfile y lo subimos a nuestro container registry

```
az acr build --resource-group rg-az500-clase-cuatro --image sample/nginx:v1 --registry acraz500esteban --file Dockerfile .
```

* Verificamso que se agrego la imagen al ACR

* Creamos el "Kubernetes Services"

> [!WARN]
> No pudimos crearlo por policies

```
{
  "code": "RequestDisallowedByPolicy",
  "message": "Create or update VMSS /subscriptions/4b6afa6f-931f-4b5f-971d-d63cc61559ee/resourceGroups/MC_rg-az500-clase-cuatro_aks-az500-esteban_eastus/providers/Microsoft.Compute/virtualMachineScaleSets/aks-agentpool-36566357-vmss failed. Resource 'aks-agentpool-36566357-vmss' was disallowed by policy. Policy identifiers: '[{\"policyAssignment\":{\"name\":\"XLabs-LabSub-Control-Initiative-253\",\"id\":\"/providers/Microsoft.Management/managementGroups/XLabs-LabSub-XLabs-253/providers/Microsoft.Authorization/policyAssignments/XLabs-253-assign\"},\"policyDefinition\":{\"name\":\"XLabs_Lab_Sub_VMScaleSets_SKUs\",\"id\":\"/providers/Microsoft.Management/managementGroups/XLabs-Lab-Subscriptions/providers/Microsoft.Authorization/policyDefinitions/4360d1d0-9890-46ee-8e52-f00cce045b56\",\"version\":\"1.0.0\"},\"policySetDefinition\":{\"name\":\"XLabs-LabSub-Control-Initiative\",\"id\":\"/providers/Microsoft.Management/managementGroups/XLabs-Lab-Subscriptions/providers/Microsoft.Authorization/policySetDefinitions/99a48ecc-3334-4f3b-afc1-b472b5e1adcf\",\"version\":\"1.0.0\"}}]'.",
  "details": [
    {
      "code": "RequestDisallowedByPolicy",
      "target": "aks-agentpool-36566357-vmss",
      "message": "Resource 'aks-agentpool-36566357-vmss' was disallowed by policy. Policy identifiers: '[{\"policyAssignment\":{\"name\":\"XLabs-LabSub-Control-Initiative-253\",\"id\":\"/providers/Microsoft.Management/managementGroups/XLabs-LabSub-XLabs-253/providers/Microsoft.Authorization/policyAssignments/XLabs-253-assign\"},\"policyDefinition\":{\"name\":\"XLabs_Lab_Sub_VMScaleSets_SKUs\",\"id\":\"/providers/Microsoft.Management/managementGroups/XLabs-Lab-Subscriptions/providers/Microsoft.Authorization/policyDefinitions/4360d1d0-9890-46ee-8e52-f00cce045b56\",\"version\":\"1.0.0\"},\"policySetDefinition\":{\"name\":\"XLabs-LabSub-Control-Initiative\",\"id\":\"/providers/Microsoft.Management/managementGroups/XLabs-Lab-Subscriptions/providers/Microsoft.Authorization/policySetDefinitions/99a48ecc-3334-4f3b-afc1-b472b5e1adcf\",\"version\":\"1.0.0\"}}]'."
    }
  ]
}

```

* En mi caso la policy es
```
XLabs-LabSub-Control-Initiative-253
XLabs_Lab_Sub_VMScaleSets_SKUs
```

* Para loguearme en kubernetes

```
az aks get-credentials --resource-group rg-az500-clase-cuatro --name aks-az500-esteban
```

# Policies

* Vamos a investrigar que policy no nos deja crear el aks
* Llegamos a esta policy

```json
{
  "properties": {
    "displayName": "XLabs_Lab_Sub_VMScaleSets_SKUs",
    "policyType": "Custom",
    "mode": "All",
    "metadata": {
      "category": "Compute",
      "createdBy": "8ca37c5b-42d4-46a4-81de-650deae1bc27",
      "createdOn": "2023-04-07T17:01:58.5920172Z",
      "updatedBy": "8ca37c5b-42d4-46a4-81de-650deae1bc27",
      "updatedOn": "2023-04-07T17:24:26.5502801Z"
    },
    "version": "1.0.0",
    "parameters": {},
    "policyRule": {
      "if": {
        "allOf": [
          {
            "field": "type",
            "equals": "Microsoft.Compute/virtualMachineScaleSets"
          },
          {
            "not": {
              "field": "Microsoft.Compute/VirtualMachineScaleSets/sku.name",
              "in": [
                "Standard_DS1_v2",
                "Standard_DS2_v2",
                "Standard_D1_v2",
                "Standard_D2s_v3",
                "Standard_B1s",
                "Standard_D2_v3",
                "Standard_D2s_v3",
                "Standard_D2_v2"
              ]
            }
          }
        ]
      },
      "then": {
        "effect": "deny"
      }
    },
    "versions": [
      "1.0.0"
    ]
  },
  "id": "/providers/Microsoft.Management/managementGroups/XLabs-Lab-Subscriptions/providers/Microsoft.Authorization/policyDefinitions/4360d1d0-9890-46ee-8e52-f00cce045b56",
  "type": "Microsoft.Authorization/policyDefinitions",
  "name": "4360d1d0-9890-46ee-8e52-f00cce045b56",
  "systemData": {
    "createdBy": "conrad.cahill@xtremelabs.io",
    "createdByType": "User",
    "createdAt": "2023-04-07T17:01:58.5546821Z",
    "lastModifiedBy": "conrad.cahill@xtremelabs.io",
    "lastModifiedByType": "User",
    "lastModifiedAt": "2023-04-07T17:24:26.5094368Z"
  }
}

```
