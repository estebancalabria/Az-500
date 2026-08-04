# Lab: Configurar Intranet Zone para SSO híbrido mediante GPO

## Escenario

Tenés un dominio on-premises de Active Directory Domain Services (AD DS) sincronizado con un tenant de Azure AD (Entra ID). Vas a implementar Single Sign-On (SSO) para recursos de Azure AD, y necesitás configurar el setting de **Intranet Zone** para todos los usuarios usando una Group Policy Object (GPO).

**Respuesta objetivo:** el setting correcto es **Logon options**, dentro de la zona Intranet, configurado como *"Automatic logon with current username and password"*. Esto habilita Integrated Windows Authentication (IWA), que es el mecanismo que permite el SSO silencioso cuando el usuario ya está autenticado en el dominio.

Como el escenario es 100% híbrido, este lab levanta toda la infraestructura desde cero: red, un Domain Controller, Azure AD Connect para sincronizar con el tenant, un cliente unido al dominio, y finalmente la GPO.

## Qué vas a necesitar

- Una suscripción de Azure activa con permisos para crear recursos (Contributor o superior)
- Un tenant de Microsoft Entra ID donde tengas rol de Global Administrator
- Aproximadamente 2-3 horas si hacés todo el lab completo
- Conocimiento básico de RDP, Server Manager y Active Directory Users and Computers

## Arquitectura del lab

| Recurso | Nombre sugerido | Función |
|---|---|---|
| Resource Group | `rg-sso-lab` | Contenedor de todos los recursos |
| Virtual Network | `vnet-sso-lab` | Red privada del lab (10.0.0.0/16) |
| Subnet | `subnet-dc` | Subred única (10.0.0.0/24) |
| VM 1 | `DC01` | Windows Server 2022, promovida a Domain Controller |
| VM 2 | `CLIENT01` | Windows 11 Pro, unida al dominio |
| Software | Azure AD Connect | Sincroniza AD DS on-prem con Entra ID |
| GPO | `SSO-IntranetZone` | Configura Site to Zone Assignment + Logon options |

---

## Parte 1 — Crear el Resource Group y la red virtual

### 1.1 Crear el Resource Group

