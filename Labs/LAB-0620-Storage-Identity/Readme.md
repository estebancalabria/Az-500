# LAB 0620 — Storage Security: Aislamiento de acceso con User-Assigned Managed Identities

## Dominio
AZ-500 — Storage Security (0400): autenticación de identidad y control de acceso a Azure Storage.

## Escenario
Dos aplicaciones, `app1` y `app2`, escriben datos en la misma cuenta de storage (`storage1`), cada una en su propio contenedor. El requisito es que **cada app pueda leer únicamente lo que ella misma escribió**, sin usar claves de acceso compartidas (account keys / SAS estáticos) y sin que una app pueda leer los blobs de la otra.

## Objetivo del laboratorio
1. Crear una cuenta de storage con autenticación Azure AD habilitada.
2. Crear dos identidades administradas **user-assigned** (una por app).
3. Asignar cada identidad a su app correspondiente.
4. Otorgar rol RBAC `Storage Blob Data Contributor` con scope acotado al contenedor de cada app.
5. Verificar que app1 no puede leer el contenedor de app2 y viceversa.
6. Discutir por qué la respuesta "oficial" del escenario tipo examen prioriza user-assigned sobre system-assigned.

---

## Prerrequisitos
- Suscripción de Azure con permisos de Owner o User Access Administrator + Contributor.
- Azure CLI autenticado (`az login`).
- Dos App Services (o Container Apps) ya creados: `app1-webapp`, `app2-webapp`. Si no existen, hay un bloque opcional al final para crearlos rápido.

```bash
# Variables
RG="rg-lab-storage-security"
LOCATION="eastus"
STORAGE="stlab0400$RANDOM"
CONTAINER1="app1-data"
CONTAINER2="app2-data"
UAMI1="uami-app1"
UAMI2="uami-app2"
```

---

## Paso 1 — Crear el resource group y la cuenta de storage

```bash
az group create --name $RG --location $LOCATION

az storage account create \
  --name $STORAGE \
  --resource-group $RG \
  --location $LOCATION \
  --sku Standard_LRS \
  --kind StorageV2 \
  --min-tls-version TLS1_2 \
  --allow-blob-public-access false
```

> Nota: por default, las cuentas de storage ya soportan autenticación Azure AD (Entra ID) para el plano de datos de Blob. No hace falta "activarla" explícitamente, pero conviene **deshabilitar el uso de claves compartidas** para forzar Azure AD:

```bash
az storage account update \
  --name $STORAGE \
  --resource-group $RG \
  --allow-shared-key-access false
```

## Paso 2 — Crear los contenedores

Como deshabilitamos shared key access, para crear contenedores hay que usar `--auth-mode login` (usando tu identidad de usuario logueada, que necesita rol de datos):

```bash
# Asignate temporalmente el rol para poder crear los containers
MYID=$(az ad signed-in-user show --query id -o tsv)
SCOPE=$(az storage account show --name $STORAGE --resource-group $RG --query id -o tsv)

az role assignment create \
  --assignee $MYID \
  --role "Storage Blob Data Contributor" \
  --scope $SCOPE

az storage container create --name $CONTAINER1 --account-name $STORAGE --auth-mode login
az storage container create --name $CONTAINER2 --account-name $STORAGE --auth-mode login
```

## Paso 3 — Crear las user-assigned managed identities

```bash
az identity create --name $UAMI1 --resource-group $RG --location $LOCATION
az identity create --name $UAMI2 --resource-group $RG --location $LOCATION

UAMI1_ID=$(az identity show --name $UAMI1 --resource-group $RG --query id -o tsv)
UAMI1_PRINCIPAL=$(az identity show --name $UAMI1 --resource-group $RG --query principalId -o tsv)

UAMI2_ID=$(az identity show --name $UAMI2 --resource-group $RG --query id -o tsv)
UAMI2_PRINCIPAL=$(az identity show --name $UAMI2 --resource-group $RG --query principalId -o tsv)
```

## Paso 4 — Asignar cada identidad a su app

```bash
az webapp identity assign \
  --name app1-webapp \
  --resource-group $RG \
  --identities $UAMI1_ID

az webapp identity assign \
  --name app2-webapp \
  --resource-group $RG \
  --identities $UAMI2_ID
```

