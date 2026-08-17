# Resumen

## Just-in-time machine access

**Defender for Servers Plan 2** en Microsoft Defender for Cloud proporciona la funcionalidad **Just-in-time (JIT) machine access**.

### Objetivo

Los atacantes buscan máquinas accesibles con puertos de administración abiertos, como **RDP o SSH**. Una máquina comprometida puede utilizarse como punto de entrada para atacar otros recursos del entorno.

Para reducir la superficie de ataque, se busca mantener cerrados la mayor cantidad posible de puertos, especialmente los de administración. Sin embargo, los usuarios legítimos necesitan acceder a ellos.

**Just-in-time access** resuelve este problema permitiendo el acceso a las VMs **solo cuando se necesita, en los puertos necesarios y durante el tiempo requerido**.

* Reduce la exposición de las VMs a ataques.
* Permite habilitar el acceso únicamente cuando se necesita.
* Está disponible cuando **Defender for Servers Plan 2** está habilitado.
* Permite habilitar JIT desde Azure Portal o mediante programación.
* Permite solicitar acceso desde Azure Portal o mediante programación.
* Permite auditar la actividad de acceso JIT.

### Just-in-time access y recursos de red

#### Azure

Al habilitar JIT, Defender for Cloud puede bloquear el tráfico entrante en puertos específicos:

* Crea reglas de **"deny all inbound traffic"** para los puertos seleccionados en el **Network Security Group (NSG)** y **Azure Firewall**.
* Estas reglas restringen el acceso a los puertos de administración de las VMs.
* Si ya existen otras reglas para esos puertos, tienen prioridad sobre las nuevas reglas de denegación.
* Si no existen reglas previas, las nuevas reglas de denegación tienen la máxima prioridad en el NSG y Azure Firewall.

Cuando un usuario solicita acceso:

1. Defender for Servers verifica que tenga permisos de **Azure RBAC** sobre la VM.
2. Si la solicitud es aprobada, Defender for Cloud configura temporalmente el NSG y Azure Firewall para permitir tráfico entrante desde la dirección IP o rango correspondiente.
3. El acceso se habilita únicamente durante el período especificado.
4. Una vez finalizado ese período, Defender for Cloud restaura los NSG a su estado anterior.
5. Las conexiones que ya estaban establecidas no se interrumpen.

#### AWS

En AWS, al habilitar JIT:

* Se revocan las reglas correspondientes de los **EC2 security groups** para los puertos seleccionados, bloqueando el tráfico entrante.
* Cuando un usuario solicita acceso y la solicitud es aprobada, Defender for Cloud crea un nuevo **EC2 security group** que permite temporalmente el tráfico entrante a los puertos especificados.
* El acceso se limita al período configurado.
* Para utilizar JIT con AWS, la cuenta de AWS debe estar conectada a Microsoft Defender for Cloud.

### Limitaciones y requisitos

**Prerequisito:**

* **Microsoft Defender for Servers Plan 2** debe estar habilitado en la suscripción.

**VMs compatibles:**

* VMs desplegadas mediante **Azure Resource Manager**.
* VMs protegidas por Azure Firewall en la misma VNET.
* Instancias **AWS EC2 (Preview)**.

**VMs no compatibles:**

* VMs desplegadas mediante modelos de implementación clásicos.
* VMs protegidas por Azure Firewall controlado mediante **Azure Firewall Manager**.

Just-in-time access no admite Azure Firewalls controlados mediante Azure Firewall Manager. El Azure Firewall debe utilizar **Rules (Classic)** y no **Firewall policies**.

Para una política JIT, el nombre de la política junto con el nombre de la VM objetivo no puede superar **56 caracteres**.

### Permisos

Para consultar el estado y los parámetros de JIT se requieren permisos **Reader** y **SecurityReader**, o un rol personalizado equivalente.

Para un rol personalizado:

**Configurar o editar una política JIT:**

* `Microsoft.Security/locations/jitNetworkAccessPolicies/write`
* `Microsoft.Compute/virtualMachines/write`

**Solicitar acceso JIT:**

* `Microsoft.Security/locations/jitNetworkAccessPolicies/initiate/action`
* `Microsoft.Security/locations/jitNetworkAccessPolicies/*/read`
* `Microsoft.Compute/virtualMachines/read`
* `Microsoft.Network/networkInterfaces/*/read`
* `Microsoft.Network/publicIPAddresses/read`

**Leer políticas JIT:**

