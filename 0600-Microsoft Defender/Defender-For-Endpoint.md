# 🛡️ 1) ¿Qué es **Microsoft Defender for Endpoint**?

**Microsoft Defender for Endpoint (MDE)** es la plataforma de **EDR/XDR empresarial** de Microsoft.  
Proporciona:

- Detección y respuesta ante amenazas (EDR)  
- Protección de endpoints (antimalware, exploit guard, firewall, web filtering)  
- Gestión de vulnerabilidades  
- Aislamiento, investigación y remediación automatizada  
- Integración nativa con Azure, Intune y Microsoft 365 Defender  

Es parte del ecosistema de seguridad de Microsoft y se integra directamente con **Microsoft Defender for Cloud** para proteger máquinas en Azure, AWS y on-premises.

---

# ☁️ 2) ¿Qué rol cumple dentro de **Microsoft Defender for Cloud**?

Defender for Cloud usa MDE como su **motor de protección de endpoints** para:

- Evaluar vulnerabilidades del sistema operativo  
- Detectar comportamientos maliciosos en VMs  
- Generar alertas de seguridad correladas con otros servicios  
- Aplicar hardening automatizado  
- Enviar señales al panel de seguridad unificado de Microsoft 365 Defender  

Cuando habilitás **Defender for Servers**, automáticamente se activa la integración con MDE.

---

# ⚙️ 3) ¿Cómo habilitar Defender for Endpoint desde Defender for Cloud?

## ✔️ Requisitos
- Suscripción con **Defender for Servers Plan 1 o Plan 2**  
- VM con OS soportado (Windows Server 2012 R2+, Windows 10+, Linux soportado)  
- Permisos: *Security Admin*, *Contributor* o *Owner*

## ✔️ Pasos

### **A) Activar Defender for Servers**
1. Azure Portal → *Microsoft Defender for Cloud*  
2. *Environment Settings*  
3. Seleccionar la suscripción  
4. Activar **Defender for Servers (P1/P2)**  
5. Guardar

### **B) Onboarding automático**
Una vez habilitado el plan:
- Defender for Cloud instala el agente necesario  
- Conecta la VM a Microsoft Defender for Endpoint  
- En ~30–60 minutos aparece en **Device Inventory**  
  👉 [https://security.microsoft.com/devices](https://security.microsoft.com/devices)

### **C) Onboarding manual (si falla el automático)**
1. Ir a Microsoft 365 Defender → *Settings → Endpoints → Onboarding*  
2. Descargar script de onboarding  
3. Ejecutarlo dentro de la VM  
4. Validar que el dispositivo aparece en el portal

---

# 🔍 4) ¿Cómo chequear que la VM está correctamente onboarded?

Validaciones clave:

- VM aparece en **Device Inventory**  
- Estado: *Active*  
- Señales de sensor: *Healthy*  
- Vulnerability assessment activo  
- Alertas y eventos visibles en el timeline  
- En Defender for Cloud → *Inventory* → VM → *Extensions* → MDE instalado

---

# 🚨 5) Lista de alertas posibles de **Microsoft Defender for Endpoint**

Aquí tenés una lista completa y organizada por categorías, ideal para documentación y cursos.

## 🧬 **A. Malware & Ransomware**
- Malware detected on device  
- Ransomware behavior detected  
- Suspicious file execution  
- Malicious payload dropped  
- Unusual encryption activity  
- Fileless malware detected  
- Suspicious PowerShell command

## 🛠️ **B. Exploits & Vulnerabilidades**
- Exploit attempt blocked  
- Vulnerable driver loaded  
- Suspicious kernel-level access  
- Privilege escalation attempt  
- Exploit against known CVE detected  
- Memory corruption exploit attempt

## 🧑‍💻 **C. Lateral Movement**
- Pass-the-Hash attempt  
- Pass-the-Ticket attempt  
- Remote code execution attempt  
- Suspicious SMB connection  
- Unusual RDP lateral movement  
- Remote service creation detected

## 🔐 **D. Credential Theft**
- LSASS memory access attempt  
- Mimikatz-like behavior detected  
- Credential dumping tools detected  
- Suspicious SAM database access  
- Unusual Kerberos ticket activity

## 🌐 **E. Network & Command & Control**
- Connection to known malicious IP/domain  
- Beaconing behavior detected  
- Suspicious DNS tunneling  
- TOR communication attempt  
- C2 framework behavior (Cobalt Strike, Metasploit, etc.)

## 🧩 **F. Persistence**
- Suspicious scheduled task created  
- Malicious registry autorun entry  
- Unusual service installation  
- Startup folder modification  
- WMI persistence detected

## 🧱 **G. Defense Evasion**
- Tampering with security tools  
- Antivirus disabled  
- EDR sensor disabled  
- Obfuscated script execution  
- Suspicious use of LOLBins (Living-off-the-land binaries)

## 📦 **H. Application & Script Abuse**
- Malicious Office macro  
- Suspicious VBA execution  
- PowerShell Empire behavior  
- Python script with malicious indicators  
- Node.js or Electron app abuse

## 🧭 **I. Cloud & Identity**
- Suspicious Azure AD sign-in from device  
- Token theft attempt  
- OAuth abuse indicators  
- Device participating in identity attacks

## 🧰 **J. Vulnerability Management Alerts**
- High-risk vulnerability detected  
- Exploitable configuration found  
- Missing critical security updates  
- Weak security baseline detected

