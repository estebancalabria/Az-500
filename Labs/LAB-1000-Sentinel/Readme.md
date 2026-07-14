# Laboratorio: Microsoft Sentinel con datos de prueba

**Nivel:** Principiante absoluto
**Duración estimada:** 60–90 min
**Costo:** Bajo (Log Analytics free tier + ingesta mínima). Recordá borrar los recursos al final.

## Qué vamos a hacer

1. Crear un workspace de Log Analytics y habilitar Sentinel sobre él.
2. Descargar un dataset público de eventos de seguridad simulados (ataques reales reproducidos en laboratorio, no producción).
3. Ingerir esos datos al workspace usando la API de Log Analytics (Data Collector API).
4. Consultarlos con KQL.
5. Crear una regla de análisis que genere un incidente a partir de esos datos.
6. Investigar el incidente en Sentinel.

---

## Paso 1 — Requisitos previos

- Una suscripción de Azure (sirve una de prueba/pay-as-you-go, no Free Trial vencida).
- Permisos de Contributor sobre la suscripción o un resource group.
- Azure CLI o PowerShell instalado, o usar Cloud Shell desde el portal (más simple para el que arranca de cero).
- Python 3 instalado si vas a usar el script de ingesta en Python (alternativa: PowerShell, doy las dos opciones).

---

## Paso 2 — Crear el Resource Group

Desde el portal (portal.azure.com):

1. Buscar "Resource groups" → **Create**.
2. Nombre: `rg-sentinel-lab`
3. Región: la más cercana (ej. East US, Brazil South).
4. Create.

O por CLI (Cloud Shell):

```bash
az group create --name rg-sentinel-lab --location eastus
```

---

## Paso 3 — Crear el Log Analytics Workspace

1. En el portal, buscar "Log Analytics workspaces" → **Create**.
2. Resource group: `rg-sentinel-lab`
3. Nombre: `law-sentinel-lab` (tiene que ser único)
4. Región: la misma que el resource group.
5. Create y esperar el deploy (1-2 min).

Por CLI:

```bash
az monitor log-analytics workspace create \
  --resource-group rg-sentinel-lab \
  --workspace-name law-sentinel-lab \
  --location eastus
```

---

## Paso 4 — Habilitar Microsoft Sentinel

1. Buscar "Microsoft Sentinel" en el portal.
2. **Create** → seleccionar el workspace `law-sentinel-lab` → **Add**.
3. Esperar unos segundos hasta que aparezca el dashboard de Sentinel.

Listo: ya tenés Sentinel corriendo, pero vacío. Ahora hay que meterle datos.

---

## Paso 5 — Conseguir el dataset de prueba

Vamos a usar el repositorio público **OTRF Security-Datasets** (proyecto conocido de la comunidad de threat hunting, usado en cursos y certificaciones, con logs reales de ataques simulados en laboratorio, en formato JSON).

1. Ir a `https://github.com/OTRF/Security-Datasets`
2. Entrar a la carpeta `datasets/atomic/windows/` (hay subcarpetas por técnica de MITRE ATT&CK, ej. `persistence`, `credential_access`, `execution`).
3. Elegir cualquier dataset chico para empezar. Por ejemplo, uno de `execution` (ejecución de PowerShell malicioso simulado).
4. Descargar el archivo `.zip` que trae los eventos en formato `.json` (son eventos de Windows Event Log exportados).

En Cloud Shell:

```bash
mkdir ~/sentinel-lab && cd ~/sentinel-lab
curl -L -o dataset.zip "<URL_DEL_ZIP_QUE_ELEGISTE>"
unzip dataset.zip
```

Vas a tener uno o varios archivos `.json`, cada línea es un evento.

---

## Paso 6 — Obtener las credenciales de ingesta

Necesitás el **Workspace ID** y una **Shared Key** del Log Analytics workspace:

1. En el portal, ir al workspace `law-sentinel-lab`.
2. Menú izquierdo → **Agents** (o "Agents management" según la versión) → ahí están:
   - Workspace ID
   - Primary Key

Guardalos, los vas a usar en el script.

---

## Paso 7 — Ingerir los datos con la Data Collector API

Esta API permite mandar JSON directo a una tabla custom del workspace. Vamos a crear una tabla llamada `SecurityLab_CL`.

### Opción A: Script en Python

