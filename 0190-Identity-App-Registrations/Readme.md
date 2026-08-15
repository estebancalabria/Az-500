# Microsoft Entra ID: Administración del acceso a aplicaciones empresariales

## Objetivo

Administrar y proteger el acceso a aplicaciones empresariales mediante Microsoft Entra ID, asegurando que solo los usuarios y aplicaciones autorizados puedan acceder a los recursos.

El módulo aborda:

* Administración del acceso a aplicaciones empresariales y configuración de concesiones de permisos OAuth.
* Administración de registros de aplicaciones.
* Configuración de ámbitos de permisos (*permission scopes*).
* Administración del consentimiento de permisos.
* Administración y uso de *service principals*.
* Administración de identidades administradas para recursos de Azure.
* Configuración y uso de Microsoft Entra Application Proxy para acceso remoto seguro a aplicaciones locales.

---

## Administrar el acceso a aplicaciones empresariales en Microsoft Entra ID

Microsoft Entra ID permite administrar el acceso a aplicaciones mediante diferentes modelos, incluyendo RBAC, ABAC, asignaciones directas, grupos y delegación de decisiones. También integra informes sobre asignaciones, errores de asignación y uso de las aplicaciones.

### Asignación de usuarios y grupos

Existen dos modalidades principales:

* **Asignación individual:** un administrador con permisos de Global Administrator puede asignar usuarios individualmente a una aplicación.
* **Asignación basada en grupos:** requiere Microsoft Entra ID P1 o P2. Se asigna un grupo a la aplicación y los usuarios obtienen acceso según pertenezcan al grupo. Pueden utilizarse grupos dinámicos basados en atributos, grupos sincronizados desde sistemas externos o grupos administrados por administradores o mediante autoservicio.

Un mismo grupo puede asignarse a varias aplicaciones para reutilizar las reglas de acceso.

### Requerir asignación de usuarios

Algunas aplicaciones permiten exigir que los usuarios estén asignados explícitamente para poder iniciar sesión.

Aplica a:

* Aplicaciones con SSO federado mediante SAML.
* Aplicaciones de Application Proxy con preautenticación de Microsoft Entra.
* Aplicaciones que utilizan OAuth 2.0/OpenID Connect después del consentimiento de usuario o administrador.

Cuando se requiere asignación:

* Solo los usuarios asignados directamente o mediante grupos pueden iniciar sesión.
* Pueden acceder desde **My Apps** o mediante un enlace directo.
* El consentimiento de usuario no está permitido, incluso si normalmente estaría habilitado.
* Se debe conceder **consentimiento administrativo para toda la organización**.

Si la opción no está disponible en las propiedades de la aplicación, puede configurarse mediante PowerShell usando `appRoleAssignmentRequired` en el *service principal*.

Cuando no se requiere asignación, los usuarios no asignados pueden no visualizar la aplicación en My Apps, pero todavía pueden acceder mediante inicio de sesión iniciado por el servicio o mediante la URL de acceso del usuario.

### Experiencia de acceso

Las aplicaciones pueden ponerse a disposición mediante:

* Microsoft Entra My Apps.
* Lanzador de aplicaciones de Microsoft 365.
* Inicio de sesión directo en aplicaciones federadas.
* Enlaces directos a aplicaciones federadas, basadas en contraseña o existentes.

### Aplicaciones de Microsoft

Las aplicaciones publicadas por Microsoft pueden recibir acceso mediante:

1. **Asignación de licencias:** directamente al usuario o mediante grupos.
2. **Consentimiento del usuario:** para aplicaciones disponibles gratuitamente.
3. **Consentimiento del administrador:** el administrador autoriza el uso de la aplicación para toda la organización.

Una aplicación puede combinar varios mecanismos.

Las aplicaciones de Microsoft 365 pueden mostrarse u ocultarse en My Apps mediante la configuración de visibilidad correspondiente.

---

## Registrar aplicaciones en Microsoft Entra ID

Al registrar una aplicación:

