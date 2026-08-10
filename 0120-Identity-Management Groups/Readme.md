# Azure Management Groups — Resumen completo

## 1. ¿Qué son los Management Groups?

Los **Azure Management Groups** permiten administrar y gobernar múltiples suscripciones de Azure de forma centralizada.

Proporcionan un nivel de **scope por encima de las suscripciones** y permiten aplicar:

* **Azure RBAC** → permisos y acceso.
* **Azure Policy** → políticas y cumplimiento.
* Configuraciones de **gobernanza**.
* Controles de **seguridad y compliance**.

La jerarquía principal es:

```text
Microsoft Entra ID Tenant
        │
        ▼
Root Management Group
        │
        ├── Management Group
        │       ├── Management Group
        │       │       └── Subscription
        │       │               └── Resource Group
        │       │                       └── Resources
        │       └── Subscription
        │
        └── Management Group
                └── Subscription
```

Las condiciones aplicadas a un Management Group se **heredan automáticamente** por sus descendientes.

Por ejemplo, una Policy aplicada a un Management Group que limita las regiones donde se pueden crear VMs se aplicará también a:

* Management Groups hijos.
* Suscripciones.
* Resource Groups.
* Recursos.

---

# 2. Requisitos y características generales

* Todas las suscripciones dentro de un Management Group deben confiar en el **mismo Microsoft Entra ID tenant**.
* Un directorio puede tener hasta **10.000 Management Groups**.
* Una jerarquía puede tener hasta **6 niveles de profundidad** de Management Groups.
* El límite de 6 niveles **no incluye**:

  * Root Management Group.
  * Subscription level.
* Cada Management Group o subscription puede tener **un solo padre**.
* Un Management Group puede tener **muchos hijos**.
* Cada directorio tiene **una única jerarquía** de Management Groups y subscriptions.

---

# 3. Root Management Group

Cada Microsoft Entra tenant tiene automáticamente un **Root Management Group**.

Por defecto:

* Display name: **Tenant root group**
* Su ID es igual al **Microsoft Entra tenant ID**.
* No puede moverse.
* No puede eliminarse.
* Todas las suscripciones y Management Groups terminan dependiendo de él.
* Las nuevas suscripciones se asignan inicialmente al Root Management Group.

El Root Management Group permite aplicar:

* Azure RBAC global.
* Azure Policy global.
* Gobernanza sobre todo el tenant.

### Acceso al Root

Un **Microsoft Entra Global Administrator** no tiene automáticamente permisos administrativos sobre el Root Management Group.

Debe elevarse inicialmente a **User Access Administrator** sobre el Root.

Después puede asignar roles de Azure a otros usuarios o grupos.

### ⚠️ Importante

Cualquier asignación de:

* User access
* Azure Policy

realizada sobre el Root puede afectar a **todos los recursos del tenant**.

Por eso las asignaciones en el Root deberían ser únicamente las que sean realmente **"Must Have"** a nivel global.

---

# 4. Initial setup

Cuando se empiezan a utilizar Management Groups:

1. Se crea el Root Management Group.
2. Las suscripciones existentes pasan a ser hijos del Root.
3. Se establece una única jerarquía dentro del tenant.

Esto permite que las políticas y permisos asignados en el Root puedan aplicarse a toda la organización.

La jerarquía puede abarcar:

```text
Root
 └── Management Groups
      └── Subscriptions
           └── Resource Groups
                └── Resources
```

---

# 5. Herencia de permisos y políticas

Los Management Groups son especialmente importantes porque los permisos y políticas **se heredan hacia abajo**.

Ejemplo:

```text
Corp Management Group
        │
        ├── Subscription A
        │      └── VM 1
        │
        └── Subscription B
               └── VM 2
```

Si asignamos:

```text
VM Contributor
```

a `Corp`, las VMs descendientes reciben ese permiso mediante herencia.

Esto evita tener que configurar RBAC individualmente en cada subscription.

---

# 6. Azure RBAC en Management Groups

Los Management Groups soportan **Azure Role-Based Access Control (Azure RBAC)**.

Se puede asignar cualquier rol Azure compatible con ese scope y la asignación se hereda hacia los recursos descendientes.

### Roles principales

