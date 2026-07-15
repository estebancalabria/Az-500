# Laboratorio: Azure Blob Versioning y Change Feed

## Objetivos

En este laboratorio aprenderás a:

- Crear una Storage Account.
- Crear un contenedor de blobs.
- Habilitar **Blob Versioning**.
- Habilitar **Change Feed**.
- Subir y modificar un archivo para generar nuevas versiones.
- Restaurar una versión anterior.
- Explorar el **Change Feed**.
- Leer un archivo **Avro** desde Visual Studio Code.
- Comprender las diferencias entre Versioning y Change Feed.

---

# Duración

45 minutos

---

# Prerrequisitos

- Suscripción de Azure
- Permisos para crear recursos
- Azure Portal
- Visual Studio Code
- Extensión **Avro Viewer** (o cualquier extensión compatible con archivos Avro)

---

# Arquitectura

```
                 Azure Storage Account
                        │
        ┌───────────────┴───────────────┐
        │                               │
 Blob Versioning                 Change Feed
        │                               │
 Guarda el contenido             Guarda eventos
 de cada versión                 de todas las operaciones
```

---

# Escenario

Una empresa almacena documentación técnica en Azure Blob Storage.

Un empleado modifica accidentalmente un archivo importante varias veces y luego lo elimina.

La empresa necesita:

- Recuperar una versión anterior del archivo.
- Auditar exactamente qué ocurrió.

Para ello utilizará:

- Blob Versioning
- Change Feed

---

# Parte 1 - Crear el Resource Group

Desde el Portal de Azure seleccionar:

```
Resource Groups
```

Seleccionar **Create**.

Completar:

| Parámetro | Valor |
|-----------|-------|
| Resource Group | rg-versioning-lab |
| Región | East US |

Seleccionar **Review + Create**.

---

# Parte 2 - Crear la Storage Account

Ir a:

```
Storage Accounts
```

Seleccionar **Create**.

Configurar:

| Parámetro | Valor |
|-----------|-------|
| Resource Group | rg-versioning-lab |
| Nombre | stversioningXXXXX |
| Región | East US |
| Performance | Standard |
| Redundancia | LRS |
| Tipo | StorageV2 |

Crear el recurso.

Esperar a que finalice la implementación.

---

# Parte 3 - Crear un contenedor

Ingresar a la Storage Account.

Ir a:

```
Data Storage
    Containers
```

Seleccionar:

```
+ Container
```

Crear:

| Parámetro | Valor |
|-----------|-------|
| Nombre | documentos |
| Public Access | Private |

---

# Parte 4 - Habilitar Versioning y Change Feed

Antes de subir archivos, habilitaremos las características de protección.

Ir a:

```
Data Management
    Data Protection
```

Activar:

- ✅ Blob Versioning
- ✅ Blob Change Feed

Opcionalmente activar también:

- Soft Delete for Blobs
- Soft Delete for Containers

Guardar los cambios.

> **Importante:** El Change Feed solo registra eventos ocurridos **después** de haber sido habilitado.

---

# Parte 5 - Crear un archivo

Crear localmente un archivo llamado:

```
Manual.txt
```

Contenido:

```text
Versión 1

Procedimiento de instalación.
```

Guardar el archivo.

---

# Parte 6 - Subir el archivo

Ingresar al contenedor **documentos**.

Seleccionar:

```
Upload
```

Subir el archivo.

Esta acción genera:

- un blob
- una entrada en el Change Feed

---

# Parte 7 - Crear nuevas versiones

Editar el archivo.

Contenido:

```text
Versión 2

Procedimiento de instalación.

Paso adicional:
Instalar dependencias.
```

Guardar.

Volver a subir el archivo utilizando exactamente el mismo nombre.

Ahora Azure crea automáticamente una nueva versión.

---

Modificar nuevamente.

Contenido:

```text
Versión 3

Procedimiento actualizado.

Paso 1

Paso 2

Paso 3
```

Guardar.

Volver a subir el archivo.

Ahora existen tres versiones del mismo blob.

---

# Parte 8 - Visualizar las versiones

Abrir el blob.

Seleccionar:

```
Versions
```

Se observará una lista similar a:

| Estado | Fecha |
|---------|--------|
| Current | Hoy |
| Previous Version | Hace unos minutos |
| Previous Version | Hace unos minutos |

Cada vez que el blob fue sobrescrito se creó una nueva versión.

---

# Parte 9 - Restaurar una versión anterior

Seleccionar la primera versión.

Elegir:

```
Make Current Version
```

Confirmar.

Descargar nuevamente el archivo.

El contenido volverá a ser:

```text
Versión 1

Procedimiento de instalación.
```

Azure no elimina las demás versiones.

Simplemente crea una nueva versión basada en la que acabamos de restaurar.

---

# Parte 10 - Eliminar el blob

Eliminar el archivo.

Si Soft Delete está habilitado podrá recuperarse.

Además, el Change Feed registrará el evento de eliminación.

