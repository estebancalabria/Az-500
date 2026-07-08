# Lab 0810: Alerta de Activity Log — Detección de eliminación de un NSG

**Objetivo:** Crear una alerta que avise por mail cuando se borra o modifica un NSG, y verla dispararse en vivo. Todo desde el portal, paso a paso.

**Duración estimada:** 20-25 min

**Prerrequisitos:** una suscripción de Azure activa con permisos para crear resource groups, NSGs y alertas.

---

## Paso 1 — Crear el Resource Group

1. En la barra de búsqueda de arriba, escribir **Resource groups**
2. Clic en **+ Create**
3. Completar:
   - **Subscription**: la tuya
   - **Resource group name**: `rg-az500-lab`
   - **Region**: la que uses normalmente (ej. East US)
4. Clic en **Review + create** → **Create**
5. Esperar el aviso de "Deployment succeeded"

---

## Paso 2 — Crear el Action Group (quién recibe el aviso)

1. Entrar a [portal.azure.com](https://portal.azure.com)
2. En la barra de búsqueda de arriba, escribir **Monitor** y hacer clic en el servicio
3. En el menú de la izquierda, clic en **Alerts**
4. Arriba, clic en **Action Groups**
5. Clic en **+ Create**
6. Completar:
   - **Subscription**: la tuya
   - **Resource group**: `rg-az500-lab`
   - **Region**: Global
   - **Action group name**: `ag-az500-lab`
   - **Display name**: `az500lab`
7. Clic en **Next: Notifications >**
8. En **Notification type** elegir **Email/SMS message/Push/Voice**
9. Se abre un panel: tildar **Email**, cargar tu dirección de mail, clic en **OK**
10. Clic en **Review + create**
11. Clic en **Create**

---

## Paso 3 — Crear un NSG de prueba (el recurso que vamos a borrar después)

1. En la barra de búsqueda, escribir **Network security groups**
2. Clic en **+ Create**
3. Completar:
   - **Resource group**: `rg-az500-lab`
   - **Name**: `nsg-demo-alert`
   - **Region**: la misma que uses normalmente
4. Clic en **Review + create** → **Create**
5. Esperar el aviso de "Deployment succeeded"

---

## Paso 4 — Crear la Alert Rule sobre Activity Log

1. Volver a **Monitor** (buscarlo de nuevo si cerraste la pestaña)
2. Menú izquierdo → **Alerts**
3. Arriba, clic en **+ Create** → **Alert rule**
4. En **Scope**, clic en **Select scope**
   - Elegir tu suscripción (o filtrar por `rg-az500-lab`)
   - Clic en **Apply**
5. En **Condition**, clic en **Add condition**
6. Se abre una lista de signals, arriba hay un filtro **Signal type** → elegir **Activity Log**
7. Buscar en la lista y seleccionar **Delete Network Security Group**
8. Clic en **Done** (no hay que tocar ningún threshold, este tipo de señal se dispara solo con que el evento ocurra)
9. Clic en **Next: Actions >**
10. Clic en **Select action groups** → tildar `ag-az500-lab` → **Select**
11. Clic en **Next: Details >**
12. Completar:
    - **Alert rule name**: `alert-nsg-delete`
    - **Severity**: **Sev 1**
13. Clic en **Review + create** → **Create**

---

## Paso 5 — Disparar la alerta en vivo (borrar el NSG)

1. Esperar 1-2 minutos para que la regla quede activa
2. Buscar **Network security groups** en la barra de búsqueda
3. Clic en `nsg-demo-alert`
4. Arriba, clic en **Delete**
5. Confirmar el nombre escribiéndolo si el portal lo pide, clic en **Delete**

---

## Paso 6 — Ver la alerta disparada

1. Ir de nuevo a **Monitor** → **Alerts**
2. Esperar 1-3 minutos y refrescar (botón de refresh arriba)
3. Debería aparecer una fila con estado **Fired**, alert rule `alert-nsg-delete`
4. Clic en la alerta para ver el detalle: quién la ejecutó (**Caller**), hora exacta, y el recurso afectado
5. Revisar la casilla de mail: debería haber llegado la notificación del Action Group

---

## Nota para la clase

Este flujo (Action Group → Alert Rule sobre Activity Log → disparo real) es ideal para mostrar cómo Azure audita y alerta sobre cambios de seguridad sin depender de métricas ni agentes instalados — justo el enfoque de detección que pide AZ-500.

---

# Parte 2 — Alerta basada en métricas (CPU de una VM)

**Objetivo:** Alertar cuando la CPU de una VM supera el 80%, y generar carga real para verla dispararse.

**Diferencia con la Parte 1:** la alerta de Activity Log se dispara al toque con el evento (control plane). La de métricas necesita juntar datos numéricos durante una ventana de tiempo antes de evaluar la condición (data plane) — por eso tarda más en dispararse.

---

## Paso 7 — Crear una VM de prueba

1. Buscar **Virtual machines** en la barra de arriba
2. Clic en **+ Create** → **Azure virtual machine**
3. Completar:
   - **Resource group**: `rg-az500-lab`
   - **Virtual machine name**: `vm-demo-alert`
   - **Region**: la misma que usás siempre
   - **Image**: Ubuntu Server (más fácil para generar carga con un comando)
   - **Size**: la más chica/barata que ofrezca (ej. B1s)
   - **Authentication type**: Password (más simple para el lab) o SSH key si preferís
4. En la pestaña **Networking**, dejar todo por defecto
5. Clic en **Review + create** → **Create**
6. Esperar el deployment (2-3 min)

---

## Paso 8 — Crear la Alert Rule de métrica

1. Ir a **Monitor** → **Alerts** → **+ Create** → **Alert rule**
2. En **Scope**, clic en **Select scope**
   - Elegir `vm-demo-alert`
   - Clic en **Apply**
3. En **Condition**, clic en **Add condition**
4. Seleccionar **Percentage CPU** de la lista de signals (tipo Metrics)
5. Se abre un panel de configuración:
   - **Threshold**: Static
   - **Operator**: Greater than
   - **Aggregation type**: Average
   - **Threshold value**: `80`
   - **Check every**: 1 minute
   - **Lookback period**: 5 minutes
6. Clic en **Done**
7. Clic en **Next: Actions >**
8. Clic en **Select action groups** → tildar `ag-az500-lab` (el mismo de la Parte 1) → **Select**
9. Clic en **Next: Details >**
10. Completar:
    - **Alert rule name**: `alert-vm-cpu-high`
    - **Severity**: **Sev 2**
11. **Review + create** → **Create**

---

## Paso 9 — Generar carga para disparar la alerta

1. Conectarse a la VM por SSH (o usar **Connect → Bastion** desde el portal si no tenés IP pública configurada)
2. Ejecutar:
```bash
sudo apt update
sudo apt install -y stress-ng
stress-ng --cpu 1 --timeout 600s
```
Esto satura la CPU al 100% durante 10 minutos.

---

## Paso 10 — Ver la alerta disparada

1. Esperar unos 5-7 minutos (la métrica necesita juntar datos y cruzar el threshold en la ventana de evaluación)
2. Ir a **Monitor → Alerts** y refrescar
3. Debería aparecer `alert-vm-cpu-high` en estado **Fired**
4. Cortar el `stress-ng` con Ctrl+C (o dejar que termine solo a los 600s) y ver cómo después de un rato la alerta pasa a **Resolved**

---

## Nota para la clase (Parte 2)

Conviene arrancar el `stress-ng` apenas empieza el bloque práctico, mientras se explica la teoría, para que la métrica ya esté "cocinando" cuando se llega a mostrar el resultado — esta alerta tarda más que la de Activity Log y puede fallar en vivo si el tiempo de clase es corto.

---

## Limpieza de recursos

Para no dejar recursos huérfanos facturando, borrar el resource group completo al terminar (esto elimina NSG, VM y todo lo creado en ambas partes):

1. Buscar **Resource groups**
2. Clic en `rg-az500-lab`
3. Clic en **Delete resource group**
4. Escribir el nombre para confirmar → **Delete**
