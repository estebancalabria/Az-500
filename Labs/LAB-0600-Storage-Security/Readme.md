# Lab: Seguridad en Storage Account

**Objetivo:** Crear un Storage Account y recorrer sus principales controles de seguridad — redundancia, access tiers, acceso público/privado, soft delete, versioning, Defender for Storage, encriptación, SAS en blobs, y acceso restringido a File Shares por VNet. Todo desde el portal, paso a paso.

**Duración estimada:** 45-60 min

**Prerrequisitos:** una suscripción de Azure activa con permisos para crear resource groups, storage accounts y VMs.

---

## Paso 1 — Crear el Resource Group

1. En la barra de búsqueda de arriba, escribir **Resource groups**
2. Clic en **+ Create**
3. Completar:
   - **Subscription**: la tuya
   - **Resource group name**: `rg-az500-clase-ocho`
   - **Region**: la que uses normalmente (ej. East US)
4. Clic en **Review + create** → **Create**
5. Esperar el aviso de "Deployment succeeded"

---

## Paso 2 — Crear el Storage Account

1. En la barra de búsqueda, escribir **Storage accounts**
2. Clic en **+ Create**
3. Pestaña **Basics**, completar:
   - **Resource group**: `rg-az500-clase-ocho`
   - **Storage account name**: `csaz500c8esteban`
   - **Region**: la misma que uses normalmente
   - **Performance**: Standard
   - **Redundancy**: dejar el que viene por defecto (**LRS** — Locally Redundant Storage)
4. Clic en **Review** → **Create**
5. Esperar el aviso de "Deployment succeeded" → clic en **Go to resource**

---

## Parte 1 — Redundancia de datos

**Concepto:** por defecto todo Storage Account replica los datos. Dos opciones principales:
- **LRS (Locally Redundant Storage)**: copia los datos en otro rack dentro del mismo datacenter. Es la opción por defecto y la más barata.
- **GRS (Geo-Redundant Storage)**: copia los datos en otra región geográfica, para tolerar la caída de todo un datacenter.

### Paso 3 — Ver la configuración de redundancia

1. Dentro del Storage Account `csaz500c8esteban`, menú izquierdo → **Data management** → **Redundancy**
2. Ver que está seleccionado **Locally-redundant storage (LRS)**
3. (Opcional, no lo cambiamos) — mostrar en el dropdown las otras opciones disponibles, entre ellas **Geo-redundant storage (GRS)**, para que quede visible la diferencia de costo/protección

---

## Parte 2 — Costos de acceso (Access Tiers)

**Concepto:** el Access Tier define el balance entre costo de almacenamiento y costo de acceso:

| Tier | Almacenamiento | Acceso | Uso típico |
|---|---|---|---|
| **Hot** | Más caro | Barato y rápido | Datos de uso frecuente |
| **Cool** | Intermedio | Intermedio | Datos de acceso poco frecuente |
| **Cold** | Barato | Caro | Backups |
| **Archive** | Baratísimo | Carísimo (espera ~2 horas para acceder) | Retención a largo plazo |

### Paso 4 — Ver el Access Tier del Storage Account

1. Menú izquierdo → **Configuration**
2. Ver el campo **Default access tier**, que por defecto viene en **Hot**
3. (Opcional) mostrar que este mismo tier se puede sobreescribir a nivel de cada blob individual más adelante

---

## Parte 3 — Acceso público vs privado

**Concepto:** el acceso a un Storage Account puede ser:
- **Público**: accesible desde internet
- **Privado**: accesible solo desde dentro de una VNet de Azure

Esto se va a configurar más adelante en la parte de File Shares (Paso 12), donde vamos a restringir el acceso público real.

---

## Parte 4 — Seguridad ante ataques (Soft Delete y Versioning)

**Concepto:**
- **Soft Delete**: define durante cuántos días un elemento borrado (blob, file share, queue, table) queda recuperable antes de eliminarse definitivamente. Protege contra un atacante que intente borrar datos para dañar a la empresa.
- **Versioning**: guarda un historial de versiones de cada blob, permitiendo auditar y revertir cambios.

