# Clase Nueve - 14 de Julio 2026

# Repaso

* Seguridad en Storage account
  * Redundancia de datos (LRS, GRS)
  * Access Tiers (Hot Cool, Cold, Archive)
  * Seguridad de Acceso
    * Publico
    * Privado
      * SAS : Shared Access Key
      * Firewall del Storage Account
  * Seguridad de Datos
    * Encriptacion
    * Soft Delete
    * Versioning
  * Tipos de almacenamiento
    * Blob
    * File Share
  * Defender
    * Defender For Storage
* Alarmas
  * Action Group (La notificacion que usamos por mail_
  * Creamos una alarma cuando se borraba un NSG
* Metricas
  * Como visualizar metricas
  * Como definir alarmas sobre esas metricas

 # Microsoft Sentinel

 * SIEM (Security Information and Event Management)
   * Recopilacion de informacion de seguridad y eventos relacionados
   * No se limita a la recoleccion de logs de seguridad solo de Azure sino que se extiende a otros medios
* SOAR (Security Orchestration, Automation and Response)
    * Permite hacer consultas, correlacionar eventos, generar alertas y automatizar respuestas

> [!NOTE]
> Para cargar datos vamos a explorar este repo : https://github.com/OTRF/Security-Datasets
> Bajamos este zip : https://github.com/OTRF/Security-Datasets/blob/master/datasets/atomic/windows/defense_evasion/host/psh_mshta_html_application_execution.zip
 
* Crear el Resource Group
  * rg-az500-clase-nueve
* Crer Log Analytics Workspace
  * log-az500-clase-nueve
* Abrir Google Colab
  * https://colab.google/
* Crear un google colab nuevo, en mi caso
  * https://colab.research.google.com/drive/1tnkD1JF77B0J4A_lmZnVwEMAxAPM7Hsm?usp=sharing
* Probar una celda de codigo para ver que funcione

```python
 print("Hola Mundo")
```

* Subir el log al log analytics Workspace (lo genere con IA)

```python
import os
import json
import base64
import hashlib
import hmac
import requests
import zipfile
from email.utils import formatdate

# -----------------------------
# Configuración
# -----------------------------

workspace_id = input("Workspace ID: ")
workspace_key = input("Primary Key: ")

LOG_TYPE = "SecurityLab"

ZIP_URL = "https://github.com/OTRF/Security-Datasets/raw/master/datasets/atomic/windows/defense_evasion/host/psh_mshta_html_application_execution.zip"

TEMP_DIR = "dataset"
ZIP_FILE = os.path.join(TEMP_DIR, "dataset.zip")

os.makedirs(TEMP_DIR, exist_ok=True)

# -----------------------------
# Descargar ZIP
# -----------------------------

print("Descargando dataset...")

response = requests.get(ZIP_URL)
response.raise_for_status()

with open(ZIP_FILE, "wb") as f:
    f.write(response.content)

print("Descomprimiendo...")

with zipfile.ZipFile(ZIP_FILE, "r") as zip_ref:
    zip_ref.extractall(TEMP_DIR)

# -----------------------------
# Firma Log Analytics
# -----------------------------

def build_signature(customer_id, shared_key, date, content_length,
                    method="POST",
                    content_type="application/json",
                    resource="/api/logs"):

    x_headers = "x-ms-date:" + date

    string_to_hash = (
        f"{method}\n"
        f"{content_length}\n"
        f"{content_type}\n"
        f"{x_headers}\n"
        f"{resource}"
    )

    bytes_to_hash = string_to_hash.encode("utf-8")
    decoded_key = base64.b64decode(shared_key)

    encoded_hash = base64.b64encode(
        hmac.new(decoded_key, bytes_to_hash, hashlib.sha256).digest()
    ).decode()

    return f"SharedKey {customer_id}:{encoded_hash}"

# -----------------------------
# Enviar evento
# -----------------------------

def send_log(data):

    body = json.dumps(data)

    rfc1123date = formatdate(timeval=None, localtime=False, usegmt=True)

    signature = build_signature(
        workspace_id,
        workspace_key,
        rfc1123date,
        len(body)
    )

    uri = (
        f"https://{workspace_id}.ods.opinsights.azure.com"
        "/api/logs?api-version=2016-04-01"
    )

    headers = {
        "Content-Type": "application/json",
        "Authorization": signature,
        "Log-Type": LOG_TYPE,
        "x-ms-date": rfc1123date
    }

    r = requests.post(uri, headers=headers, data=body)

    if r.status_code not in (200, 202):
        print(r.status_code)
        print(r.text)
    else:
        print("Evento enviado")

# -----------------------------
# Buscar JSON y JSONL
# -----------------------------

for root, _, files in os.walk(TEMP_DIR):

    for file in files:

        if not file.endswith(".json"):
            continue

        path = os.path.join(root, file)

        print(f"Procesando {path}")

        with open(path, encoding="utf8") as f:
            text = f.read().strip()

        try:
            # Intentar leer como JSON normal
            content = json.loads(text)

            if isinstance(content, list):
                for item in content:
                    send_log(item)
            else:
                send_log(content)

        except json.JSONDecodeError:
            # Si falla, asumir JSON Lines (NDJSON)
            for line in text.splitlines():
                line = line.strip()

                if not line:
                    continue

                try:
                    send_log(json.loads(line))
                except Exception as e:
                    print(f"Error procesando línea: {e}")

print("Carga finalizada.")
```

* Copiar el Workspace ID del overview

* Abrir el CLI en una ventana nueva y ejecutar este comando

```
az monitor log-analytics workspace get-shared-keys --resource-group rg-az500-clase-nueve --workspace-name log-az500-clase-nueve --query primarySharedKey -o tsv
```