* `Microsoft.Security/locations/jitNetworkAccessPolicies/read`
* `Microsoft.Security/locations/jitNetworkAccessPolicies/initiate/action`
* `Microsoft.Security/policies/read`
* `Microsoft.Security/pricings/read`
* `Microsoft.Compute/virtualMachines/read`
* `Microsoft.Network/*/read`

Para AWS solo son relevantes los permisos de **Microsoft.Security**.

Para crear un rol con privilegios mínimos destinado únicamente a solicitar acceso JIT a una VM se puede utilizar el script **Set-JitLeastPrivilegedRole**.

### Estados de las VMs en JIT

Las VMs aparecen agrupadas en:

**Configured**

* VMs configuradas para JIT.
* Muestra las solicitudes JIT aprobadas durante los últimos siete días.
* Última fecha y hora de acceso.
* Detalles de conexión configurados.
* Último usuario.

**Not configured**

* VMs que no tienen JIT habilitado pero son compatibles.
* Se recomienda habilitar JIT.

**Unsupported**
VMs que no admiten JIT por diferentes motivos:

* Falta un NSG o Azure Firewall.
* La VM utiliza el modelo de implementación clásico.
* La solución JIT está deshabilitada en la política de seguridad de la suscripción o grupo de recursos.

### Habilitar JIT desde Microsoft Defender for Cloud

1. Abrir **Workload protections**.
2. En **Advanced protections**, seleccionar **Just-in-time VM access**.
3. En **Not configured virtual machines**, seleccionar las VMs.
4. Seleccionar **Enable JIT on VMs**.
5. Configurar los puertos que se desean proteger.

Puertos recomendados:

| Puerto | Uso   |
| ------ | ----- |
| 22     | SSH   |
| 3389   | RDP   |
| 5985   | WinRM |
| 5986   | WinRM |

Para cada puerto se puede configurar:

* **Protocol:** protocolo permitido cuando se aprueba una solicitud.
* **Allowed source IPs:** rangos IP permitidos.
* **Maximum request time:** tiempo máximo durante el cual puede abrirse el puerto.

### Editar la configuración JIT

En **Just-in-time VM access → Configured**:

1. Seleccionar la VM.
2. Elegir **Edit**.
3. Modificar los puertos existentes o seleccionar **Add a new custom port**.
4. Configurar los parámetros.
5. Seleccionar **Save**.

### Solicitar acceso a una VM protegida con JIT

Cuando una VM tiene JIT habilitado, es necesario **solicitar acceso** para conectarse.

Desde **Just-in-time VM access**:

1. Abrir la pestaña **Configured**.
2. Seleccionar la VM.
3. Seleccionar **Request access**.
4. Elegir los puertos que se desean abrir.
5. Especificar las direcciones IP de origen.
6. Definir el período de acceso.
7. Seleccionar **Open ports**.

Si el usuario está detrás de un proxy, puede introducir el rango de direcciones IP del proxy.

La columna **Connection Details** muestra:

* El usuario.
* Los puertos que pueden acceder a la VM.
* Si JIT está habilitado en el NSG o Firewall.

Si está habilitado en ambos, se muestra únicamente el icono del Firewall.

### Azure Virtual Machines

También se puede habilitar JIT directamente desde la página de una VM en Azure Portal:

1. Buscar **Virtual machines**.
2. Seleccionar la VM.
3. Abrir **Configuration**.
4. En **Just-in-time access**, seleccionar **Enable just-in-time**.

Configuración predeterminada:

**Windows:**

* RDP: **3389**
* Acceso máximo: **3 horas**
* IPs de origen: **Any**

**Linux:**

* SSH: **22**
* Acceso máximo: **3 horas**
* IPs de origen: **Any**

Los valores y puertos adicionales se pueden modificar desde **Microsoft Defender for Cloud → Just-in-time VM access → Configured → Edit**.

### Solicitar acceso desde la página Connect de una VM

1. Abrir **Virtual machines** en Azure Portal.
2. Seleccionar la VM.
3. Abrir **Connect**.
4. Azure verifica si JIT está habilitado.
5. Si no está habilitado, se solicita habilitarlo.
6. Si está habilitado, seleccionar **Request access**.
7. La solicitud utiliza la IP solicitante, el rango de tiempo y los puertos configurados para la VM.

Cuando la VM está protegida por **Azure Firewall**, después de aprobar la solicitud Defender for Cloud proporciona los detalles de conexión necesarios, incluido el mapeo de puertos de la tabla **DNAT**.

### Idea fundamental

**JIT = puertos de administración cerrados por defecto + apertura temporal, controlada y específica cuando un usuario necesita conectarse a una VM.**
