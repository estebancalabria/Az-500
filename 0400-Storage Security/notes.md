# Notes

* Para que cada app pueda leer solo lo que escribió, hay que asignarle a cada una una **identidad administrada (system-assigned)** y configurar storage1 para usar **autenticación de Azure AD**; luego se otorgan roles RBAC de Storage (por ej. Storage Blob Data Contributor) con scope acotado al contenedor/blob de cada app, evitando compartir claves de acceso comunes a ambas.
