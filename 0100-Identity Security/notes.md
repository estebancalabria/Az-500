# Module Notes

 * Al registrar una enterprise application en Azure AD desde el portal, automáticamente se crea un **service principal** asociado en ese tenant (es la identidad local que representa a la app y permite asignarle permisos/roles).

* Habilitar PIM por primera vez requiere **Global Administrator** (o Privileged Role Administrator) en Entra ID — es un paso único de activación del servicio, a nivel de directorio. Distinto es el rol que necesita el **service principal de PIM** para gestionar asignaciones de roles en una suscripción: ahí se le debe asignar **User Access Administrator** (rol de Azure RBAC), siguiendo el principio de mínimo privilegio.

* La opción "token encryption" solo se habilita en el portal después de **subir un certificado** (X.509) para la app; sin certificado cargado, el toggle permanece deshabilitado/no disponible.

* Para que una app pueda acceder a Microsoft Graph y modificar propiedades de usuarios en Azure AD, primero hay que crear un **app registration** en Azure AD: eso genera la identidad de la app y permite luego configurarle permisos de API (por ej. User.ReadWrite.All) y credenciales.

* Azure AD Identity Protection con user risk policy y sign-in risk policy requiere licencias **Azure AD Premium P2**; con Plan 1 no está disponible, por lo que primero hay que **comprar licencias Premium P2** para los usuarios.

* Para probar el impacto de una Conditional Access policy sin afectar a los usuarios, la policy debe estar en **Report-only**: en ese modo se registran los resultados de evaluación (incluido lo que arroja la herramienta What If) pero no se aplica el bloqueo/enforcement real.
