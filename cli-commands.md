## Resource Group

** Powershell **
```powershell
New-AzResourceGroup -Name Az500-Clase2 -Location eastus
```
**Az**
```
az group create --name Az500-Clase2 --location eastus
```


## Microsoft Entra (Ex Azure Active Directory)

Para Poder Usar Active Directory en PowerShell
> Connect-AzureAD

### Obtener Detalles del TEanant
> Get-AzureADTenantDetail
Obtener el domain del tennant
> (Get-AzureADTenantDetail).VerifiedDomains[0].Name

### Listar Usuarios Active Directory
#### Powershell
> Get-AzureADUser
#### Bash
> az ad user list

### Crear un usuario nuevo (Powershell)
> $pass = New-Object -TypeName Microsoft.Open.AzureAD.Model.PasswordProfile
> $pass.Password = "Pa55w.rd"
> New-AzureADUser -DisplayName "Mark" -PasswordProfile $pass -UserPrincipalName "mark@estebancalabriagmail.onmicrosoft.com" -AccountEnabled $true -MailNickName "Mark"

### Crear un usuario nuevo(Bash)
$ az ad user create --display-name Tracy --password Pa55w.rd --user-principal-name Tracy@estebancalabriagmail.onmicrosoft.com

### Crear un Grupo Nuevo (Bash)
		az ad group create --display-name ServiceDesk --mail-nickname ServiceDesk

	Crear un Grupo Nuevo (Powershell)
> New-AzureADGroup -DisplayName "Junior Admins" -MailEnabled $false -SecurityEnabled $true -MailNickName JuniorAdmins

	Asignar un rol a un usuario

> $scope ="/subscriptions/91dc3067-fd7a-4ed3-95eb-8c938f69cbfe"
> New-AzRoleAssignment -SignInName bill@estebancalabriagmail.onmicrosoft.com -RoleDefinitionName Reader -Scope $scope

	Remover un Rol de un Usuario
> Remove-AzRoleAssignment -SignInName bill@estebancalabriagmail.onmicrosoft.com -RoleDefinitionName Reader -Scope /subscriptions/91dc3067-fd7a-4ed3-95eb-8c938f69cbfe

	Ver Asignaciones de Roles de un usuario
> Get-AzRoleAssignment -SignInName bill@estebancalabriagmail.onmicrosoft.com

	Ver Definicion de Roles
> Get-AzRoleDefinition -Name "Virtual Machine Contributor"

## Storage Account

	Crear Storage Account
> New-AzStorageAccount -ResourceGroupName Az500-Clase4 -Name demo-storage-1 -Location eastus -Kind StorageV2

## Key Vault

> New-AzKeyVault -VaultName Az500-Clase2-KeyVault -ResourceGroupName Az500-Clase2 -Location eastus

> Set-AzKeyVaultAccessPolicy -VaultName 'Az500-Clase2-KeyVault' -ResourceGroupName 'Az500-Clase2' -ServicePrincipalName '53741083-4c43-4bdd-86d1-e98e350ebe7d'  -PermissionsToKeys get,wrapKey,unwrapKey,sign,verify,list


## Locks

Crear un Lock en un Recurso
> New-AzResourceLock -LockLevel CanNotDelete -LockName NoBorrar -ResourceGroupName Az500-Clase4 -ResourceType Microsoft.Storage/storageAccounts -ResourceName eccdemostorage2

	Borrar un Lock

> Remove-AzResourceLock -LockName EnUso -ResourceGroupName Az500-Clase4 -ResourceType Microsoft.Storage/storageAccounts -ResourceName eccdemostorage1

Public IP
Crear Una Public IP
New-AzPublicIpAddress -ResourceGroupName Rg-Az104-Clase-Cinco -Name Ip-Az104-Vm-Public -Location eastus -Sku Standard -AllocationMethod Static

Asociar una IP Publica a una Placa de Red
Set-AzNetworkInterfaceIpConfig -Name Vm-Az104-Uno -NetworkInterface (Get-AzNetworkInterface -Name Vm-Az104-Uno) -PublicIpAddress (Get-AzPublicIpAddress -Name Ip-Az104-Vm-Public) | Set-AzNetworkInterface

Virtual Machine

Crear unaVM
New-AzVm -ResourceGroup Rg-Az104-Clase-Cinco -Name Vm-Az104-Uno -Location eastus -VirtualNetworkName Vnet-Az104-Clase-Cinco -SubnetName Vnet-Subnet-Uno -SecurityGroupName Nsg-Az104-Clase-Cinco -Image MicrosoftWindowsServer:WindowsServer:2022-Datacenter:latest -PublicIpSku Standard
Virtual Network

Crear una Virtual Network
New-AzVirtualNetwork -ResourceGroupName Az500-Clase7 -Name myVnetSpiderMan -Location eastus -AddressPrefix 10.1.0.0/16 -Subnet (New-AzVirtualNetworkSubnetConfig -Name mySubnet -AddressPrefix 10.1.0.0/24  )

Network Security Group

Crear un Network Security Group
 New-AzNetworkSecurityGroup -Name Nsg-Az104-Clase-Cinco -ResourceGroupName Rg-Az104-Clase-Cinco -Location eastus
Agregar una Regla a un Network Security Group
Add-AzNetworkSecurityRuleConfig -Name Allow-RDP -Description "Permitir RDP" -Direction Inbound -Access Allow -Protocol tcp -Priority 200 -SourceAddressPrefix * -SourcePortRange * -DestinationAddressPrefix * -DestinationPortRange 3389 -NetworkSecurityGroup (Get-AzNetworkSecurityGroup -Name Nsg-Az104-Clase-Cinco) | Set-AzNetworkSecurityGroup

SQL Databases

Crear un Resource Group en el CLI
> New-AzResourceGroup -Location eastus -ResourceGroupName AdvNetClase5
Crear un servidor de SQL Server
> New-AzSQLServer -ResourceGroupName AdvNetClase5 -Location eastus -Name advnetclase5-esteban-server

cmdlet New-AzSqlServer at command pipeline position 1
Supply values for the following parameters:
(Type !? for Help.)
SqlAdministratorCredentials
User: AzureUser
Password for user AzureUser: ************
Agregar la regla del firewall para conectarnos
> New-AzSqlServerFirewallRule -ResourceGroupName AdvNetClase5 -FirewallRuleName AllowMyIp -ServerName advnetclase5-esteban-server -StartIpAddress 190.245.165.140 -EndIpAddress 190.245.165.140
Crear una base de datos
  > New-AzSqlDatabase -ResourceGroupName AdvNetClase5 -ServerName advnetclase5-esteban-server -DatabaseName AdvNetClase5 -Edition Basic -Family Gen4 -VCore 0

	CREAR USUARIO CON SQL

CREATE LOGIN [Dummy] 
WITH PASSWORD=N'Pa55w.rd1234'
GO


		(HAcerlo sobre la base de datos sobre la que queremos crear el usuario)
CREATE USER [Dummy] FOR LOGIN [Dummy] 
WITH DEFAULT_SCHEMA=[dbo]

EXEC sp_addrolemember N'db_datareader', N'Dummy'




