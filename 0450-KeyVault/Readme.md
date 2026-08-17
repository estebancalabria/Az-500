# Resumen Completo de Azure Key Vault, RBAC, Certificados, Rotación de Claves, Backup/Recovery y Azure Dedicated HSM

## 1. Azure Key Vault: Conceptos Fundamentales
Azure Key Vault es un servicio para almacenar y acceder de forma segura a **claves, secretos y certificados**.  
Contenedores:
- **Vaults**: claves software y HSM, secretos y certificados.  
- **Managed HSM pools**: solo claves HSM.

Conceptos:
- **Tenant**: organización propietaria de servicios Microsoft Cloud.  
- **Vault owner**: control total del vault, auditoría y ciclo de vida.  
- **Vault consumer**: accede según permisos otorgados.  
- **Managed HSM roles**: Administrators, Crypto Officer/User, Crypto Service Encryption User.  
- **Resource / Resource group**: elementos administrables y agrupación lógica.  
- **Security principal**: identidad (usuario, grupo, service principal).  
- **Managed identities**: identidad administrada automáticamente para autenticación sin credenciales.

## 2. Autenticación y Seguridad de Red
Autenticación mediante:
- **Managed identities** (recomendado).  
- **Service principal + certificado** (no recomendado).  
- **Service principal + secret** (no recomendado).

Seguridad:
- **TLS 1.2/1.3**, RSA 2048 bits, Perfect Forward Secrecy.  
- **Firewall** configurable con IPs, VNets, service endpoints, Private Link.  
- Flujo de solicitud: autenticación → firewall → validación de token → autorización → operación.

## 3. Modelos de Acceso: Management Plane vs Data Plane
- **Management plane**: administrar el vault (crear, borrar, propiedades). → Azure RBAC.  
- **Data plane**: operar claves, secretos y certificados. → Access Policies o Azure RBAC.

Roles pueden asignarse en:
- Subscription  
- Resource group  
- Recurso específico  

**Access Policy permite que un Contributor se otorgue permisos**, por eso se recomienda **Azure RBAC** para separar responsabilidades.

## 4. Azure RBAC para Key Vault
Permite permisos granulares sobre:
- Claves  
- Secretos  
- Certificados  
- Objetos individuales

Buenas prácticas:
- Un vault por aplicación por entorno (Dev, PreProd, Prod).  
- Permisos individuales solo para escenarios específicos (ej. compartir un secreto puntual).

### Roles integrados (data plane)
- **Key Vault Administrator**: todas las operaciones del data plane.  
- **Key Vault Certificates Officer**: administrar certificados.  
- **Key Vault Crypto Officer**: administrar claves.  
- **Key Vault Crypto Service Encryption User**: wrap/unwrap + metadata.  
- **Key Vault Crypto User**: operaciones criptográficas.  
- **Key Vault Reader**: solo metadatos.  
- **Key Vault Secrets Officer**: administrar secretos.  
- **Key Vault Secrets User**: leer secretos y certificados con clave privada.

**No existe Certificate User → usar Secrets User.**

### Key Vault Data Access Administrator (preview)
Administra asignaciones de roles del data plane. Incluye condición ABAC.

## 5. Certificados en Key Vault
Características:
- Crear o importar certificados (self-signed o CA).  
- Gestión del ciclo de vida mediante **certificate policy**.  
- Contactos para notificaciones de expiración/renovación.  
- Renovación automática con DigiCert y GlobalSign.

Composición:
- Certificado  
- Key direccionable  
- Secret direccionable  
- Metadatos X.509

Claves:
- **Exportables**: RSA, EC.  
- **No exportables**: HSM (RSA-HSM, EC-HSM).

Certificate policy incluye:
- Propiedades X.509  
- Tipo y longitud de clave  
- Exportabilidad  
- Secret content type  
- Lifetime actions (emailContacts, autoRenew)  
- Validación OV/EV  
- Issuer  
- Atributos

## 6. Rotación de Claves (Key Rotation)
Rotación automática mediante **key rotation policy** por clave individual.

Recomendación: rotar claves cada **2 años**.

Configuración:
- **Expiry time**  
- **Enabled/disabled**  
- **Rotation type**:  
  - Tiempo después de creación (default)  
  - Tiempo antes de expiración  
- **Rotation time** (mínimo 7 días)  
- **Notification time** (Event Grid)

Rotación genera **nueva versión** de la clave.  
Servicios deben usar **URI sin versión** para obtener la última versión.

Permisos necesarios:
- **Key Vault Crypto Officer**

Gobernanza:
- Azure Policy: “Keys should have a rotation policy…”  
- Parámetro: días máximos para rotar (ej. 730).

Rotación manual:
- Portal: *Rotate Now*  
- CLI: `az keyvault key rotate`  
- PowerShell: `Invoke-AzKeyVaultKeyRotation`

## 7. Backup y Recovery
Key Vault ofrece:
- **Alta disponibilidad** con failover automático.  
- **Soft-delete** y **purge protection** para evitar pérdida accidental/maliciosa.

Consideraciones:
- No existe backup completo del vault.  
- Backup descarga un **blob cifrado** que solo puede restaurarse en **misma suscripción y geografía**.  
- Backups de objetos con múltiples versiones pueden fallar.  
- Exceso de requests puede causar throttling.

Portal:
- Backup: seleccionar objeto → Download Backup  
- Restore: seleccionar objeto → Restore Backup

## 8. Seguridad en Azure Backup (relacionado)
Azure Backup protege datos en tránsito y en reposo:
- HTTPS/TLS  
- Encriptación con claves administradas por Microsoft o CMK  
- Soft delete (14 días)  
- Inmutabilidad  
- Multi-User Authorization (MUA)  
- Private Endpoints  
- Monitoreo y alertas  
- Seguridad para backups híbridos (MARS agent)

## 9. Azure Dedicated HSM
Servicio para almacenar claves en **HSM dedicados FIPS 140-2 Level 3**.

Características:
- Dispositivos Thales Luna 7 A790  
- Alta disponibilidad (pares)  
- Distribución regional  
- Conexión directa a VNet  
- Acceso on-premises vía VPN  
- Cliente tiene control administrativo total  
- Microsoft solo monitorea hardware (opcional)

Ventajas:
- Cumplimiento FIPS 140-2 Level 3  
- Single-tenant  
- Control exclusivo  
- Alto rendimiento (10.000 ops/s RSA-2048)  
- 10 particiones por dispositivo

Cuándo usarlo:
- Escenarios **lift-and-shift** con acceso directo al HSM:  
  - Migraciones on-premises → Azure VM  
  - Migraciones AWS CloudHSM Classic  
  - Software empaquetado (Apache/Nginx SSL Offload, Oracle TDE, ADCS)

No adecuado para:
- Servicios cloud con CMK no integrados con Dedicated HSM (AIP, ADE, Storage, SQL, etc.)

Requisitos:
- Tener Microsoft Account Manager  
- Compromiso anual ≥ **USD 5M**

## 10. Casos de Uso de Key Vault
### Desarrollador Azure
- Claves externas a la app  
- HSM estándar  
- Baja latencia

### SaaS
- Clientes administran sus claves  
- App no accede a claves del cliente

### CSO
- Cumplimiento FIPS  
- Auditoría casi en tiempo real  
- Administración centralizada

---

Este es el **resumen completo**, listo para copiar y pegar, sin agregar contenido externo y sintetizado manteniendo todos los conceptos fundamentales.