| Rol                              | Create | Rename | Move | Delete | Assign Access | Assign Policy | Read |
| -------------------------------- | ------ | ------ | ---- | ------ | ------------- | ------------- | ---- |
| **Owner**                        | ✅      | ✅      | ✅    | ✅      | ✅             | ✅             | ✅    |
| **Contributor**                  | ✅      | ✅      | ✅    | ✅      | ❌             | ❌             | ✅    |
| **Management Group Contributor** | ✅      | ✅      | ✅    | ✅      | ❌             | ❌             | —    |
| **Reader**                       | —      | —      | —    | —      | —             | —             | ✅    |
| **Management Group Reader**      | —      | —      | —    | —      | —             | —             | ✅    |
| **Resource Policy Contributor**  | —      | —      | —    | —      | —             | ✅             | —    |
| **User Access Administrator**    | —      | —      | —    | —      | ✅             | —             | ✅    |

### Management Group Contributor / Reader

Estos roles están específicamente diseñados para acciones sobre el **Management Group scope**.

---

# 7. Custom Roles

También se pueden definir **Azure Custom Roles** utilizando un Management Group como `assignableScope`.

El scope debe utilizar el **ID del Management Group**, no su display name:

```text
/providers/Microsoft.Management/managementGroups/{groupId}
```

El Custom Role podrá asignarse en:

* El Management Group.
* Management Groups hijos.
* Subscriptions hijas.
* Resource Groups.
* Resources.

La definición del rol se hereda por la jerarquía.

---

# 8. Problema al mover recursos con Custom Roles

Hay que tener cuidado cuando una Custom Role está definida en un Management Group padre y asignada en una subscription hija.

Ejemplo:

```text
Sandbox Management Group
        │
        └── Custom Role Definition
                  │
                  ├── Sandbox Subscription 1
                  └── Sandbox Subscription 2
```

Si intentamos mover `Sandbox Subscription 1` a:

```text
Corp Management Group
        │
        └── Sandbox Subscription 1
```

podemos romper la relación entre:

```text
Role Definition
      ↓
Role Assignment
```

porque la nueva ubicación podría quedar fuera del `assignableScope` del Custom Role.

Azure puede impedir el movimiento.

### Soluciones

Se puede:

1. Eliminar la asignación del rol antes de mover la subscription.
2. Agregar la nueva subscription al `assignableScope`.
3. Cambiar el `assignableScope` para utilizar un Management Group superior.
4. Crear otro Custom Role en la nueva rama y cambiar la asignación.

### Limitaciones de Custom Roles

* Un nuevo Custom Role puede tener solamente **un Management Group** en sus `assignableScopes`.
* No se pueden definir determinadas acciones de **Resource Provider data plane** en Custom Roles de Management Groups debido a problemas de latencia.
* Azure Resource Manager **no valida que el Management Group indicado en `assignableScopes` realmente exista**.
* Si se escribe mal el Management Group ID, el role definition puede crearse igualmente.

---

# 9. Cambiar el nombre de un Management Group

El **display name** de un Management Group se puede modificar desde:

* Azure Portal.
* PowerShell.
* Azure CLI.

### Portal

```text
Azure Portal
→ All services
→ Management groups
→ Seleccionar Management Group
→ Details
→ Rename group
→ Nuevo nombre
→ Save
```

### PowerShell

```powershell
Update-AzManagementGroup `
    -GroupId 'ContosoIt' `
    -DisplayName 'Contoso Group'
```

### Azure CLI

```bash
az account management-group update \
    --name 'Contoso' \
    --display-name 'Contoso Group'
```

⚠️ El **display name** y el **Management Group ID** son conceptos diferentes.

---

# 10. Eliminar un Management Group

Para eliminar un Management Group deben cumplirse estas condiciones:

### El Management Group debe estar vacío

No puede tener:

* Management Groups hijos.
* Subscriptions hijas.

Primero hay que mover esos elementos a otro Management Group.

### Permisos necesarios

Se necesita permiso de escritura sobre el Management Group.

Roles típicos:

* **Owner**
* **Contributor**
* **Management Group Contributor**

### Portal

```text
Azure Portal
→ All services
→ Management groups
→ Details
→ Delete
→ Yes
```

Si el botón aparece deshabilitado, al colocar el cursor sobre él se puede visualizar el motivo.

### PowerShell

```powershell
Remove-AzManagementGroup -GroupId 'Contoso'
```

### Azure CLI

```bash
az account management-group delete --name 'Contoso'
```

⚠️ El **Root Management Group no puede eliminarse**.

---