### Paso 5 — Habilitar Soft Delete

1. Menú izquierdo → **Data management** → **Data protection**
2. Tildar **Enable soft delete for blobs**
3. Definir la cantidad de días de retención (ej. `7`)
4. Repetir si querés para **containers** y **file shares** con la misma lógica
5. Clic en **Save**

### Paso 6 — Habilitar Versioning

1. En la misma pantalla **Data protection**
2. Tildar **Enable versioning for blobs**
3. Clic en **Save**

---

## Parte 5 — Seguridad automática (Microsoft Defender for Storage)

**Concepto:** Microsoft Defender for Storage detecta automáticamente comportamientos sospechosos sobre el Storage Account, como:
- Acceso desde una IP sospechosa
- Acceso desde una app sospechosa
- Acceso desde una ubicación inusual
- Cantidad de datos extraídos inusual

> [!NOTE]
> Lista completa de alertas de Defender for Storage: https://learn.microsoft.com/en-us/azure/defender-for-cloud/alerts-azure-storage

### Paso 7 — Habilitar Microsoft Defender for Storage

1. Menú izquierdo → **Security + networking** → **Microsoft Defender for Cloud**
2. Ver el estado de **Microsoft Defender for Storage**
3. Si figura como **Disabled**, clic en **Enable on this storage account**
4. Confirmar

---

## Parte 6 — Encriptación de datos

**Concepto:** toda la información de un Storage Account se guarda encriptada. Hay dos opciones de clave:
- **Clave de Microsoft**: encriptación transparente, gestionada por Azure (opción por defecto)
- **Clave propia**: la clave se guarda en un Azure Key Vault, dando control total al cliente

### Paso 8 — Ver la configuración de encriptación

1. Menú izquierdo → **Security + networking** → **Encryption**
2. Ver que está seleccionado **Microsoft-managed keys (MMK)**
3. (Opcional, no lo cambiamos en este lab) mostrar la opción **Customer-managed keys (CMK)**, que requeriría tener un Key Vault ya creado

---

## Parte 7 — Seguridad de acceso a Blobs

**Concepto:**
- Si el contenedor es **público**, cualquiera puede acceder a sus blobs por URL directa.
- Si el contenedor es **privado**, sus blobs no son accesibles por URL directa.
- Para dar acceso puntual a un recurso privado se usa un **SAS (Shared Access Signature)**: una URL especial y temporal, que puede ser:
  - **Independiente**: definiendo los permisos individuales del archivo en el momento
  - **Asociada a una Access Policy**: donde los permisos ya están definidos de antemano

### Paso 9 — Crear un contenedor privado y subir un blob

1. Menú izquierdo → **Data storage** → **Containers**
2. Clic en **+ Container**
3. Nombre: `contenedor-privado`
4. **Public access level**: **Private (no anonymous access)**
5. Clic en **Create**
6. Entrar al contenedor → **Upload** → subir cualquier archivo de prueba (ej. una imagen o un .txt)

### Paso 10 — Intentar acceder al blob por URL (debería fallar)

1. Clic en el blob subido
2. Copiar el **URL** que aparece en el panel de detalle
3. Pegarlo en una pestaña nueva del navegador → debería devolver error de acceso denegado (porque el contenedor es privado)

### Paso 11 — Generar un SAS para acceder al blob

1. Con el blob seleccionado, clic en **Generate SAS**
2. Definir:
   - **Permissions**: Read (como mínimo)
   - **Start / Expiry time**: dejar el rango por defecto o acortarlo (ej. 1 hora)
3. Clic en **Generate SAS token and URL**
4. Copiar la **Blob SAS URL** generada y pegarla en una pestaña nueva → esta vez sí debería poder verse/descargarse el archivo

---

## Parte 8 — Seguridad en File Shares

**Concepto:** un File Share se puede conectar públicamente usando:
- **Autenticación con Entra ID**
- **Access Key del Storage Account** (clave compartida) — ojo, si esta clave se filtra, cualquiera puede conectarse

