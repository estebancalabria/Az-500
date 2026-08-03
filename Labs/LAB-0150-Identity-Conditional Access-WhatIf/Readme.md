# Laboratorio: Simulación de políticas con la herramienta "What If" de Conditional Access

**Curso:** AZ-500 – Microsoft Azure Security Technologies
**Duración estimada:** 45-60 minutos
**Módulo relacionado:** Identity and Access Management → Conditional Access

## Objetivos

Al finalizar este laboratorio, el estudiante podrá:

- Crear una política de Conditional Access en modo Report-only.
- Usar la herramienta What If para simular el resultado de las políticas sin afectar usuarios reales.
- Interpretar por qué una política se aplica, no se aplica, o queda excluida en un escenario dado.
- Validar una política antes de activarla en producción.

## Prerequisitos

- Tenant de Microsoft Entra ID con licencia Entra ID P1 o P2 (o trial).
- Al menos 2 usuarios de prueba (uno que caerá dentro del scope de la política, otro que quedará excluido).
- Un grupo de seguridad para agrupar a los usuarios de prueba.
- Rol de **Conditional Access Administrator** o **Security Administrator**.

> Nota: si el tenant es compartido (por ejemplo, Xtreme Labs) y los permisos están restringidos, confirmá antes con el administrador que podés crear políticas de Conditional Access; en tenants de laboratorio compartidos suele estar bloqueado por diseño.

## Parte 1 — Preparar el escenario

1. Andá a **Entra admin center** → **Identity** → **Users** → **Groups** y creá un grupo de seguridad llamado `CA-Lab-Finance`.
2. Agregá al Usuario A (el que sí debe verse afectado) a ese grupo. Dejá al Usuario B fuera.
3. Andá a **Protection** → **Conditional Access** → **Policies** → **New policy**.
4. Configurá la política así:
   - **Nombre:** `LAB - Require MFA for Finance app`
   - **Users:** Include → grupo `CA-Lab-Finance`
   - **Target resources:** Cloud apps → seleccioná una app de prueba (por ejemplo, Office 365 o una app registrada de laboratorio)
   - **Conditions → Locations:** Include → Any location; Exclude → tu ubicación confiable (named location, si ya tenés una configurada)
   - **Grant:** Require multifactor authentication
   - **Enable policy:** **Report-only**
5. Guardá la política. En modo Report-only, la política se evalúa pero **no bloquea a nadie** — queda solo para auditoría en sign-in logs.

## Parte 2 — Simular con What If

1. En el mismo blade de **Conditional Access**, hacé clic en la pestaña **What If**.
2. Completá el escenario 1:
   - **User:** Usuario A
   - **Cloud apps:** la app que target·easte en la política
   - **Sign-in risk:** None
   - **Device platform:** Windows
   - **Client app:** Browser
3. Hacé clic en **What If**. Revisá el resultado:
   - ¿Aparece la política `LAB - Require MFA for Finance app` en la lista de **"Policies that will apply"**?
   - Anotá el grant control resultante.
4. Repetí el escenario, esta vez con **Usuario B** (el que no está en el grupo). Confirmá que la política aparece en **"Policies that will not apply"** y revisá el motivo que muestra la herramienta.
5. Repetí una tercera simulación con Usuario A pero cambiando la ubicación (IP address) a una dirección dentro del named location excluido. Confirmá que ahora la política tampoco se aplica, y que el motivo listado es la exclusión de ubicación.

## Parte 3 — Escenario con riesgo (si el tenant tiene Entra ID P2)

1. Volvé a la pestaña What If.
2. Simulá con Usuario A, **Sign-in risk: High**.
3. Observá si aparecen políticas adicionales relacionadas a Identity Protection (si hay alguna configurada en el tenant) además de la política del laboratorio.

## Parte 4 — De Report-only a producción

1. Andá a **Sign-in logs** → filtrá por el Usuario A → pestaña **Conditional Access** dentro del detalle del sign-in. Confirmá que la política aparece marcada como "Report-only: Success/Failure" (según haya cumplido MFA o no).
2. Volvé a la política y cambiá **Enable policy** de Report-only a **On**.
3. (Opcional) Repetí la simulación en What If: el resultado no cambia, porque What If evalúa la lógica de la política independientemente del estado Report-only/On — la diferencia real está en el enforcement real durante un sign-in.