# 11. Ver Management Groups

Un usuario puede visualizar los Management Groups sobre los cuales tiene un rol Azure **directo o heredado**.

### Portal

```text
Azure Portal
→ All services
→ Management groups
```

Se muestra la jerarquía completa que el usuario tiene permiso para explorar.

La navegación funciona de forma similar a un explorador de archivos:

```text
Management Group
    ↓
Management Group / Subscription
    ↓
Nivel inferior
```

Para ver los detalles se selecciona **Details**.

Si el enlace no aparece, probablemente el usuario no tiene permisos suficientes para visualizar ese Management Group.

---

# 12. Consultar Management Groups con PowerShell

Para obtener los Management Groups:

```powershell
Get-AzManagementGroup
```

Para obtener uno específico:

```powershell
Get-AzManagementGroup -GroupId 'Contoso'
```

---

# 13. Consultar Management Groups con Azure CLI

Para listar todos:

```bash
az account management-group list
```

Para obtener uno específico:

```bash
az account management-group show --name 'Contoso'
```

Para obtener un Management Group y todos los niveles inferiores:

```bash
az account management-group show \
    --name 'Contoso' \
    -e \
    -r
```

Donde:

* `-e` → expande la jerarquía.
* `-r` → recorre los niveles inferiores.

---

# 14. Mover Management Groups y Subscriptions

Una de las funciones principales de los Management Groups es **agrupar subscriptions**.

Importante:

> Solo **Management Groups y Subscriptions** pueden ser hijos directos de un Management Group.

Una subscription que se mueve a otro Management Group automáticamente **hereda las políticas y permisos del nuevo padre**.

---

# 15. Permisos necesarios para mover una Subscription o Management Group

Para mover un elemento se deben cumplir permisos en tres niveles.

### 1. Elemento que se está moviendo

En la subscription o Management Group hijo:

```text
Microsoft.management/managementgroups/write
Microsoft.management/managementgroups/subscriptions/write
Microsoft.Authorization/roleAssignments/write
Microsoft.Authorization/roleAssignments/delete
Microsoft.Management/register/action
```

El permiso:

```text
Microsoft.management/managementgroups/subscriptions/write
```

aplica específicamente al movimiento de subscriptions.

### 2. Management Group destino

Se necesita:

```text
Microsoft.management/managementgroups/write
```

### 3. Management Group padre actual

También:

```text
Microsoft.management/managementgroups/write
```

### Excepción: Root Management Group

Si el Management Group actual o el destino es el **Root Management Group**, los requisitos de permisos sobre ese Root no aplican.

Esto se debe a que el Root es el punto de partida de la jerarquía.

---

# 16. Caso especial: Owner heredado

Hay una regla importante cuando el rol **Owner** de una subscription proviene por herencia del Management Group actual.

Ejemplo:

```text
Current MG
   │
   └── Owner
         ↓
   Subscription
```

Si el usuario obtiene Owner de esa manera, solo puede mover la subscription a un Management Group donde también tenga **Owner**.

No puede moverla a un Management Group donde solamente tenga **Contributor**, porque perdería el Owner heredado.

### Si Owner está asignado directamente

Si el usuario tiene:

```text
Owner → directamente en Subscription
```

puede mover la subscription a un Management Group donde tenga **Contributor**.

---

# 17. Agregar una Subscription existente a un Management Group

### Portal

```text
Azure Portal
→ All services
→ Management groups
→ Seleccionar Management Group padre
→ Add subscription
→ Seleccionar Subscription
→ Save
```

Al incorporarla, la subscription comienza a **heredar las políticas y permisos del nuevo Management Group**.

---

# 18. Remover / mover una Subscription

Desde el Management Group actual:

```text
Management Groups
→ Seleccionar Management Group
→ Menú (...) de la Subscription
→ Move
→ Seleccionar Parent Management Group
→ Save
```

Conceptualmente, no se elimina la subscription: se cambia su **Management Group padre**.

---

# 19. Mover Subscriptions con PowerShell

Para agregar una subscription a un Management Group:

```powershell
New-AzManagementGroupSubscription `
    -GroupId 'Contoso' `
    -SubscriptionId '12345678-1234-1234-1234-123456789012'
```

Para quitar el vínculo:

```powershell
Remove-AzManagementGroupSubscription `
    -GroupId 'Contoso' `
    -SubscriptionId '12345678-1234-1234-1234-123456789012'
```