1. Entrá al [portal de Azure](https://portal.azure.com)
2. En la barra de búsqueda superior, escribí **Resource groups** y entrá
3. Click en **+ Create**
4. Completá:
   - **Subscription**: tu suscripción
   - **Resource group**: `rg-sso-lab`
   - **Region**: elegí una región cercana (ej. `East US` o `Brazil South`)
5. Click **Review + create** → **Create**

### 1.2 Crear la Virtual Network

1. Buscá **Virtual networks** en la barra superior → **+ Create**
2. Pestaña **Basics**:
   - **Resource group**: `rg-sso-lab`
   - **Name**: `vnet-sso-lab`
   - **Region**: la misma que usaste en el paso anterior (importante, tiene que coincidir)
3. Pestaña **IP Addresses**:
   - Borrá el espacio de direcciones por default y agregá `10.0.0.0/16`
   - En **Subnets**, editá la subnet default:
     - **Name**: `subnet-dc`
     - **Starting address**: `10.0.0.0`
     - **Subnet size**: `/24`
4. Click **Review + create** → **Create**

---

## Parte 2 — Crear la VM del Domain Controller (DC01)

### 2.1 Provisionar la VM

1. Buscá **Virtual machines** → **+ Create** → **Azure virtual machine**
2. Pestaña **Basics**:
   - **Resource group**: `rg-sso-lab`
   - **Virtual machine name**: `DC01`
   - **Region**: la misma que la VNet
   - **Image**: `Windows Server 2022 Datacenter: Azure Edition - x64 Gen2`
   - **Size**: `Standard_D2s_v3` (mínimo recomendable; con menos memoria AD DS anda lento)
   - **Username**: definí un admin local, ej. `azureadmin`
   - **Password**: una contraseña compleja, anotala
   - **Public inbound ports**: `Allow selected ports` → seleccioná `RDP (3389)`
3. Pestaña **Networking**:
   - **Virtual network**: `vnet-sso-lab`
   - **Subnet**: `subnet-dc`
   - **Public IP**: dejá que cree una nueva (la vamos a necesitar para conectarnos por RDP; se puede sacar después)
4. Dejá el resto en default → **Review + create** → **Create**
5. Esperá a que termine el deployment (unos 3-5 minutos)

### 2.2 Fijar la IP privada de DC01 como estática

Esto es crítico: si la IP cambia, se rompe la resolución DNS del dominio para el resto de las VMs.

1. Andá al recurso `DC01` → en el menú izquierdo, **Networking**
2. Click en la interfaz de red (NIC) asociada
3. En el menú de la NIC, andá a **IP configurations** → click en `ipconfig1`
4. Cambiá **Private IP address settings** de `Dynamic` a `Static`
5. Anotá la IP asignada (debería ser `10.0.0.4`, la vamos a usar como DNS server más adelante)
6. **Save**

---

## Parte 3 — Promover DC01 a Domain Controller

### 3.1 Conectarte por RDP

1. Volvé al recurso `DC01` en el portal → **Connect** → **RDP** → **Download RDP File**
2. Abrí el archivo descargado, ingresá el usuario y contraseña que definiste

### 3.2 Instalar el rol de AD DS

1. Dentro de la VM, abrí **Server Manager** (se abre solo al iniciar sesión)
2. Click en **Manage** (arriba a la derecha) → **Add Roles and Features**
3. En el wizard:
   - **Installation Type**: Role-based or feature-based installation → Next
   - **Server Selection**: dejá el servidor local seleccionado → Next
   - **Server Roles**: tildá **Active Directory Domain Services** → cuando aparezca el popup, click **Add Features** → Next
   - Saltá **Features** → Next → Next (AD DS info) → **Install**
4. Esperá a que termine la instalación (no reinicies todavía)

### 3.3 Promover el servidor a Domain Controller

1. Al finalizar la instalación, en la barra de notificaciones de Server Manager (ícono de bandera amarilla, arriba a la derecha) aparece un aviso → click en **"Promote this server to a domain controller"**
2. En el wizard de configuración de Deployment:
   - Seleccioná **Add a new forest**
   - **Root domain name**: `lab.contoso.com` (o el dominio que prefieras)
   - Next
3. **Domain Controller Options**:
   - Forest functional level y Domain functional level: dejá el default (Windows Server 2016 o superior)
   - Tildá **Domain Name System (DNS) server** (debería venir tildado)
   - **Directory Services Restore Mode (DSRM) password**: definila y anotala, es distinta a la del admin local
   - Next
4. **DNS Options**: ignorá el warning de delegación → Next
5. **Additional Options**: el NetBIOS name se autocompleta (`LAB`) → Next
6. **Paths**: dejá los paths default para la base de datos, logs y SYSVOL → Next
7. **Review Options**: revisá y confirmá → Next
8. **Prerequisites Check**: puede mostrar algunos warnings, son normales en un lab. Click **Install**
9. El servidor reinicia automáticamente al finalizar

### 3.4 Verificar la promoción

1. Reconectate por RDP, ahora el login va a pedir el usuario en formato `LAB\azureadmin` (o el dominio que hayas elegido)
2. Abrí **Server Manager** → deberías ver **AD DS** y **DNS** en el menú de roles instalados, sin errores

---

## Parte 4 — Crear una OU y un usuario de prueba

### 4.1 Crear la Organizational Unit

1. Dentro de `DC01`, abrí **Active Directory Users and Computers** (buscalo en el menú Start, o desde Server Manager → Tools)
2. Click derecho sobre el dominio (`lab.contoso.com`) → **New** → **Organizational Unit**
3. Nombre: `LabUsers` → **OK**
   - *Tip: destildá "Protect container from accidental deletion" si vas a estar borrando y recreando la OU seguido durante pruebas.*

### 4.2 Crear el usuario de prueba

1. Click derecho sobre `LabUsers` → **New** → **User**
2. Completá:
   - **First name**: `User1`
   - **User logon name**: `user1` — y en el dropdown del sufijo UPN, seleccioná el dominio que después vas a verificar en Entra ID (ver Parte 5.2). Si vas a usar el dominio `.onmicrosoft.com` default del tenant, dejalo como `lab.contoso.com` por ahora y lo ajustamos después con un UPN suffix.
3. Next → asignale una contraseña, destildá "User must change password at next logon" (para simplificar el lab) → Next → Finish

---

## Parte 5 — Sincronizar con Entra ID usando Azure AD Connect

### 5.1 Verificar que tenés un tenant de Entra ID

Si ya tenés un tenant con rol de Global Administrator, saltá a 5.2. Si no:

1. Buscá **Microsoft Entra ID** en el portal → si no tenés ninguno, andá a **Manage tenants** → **Create**
2. Elegí **Microsoft Entra ID** como tipo de tenant, completá organización, dominio inicial (`tunombre.onmicrosoft.com`) y región → **Create**

### 5.2 Verificar el dominio custom en Entra ID (opcional pero recomendado)

Si querés que el UPN de tus usuarios sea `@lab.contoso.com` en vez del `.onmicrosoft.com` default, necesitás un dominio real que controles:

1. En **Microsoft Entra ID** → **Custom domain names** → **Add custom domain**
2. Escribí tu dominio (ej. `lab.contoso.com`) → **Add domain**
3. Azure te da un registro TXT para agregar en el DNS público de ese dominio
4. Una vez agregado el registro en tu proveedor de DNS, volvé y click **Verify**

> **Si no tenés un dominio propio para verificar:** podés simplificar el lab dejando el UPN de `user1` en `user1@tunombre.onmicrosoft.com`. Para esto, en el paso 4.2 elegís ese sufijo directamente, sin pasar por este paso.

### 5.3 Descargar e instalar Azure AD Connect

1. Dentro de `DC01`, abrí un navegador y descargá **Azure AD Connect** desde el Microsoft Download Center (buscá "Download Azure AD Connect" — el instalador se llama `AzureADConnect.msi`)
2. Ejecutá el instalador
3. Aceptá términos → **Continue**
4. **Express Settings**: para este lab, usá **Customize** en lugar de Express, así controlamos qué OU sincronizar
5. **Install required components** → Next
6. **User sign-in**: dejá **Password Hash Synchronization** seleccionado (es el método más simple para lab) → Next
7. **Connect to Azure AD**: ingresá las credenciales de tu Global Administrator del tenant → Next
8. **Connect your directories**: debería detectar automáticamente el forest `lab.contoso.com`. Click **Add Directory** si no aparece, usando credenciales de Enterprise Admin (tu `azureadmin`) → Next
9. **Azure AD sign-in configuration**: verificá que el UPN se mapea correctamente → Next
10. **Domain and OU filtering**: seleccioná **Sync selected domains and OUs** → tildá únicamente `LabUsers` → Next
11. **Uniquely identifying your users**, **Filter users and devices**, **Optional features**: dejá los defaults → Next
12. **Ready to configure**: click **Install**
13. Esperá a que corra la sincronización inicial (puede tardar unos minutos)

### 5.4 Verificar la sincronización

1. Volvé al portal de Azure → **Microsoft Entra ID** → **Users**
2. Deberías ver `User1` listado, con **Source** = `Windows Server AD`
3. Si no aparece, volvé a `DC01` y abrí **Synchronization Service Manager** (buscalo en Start) para revisar el estado del último ciclo de sync

---

## Parte 6 — Crear el cliente unido al dominio (CLIENT01)

### 6.1 Configurar el DNS de la VNet (importante, antes de crear el cliente)

Para que cualquier VM nueva resuelva el dominio automáticamente sin configurar DNS manualmente:

1. Andá al recurso `vnet-sso-lab` → **DNS servers** (en el menú izquierdo)
2. Seleccioná **Custom**
3. Agregá la IP privada de `DC01` (`10.0.0.4`)
4. **Save**

> Si ya tenías VMs corriendo antes de este cambio, necesitás reiniciarlas para que tomen el nuevo DNS.

### 6.2 Provisionar CLIENT01

1. **Virtual machines** → **+ Create**
2. **Basics**:
   - **Resource group**: `rg-sso-lab`
   - **Name**: `CLIENT01`
   - **Region**: la misma
   - **Image**: `Windows 11 Pro` (si no aparece en la lista básica, buscala en **See all images** → marketplace)
   - **Size**: `Standard_D2s_v3` o similar
   - **Username/Password**: definí un admin local
3. **Networking**: misma `vnet-sso-lab`, subnet `subnet-dc`
4. **Review + create** → **Create**

### 6.3 Unir CLIENT01 al dominio

1. RDP a `CLIENT01` usando el admin local
2. Abrí **Settings** → **System** → **About** → **Domain or workgroup** (o buscá "Access work or school" / usá el ícono This PC → Properties, según la versión de Windows 11)
3. Click **Change** → seleccioná **Domain** → escribí `lab.contoso.com`
4. Te va a pedir credenciales: usá `LAB\azureadmin` (o el usuario Enterprise Admin del dominio)
5. Aparece el mensaje de bienvenida al dominio → **OK** → reiniciar cuando lo pida
6. Al reiniciar, loguéate con `LAB\user1` y la contraseña que definiste en la Parte 4.2

---

## Parte 7 — Crear y configurar la GPO

### 7.1 Abrir Group Policy Management

1. Volvé a `DC01` por RDP
2. Abrí **Group Policy Management** (Server Manager → Tools → Group Policy Management, o buscalo en Start)

### 7.2 Crear la GPO

1. En el árbol izquierdo, expandí **Forest** → **Domains** → `lab.contoso.com`
2. Click derecho sobre `lab.contoso.com` → **Create a GPO in this domain, and Link it here**
3. Nombre: `SSO-IntranetZone` → **OK**

### 7.3 Configurar Site to Zone Assignment List

Este paso agrega las URLs de Azure AD a la zona Intranet, requisito previo para que el logon automático aplique.

1. Click derecho sobre la GPO `SSO-IntranetZone` → **Edit** (abre el Group Policy Management Editor)
2. Navegá a:
   `User Configuration` → `Policies` → `Administrative Templates` → `Windows Components` → `Internet Explorer` → `Internet Control Panel` → `Security Page` → `Site to Zone Assignment List`
3. Doble click sobre la política → seleccioná **Enabled**
4. Click **Show...** junto a "Site to Zone Assignment List"
5. Agregá las siguientes entradas (Value name = URL, Value = número de zona):

   | Value name | Value |
   |---|---|
   | `https://login.microsoftonline.com` | `1` |
   | `https://login.microsoft.com` | `1` |
   | `https://autologon.microsoftazuread-sso.com` | `1` |

   (Zona `1` = Intranet Zone)
6. **OK** → **OK**

### 7.4 Configurar Logon options en la zona Intranet

Este es el setting que responde directamente a la pregunta del lab.

1. En el mismo árbol, navegá a:
   `User Configuration` → `Policies` → `Administrative Templates` → `Windows Components` → `Internet Explorer` → `Internet Control Panel` → `Security Page` → `Intranet Zone`
2. Doble click en **Logon options**
3. Seleccioná **Enabled**
4. En el dropdown **Logon options**, elegí **Automatic logon with current username and password**
5. **OK**

> Podés repetir 7.3 y 7.4 en `Computer Configuration` con la misma ruta si querés que aplique también a nivel máquina, aunque para SSO de usuario el nodo de `User Configuration` es el relevante.

### 7.5 Cerrar el editor

Cerrá el Group Policy Management Editor. La GPO ya quedó linkeada al dominio completo desde el paso 7.2.

---

## Parte 8 — Aplicar y verificar en el cliente

### 8.1 Forzar la actualización de políticas

1. En `CLIENT01`, logueado como `LAB\user1`, abrí una consola **cmd** o **PowerShell**
2. Ejecutá:
   ```
   gpupdate /force
   ```
3. Esperá el mensaje de confirmación (Computer Policy y User Policy actualizadas)

### 8.2 Verificar que la GPO se aplicó correctamente

1. En la misma consola, ejecutá:
   ```
   gpresult /r
   ```
2. Buscá en la sección **User Settings** → **Applied Group Policy Objects** → debería listar `SSO-IntranetZone`

Para un detalle más visual:
```
gpresult /h C:\gpreport.html
```
Y abrí el archivo generado en el navegador para ver el reporte completo.

### 8.3 Probar el SSO

1. Abrí Microsoft Edge en `CLIENT01`
2. Navegá a `https://portal.azure.com` o `https://myapps.microsoft.com`
3. Si todo está bien configurado, debería autenticarte automáticamente sin pedir usuario/contraseña (Integrated Windows Authentication), o como mucho mostrar una pantalla de selección de cuenta sin pedir password.

> Si te sigue pidiendo credenciales: verificá que las URLs del paso 7.3 estén bien escritas, que `gpupdate` corrió sin errores, y que el usuario `user1` efectivamente sincronizó a Entra ID (Parte 5.4). También revisá que el navegador no tenga configuraciones de zona propias sobreescribiendo la GPO (`Internet Options` → `Security` → `Local intranet` → `Sites` debería mostrar las URLs agregadas).

---

## Limpieza de recursos

Para no seguir pagando por el lab una vez terminado:

1. Buscá **Resource groups** → `rg-sso-lab`
2. Click **Delete resource group**
3. Escribí el nombre del resource group para confirmar → **Delete**

Esto elimina VMs, discos, IPs públicas, NICs y la VNet en un solo paso.

---

## Resumen de la lección

| Pregunta | Respuesta |
|---|---|
| ¿Qué setting de GPO controla el comportamiento de autenticación automática para SSO en la zona Intranet? | **Logon options**, configurado como *"Automatic logon with current username and password"* |
| ¿Qué ruta en el GPO Editor lleva a ese setting? | `User Configuration > Administrative Templates > Windows Components > Internet Explorer > Internet Control Panel > Security Page > Intranet Zone > Logon options` |
| ¿Qué paso previo es indispensable para que el setting tenga efecto? | Agregar las URLs de Azure AD (`login.microsoftonline.com`, etc.) a la Intranet Zone vía **Site to Zone Assignment List** |