* Se crea un **application object** y un **service principal** en el tenant de origen.
* El *service principal* representa la identidad de la aplicación en el tenant.
* Los recursos a los que puede acceder la aplicación están restringidos por los roles asignados al *service principal*.
* Para herramientas automatizadas se recomienda utilizar *service principals* en lugar de identidades de usuario.
* Cuando el código se ejecuta en un servicio compatible con identidades administradas y accede a recursos compatibles con autenticación de Microsoft Entra, se recomienda utilizar **managed identities**.

### Registro de una aplicación

1. Ingresar al Microsoft Entra admin center con al menos el rol **Cloud Application Administrator**.
2. Ir a **Identity > Applications > App registrations > New registration**.
3. Especificar el nombre.
4. Seleccionar el tipo de cuenta compatible.
5. Para aplicaciones web, configurar el **Redirect URI** de tipo Web.
6. Registrar la aplicación.

Para registrar aplicaciones se requiere una cuenta de Microsoft Entra y permisos suficientes, incluyendo `Application.ReadWrite.All`.

### Asignar permisos Azure a la aplicación

Para que la aplicación acceda a recursos de una suscripción:

1. Seleccionar el ámbito: **subscription**, **resource group** o **resource**.
2. Ir a **Access control (IAM)**.
3. Seleccionar **Add role assignment**.
4. Elegir el rol necesario.
5. Asignarlo al **service principal** de la aplicación.

Los permisos asignados a un ámbito se heredan en los niveles inferiores.

El *service principal* puede administrarse desde **Enterprise applications**, donde se pueden revisar permisos, consentimientos y actividad de inicio de sesión.

### Autenticación del service principal

Para autenticación programática se utilizan:

* **Directory (tenant) ID**
* **Application (client) ID**
* Un certificado o un secreto.

Métodos disponibles:

* **Certificado confiable:** opción recomendada.
* **Certificado autofirmado:** únicamente para pruebas.
* **Client secret:** alternativa cuando no se utiliza certificado.

Los certificados y secretos se administran desde **Certificates & secrets**. El valor de un client secret se muestra una sola vez y debe almacenarse de forma segura.

### Acceso adicional a recursos

Además de los roles Azure, determinados recursos pueden requerir permisos específicos. Por ejemplo, un Key Vault puede necesitar una política de acceso que otorgue al *service principal* permisos sobre claves, secretos o certificados.

---

## Configurar ámbitos de permisos de una app registration

Para exponer una Web API y proporcionar acceso basado en permisos:

1. Registrar la Web API en Microsoft Entra ID.
2. Asignar propietarios a la API y a la aplicación cliente.
3. Crear los **app roles** necesarios.
4. Configurar **Expose an API**.
5. Definir los **scopes** que podrán solicitar las aplicaciones cliente.

### App roles

Un *app role* define permisos para la aplicación.

Puede configurarse:

* **Display name:** nombre del rol.
* **Allowed member types:** usuarios/grupos y/o aplicaciones.
* **Value:** valor incluido en el claim `roles` del token.
* **Description:** descripción del permiso.

### Scopes

Los scopes proporcionan acceso granular a operaciones de una Web API.

Para crear uno:

1. Ir a **App registrations > Expose an API**.
2. Configurar el **Application ID URI**.
3. Seleccionar **Add a scope**.
4. Definir:

   * Nombre del scope.
   * Quién puede consentirlo: usuarios y administradores o solo administradores.
   * Descripción y nombre visibles para administradores.
   * Descripción y nombre visibles para usuarios.
5. Establecerlo como **Enabled**.

Los scopes de mayor privilegio pueden configurarse para requerir exclusivamente consentimiento administrativo.

La cadena completa del scope combina el **Application ID URI** y el nombre del scope. Por ejemplo:

`https://contoso.com/api/Employees.Read.All`

### Preautorización

Una aplicación cliente confiable puede ser **preautorizada** para determinados scopes.

Esto evita que los usuarios reciban solicitudes de consentimiento para esos permisos. Solo deben preautorizarse aplicaciones cliente confiables.

---

## Administrar el consentimiento de permisos

El consentimiento permite que usuarios o administradores autoricen a una aplicación a acceder a recursos protegidos.

Existen dos escenarios principales:

### Acceso delegado

