# Lab: Autenticación híbrida con Entra Connect (PHS y PTA)

## Objetivo
Desplegar una VM como controlador de dominio + servidor de Entra Connect, sincronizar usuarios hacia Entra ID, y validar el login desde el navegador primero con Password Hash Sync y luego migrando a Pass-through Authentication.

## Prerrequisitos
- Suscripción de Azure con permisos de Global Administrator en el tenant
- Un dominio de Entra ID personalizado (opcional, podés usar el .onmicrosoft.com)
- Cuota disponible para al menos 1 VM Windows Server

---

## Parte 1 – Crear la VM (controlador de dominio)

1. Portal de Azure → **Crear recurso** → **Windows Server 2022 Datacenter**
2. Configuración:
   - Nombre: `VM-DC01`
   - Región: la que tengas más cerca (East US, Brazil South, etc.)
   - Tamaño: `Standard_D2s_v3` (mínimo recomendado para correr AD DS + Entra Connect)
   - Usuario admin local: definilo vos
   - Puertos entrantes: RDP (3389)
3. Red: creá una VNet nueva, ej. `vnet-lab` con subnet `10.0.0.0/24`
4. Deploy y esperá que termine el provisioning

---

## Parte 2 – Promover a controlador de dominio

Conectate por RDP a `VM-DC01` y ejecutá en PowerShell:

```powershell
Install-WindowsFeature -Name AD-Domain-Services -IncludeManagementTools

Install-ADDSForest `
  -DomainName "weylandindustries.com" `
  -DomainNetbiosName "WEYLAND" `
  -InstallDNS `
  -SafeModeAdministratorPassword (ConvertTo-SecureString "TuP@ssw0rdSegura!" -AsPlainText -Force)
```

La VM reinicia sola. Reconectate ya autenticado contra el dominio.

**Configurar el DNS de la NIC de la VM** para que apunte a sí misma (127.0.0.1 o su IP privada) antes del reboot si no lo hizo automáticamente — esto es crítico para que Entra Connect pueda resolver el dominio.

---

## Parte 3 – Crear usuarios de prueba en AD local

```powershell
New-ADUser -Name "Ripley Test" -SamAccountName "ripley" `
  -UserPrincipalName "ripley@weylandindustries.com" `
  -AccountPassword (ConvertTo-SecureString "P@ssw0rd123!" -AsPlainText -Force) `
  -Enabled $true -PasswordNeverExpires $true

New-ADUser -Name "Dallas Test" -SamAccountName "dallas" `
  -UserPrincipalName "dallas@weylandindustries.com" `
  -AccountPassword (ConvertTo-SecureString "P@ssw0rd123!" -AsPlainText -Force) `
  -Enabled $true -PasswordNeverExpires $true
```

---

## Parte 4 – Verificar el dominio en Entra ID

1. Portal Azure → **Entra ID** → **Custom domain names**
2. Si vas a usar `weylandindustries.com` como UPN sincronizado, agregalo y verificalo con el registro TXT en tu DNS público
3. Si preferís simplificar el lab, dejá que los usuarios se sincronicen con el sufijo `.onmicrosoft.com` (Entra Connect lo mapea igual, sin necesidad de verificar dominio)

---

## Parte 5 – Instalar Entra Connect (modo Password Hash Sync)

1. Descargá el instalador desde el portal de Entra ID → **Entra Connect** → o directo desde `https://www.microsoft.com/download/details.aspx?id=47594`
2. Copialo a `VM-DC01` (podés subirlo a un storage account y descargarlo desde ahí, o pegarlo por RDP clipboard)
3. Ejecutá el instalador → **Use express settings**
4. Te va a pedir:
   - Credenciales de Global Admin de Entra ID
   - Credenciales de Enterprise Admin del AD local
5. Express settings instala **Password Hash Sync** por defecto
6. Finalizá la instalación y dejá correr el primer sync

Verificá en el portal: **Entra ID → Users** → deberían aparecer `ripley` y `dallas` con **Directory synced = Yes**

---

## Parte 6 – Probar el login con PHS

1. Abrí una ventana in-privado en el navegador
2. Andá a `https://myaccount.microsoft.com` o `https://portal.azure.com`
3. Logueate con `ripley@weylandindustries.com` y la contraseña que le pusiste en AD local
4. Debería autenticar directo contra Entra ID (el hash ya está sincronizado ahí)

**Prueba de aislamiento:** apagá la VM `VM-DC01` y probá loguearte de nuevo. Con PHS puro, el login debería seguir funcionando porque la validación ocurre 100% en la nube.

---

## Parte 7 – Migrar a Pass-through Authentication

Con la VM prendida de nuevo:

1. En `VM-DC01`, abrí **Azure AD Connect** (el wizard) → **Configure** → **Change user sign-in**
2. Seleccioná **Pass-through Authentication**
3. Marcá también **Enable single sign-on** (Seamless SSO) si querés probarlo
4. El wizard instala el agente PTA local y lo registra contra Entra ID
5. Verificá el estado del agente: **Entra ID → Entra Connect → Pass-through authentication** → debería figurar `VM-DC01` como Active

---

## Parte 8 – Probar el login con PTA

1. Ventana in-privado nueva
2. Login con `ripley@weylandindustries.com`
3. Esta vez la validación de credenciales viaja (cifrada) hasta el agente PTA en `VM-DC01`, que consulta al AD local en tiempo real

**Prueba de dependencia:** apagá `VM-DC01` de nuevo y probá loguearte. Con PTA puro (sin agente de respaldo en otra VM), el login debería **fallar** — a diferencia de PHS. Esto te deja ver en la práctica la diferencia de resiliencia entre ambos métodos.

---

## Parte 9 – Comparar logon hours / política de contraseñas

Para reforzar el concepto del lab original:

```powershell
Set-ADUser ripley -LogonWorkstations "VM-DC01"
```

o restringí el horario de logon con `Set-ADAccountControl` / ADUC → pestaña **Logon Hours**. Con PTA activo, esa restricción se respeta en el login vía Entra ID. Con PHS puro, no.

---

¿Querés que lo arme también como documento con el tagging propio que venís usando para los labs de AZ-500, o lo dejamos así en formato plano?