---

# 20. Mover Subscriptions con Azure CLI

Agregar:

```bash
az account management-group subscription add \
    --name 'Contoso' \
    --subscription '12345678-1234-1234-1234-123456789012'
```

Remover:

```bash
az account management-group subscription remove \
    --name 'Contoso' \
    --subscription '12345678-1234-1234-1234-123456789012'
```

---

# 21. Mover Management Groups

Un Management Group también puede cambiar de padre.

Ejemplo:

```text
Antes:

Root
 ├── Corp
 └── Sandbox


Después:

Root
 └── Corp
      └── Sandbox
```

---

## Portal

```text
Azure Portal
→ All services
→ Management groups
→ Seleccionar Management Group padre
→ Add management group
→ Seleccionar Management Group existente
→ Save
```

---

## PowerShell

Primero obtener el Management Group padre:

```powershell
$parentGroup = Get-AzManagementGroup -GroupId ContosoIT
```

Luego mover el Management Group:

```powershell
Update-AzManagementGroup `
    -GroupId 'Contoso' `
    -ParentId $parentGroup.id
```

---

## Azure CLI

```bash
az account management-group update \
    --name 'Contoso' \
    --parent ContosoIT
```

---

# 22. Azure Resource Manager y caché

Azure Resource Manager puede almacenar información de la jerarquía de Management Groups en caché durante **hasta 30 minutos**.

Por eso, después de mover una subscription o Management Group, el cambio puede **no aparecer inmediatamente en Azure Portal**.

---

# 23. Auditoría mediante Activity Log

Los Management Groups están integrados con **Azure Activity Log**.

Se pueden consultar eventos como:

* Cambios de Role Assignments.
* Cambios de Policy Assignments.
* Operaciones realizadas sobre Management Groups.

Esto permite auditar modificaciones de gobernanza desde un punto centralizado.

El scope de un Management Group fuera del portal es:

```text
/providers/Microsoft.Management/managementGroups/{yourMgID}
```

---

# 24. Referenciar Management Groups desde otros Resource Providers

Cuando se referencia un Management Group desde PowerShell, Azure CLI o REST API, se utiliza:

```text
/providers/Microsoft.Management/managementGroups/{yourMgID}
```

Por ejemplo, para asignar un Role:

```powershell
New-AzRoleAssignment `
    -Scope "/providers/Microsoft.Management/managementGroups/Contoso"
```

La misma ruta se utiliza para referenciar otros recursos asociados al Management Group, por ejemplo una Policy Definition:

```text
GET https://management.azure.com/providers/Microsoft.Management/managementgroups/MyManagementGroup/providers/Microsoft.Authorization/policyDefinitions/ResourceNaming?api-version=2019-09-01
```

---

# 25. Auditoría mediante Diagnostic Settings

Los Activity Logs relacionados con Management Groups también pueden enviarse mediante **Diagnostic Settings** a:

* **Log Analytics Workspace**
* **Azure Storage**
* **Azure Event Hub**

Esto permite integrar la auditoría con soluciones de monitoreo y SIEM.

---

# 26. Ejemplo completo de arquitectura

Una organización podría estructurar Azure así:

```text
Tenant
│
└── Root Management Group
    │
    ├── Corp
    │   │
    │   ├── Production
    │   │   ├── Subscription-Prod-01
    │   │   └── Subscription-Prod-02
    │   │
    │   └── Development
    │       ├── Subscription-Dev-01
    │       └── Subscription-Dev-02
    │
    └── Sandbox
        ├── Subscription-Sandbox-01
        └── Subscription-Sandbox-02
```

Podemos aplicar una Policy a `Corp`:

```text
Corp
 └── Policy: Solo regiones autorizadas
```

La Policy se heredará:

```text
Corp
 ↓
Production
 ↓
Subscriptions
 ↓
Resource Groups
 ↓
VMs / Storage / Databases / etc.
```

También podemos asignar RBAC:

```text
Corp
 └── Contributor → Equipo IT
