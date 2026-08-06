# Notes

* Cuando se necesita forzar manualmente por dónde pasa el tráfico, el control lo tiene quien define la ruta, no el sistema por defecto.

* Cuando se exporta la clave privada del servidor on-premises para migrar el certificado a un web app en Azure, el archivo PFX debe ir cifrado con TripleDES, no con AES256, porque es el único algoritmo que Azure App Service acepta para certificados privados.
    * Fuente: https://learn.microsoft.com/azure/app-service/configure-ssl-certificate#private-certificate-requirements