La aplicación actúa **en nombre de un usuario conectado**.

* Utiliza **delegated permissions**, también llamadas **scopes**.
* El cliente y el usuario deben estar autorizados.
* La aplicación nunca puede acceder a recursos que el usuario no podría acceder por sí mismo.
* Los permisos del usuario pueden depender de Microsoft Entra RBAC o de otros sistemas de autorización.

Ejemplo: si una aplicación tiene `Files.Read.All` como permiso delegado para un usuario, solo puede acceder a los archivos que ese usuario puede acceder.

### Acceso solo de aplicación

La aplicación actúa **sin un usuario conectado**.

Es apropiado para:

* Automatización.
* Backups.
* Servicios en segundo plano.
* Daemons.

Utiliza **app roles**, también llamados *application permissions*.

La aplicación puede acceder a los datos asociados al permiso, independientemente de un usuario conectado. El consentimiento para permisos de aplicación solo puede ser otorgado por un administrador o propietario del *service principal*.

### Comparación

| Característica          | Permisos delegados                         | Permisos de aplicación                       |
| ----------------------- | ------------------------------------------ | -------------------------------------------- |
| Contexto                | En nombre de un usuario                    | Sin usuario                                  |
| Aplicaciones            | Web, móvil, SPA                            | Web, daemon                                  |
| Permisos                | Scopes / OAuth2 permission scopes          | App roles / application permissions          |
| Consentimiento          | Usuario o administrador                    | Solo administrador                           |
| Acceso                  | Limitado a lo que puede acceder el usuario | Según los permisos otorgados a la aplicación |
| Consentimiento dinámico | Sí                                         | No                                           |

### Consentimiento del usuario

Cuando un usuario inicia sesión y no existe un consentimiento previo para los permisos solicitados, puede aparecer una pantalla de consentimiento.

También puede aparecer cuando:

* Se revoca un consentimiento anterior.
* La aplicación solicita explícitamente consentimiento durante el inicio de sesión.
* La aplicación utiliza consentimiento dinámico para solicitar nuevos permisos.

La solicitud muestra los permisos requeridos y la información del publicador.

### Consentimiento administrativo

Algunos permisos requieren consentimiento de administrador, especialmente:

* Permisos de aplicación.
* Muchos permisos delegados de alto privilegio.

El administrador puede conceder consentimiento para sí mismo o para toda la organización.

### Preautorización

La preautorización permite al propietario de una aplicación de recursos autorizar previamente a una aplicación cliente, evitando que los usuarios reciban nuevamente una solicitud de consentimiento para esos permisos.

---

## Administrar y utilizar Service Principals

### Application object

El **application object** es la representación global de una aplicación en su tenant de origen.

Define:

* Cómo se pueden emitir tokens para acceder a la aplicación.
* Qué recursos puede necesitar.
* Qué acciones puede realizar.

Funciona como plantilla para crear los *service principals* correspondientes.

### Service principal object

El **service principal** es la identidad de seguridad de una aplicación dentro de un tenant.

Permite:

* Autenticación.
* Autorización.
* Definir qué puede hacer la aplicación.
* Determinar quién puede utilizarla.
* Determinar a qué recursos puede acceder.

Existen tres tipos:

1. **Application:** representación local de una aplicación global.
2. **Managed identity:** representa una identidad administrada.
3. **Legacy:** representa aplicaciones antiguas creadas antes de las app registrations o mediante experiencias heredadas.

Los *service principals* se administran desde **Enterprise applications**.

### Relación entre Application Object y Service Principal

* Un **application object** tiene una relación uno a uno con la aplicación de software.
* Puede tener una relación uno a muchos con sus *service principals*.
* Existe un *service principal* en cada tenant donde se utiliza una aplicación.
* Una aplicación single-tenant tiene un *service principal* en su tenant de origen.
* Una aplicación multi-tenant puede tener un *service principal* en cada tenant donde se haya autorizado su uso.

El **application object** es la representación global; el **service principal** es la representación local de esa aplicación en un tenant concreto.

Los cambios realizados en el *application object* también se reflejan en su *service principal* del tenant de origen. Al eliminar el *application object*, también se elimina el *service principal* correspondiente del tenant de origen.