```

El equipo IT heredará Contributor sobre los recursos descendientes.

---

# 27. Conceptos clave para examen

| Concepto              | Punto clave                                             |
| --------------------- | ------------------------------------------------------- |
| **Management Group**  | Scope superior a Subscription                           |
| **Jerarquía**         | Tenant → MG → Subscription → RG → Resource              |
| **Herencia**          | RBAC y Policy bajan por la jerarquía                    |
| **Root MG**           | Único por tenant                                        |
| **Root ID**           | Igual al Microsoft Entra tenant ID                      |
| **Root**              | No se puede mover ni eliminar                           |
| **Máximo MG**         | 10.000 por directorio                                   |
| **Profundidad**       | 6 niveles de MG                                         |
| **Padres**            | Cada MG/Subscription tiene un solo padre                |
| **Hijos**             | Un MG puede tener muchos                                |
| **Custom Role**       | Puede usar MG como `assignableScope`                    |
| **Custom Role scope** | Usar MG **ID**, no display name                         |
| **Delete MG**         | Debe estar vacío                                        |
| **Move**              | Requiere permisos sobre origen, destino y elemento      |
| **Root en Move**      | Excepción a requisitos de permisos                      |
| **Owner heredado**    | El destino debe conservar Owner                         |
| **Owner directo**     | Puede moverse a un MG donde tenga Contributor           |
| **Activity Log**      | Permite auditar operaciones                             |
| **Scope URI**         | `/providers/Microsoft.Management/managementGroups/{id}` |
| **ARM cache**         | Puede tardar hasta 30 minutos                           |

---

# 28. Comandos que conviene recordar

### PowerShell

```powershell
# Listar
Get-AzManagementGroup

# Obtener uno
Get-AzManagementGroup -GroupId 'Contoso'

# Renombrar
Update-AzManagementGroup `
    -GroupId 'ContosoIt' `
    -DisplayName 'Contoso Group'

# Eliminar
Remove-AzManagementGroup -GroupId 'Contoso'

# Agregar subscription
New-AzManagementGroupSubscription `
    -GroupId 'Contoso' `
    -SubscriptionId 'SUBSCRIPTION-ID'

# Quitar subscription
Remove-AzManagementGroupSubscription `
    -GroupId 'Contoso' `
    -SubscriptionId 'SUBSCRIPTION-ID'

# Mover Management Group
$parentGroup = Get-AzManagementGroup -GroupId ContosoIT

Update-AzManagementGroup `
    -GroupId 'Contoso' `
    -ParentId $parentGroup.id

# Role Assignment en Management Group
New-AzRoleAssignment `
    -Scope "/providers/Microsoft.Management/managementGroups/Contoso"
```

### Azure CLI

```bash
# Listar
az account management-group list

# Obtener uno
az account management-group show --name 'Contoso'

# Obtener jerarquía completa
az account management-group show --name 'Contoso' -e -r

# Renombrar
az account management-group update \
    --name 'Contoso' \
    --display-name 'Contoso Group'

# Eliminar
az account management-group delete --name 'Contoso'

# Agregar subscription
az account management-group subscription add \
    --name 'Contoso' \
    --subscription 'SUBSCRIPTION-ID'

# Quitar subscription
az account management-group subscription remove \
    --name 'Contoso' \
    --subscription 'SUBSCRIPTION-ID'

# Mover Management Group
az account management-group update \
    --name 'Contoso' \
    --parent ContosoIT
```

---

# 29. 🧠 Resumen final para memorizar

**Management Groups = gobernanza a escala por encima de las subscriptions.**

```text
Tenant
  ↓
Root Management Group
  ↓
Management Groups
  ↓
Subscriptions
  ↓
Resource Groups
  ↓
Resources
```

Las dos capacidades principales son:

```text
Management Group
      │
      ├── Azure Policy
      │      ↓
      │   Herencia
      │
      └── Azure RBAC
             ↓
          Herencia
```

### Reglas fundamentales

* **1 Root por tenant.**
* **10.000 MG máximo.**
* **6 niveles de MG.**
* **1 solo padre por MG/subscription.**
* **Muchos hijos permitidos.**
* **RBAC y Policy se heredan.**
* **Root no se puede eliminar ni mover.**
* **MG vacío para poder eliminarlo.**
* Para mover un recurso jerárquico se requieren permisos en **origen + destino + elemento**, con excepción del Root.
* Cuidado con **Custom Roles + movimientos**, porque se puede romper el `assignableScope`.
* Para APIs/CLI/PowerShell, el scope es:

```text
/providers/Microsoft.Management/managementGroups/{management-group-id}
```

* **Activity Log** permite auditar cambios.
* Los cambios de jerarquía pueden tardar hasta **30 minutos** en reflejarse por caché de Azure Resource Manager.
