# Multicloud en Defender for Cloud (AWS/GCP) vía Environment Settings

## Intro

Microsoft Defender for Cloud es una plataforma CNAPP (Cloud-Native Application Protection Platform): no protege solo Azure. Nació como "Azure Security Center", pero hoy da cobertura unificada de **CSPM** (Cloud Security Posture Management — postura y recomendaciones) y **CWPP** (Cloud Workload Protection Platform — protección en runtime) sobre **Azure, AWS, GCP y entornos on-premises/híbridos**, todo desde un mismo dashboard, con un solo secure score y las mismas recomendaciones normalizadas entre providers.

Esto se logra mediante **connectors nativos**: para AWS y GCP, Defender for Cloud usa autenticación federada (sin credenciales de larga duración almacenadas) y despliega roles/permisos de solo lectura del lado del proveedor cloud para poder escanear configuración, logs y — si se habilitan los planes pagos — proteger cargas de trabajo específicas (VMs/instancias, contenedores, bases de datos, storage). También puede sumar servers on-prem vía Azure Arc, y cubrir DevOps (repos e IaC en GitHub, Azure DevOps, GitLab) y registries de contenedores (Docker Hub, JFrog).

**Environment settings** es la sección de gestión central donde se administra todo esto: es el lugar donde se define **qué entornos están conectados** (Azure, AWS, GCP, DevOps/repos) y **qué planes de Defender** están habilitados sobre cada uno. Es el punto de entrada obligatorio para cualquier onboarding multicloud — sin conectar el environment ahí, Defender for Cloud no tiene visibilidad sobre esos recursos.

Se accede desde el menú lateral: **Defender for Cloud → Management → Environment settings**.

## Vista general

<img width="1568" height="676" alt="image" src="https://github.com/user-attachments/assets/a91eae1c-2af0-4666-8649-7d10d2a205f0" />


Al entrar, ves la subscription actual (en este caso "Visual Studio Enterprise Subscription") y una serie de tiles con las distintas áreas de configuración:

- **Governance rules** – asignar owners y SLAs a las recomendaciones de seguridad.
- **Data sensitivity** – definir sensibilidad de recursos según tipo de info o sensitivity labels.
- **Direct onboarding** – conectar servers non-Azure directo con Defender for Endpoint (sin pasar por Arc).
- **Integrations** – conectar herramientas de ITSM, CI/CD y soluciones de terceros.
- **Exemptions** – excluir recursos de policies específicas.
- **Resource criticality** – marcar assets como críticos ("crown jewels").
- **Security rules** – reglas y scopes de seguridad organizacional.

El botón clave arriba a la izquierda es **"+ Add environment"**: desde ahí arranca el onboarding de cuentas externas.

## Add environment – opciones disponibles

<img width="1036" height="487" alt="image" src="https://github.com/user-attachments/assets/08a3b13d-1c2e-4f0f-868d-22d739bbca02" />


Al hacer clic en **Add environment**, se despliega el menú con los entornos que Defender for Cloud puede conectar:

- **Amazon Web Services (AWS)**
- **Google Cloud Platform (GCP)**
- **GitHub**
- **Azure DevOps**
- **GitLab**
- **Docker Hub**
- **JFrog**

Los primeros dos (AWS, GCP) habilitan **Cloud Security Posture Management (CSPM)** y, opcionalmente, **Defender plans** (Servers, Containers, etc.) sobre esas cuentas cloud. Los últimos cinco (GitHub, Azure DevOps, GitLab, Docker Hub, JFrog) corresponden a **DevOps security** — escaneo de repos, pipelines, IaC y registries de contenedores.

## Flujo típico de onboarding (AWS/GCP)

1. **Environment settings → Add environment → Amazon Web Services / Google Cloud Platform.**
2. Elegís el modo: **single account** o **management/organization account** (para conectar toda una org de una).
3. Seleccionás los **Defender plans** que querés habilitar (CSPM gratis siempre; Defender for Servers, Containers, etc. son pagos).
4. Azure te genera un template (CloudFormation para AWS, script de Cloud Shell/Terraform para GCP) que creás del lado del proveedor cloud para dar acceso de solo lectura (rol IAM cross-account).
5. Confirmás la conexión — el connector queda listado en Environment settings junto con las subscriptions de Azure.

## Puntos clave para el examen

- **Environment settings es siempre el primer paso** para cualquier pregunta de tipo "necesito agregar una cuenta AWS/GCP a Defender for Cloud, ¿qué hago primero?".
- La initiative de policy que Defender for Cloud asigna por default a nivel de subscription (antes "ASC Default" / "Enable Monitoring in ASC") hoy es el **Microsoft Cloud Security Benchmark (MCSB)** — se ve reflejada en la vista de Security posture, no acá directamente.
- **Governance rules** y **Exemptions** suelen aparecer como distractores en preguntas de onboarding — no son el primer paso, son configuración posterior sobre recomendaciones ya existentes.