---

## Managed Identities para recursos de Azure

Las **managed identities** eliminan la necesidad de administrar secretos, credenciales, certificados y claves para que las aplicaciones accedan a recursos compatibles con Microsoft Entra authentication.

Permiten que las aplicaciones obtengan tokens de Microsoft Entra sin administrar credenciales.

### Beneficios

* No es necesario administrar credenciales.
* Las credenciales no son accesibles directamente.
* Pueden utilizarse con recursos que admiten autenticación de Microsoft Entra.
* No tienen coste adicional.

### Tipos

#### System-assigned

* Se crea directamente sobre un recurso Azure.
* Su *service principal* está vinculado al ciclo de vida del recurso.
* Al eliminarse el recurso, se elimina automáticamente la identidad.
* Solo ese recurso puede utilizar la identidad.
* Puede autorizarse para acceder a uno o más servicios.

#### User-assigned

* Se crea como un recurso Azure independiente.
* Su ciclo de vida es independiente de los recursos que la utilizan.
* Puede asignarse a múltiples recursos.
* Debe eliminarse explícitamente.

| Característica | System-assigned                | User-assigned                           |
| -------------- | ------------------------------ | --------------------------------------- |
| Creación       | Como parte de un recurso Azure | Recurso independiente                   |
| Ciclo de vida  | Unido al recurso               | Independiente                           |
| Compartir      | No                             | Sí                                      |
| Uso típico     | Un único recurso/workload      | Varios recursos que comparten identidad |

### Uso

1. Crear una identidad administrada.
2. Si es user-assigned, asociarla al recurso origen, por ejemplo una VM, Logic App o Web App.
3. Autorizar la identidad sobre el recurso destino.
4. Utilizarla para acceder al recurso, mediante Azure SDK/Azure.Identity o conectores compatibles.

### Administración

Se pueden:

* Habilitar o deshabilitar identidades system-assigned.
* Crear, leer, actualizar y eliminar identidades user-assigned.
* Asignar permisos mediante RBAC.
* Revisar operaciones CRUD en Azure Activity Logs.
* Revisar actividad de inicio de sesión en Microsoft Entra ID.

La administración puede realizarse mediante:

* Azure Resource Manager templates.
* Azure Portal.
* Azure CLI.
* PowerShell.
* REST APIs.

---

# Microsoft Entra Application Proxy

Microsoft Entra Application Proxy proporciona acceso remoto seguro y rentable a aplicaciones locales, especialmente aplicaciones heredadas que todavía no utilizan protocolos modernos.

Permite proporcionar acceso remoto sin necesidad de VPN o reverse proxy.

**Application Proxy está orientado principalmente a usuarios remotos. No se recomienda para usuarios dentro de la red corporativa**, debido a la latencia y posibles problemas de rendimiento.

## Prerrequisitos

### Connectors

Los **connectors** son agentes ligeros que pueden instalarse en:

* Hardware físico on-premises.
* VM en cualquier hipervisor.
* VM en Azure.

Requisitos y recomendaciones:

* TLS 1.2 debe estar habilitado antes de instalar el connector.
* Preferentemente deben ubicarse en la misma red/segmento que los servidores de aplicaciones.
* Cada connector group debe tener al menos **dos connectors** para alta disponibilidad y escalabilidad.
* Tres connectors proporcionan mayor margen para mantenimiento.

### Conectividad

Los connectors se conectan a Azure mediante:

* HTTPS/TCP 443.
* HTTP/TCP 80.

No se admite terminar el tráfico TLS del connector ni realizar inspección TLS inline sobre las comunicaciones salientes entre connectors y Azure.

No es necesario balancear los connectors.

### Identidades

Antes de implementar Application Proxy, las identidades deben:

* Sincronizarse desde un directorio on-premises, o
* Crearse directamente en Microsoft Entra ID.

Esto permite la preautenticación y proporciona la información necesaria para SSO.

### Conditional Access

Para acceso remoto desde Internet se recomienda:

* Preautenticación de Microsoft Entra ID.
* Conditional Access.
* MFA cuando corresponda.