## Paso 5 — RBAC con scope acotado al contenedor de cada app

El scope de un role assignment puede bajar hasta nivel de **contenedor** (no solo cuenta de storage), usando el resource ID extendido con `/blobServices/default/containers/<container>`.

```bash
CONTAINER1_SCOPE="$SCOPE/blobServices/default/containers/$CONTAINER1"
CONTAINER2_SCOPE="$SCOPE/blobServices/default/containers/$CONTAINER2"

# app1 solo puede leer/escribir en su contenedor
az role assignment create \
  --assignee $UAMI1_PRINCIPAL \
  --role "Storage Blob Data Contributor" \
  --scope $CONTAINER1_SCOPE

# app2 solo puede leer/escribir en el suyo
az role assignment create \
  --assignee $UAMI2_PRINCIPAL \
  --role "Storage Blob Data Contributor" \
  --scope $CONTAINER2_SCOPE
```

## Paso 6 — Verificar el aislamiento con una app .NET

Creamos una consola .NET mínima que usa `DefaultAzureCredential` apuntando al `client-id` de la user-assigned identity, y prueba leer su propio contenedor y el del otro.

```bash
dotnet new console -n IdentityCheck
cd IdentityCheck
dotnet add package Azure.Identity
dotnet add package Azure.Storage.Blobs
```

`Program.cs`:

```csharp
using Azure.Identity;
using Azure.Storage.Blobs;

Console.Write("Client ID de la UAMI (uami-app1 o uami-app2): ");
string uamiClientId = Console.ReadLine()!;

Console.Write("Nombre de la storage account: ");
string storageAccount = Console.ReadLine()!;

Console.Write("Contenedor propio: ");
string ownContainer = Console.ReadLine()!;

Console.Write("Contenedor ajeno: ");
string otherContainer = Console.ReadLine()!;

var credential = new DefaultAzureCredential(
    new DefaultAzureCredentialOptions { ManagedIdentityClientId = uamiClientId });

var serviceUri = new Uri($"https://{storageAccount}.blob.core.windows.net");
var blobServiceClient = new BlobServiceClient(serviceUri, credential);

await TryList("Contenedor propio", blobServiceClient, ownContainer);
await TryList("Contenedor ajeno", blobServiceClient, otherContainer);

static async Task TryList(string label, BlobServiceClient client, string containerName)
{
    var container = client.GetBlobContainerClient(containerName);
    try
    {
        await foreach (var blob in container.GetBlobsAsync())
        {
            Console.WriteLine($"[{label}] OK - blob encontrado: {blob.Name}");
        }
        Console.WriteLine($"[{label}] Acceso permitido a '{containerName}'");
    }
    catch (Azure.RequestFailedException ex) when (ex.Status == 403)
    {
        Console.WriteLine($"[{label}] Acceso DENEGADO (403) a '{containerName}' — esperado si es el contenedor ajeno");
    }
}
```

Correr localmente pasando el `client-id` de cada UAMI (para probar sin desplegar, usando tu identidad de usuario logueada no sirve para este test — hay que correrlo *desde* un recurso con la managed identity asignada, por ejemplo la VM/App Service, o simular con `az login --identity --username <clientId>` en Cloud Shell si el recurso lo permite):

```bash
UAMI1_CLIENT_ID=$(az identity show --name $UAMI1 --resource-group $RG --query clientId -o tsv)
UAMI2_CLIENT_ID=$(az identity show --name $UAMI2 --resource-group $RG --query clientId -o tsv)

echo "UAMI1: $UAMI1_CLIENT_ID"
echo "UAMI2: $UAMI2_CLIENT_ID"
echo "Storage: $STORAGE"
echo "Container1: $CONTAINER1"
echo "Container2: $CONTAINER2"

dotnet run
```

Al ejecutar, el programa va pidiendo cada dato por consola. Corrida desde app1: cargar `UAMI1_CLIENT_ID`, `STORAGE`, `CONTAINER1` como propio y `CONTAINER2` como ajeno (esperado: OK / 403). Corrida desde app2: invertir `CONTAINER1` y `CONTAINER2` (esperado: OK / 403 en sentido opuesto).

## Paso 7 — Limpieza

```bash
az group delete --name $RG --yes --no-wait
```
