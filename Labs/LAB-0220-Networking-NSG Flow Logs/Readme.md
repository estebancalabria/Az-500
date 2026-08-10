# Lab: NSG Flow Logs

**Objetivo:** habilitar flow logs en una NSG y verificar que el tráfico se registre en un storage account.

**Prerequisitos:** una suscripción con Network Watcher habilitado en la región (está on por default en la mayoría).

---

## Paso 1 – Crear el resource group y variables

```bash
az login

RG="rg-nsgflow-lab"
LOC="eastus"
VNET="vnet-lab"
SUBNET="subnet-lab"
NSG="nsg-lab"
STORAGE="stnsgflow$RANDOM"

az group create -n $RG -l $LOC
```

## Paso 2 – Crear VNet, subnet y NSG

```bash
az network vnet create -g $RG -n $VNET --address-prefix 10.0.0.0/16 \
  --subnet-name $SUBNET --subnet-prefix 10.0.1.0/24

az network nsg create -g $RG -n $NSG

az network vnet subnet update -g $RG --vnet-name $VNET -n $SUBNET \
  --network-security-group $NSG
```

## Paso 3 – Agregar una regla que permita RDP/SSH (para generar tráfico real)

```bash
az network nsg rule create -g $RG --nsg-name $NSG -n Allow-SSH \
  --priority 1000 --access Allow --direction Inbound --protocol Tcp \
  --destination-port-ranges 22
```

## Paso 4 – Crear una VM en esa subnet (Linux, más rápida de levantar)

```bash
az vm create -g $RG -n vm-lab \
  --image Ubuntu2204 \
  --vnet-name $VNET --subnet $SUBNET \
  --admin-username azureuser --generate-ssh-keys \
  --nsg "" \
  --public-ip-sku Standard
```

> Nota: `--nsg ""` evita que Azure cree una NSG extra a nivel de NIC; así el tráfico pasa únicamente por la NSG de subnet que ya tenés.

## Paso 5 – Crear el storage account destino

```bash
az storage account create -g $RG -n $STORAGE -l $LOC --sku Standard_LRS
```

## Paso 6 – Habilitar NSG Flow Logs (versión 2, con metadata de performance)

Necesitás el resource ID de la NSG y del storage account:

```bash
NSG_ID=$(az network nsg show -g $RG -n $NSG --query id -o tsv)
STORAGE_ID=$(az storage account show -g $RG -n $STORAGE --query id -o tsv)

az network watcher flow-log create \
  --location $LOC \
  --resource-group $RG \
  --name fl-nsg-lab \
  --nsg $NSG_ID \
  --storage-account $STORAGE_ID \
  --enabled true \
  --format JSON \
  --log-version 2 \
  --retention 7
```

## Paso 7 – Generar tráfico

```bash
IP=$(az vm show -d -g $RG -n vm-lab --query publicIps -o tsv)
ssh azureuser@$IP "echo test"
```

Repetí un par de conexiones (SSH, o un `curl` desde otra VM) para asegurarte que haya algo que loguear.

## Paso 8 – Verificar que los logs lleguen al storage

Los flow logs tardan unos minutos en aparecer. Buscalos en el container:

```
insights-logs-networksecuritygroupflowevent
```

```bash
az storage blob list \
  --account-name $STORAGE \
  --container-name insights-logs-networksecuritygroupflowevent \
  --auth-mode login \
  -o table
```

Bajá uno y mirá el contenido (es JSON):

```bash
az storage blob download \
  --account-name $STORAGE \
  --container-name insights-logs-networksecuritygroupflowevent \
  --name "<blob_path>" \
  --file flowlog.json \
  --auth-mode login

cat flowlog.json | jq .
```

Vas a ver algo tipo:

```json
{
  "properties": {
    "Version": 2,
    "flows": [
      {
        "rule": "Allow-SSH",
        "flows": [
          {
            "mac": "...",
            "flowTuples": [
              "1691683200,10.0.0.5,10.0.1.4,50321,22,T,I,A,B,..."
            ]
          }
        ]
      }
    ]
  }
}
```

Cada `flowTuple` es: timestamp, IP origen, IP destino, puerto origen, puerto destino, protocolo, dirección (I/O), acción (A/D), estado del flow, y (en v2) bytes/paquetes.

## Paso 9 (opcional) – Traffic Analytics

Si querés visualizar en vez de leer JSON crudo, agregás un Log Analytics workspace y activás Traffic Analytics sobre el mismo flow log:

```bash
az monitor log-analytics workspace create -g $RG -n law-nsgflow-lab

WORKSPACE_ID=$(az monitor log-analytics workspace show -g $RG -n law-nsgflow-lab --query id -o tsv)

az network watcher flow-log update \
  --name fl-nsg-lab \
  --resource-group $RG \
  --traffic-analytics true \
  --workspace $WORKSPACE_ID \
  --interval 10
```

Esto es justo el punto que te va a servir para diferenciar en el examen: **flow logs = raw data en storage**, **Traffic Analytics = análisis/visualización en Log Analytics, requiere flow logs como fuente**. No podés tener Traffic Analytics sin flow logs habilitado.

## Paso 10 – Cleanup

```bash
az group delete -n $RG --yes --no-wait
```

---

**Punto clave para el examen:** flow logs se configuran a nivel de **NSG**, no de VM ni de subnet directamente (aunque afecten todo lo que pase por esa NSG). Y solo pueden ir a storage account — no a Log Analytics directamente, salvo que uses Traffic Analytics como capa intermedia.
