# Module Notes

 * Al registrar una enterprise application en Azure AD desde el portal, automáticamente se crea un **service principal** asociado en ese tenant (es la identidad local que representa a la app y permite asignarle permisos/roles).

* Habilitar PIM por primera vez requiere **Global Administrator** (o Privileged Role Administrator) en Entra ID — es un paso único de activación del servicio, a nivel de directorio. Distinto es el rol que necesita el **service principal de PIM** para gestionar asignaciones de roles en una suscripción: ahí se le debe asignar **User Access Administrator** (rol de Azure RBAC), siguiendo el principio de mínimo privilegio.
