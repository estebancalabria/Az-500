# LAB: Bulk Add Members a una Administrative Unit (Entra ID)

## Objetivo
Aprender a agregar usuarios en masa a una Administrative Unit (AU) usando un archivo CSV, y entender por qué ese archivo requiere el **User Principal Name (UPN)** de cada usuario y no el Object ID.

## Duración estimada
15-20 minutos

## Pre-requisitos
- Cuenta con rol **Groups Administrator** (o superior, ej. Privileged Role Administrator / Global Administrator) en el tenant.
- Al menos 5 usuarios ya creados en el tenant (pueden ser de prueba).
- Acceso a un navegador.

---

## Parte 1: Ingresar al portal

1. Abrí el navegador y andá a **https://entra.microsoft.com**
2. Iniciá sesión con tu cuenta de administrador del tenant (la que tiene el rol Groups Administrator).
3. Si te pide autenticación multifactor (MFA), completala.
4. Vas a caer en el **Microsoft Entra admin center** (el panel principal con el menú a la izquierda).

## Parte 2: Crear la Administrative Unit (si todavía no existe)

5. En el menú de la izquierda, buscá **Identity** (si no lo ves expandido, hacé clic en el ícono de menú ☰ arriba a la izquierda).
6. Dentro de **Identity**, andá a **Roles & admins**.
7. Hacé clic en **Admin units** (Administrative units).
8. Hacé clic en **+ New administrative unit** (arriba a la izquierda de la pantalla).
9. Completá:
   - **Name**: `AU1`
   - **Description**: `AU de prueba para lab de bulk add members` (opcional)
10. Hacé clic en **Review + create**, revisá los datos y hacé clic en **Create**.
11. Esperá la notificación de confirmación (aparece arriba a la derecha) y volvé a la lista de **Admin units**.

> Si la AU ya existe, saltá directo al Parte 3 y buscala en la lista por su nombre.

## Parte 3: Entrar a la AU y descargar el template CSV

12. En la lista de **Admin units**, hacé clic en el nombre de tu AU (`AU1`).
13. En el menú de la izquierda de la AU, hacé clic en **Users**.
14. Arriba de la lista de usuarios vas a ver un botón **Bulk operations**. Hacé clic ahí.
15. Se despliega un submenú: hacé clic en **Bulk add members**.
16. Se abre un panel a la derecha. Ahí vas a ver un link que dice **Download** (para descargar el CSV template). Hacé clic en él.
17. El archivo se descarga a tu carpeta de Descargas, normalmente con un nombre como `BulkAddMembers.csv` o similar.

## Parte 4: Editar el archivo CSV

18. Abrí el archivo descargado con Excel, Google Sheets, o un editor de texto plano (Notepad, VS Code).
19. Vas a ver algo así:

   ```csv
   User Principal Name [userPrincipalName] Required
   Example: azureadmember@yourdomain.onmicrosoft.com
   ```

20. **No borres ni modifiques la primera fila** (el encabezado). Es obligatoria tal cual viene.
21. La segunda fila es un ejemplo — **borrala** y reemplazala por usuarios reales.
22. Agregá una fila por cada usuario que quieras sumar a la AU, usando su **UPN completo** (el mismo que usan para loguearse, tipo `usuario@tudominio.onmicrosoft.com`).

   Ejemplo final del archivo:

   ```csv
   User Principal Name [userPrincipalName] Required
   alumno01@tudominio.onmicrosoft.com
   alumno02@tudominio.onmicrosoft.com
   alumno03@tudominio.onmicrosoft.com
   ```

23. Guardá el archivo, manteniendo el formato **CSV** (si usás Excel: *Guardar como* → tipo *CSV UTF-8*).

## Parte 5: Ejercicio de error intencional (para entender el concepto)

Este paso es opcional pero muy recomendable para que quede claro el punto pedagógico.

24. Andá a **Identity** → **Users** → **All users**.
25. Buscá y abrí uno de los usuarios que ibas a agregar (ej. `alumno01`).
26. En su perfil, buscá el campo **Object ID** y copialo (es un GUID tipo `9832aad8-e4fe-496b-a604-95c6ef01ae75`).
27. Volvé al archivo CSV y reemplazá el UPN de ese usuario por el Object ID que copiaste.
28. Guardá el archivo con ese "error" y subilo siguiendo los pasos de la Parte 6.
29. **Resultado esperado**: el sistema va a rechazar esa fila o marcarla como inválida en la validación, porque el campo espera un UPN, no un Object ID.
30. Corregí el archivo (volvé a poner el UPN correcto) antes de continuar.

## Parte 6: Subir el archivo y confirmar

31. Volvé al panel **Bulk add members** (Parte 3, paso 14-15, si te habías salido).
32. Hacé clic en el área de carga de archivo (**Upload your CSV file** o el botón de carpeta) y seleccioná tu CSV ya corregido.
33. El sistema valida el archivo automáticamente. Si hay errores, te los va a marcar ahí mismo (fila por fila).
34. Si todo está en orden, vas a ver un mensaje de validación exitosa.
35. Hacé clic en **Submit**.
36. Esperá la confirmación (puede tardar unos segundos a un par de minutos según cuántos usuarios sean).

## Parte 7: Verificar el resultado

37. Andá de nuevo a tu AU (**Admin units** → `AU1`) → **Users**.
38. Deberías ver ahí a los usuarios que agregaste por CSV.
39. Si alguno no aparece, revisá el historial de la operación bulk: **Bulk operations** → **Bulk operation results** (o similar, según la versión del portal) para ver el detalle de qué falló y por qué.

---

## Punto pedagógico clave

| Operación | Qué acepta el CSV |
|---|---|
| **Bulk add members a una Administrative Unit** | Solo **UPN** |
| **Bulk import members a un Grupo** | **UPN o Object ID** (cualquiera de los dos) |

Esta diferencia es una trampa clásica en exámenes tipo AZ-104 / SC-300: mezclan ambos flujos a propósito para ver si el alumno memorizó el detalle o entendió el concepto.

## Extensión opcional (para alumnos avanzados)

Repetir el mismo ejercicio pero en un **grupo** (*Groups* → seleccionar grupo → *Members* → *Import members*) usando esta vez el **Object ID**, para que el alumno compare en vivo cómo ahí sí funciona y entienda la diferencia por experiencia directa, no solo de memoria.
