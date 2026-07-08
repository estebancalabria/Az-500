## Laboratorio: Azure Firewall con una sola VM

---

### Paso 1 — Crear el Resource Group

Ir a **Resource Groups** → Create.

- Nombre: `rg-firewall-lab`
- Región: East US (o la que uses habitualmente)

**Verificación:** Ir a **Resource Groups** y confirmar que `rg-firewall-lab` aparece en la lista con la región correcta.

---

### Paso 2 — Crear la VNet

Ir a **Virtual Networks** → Create.

- Nombre: `vnet-lab`
- Espacio de direcciones: `10.0.0.0/16`
- Resource Group: `rg-firewall-lab`
- Región: la misma que el RG

No agregar subnets todavía, las creamos en el paso siguiente.

**Verificación:** Abrir `vnet-lab` → **Address space** y confirmar que muestra `10.0.0.0/16`.

---

### Paso 3 — Crear las Subnets

Ir a `vnet-lab` → **Subnets** → Add subnet. Crear las tres siguientes:

**Subnet principal:**
- Nombre: `snet-workload`
- Rango: `10.0.1.0/24`

**Subnet del firewall:**
- Nombre: `AzureFirewallSubnet` (nombre exacto, obligatorio)
- Rango: `10.0.2.0/26`

**Subnet de management del firewall:**
- Nombre: `AzureFirewallManagementSubnet` (nombre exacto, obligatorio)
- Rango: `10.0.3.0/26`

**Verificación:** Ir a `vnet-lab` → **Subnets** y confirmar que aparecen las tres subnets con sus rangos. Prestar atención a que los nombres `AzureFirewallSubnet` y `AzureFirewallManagementSubnet` estén escritos exactamente así, el deploy del firewall falla si hay cualquier diferencia.

---

### Paso 4 — Crear el NSG

Ir a **Network Security Groups** → Create.

- Nombre: `nsg-workload`
- Resource Group: `rg-firewall-lab`

Una vez creado, ir a `nsg-workload` → **Inbound security rules** → Add.

- Nombre: `allow-rdp`
- Source: Any
- Source port ranges: `*`
- Destination: Any
- Destination port ranges: `3389`
- Protocol: TCP
- Action: **Allow**
- Priority: `100`

**Verificación:** Ir a `nsg-workload` → **Inbound security rules** y confirmar que la regla `allow-rdp` aparece con puerto 3389, acción Allow y prioridad 100.

---

### Paso 5 — Crear la VM

Ir a **Virtual Machines** → Create.

- Nombre: `vm-lab`
- Resource Group: `rg-firewall-lab`
- OS: Windows Server 2022
- VNet: `vnet-lab`
- Subnet: `snet-workload`
- IP pública: crear una nueva, nombre `pip-vm`
- NIC NSG: seleccionar **Advanced** y asociar `nsg-workload`

**Verificación:** Abrir `vm-lab` → **Networking** y confirmar que la NIC está en la subnet `snet-workload` y que el NSG asociado es `nsg-workload`. Anotar la IP privada de la VM (campo **Private IP address**), la necesitás en el paso 8.

---

### Paso 6 — Conectarse a la VM y verificar acceso a internet

Ir a `vm-lab` → **Overview** → **Connect** → RDP. Descargar el archivo RDP y conectarse usando las credenciales configuradas.

Dentro de la VM, abrir **Microsoft Edge** y navegar a `https://www.google.com`.

**Verificación:** La página debe cargar correctamente. Esto confirma que antes del firewall la VM sale a internet directamente por su propia IP pública. Este es el estado inicial del laboratorio.

---

### Paso 7 — Crear el Firewall

Primero crear dos IPs públicas para el firewall.

Ir a **Public IP Addresses** → Create:
- Nombre: `pip-fw-main`, SKU: Standard, Asignación: Static

Repetir para:
- Nombre: `pip-fw-mgmt`, SKU: Standard, Asignación: Static

Luego ir a **Firewalls** → Create.

- Nombre: `fw-lab`
- Resource Group: `rg-firewall-lab`
- Tier: **Standard**
- Firewall management: **Use Firewall Policy to manage this firewall**
- Firewall Policy: crear nueva, nombre `fwpol-lab`
- VNet: `vnet-lab`
- IP pública (AzureFirewallSubnet): `pip-fw-main`
- IP pública de management (AzureFirewallManagementSubnet): `pip-fw-mgmt`

El deploy tarda entre 5 y 10 minutos.

**Verificación:** Abrir `fw-lab` → **Overview**. Confirmar que el campo **Private IP address** tiene un valor asignado (ej. `10.0.2.4`). Anotarlo, es el next hop de la route table. Confirmar también que el campo **Provisioning state** muestra **Succeeded**.

---

### Paso 8 — Crear la Route Table

Ir a **Route Tables** → Create.

- Nombre: `rt-workload`
- Resource Group: `rg-firewall-lab`
- Región: la misma que las VNets
- Propagate gateway routes: **Disabled**

Abrir `rt-workload` → **Routes** → Add.

- Nombre: `route-to-fw`
- Address prefix: `0.0.0.0/0`
- Next hop type: **Virtual Appliance**
- Next hop address: IP privada del firewall anotada en el paso 7

Luego ir a `rt-workload` → **Subnets** → Associate.

- VNet: `vnet-lab`
- Subnet: `snet-workload`

**Verificación:** Ir a `vnet-lab` → **Subnets** → clic en `snet-workload`. El campo **Route table** debe mostrar `rt-workload`. Luego ir a `vm-lab` → **Networking** → clic en la NIC → **Effective routes**. Confirmar que aparece una ruta `0.0.0.0/0` con Next Hop Type **VirtualAppliance** y la IP del firewall como next hop address.

