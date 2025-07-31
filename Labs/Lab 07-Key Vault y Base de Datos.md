
Pasos:
1. Crear el Resource Group
2. Hacer deploy del template https://github.com/MicrosoftLearning/AZ500-AzureSecurityTechnologies/blob/master/Allfiles/Labs/10/az-500-10_azuredeploy.json
3. Si No crea la VM crearla con este script

```powershell
# 1. Obtener recursos necesarios
$resourceGroup = "rg-az500-clase-06"
$location = "westus"
$nicName = "az500-10-nic1"
$vmName = "az500-10-vm1"
$vmSize = "Standard_DS2_V2"

$cred = Get-Credential

$nic = Get-AzNetworkInterface -Name $nicName -ResourceGroupName $resourceGroup

# 2. Crear configuración básica de la VM
$vmConfig = New-AzVMConfig -VMName $vmName -VMSize $vmSize

# 3. Agregar credenciales
$vmConfig = Set-AzVMOperatingSystem `
  -VM $vmConfig `
  -Windows `
  -ComputerName $vmName `
  -Credential $cred `
  -ProvisionVMAgent `
  -EnableAutoUpdate

# 4. Agregar imagen (en tu caso Visual Studio 2019)
$vmConfig = Set-AzVMSourceImage `
  -VM $vmConfig `
  -PublisherName "MicrosoftVisualStudio" `
  -Offer "VisualStudio2019latest" `
  -Skus "active-dummyplan-for-deprecation-requirement-vs19" `
  -Version "latest"

# 5. Asignar NIC existente
$vmConfig = Add-AzVMNetworkInterface -VM $vmConfig -Id $nic.Id

# 6. Crear la VM
New-AzVM -ResourceGroupName $resourceGroup -Location $location -VM $vmConfig
```
   
4. Crear un Key Vault

```powershell
New-AzKeyVault -VaultName 'kvaz500c06' -ResourceGroupName rg-az500-clase-06  -Location westus -DisableRbacAuthorization  
```

5. Agregar un Acces Policy para que el usuario actual tenga permisos para trabajar con la key vault (todos los permisos)

6. Crear una clave en la key vault (la vamos a usar luego)
   
```powershell
Add-AzKeyVaultKey -VaultName 'kvaz500c06 ' -Name 'MyLabKey' -Destination 'Software'                                                                                
```
   
7. . Crear un secreto la key vault (la vamos a usar luego)

```powershell
$secretValue = ConvertTo-SecureString "Pa55w.rd1234" -AsPlainText -Force
Set-AzKeyVaultSecret -Name "SQLPasswordk" -SecretValue $secretValue  -VaultName kvaz500c06    
```
   
8. Ir a App Registrations y Registrar una aplicacion Nueva  *
     * Copiarse el Application Client ID
     * Crear un secreto Key1

9. Crear un access Policy para que la aplicacion pueda acceder al key Vault

```powershell
Set-AzKeyVaultAccessPolicy -VaultName kvaz500c06 -ResourceGroupName rg-az500-clase-06  -ServicePrincipalName 3367c522-0ee3-457d-a4e8-51708d2b55ba  -PermissionsToKeys get,wrapKey,unwrapKey,sign,verify,list    
```

10.  Obtener el Connection String de autenticacion por SQL Server de la base de datos

11.  Permitir a la VM conectarse al SQL Server

12.  Conectarse a la virtual e instalar el iis

> https://learn.microsoft.com/en-us/ssms/install/install?preserve-view=true&view=sql-server-2017

13. Conectarse a la base de datos con el Management Studio

14.  Ejecutar el Siguiente SQL en la base de datos medical (new..query)

```sql
 CREATE TABLE [dbo].[Patients](
    [PatientId] [int] IDENTITY(1,1),
    [SSN] [char](11) NOT NULL,
    [FirstName] [nvarchar](50) NULL,
    [LastName] [nvarchar](50) NULL,
    [MiddleName] [nvarchar](50) NULL,
    [StreetAddress] [nvarchar](50) NULL,
    [City] [nvarchar](50) NULL,
    [ZipCode] [char](5) NULL,
    [State] [char](2) NULL,
    [BirthDate] [date] NOT NULL 
 PRIMARY KEY CLUSTERED ([PatientId] ASC) ON [PRIMARY] );
```

15.  Boton derecho sobre la tabla en el SSMS ponemos "Encript Columns" (Allways Encrypted Wizard")
   
16. Encriptar Columna SSN
Primero

```powershell
Connect-AzAccount -UseDeviceAuthentication
```

```powershell
# PASO 1: Definir variables
$vaultName = "kvaz500c06"
$keyName = "MyLabKey"
$sqlServer = "sqlserverhaglkrydgkfmo.database.windows.net"
$database = "medical"
$table = "Patients"
$column = "SSN"
$cmkName = "CMK_KeyVault"
$cekName = "CEK_KeyVault"
$username = "AzureUser"
$password = "Pa55w.rd1234"

# PASO 2: Obtener la URI completa de la clave en Key Vault
$key = Get-AzKeyVaultKey -VaultName $vaultName -Name $keyName
$keyPath = $key.Id  # Ejemplo: https://kvaz500c06.vault.azure.net/keys/MyLabKey/xxxxxxxxxxxxxxxx

# PASO 3: Crear la Column Master Key (CMK) si no existe
$queryCmk = @"
IF NOT EXISTS (
    SELECT * FROM sys.column_master_keys WHERE name = '$cmkName'
)
BEGIN
    CREATE COLUMN MASTER KEY [$cmkName]
    WITH (
        KEY_STORE_PROVIDER_NAME = N'AZURE_KEY_VAULT',
        KEY_PATH = N'$keyPath'
    );
END
"@

Invoke-Sqlcmd -ServerInstance $sqlServer `
              -Database $database `
              -Username $username `
              -Password $password `
              -Query $queryCmk

```

17.  Instalar el DotNet
   