### Paso 12 — Crear un File Share

1. Menú izquierdo → **Data storage** → **File shares**
2. Clic en **+ File share**
3. Nombre: `fileshare-demo`
4. Tier: dejar el que venga por defecto
5. Clic en **Create**

### Paso 13 — Ver la Access Key del Storage Account

1. Menú izquierdo → **Security + networking** → **Access keys**
2. Clic en **Show** en la key1 (o key2)
3. Esta clave es la que se usa para autenticarse por SMB desde afuera — remarcar en clase que si se filtra, queda comprometido todo el Storage Account

---

## Parte 9 — Restringir el acceso al File Share solo desde una VM

**Objetivo:** conectarse al File Share primero desde la PC local (acceso público), y después restringir el acceso para que solo una VM dentro de la VNet de Azure pueda conectarse.

### Paso 14 — Crear la VM

1. Buscar **Virtual machines** en la barra de arriba
2. Clic en **+ Create** → **Azure virtual machine**
3. Completar:
   - **Resource group**: `rg-az500-clase-ocho`
   - **Virtual machine name**: `vm-az500-clase-ocho`
   - **Region**: la misma que usás siempre
   - **Image**: Windows Server (para conectar por RDP)
   - **Size**: la más chica/barata que ofrezca
   - **Authentication type**: Password
4. En la pestaña **Networking**, dejar todo por defecto (se va a crear una VNet nueva)
5. Clic en **Review + create** → **Create**
6. Esperar el deployment (3-5 min)

### Paso 15 — Conectarse a la VM por RDP

1. Ir al recurso `vm-az500-clase-ocho`
2. Clic en **Connect** → **RDP**
3. Descargar el archivo .rdp y abrirlo
4. Ingresar usuario y contraseña definidos en el Paso 14

### Paso 16 — Conectar el File Share desde la VM

1. Volver al Storage Account `csaz500c8esteban` → **File shares** → `fileshare-demo`
2. Clic en **Connect**
3. Copiar el script de PowerShell que ofrece el portal (el mismo que se usaría para montarlo desde una PC local)
4. Pegar y ejecutar ese script en la consola de PowerShell **dentro de la VM**
5. Verificar que el file share queda montado como una unidad de red

### Paso 17 — Restringir el acceso público a la VNet de la VM

1. Volver al Storage Account → **Security + networking** → **Networking**
2. Vas a estar en la pestaña **Public access**
3. Al lado de **Public network access**, clic en el botón **Manage**
4. Se abre una pantalla nueva **Public network access**:
   - **Public network access**: dejar en **Enable**
   - **Public network access scope**: seleccionar **Enable from selected networks**
5. Más abajo, en la sección **Virtual Networks**, clic en **Add a virtual network**
6. Seleccionar la VNet y la subnet de `vm-az500-clase-ocho`
7. Clic en **Add**
8. Clic en **Save**

### Paso 18 — Verificar el acceso restringido

1. Desde la PC local (fuera de la VNet), intentar acceder al Storage Account o al File Share → debería fallar
2. Desde la VM `vm-az500-clase-ocho` (dentro de la VNet), volver a probar el acceso al File Share → debería seguir funcionando, ahora que esa VNet quedó autorizada

---

## Nota para la clase

Este lab conecta varios controles de seguridad de Storage Account de punta a punta: redundancia, costos, soft delete/versioning, Defender, encriptación, SAS para blobs, y por último el caso más completo — restringir un File Share para que solo sea accesible desde una VNet específica de Azure. Es un buen ejemplo para mostrar que "seguridad" en Azure no es una sola configuración, sino varias capas independientes que se combinan.

---

## Limpieza de recursos

Para no dejar recursos huérfanos facturando, borrar el resource group completo al terminar:

1. Buscar **Resource groups**
2. Clic en `rg-az500-clase-ocho`
3. Clic en **Delete resource group**
4. Escribir el nombre para confirmar → **Delete**
