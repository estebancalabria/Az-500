# Laboratorios : Seguridad en Bases de Datos

Pasos
1. Crear un Resource Group
2. Crear un Motor SQL Server
   * En la parte de networking habilitar cuales son  las IP que se pueden conectar al servidor
   * Habilitar el Defender para ver Recomendaciones de Seguridad y Analisis de Vulnerabilidad
4. Crear una Base de datos de SQL Server
  * Levanta la base de datos de ejemplo AdvetureWorksLTD
5. Configurar la proteccion de los datos
   * Ir a la opcion Security/"Data Discovery & Classification" y aceptar la clasificacion de columnas sensibles que ofrece
6. Crear un Log Analytics Workspace
7. Crear un Storage Account
8. Configurar Auditoria sobre las columnas anteriores
9. Tratar de hacer un login fallido y un select sobre la tabla de Customers
10. Verlo los logs de auditoria
