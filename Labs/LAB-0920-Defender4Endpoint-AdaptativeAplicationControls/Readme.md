# 🧪 Laboratorio: Bloqueo de aplicaciones con Adaptive Application Controls (Azure Defender for Cloud)

## 🎯 Objetivo
Crear una VM en Azure, habilitar Defender for Cloud, configurar Adaptive Application Controls y demostrar cómo bloquea una aplicación no permitida (**Notepad++**).

---

# 📌 **1. Crear la máquina virtual en Azure**

## 1.1 Ingresar al portal
```
https://portal.azure.com
```

## 1.2 Crear la VM
1. Ir a **Create a resource**  
2. Seleccionar **Virtual Machine**
3. Completar:
   - **Resource Group:** RG-LAB-AAC  
   - **VM name:** VM-LAB-AAC  
   - **Region:** South Central US (o la que prefieras)  
   - **Image:** Windows Server 2022 Datacenter  
   - **Size:** Standard B2s  
   - **Username:** labadmin  
   - **Password:** Lab123456!  
4. En **Inbound ports**, habilitar **RDP (3389)**  
5. Crear la VM

---

# 📌 **2. Habilitar Defender for Cloud (Defender for Servers)**

## 2.1 Activar plan
1. Ir a **Microsoft Defender for Cloud**  
2. Abrir **Environment Settings**  
3. Seleccionar la suscripción  
4. Activar:
   - **Defender for Servers Plan 2** (necesario para Adaptive Application Controls)

---

# 📌 **3. Verificar que la VM está siendo monitoreada**

1. Ir a **Microsoft Defender for Cloud → Inventory**  
2. Buscar **VM-LAB-AAC**  
3. Confirmar que aparece con el agente instalado automáticamente (MDE/MMA según plan)

---

# 📌 **4. Configurar Adaptive Application Controls**

## 4.1 Abrir la funcionalidad
1. Ir a **Microsoft Defender for Cloud**  
2. Seleccionar **Workload protections**  
3. Abrir **Adaptive application controls**

<img width="470" height="289" alt="image" src="https://github.com/user-attachments/assets/36974b8e-b58d-4619-88ef-63bbdccd4f49" />


## 4.2 Crear la política
1. Defender agrupa automáticamente las VMs según comportamiento → seleccionar el grupo donde aparece **VM-LAB-AAC**  
2. Hacer clic en **Create policy**  
3. Verás una lista de aplicaciones detectadas como “permitidas”  
4. **No agregar Notepad++**  
5. Guardar la política

---

# 📌 **5. Conectarse a la VM**

## 5.1 Obtener el RDP
1. Ir a la VM → **Connect → RDP**  
2. Descargar el archivo `.rdp`  
3. Conectarse con:
   - Usuario: `labadmin`
   - Password: `Lab123456!`

---

# 📌 **6. Instalar la aplicación NO permitida (Notepad++)**

## 6.1 Descargar instalador
Dentro de la VM, abrir PowerShell y ejecutar:

```powershell
Invoke-WebRequest -Uri "https://github.com/notepad-plus-plus/notepad-plus-plus/releases/download/v8.6.4/npp.8.6.4.Installer.x64.exe" -OutFile "C:\npp.exe"
```

## 6.2 Ejecutar el instalador
```powershell
Start-Process "C:\npp.exe"
```

---

# 📌 **7. Ver el bloqueo de Adaptive Application Controls**

Dependiendo del modo configurado (Audit / Enforce), ocurrirá:

### 🔒 **Modo Enforce (bloqueo activo)**
- El instalador **NO se ejecuta**  
- Aparece un mensaje indicando que la aplicación fue bloqueada  
- En Defender for Cloud se genera una alerta

### 📝 **Modo Audit (solo registro)**
- La app se ejecuta  
- Defender registra que se ejecutó una aplicación no permitida  
- Se genera una recomendación/alerta

---

# 📌 **8. Ver la alerta en Defender for Cloud**

1. Ir a **Microsoft Defender for Cloud → Security alerts**  
2. Buscar:
```
Execution of an application that is not allowed by the adaptive application control policy
```
3. Abrir la alerta y revisar:
   - Nombre del ejecutable: `npp.exe`
   - Ruta: `C:\npp.exe`
   - Usuario que lo ejecutó
   - Severidad

---

# 📌 **9. (Opcional) Agregar Notepad++ a la lista permitida**

1. Volver a **Adaptive application controls**  
2. Abrir la política del grupo  
3. Agregar `npp.exe` como aplicación permitida  
4. Guardar  
5. Reintentar la instalación → ahora funciona

---

# 🧩 **Laboratorio completado**

Este lab demuestra de forma clara cómo Adaptive Application Controls:
- Aprende el comportamiento de la VM  
- Define una lista de aplicaciones permitidas  
- Bloquea aplicaciones no autorizadas  
- Genera alertas de seguridad

---

Si querés, te lo convierto en **versión PDF-ready**, **versión para alumnos**, o **versión con diagramas**. También puedo armarte un **lab avanzado** con múltiples VMs y grupos de control.