```python
import json
import requests
import datetime
import hashlib
import hmac
import base64

workspace_id = "TU_WORKSPACE_ID"
shared_key = "TU_PRIMARY_KEY"
log_type = "SecurityLab"  # va a aparecer como SecurityLab_CL

def build_signature(date, content_length):
    x_headers = f"x-ms-date:{date}"
    string_to_hash = f"POST\n{content_length}\napplication/json\n{x_headers}\n/api/logs"
    bytes_to_hash = string_to_hash.encode("utf-8")
    decoded_key = base64.b64decode(shared_key)
    encoded_hash = base64.b64encode(
        hmac.new(decoded_key, bytes_to_hash, digestmod=hashlib.sha256).digest()
    ).decode()
    return f"SharedKey {workspace_id}:{encoded_hash}"

def post_data(body):
    date = datetime.datetime.utcnow().strftime("%a, %d %b %Y %H:%M:%S GMT")
    content_length = len(body)
    signature = build_signature(date, content_length)
    uri = f"https://{workspace_id}.ods.opinsights.azure.com/api/logs?api-version=2016-04-01"
    headers = {
        "content-type": "application/json",
        "Authorization": signature,
        "Log-Type": log_type,
        "x-ms-date": date,
    }
    response = requests.post(uri, data=body, headers=headers)
    print(response.status_code, response.text)

# Leer el archivo del dataset descargado (ajustar nombre de archivo)
with open("evento_ejemplo.json", "r") as f:
    eventos = [json.loads(line) for line in f if line.strip()]

# Mandar en lotes de 100
lote = 100
for i in range(0, len(eventos), lote):
    chunk = eventos[i:i+lote]
    post_data(json.dumps(chunk))
```

Instalar dependencia si falta: `pip install requests`

### Opción B: PowerShell (si preferís no usar Python)

```powershell
$workspaceId = "TU_WORKSPACE_ID"
$sharedKey = "TU_PRIMARY_KEY"
$logType = "SecurityLab"

$json = Get-Content ".\evento_ejemplo.json" | ConvertFrom-Json

function Build-Signature ($date, $contentLength) {
    $xHeaders = "x-ms-date:" + $date
    $stringToHash = "POST`n$contentLength`napplication/json`n$xHeaders`n/api/logs"
    $bytesToHash = [Text.Encoding]::UTF8.GetBytes($stringToHash)
    $keyBytes = [Convert]::FromBase64String($sharedKey)
    $hmacsha256 = New-Object System.Security.Cryptography.HMACSHA256
    $hmacsha256.Key = $keyBytes
    $calculatedHash = $hmacsha256.ComputeHash($bytesToHash)
    $encodedHash = [Convert]::ToBase64String($calculatedHash)
    return "SharedKey ${workspaceId}:${encodedHash}"
}

$body = $json | ConvertTo-Json -Depth 10
$date = [DateTime]::UtcNow.ToString("r")
$signature = Build-Signature $date ([System.Text.Encoding]::UTF8.GetBytes($body).Length)
$uri = "https://$workspaceId.ods.opinsights.azure.com/api/logs?api-version=2016-04-01"

$headers = @{
    "Authorization" = $signature
    "Log-Type" = $logType
    "x-ms-date" = $date
}

Invoke-RestMethod -Uri $uri -Method Post -Body $body -Headers $headers -ContentType "application/json"
```

---

## Paso 8 — Verificar que llegaron los datos

1. En Sentinel (o en el Log Analytics workspace), ir a **Logs**.
2. Ejecutar:

```kql
SecurityLab_CL
| take 20
```

Si ves filas, la ingesta funcionó. Puede tardar 2-5 minutos en aparecer la primera vez.

---

## Paso 9 — Crear una regla de análisis (Analytics Rule)

1. En Sentinel → **Analytics** → **Create** → **Scheduled query rule**.
2. Nombre: `Deteccion basica - Lab`
3. Query (ejemplo simple, ajustar según los campos reales de tu dataset — inspeccioná las columnas con `SecurityLab_CL | take 1`):

```kql
SecurityLab_CL
| where EventID_d == 4688  // ejemplo: creación de proceso
```

4. Configurar: se ejecuta cada 5 minutos, mira los últimos 5 minutos.
5. En "Incident settings", dejar que agrupe alertas en un incidente.
6. Review + Create.

---

## Paso 10 — Investigar el incidente

1. Ir a Sentinel → **Incidents**.
2. Cuando la regla dispare (podés forzarlo corriendo el script de ingesta de nuevo), vas a ver un incidente nuevo.
3. Abrirlo → **Investigate** para ver el grafo de entidades relacionadas.

---

## Paso 11 — Limpieza (importante para no seguir pagando)

```bash
az group delete --name rg-sentinel-lab --yes --no-wait
```

Esto borra el workspace, Sentinel y todo lo asociado.

---