---

### Paso 9 — Verificar que la VM pierde conectividad

Al asociar la route table, el tráfico de la VM ahora va hacia el firewall, que no tiene ninguna regla configurada todavía.

Si la sesión RDP sigue abierta, va a caerse sola en segundos. Si no se cayó, intentar navegar a cualquier sitio en el browser.

**Verificación:** La sesión RDP debe desconectarse. Intentar reconectarse desde tu máquina local usando la IP pública de la VM (`pip-vm`): no debe conectar. Esto confirma que el tráfico pasa por el firewall y el firewall lo bloquea todo por defecto.

---

### Paso 10 — Agregar reglas para recuperar acceso RDP

**Regla DNAT** para redirigir el RDP entrante al firewall hacia la VM:

Ir a `fwpol-lab` → **DNAT Rules** → Add a rule collection.

- Nombre: `drc-rdp`
- Priority: `100`

Agregar una regla:

- Nombre: `dnat-rdp`
- Protocol: TCP
- Source: `*`
- Destination: IP pública del firewall (`pip-fw-main`)
- Destination Port: `3389`
- Translated Address: IP privada de la VM (anotada en el paso 5)
- Translated Port: `3389`

**Regla de red** para permitir el tráfico RDP:

Ir a `fwpol-lab` → **Network Rules** → Add a rule collection.

- Nombre: `nrc-rdp`
- Priority: `100`
- Action: **Allow**

Agregar una regla:

- Nombre: `allow-rdp`
- Protocol: TCP
- Source: `*`
- Destination: IP privada de la VM
- Destination Port: `3389`

**Verificación:** Abrir **Remote Desktop Connection** en tu máquina local e intentar conectarte a la IP pública del **firewall** (`pip-fw-main`), no a la de la VM. La sesión debe abrirse en `vm-lab`. Para confirmarlo, dentro de la sesión RDP abrir **Command Prompt** y ejecutar `hostname`. Debe devolver `vm-lab`.

---

### Paso 11 — Verificar que no hay acceso a internet desde la VM

Dentro de la sesión RDP conectada en el paso anterior, abrir **Microsoft Edge** e intentar navegar a `https://www.google.com`.

**Verificación:** El browser debe mostrar error de conexión. Probar también con `https://www.microsoft.com`. Ningún sitio debe cargar. Esto confirma que el RDP funciona (regla de red) pero el tráfico web sigue bloqueado porque no hay ninguna Application Rule todavía.

---

### Paso 12 — Agregar Application Rule para permitir navegación

Ir a `fwpol-lab` → **Application Rules** → Add a rule collection.

- Nombre: `arc-allow-web`
- Priority: `100`
- Action: **Allow**

Agregar una regla:

- Nombre: `allow-internet`
- Source: `10.0.1.0/24`
- Protocol: `http:80, https:443`
- Destination type: FQDN Tags → seleccionar **WindowsUpdate** si querés algo predefinido, o usar FQDN con valor `*` para permitir todo.

Para el lab es más claro usar FQDN `*` para mostrar que ahora navega sin restricción, y en el paso siguiente agregar la exclusión.

Guardar y esperar 2 minutos.

**Verificación:** Desde la VM, navegar a `https://www.google.com` y a `https://www.microsoft.com`. Ambos deben cargar. Ir al portal → `fwpol-lab` → **Application Rules** y confirmar que la collection `arc-allow-web` aparece con estado **Enabled**.

---

### Paso 13 — Agregar Application Rule para bloquear un sitio

Ir a `fwpol-lab` → **Application Rules** → Add a rule collection.

- Nombre: `arc-deny-site`
- Priority: `90` (número menor = se evalúa antes que la collection de priority 100)
- Action: **Deny**

Agregar una regla:

- Nombre: `deny-google`
- Source: `10.0.1.0/24`
- Protocol: `http:80, https:443`
- Destination FQDN: `www.google.com`

Guardar y esperar 2 minutos.

**Verificación:** Desde la VM, intentar navegar a `https://www.google.com`. Debe fallar. Luego navegar a `https://www.microsoft.com`. Debe seguir funcionando. Ir a `fwpol-lab` → **Application Rules** y mostrar las dos collections: `arc-deny-site` con priority 90 y `arc-allow-web` con priority 100. Esto demuestra que el firewall evalúa las reglas en orden de priority y el deny con número menor gana contra el allow con número mayor.

---

---
# RESUMEN - Laboratorio 3B
Uso del Firewall con una sola VM

## Pasos
1. Crear el Resource Group
2. Crear la VNET
3. Crear las Subnets
      * Subnet principal
      * Subnet "AzureFirewallSubnet"
      * Subnet "AzureFirewallManagementSubnet"
4. Crear un NSG
     * Permitir todo el trafico RDP (3389) hacia la red
5. Crear a Virtual Machine
     * Asociarla a la subnet pricipal y al nsg creados anteriormente
6. Conectarnos a la VM por RDP y fijarse que navegamos por internet
7. Crear el Firewal
8. Crear la tabla re ruteo
    * De la IP de la VM que pase por la IP del Firewall
    * Asociar la tabla re ruteo a la subnet pruncipal
9. Me deberia desconectar de la VM
10. Agregar reglas en el firewal
    * Agregar una regla de NAT para que cuando te conectes a la IP publica del Firewall lo redirija a la IP privada de la VM en el 3389
    * Agregar una Network Rule para permitir el trafico al RDP 3389  
11. Conectar a la Virtual  
    * Vemos que no se puede navegar desde internet  
12 Agregar regla Application Rule para que pueda Navegar por Internet
    * Ahora puede navegar por Internet  
13 Agregar regla Application Rule para excluir algun  a pagina
    * Ahora no se puede navegar por la pagina excluida