No se recomienda Application Proxy para acceso desde la intranet.

### Otros requisitos

* **Licencia:** Microsoft Entra ID P1 o P2.
* **Certificado público:** necesario para dominios personalizados; admite certificados estándar, wildcard y SAN.
* **DNS público:** un registro CNAME debe resolver el dominio externo personalizado hacia la dirección de Application Proxy.
* **DNS interno:** los hosts de connector deben resolver la URL interna de las aplicaciones.
* **KCD/SSO:** para Kerberos Constrained Delegation, el connector y el servidor de la aplicación deben estar unidos al mismo dominio o a dominios de confianza.
* La instalación del connector requiere permisos de administrador local y al menos **Application Administrator** para registrar el connector.

## Descubrimiento de aplicaciones

Antes de publicar aplicaciones conviene realizar un inventario que incluya:

* Tipo de servicio.
* Plataforma de aplicación.
* Dominio.
* Ubicación.
* URL interna.
* URL externa.
* Certificado público.
* Tipo de autenticación.
* Connector group.
* Usuarios y grupos autorizados.
* Requisitos adicionales de acceso o seguridad.

## Requisitos organizacionales

### Acceso

* Usuarios remotos pueden acceder de forma segura.
* Puede utilizarse SSO.
* Los dispositivos personales pueden requerir MFA y Microsoft Authenticator.

### Gobernanza

* Administrar y supervisar el ciclo de vida de las asignaciones.

### Seguridad

* Solo usuarios asignados individualmente o mediante grupos pueden acceder.

### Rendimiento

* El acceso remoto no debería degradar el rendimiento respecto del acceso interno.

### Experiencia

* Utilizar URLs corporativas familiares.

### Auditoría

* Auditar la actividad de acceso de usuarios.

---

## Implementación y publicación

### Connector Groups

Cada aplicación debe asociarse al connector group correspondiente.

Se recomienda:

* Mínimo dos connectors por grupo.
* Tres connectors como configuración óptima.
* Separar connectors por red o ubicación cuando sea necesario.

### Backend Application Timeout

El timeout **Long** proporciona hasta **180 segundos** para aplicaciones que necesitan más tiempo para procesar una transacción.

### Cookies

* **HTTP-Only Cookie:** ayuda a mitigar ataques como XSS.
* **Secure Cookie:** debe habilitarse para que la cookie solo se envíe mediante TLS.
* **Persistent Cookie:** mantiene la sesión después de cerrar el navegador; debe utilizarse con precaución porque puede aumentar el riesgo de acceso no autorizado.

### Translate URLs

**Translate URLs in Headers** puede utilizarse cuando no existe Split DNS o cuando se necesita traducir entre los nombres internos y externos.

**Translate URLs in Application Body** permite traducir enlaces internos presentes en respuestas HTML/CSS, especialmente en aplicaciones con URLs internas codificadas o enlaces a otras aplicaciones locales publicadas.

Si varias aplicaciones publicadas se enlazan entre sí, puede habilitarse la traducción de enlaces en cada aplicación.

---

## Administración del acceso a aplicaciones publicadas

El acceso puede gestionarse mediante:

* Grupos sincronizados desde on-premises.
* Dynamic Groups basados en atributos.
* Self-service groups.
* Asignaciones directas de usuarios o grupos.
* Combinaciones de estos mecanismos.

Los usuarios pueden solicitar acceso mediante My Apps si se configura el autoservicio, pudiendo recibir aprobación automática o requerir aprobación de un responsable.

También pueden utilizarse usuarios invitados mediante Microsoft Entra B2B.

Para aplicaciones que normalmente son anónimas puede deshabilitarse el requisito de asignación, pero esto debe utilizarse con precaución porque permite acceso sin permisos explícitos.

---

## Preautenticación

Para habilitarla:

**Identity > Applications > Enterprise applications > All applications > aplicación > Application Proxy > Pre-Authentication > Microsoft Entra ID**

Con la preautenticación habilitada:

1. Microsoft Entra ID autentica al usuario.
2. Si existe SSO, el connector autentica al usuario frente a la aplicación local.
3. Se concede acceso cuando ambas validaciones son satisfactorias.