---

# Parte 11 - Explorar el Change Feed

Ingresar nuevamente a la Storage Account.

Ir a:

```
Storage Browser (Preview)
```

o utilizar **Azure Storage Explorer**.

Buscar el contenedor especial:

```
$blobchangefeed
```

Dentro encontraremos una estructura similar a:

```
$blobchangefeed

    idx

    log

        00

            2026

                07

                    14

                        00000.avro
```

Los archivos con extensión **.avro** contienen todos los eventos registrados por Azure.

---

# Parte 12 - Abrir el archivo Avro

Descargar uno de los archivos **.avro**.

Abrirlo con **Visual Studio Code** utilizando una extensión compatible con Avro.

Se observarán registros similares a:

```json
{
  "eventType": "BlobCreated",
  "eventTime": "2026-07-14T22:30:41Z",
  "subject": "/blobServices/default/containers/documentos/blobs/Manual.txt",
  "data": {
    "api": "PutBlob",
    "contentLength": 44,
    "blobType": "BlockBlob"
  }
}
```

También podrán observarse eventos como:

- BlobCreated
- BlobDeleted
- BlobPropertiesUpdated
- BlobTierChanged
- BlobSnapshotCreated

Cada registro representa una operación realizada sobre algún blob de la Storage Account.

---

# Parte 13 - ¿Qué registra cada característica?

Supongamos que ocurre la siguiente secuencia:

```
10:00 Crear Manual.txt

10:05 Editar Manual.txt

10:10 Editar Manual.txt

10:20 Eliminar Manual.txt
```

## Blob Versioning

Blob Versioning conserva el contenido del archivo.

```
Versión 1

↓

Versión 2

↓

Versión 3
```

Podemos volver a cualquiera de esas versiones.

---

## Change Feed

El Change Feed registra únicamente los eventos.

```
10:00 BlobCreated

10:05 BlobModified

10:10 BlobModified

10:20 BlobDeleted
```

No almacena el contenido del archivo.

Solo conserva el historial de operaciones.

---

# Diferencias entre Versioning y Change Feed

| Blob Versioning | Change Feed |
|-----------------|-------------|
| Guarda el contenido de cada versión del blob. | Guarda un historial inmutable de eventos. |
| Permite restaurar archivos. | Permite realizar auditorías. |
| Se crea una nueva versión cuando un blob se sobrescribe. | Se registra un evento por cada operación realizada. |
| Consume almacenamiento proporcional al tamaño de cada versión. | Consume poco espacio porque solo almacena metadatos de los eventos. |
| Responde a "¿cómo era el archivo?". | Responde a "¿qué ocurrió con el archivo?". |

---

# Analogía

Imaginemos un documento de Word.

## Versioning

Es como guardar una copia del documento cada vez que hacemos cambios.

Podemos abrir cualquier versión anterior.

## Change Feed

Es como el historial de actividad que dice:

- 10:00 Se creó el documento.
- 10:10 Se modificó.
- 10:15 Se volvió a modificar.
- 10:20 Se eliminó.

Pero nunca almacena el contenido del documento.

---

# Preguntas

## ¿Qué operación crea una nueva versión?

- Crear un blob
- Leer un blob
- **Modificar un blob existente**
- Descargar un blob

Respuesta:

**Modificar un blob existente.**

---

## ¿Las versiones ocupan espacio?

Sí.

Cada versión almacena una copia del contenido.

---

## ¿El Change Feed guarda el archivo?

No.

Solo registra los eventos.

---

## ¿Versioning reemplaza al Backup?

No.

Versioning protege frente a modificaciones accidentales.

No reemplaza una estrategia de respaldo ni recuperación ante desastres.

---

## ¿Cuál utilizar?

La respuesta es:

**Ambos.**

Versioning protege los datos.

Change Feed permite auditar todo lo que sucede.

En entornos empresariales es habitual habilitar ambas características.

---

# Desafío

Realizar las siguientes tareas:

1. Crear una cuarta versión del archivo.
2. Restaurar la segunda versión.
3. Eliminar el blob.
4. Esperar algunos minutos.
5. Revisar nuevamente el Change Feed.
6. Identificar todos los eventos generados.
7. Contar cuántas versiones del blob existen al finalizar el laboratorio.

---

# Conclusión

En este laboratorio se implementaron dos de las principales funcionalidades de protección de datos de Azure Blob Storage.

- **Blob Versioning** permitió conservar múltiples versiones del archivo y restaurar una versión anterior cuando fue necesario.
- **Change Feed** registró todas las operaciones realizadas sobre el blob en archivos Avro, proporcionando un historial de auditoría inmutable.

Aunque ambas características registran cambios, cumplen objetivos diferentes y complementarios: **Versioning protege el contenido de los datos**, mientras que **Change Feed registra el historial de las operaciones**. En escenarios empresariales es una práctica recomendada habilitar ambas para mejorar la resiliencia y la trazabilidad de la información.
