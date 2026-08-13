
# Etiquetado en Microsoft Purview Data Map

El etiquetado en Microsoft Purview Data Map está actualmente en **PREVIEW**.

La aplicación de etiquetas de sensibilidad al contenido permite mantener los datos seguros indicando qué nivel de sensibilidad tienen dentro de la organización.

Las etiquetas abstraen los datos, permitiendo realizar un seguimiento del tipo de información sin exponer los datos confidenciales en otra plataforma.

Por ejemplo, una etiqueta **Highly Confidential** aplicada a un documento que contiene números de seguridad social y números de tarjetas de crédito permite identificar la sensibilidad del documento sin conocer los datos reales contenidos.

## Beneficios del etiquetado en Microsoft Purview

Microsoft Purview permite aplicar etiquetas de sensibilidad a los activos para clasificar y proteger los datos.

### La etiqueta viaja con los datos

Las etiquetas de sensibilidad creadas en Microsoft Purview Information Protection también pueden extenderse a:

* Microsoft Purview Data Map.
* SharePoint.
* Teams.
* Power BI.
* SQL.

Cuando se aplica una etiqueta a un documento de Office y posteriormente se escanea en Microsoft Purview Data Map, la etiqueta se aplica al activo de datos.

En Microsoft Purview Information Protection la etiqueta se aplica al archivo real, mientras que en Microsoft Purview Data Map se agrega como metadato.

Aunque existen diferencias en cómo se aplica una etiqueta a un activo entre distintos servicios y aplicaciones, las etiquetas viajan con los datos y son reconocidas por los servicios a los que se extienden.

### Visión general del patrimonio de datos

Microsoft Purview proporciona información sobre el patrimonio de datos mediante informes predefinidos.

Cuando los datos se escanean en Microsoft Purview Data Map, los informes se alimentan con información sobre:

* Activos existentes.
* Historial de escaneos.
* Clasificaciones encontradas.
* Etiquetas aplicadas.
* Términos del glosario.

### Etiquetado automático

Las etiquetas pueden aplicarse automáticamente según la sensibilidad de los datos.

Cuando un activo se escanea para detectar datos confidenciales, las reglas de etiquetado automático determinan qué etiqueta de sensibilidad aplicar.

Se pueden crear reglas de etiquetado automático para cada etiqueta de sensibilidad, definiendo qué clasificación o tipo de información confidencial constituye esa etiqueta.

### Aplicar etiquetas a archivos y columnas de bases de datos

Las etiquetas pueden aplicarse a:

* Archivos almacenados en Azure Data Lake o Azure Files.
* Datos estructurados, como columnas de Azure SQL Database.

Las **etiquetas de sensibilidad** son etiquetas que pueden aplicarse a los activos para clasificar y proteger los datos.

## Cómo aplicar etiquetas a los activos en Microsoft Purview Data Map

1. Crear nuevas etiquetas de sensibilidad o aplicar etiquetas existentes en el portal de cumplimiento de Microsoft Purview. La creación de etiquetas incluye reglas de etiquetado automático que determinan qué etiqueta debe aplicarse según las clasificaciones encontradas en los datos.
2. Registrar y escanear el activo en Microsoft Purview Data Map.
3. Microsoft Purview aplica las clasificaciones: cuando se programa un escaneo, Microsoft Purview analiza el tipo de datos del activo y aplica las clasificaciones en Data Map. La aplicación de clasificaciones se realiza automáticamente.
4. Microsoft Purview aplica las etiquetas: una vez encontradas las clasificaciones en un activo, Microsoft Purview aplica las etiquetas según las reglas de etiquetado automático. La aplicación de etiquetas también se realiza automáticamente siempre que se hayan creado las etiquetas con sus reglas correspondientes.

### Reglas de etiquetado automático

Las **autolabeling rules** son condiciones que determinan cuándo debe aplicarse una etiqueta específica.

Cuando se cumplen las condiciones, la etiqueta se asigna automáticamente a los datos.

Al crear las etiquetas, se deben definir reglas de etiquetado automático tanto para:

* Archivos.
* Columnas de bases de datos.

De esta manera, las etiquetas se aplican automáticamente con cada escaneo.

## Etiquetado para bases de datos SQL

Además del etiquetado de activos de datos estructurados de Microsoft Purview Data Map, Microsoft admite el etiquetado de columnas de bases de datos SQL mediante la clasificación de datos SQL en SQL Server Management Studio (SSMS).

* Microsoft Purview utiliza las etiquetas de sensibilidad globales.
* SSMS utiliza únicamente etiquetas definidas localmente.

El etiquetado en Microsoft Purview y el etiquetado en SSMS son procesos independientes y actualmente no interactúan entre sí.

Por lo tanto:

* Las etiquetas aplicadas en SSMS no aparecen en Microsoft Purview.
* Las etiquetas aplicadas en Microsoft Purview no aparecen en SSMS.

Se recomienda Microsoft Purview para etiquetar bases de datos SQL porque las etiquetas pueden aplicarse globalmente en múltiples plataformas.