Cambiar de **Passthrough** a **Microsoft Entra ID** también configura HTTPS para la URL externa.

---

## Single Sign-On

SSO permite que el usuario se autentique una sola vez en Microsoft Entra ID.

El connector realiza posteriormente la autenticación frente a la aplicación local en nombre del usuario.

Para utilizar SSO:

* Microsoft Entra ID debe poder identificar al usuario.
* La aplicación debe utilizar preautenticación de Microsoft Entra ID.

Con **Passthrough**, los usuarios pueden acceder a la aplicación sin autenticarse previamente en Microsoft Entra ID.

Application Proxy también puede trabajar con aplicaciones desarrolladas mediante **Microsoft Authentication Library (MSAL)** y clientes nativos mediante tokens emitidos por Microsoft Entra ID.

---

## Conditional Access con Application Proxy

Conditional Access permite reforzar la seguridad aplicando políticas basadas en:

* **Usuario y ubicación:** restringir por ubicación geográfica o dirección IP.
* **Dispositivo:** exigir dispositivos registrados, aprobados o compatibles.
* **Aplicación:** proteger aplicaciones cloud y on-premises.
* **Riesgo:** aplicar políticas basadas en el riesgo detectado.

**Microsoft Entra My Apps** proporciona un punto central para descubrir y acceder a las aplicaciones y permite capacidades de autoservicio.

---

## Buenas prácticas para un piloto

* Comenzar con una aplicación IIS sencilla con autenticación integrada de Windows.
* Utilizar inicialmente el Default connector group.
* Probar la aplicación antes de moverla a un connector group de producción.
* Utilizar URLs corporativas familiares en lugar de dominios `msappproxy.net` u `onmicrosoft.com`.
* Restringir inicialmente la visibilidad de la aplicación al grupo piloto.
* Resolver previamente las dependencias necesarias para SSO, especialmente KCD.
* Utilizar TLS entre el connector y la aplicación de destino.
* Implementar y probar de forma incremental.

### Secuencia recomendada de pruebas

1. Probar el acceso con preautenticación deshabilitada.
2. Habilitar preautenticación, asignar usuarios/grupos y probar.
3. Configurar SSO y volver a probar.
4. Aplicar Conditional Access y MFA cuando corresponda y probar nuevamente.

### Troubleshooting

Comenzar comprobando el acceso a la aplicación desde el navegador del host donde está instalado el connector.

Para aislar problemas:

* Utilizar una configuración mínima.
* Probar con un único connector.
* Probar sin SSO.
* Utilizar herramientas de depuración web cuando sea necesario.

---

## Administración y seguridad

Se recomienda aplicar el principio de **mínimo privilegio**.

Roles principales:

| Función                                        | Rol                       |
| ---------------------------------------------- | ------------------------- |
| Help desk                                      | Helpdesk Administrator    |
| Administración de identidad                    | Security Reader           |
| Propietario de aplicación                      | Application Administrator |
| Administración de infraestructura/certificados | Application Administrator |

Para operaciones privilegiadas se recomienda utilizar **Privileged Identity Management (PIM)** con acceso *just-in-time* para proporcionar acceso administrativo bajo demanda y facilitar la auditoría.

---

## Monitoreo y auditoría

Microsoft Entra ID proporciona información sobre:

* Uso de aplicaciones.
* Estado operativo.
* Asignaciones.
* Inicio de sesión.
* Auditoría.

Application Proxy permite supervisar los connectors desde el Microsoft Entra admin center y mediante Windows Event Logs.

### Application audit logs

Proporcionan información detallada sobre:

* Inicios de sesión.
* Usuarios.
* Dispositivos.
* Acceso a aplicaciones.

Los registros pueden consultarse en Microsoft Entra ID y exportarse mediante Audit API.

### Connector monitoring

Los connectors y el servicio gestionan las tareas de alta disponibilidad. Su estado puede supervisarse desde la página de Application Proxy.

### Windows Event Logs

Los connectors disponen de:

* **Admin logs:** eventos y errores principales.
* **Session logs:** transacciones y detalles de procesamiento.
