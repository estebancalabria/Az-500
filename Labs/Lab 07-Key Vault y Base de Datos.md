
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
$password = "..."

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
18.  Crear una App de consola nueva
```cmd
dotnet new console --name OpsEncrypt
```
19.  Copiar el contenido de Program.cs desde aqui:

```cs
 using Azure.Core;
 using Azure.Identity;
 using System;
 using System.Collections.Generic;
 using System.Linq;
 using System.Text;
 using System.Threading.Tasks;
 using System.Data;
 using Microsoft.Data.SqlClient;
 using Microsoft.Data.SqlClient.AlwaysEncrypted.AzureKeyVaultProvider;
 using Microsoft.IdentityModel.Clients.ActiveDirectory;


 namespace AlwaysEncryptedConsoleAKVApp
 {
 class Program
 {
     // Update this line with your Medical database connection string from the Azure portal.
     static string connectionString = @"...";
     static string clientId = @"...";
     static string clientSecret = "...";
     static string tenantId = "..";
     static void Main(string[] args)
     {
         InitializeAzureKeyVaultProvider();
         Console.WriteLine("Signed in as: " + _clientCredential.ClientId);
         Console.WriteLine("Original connection string copied from the Azure portal:");
         Console.WriteLine(connectionString);
         // Create a SqlConnectionStringBuilder.
         SqlConnectionStringBuilder connStringBuilder =
             new SqlConnectionStringBuilder(connectionString);
         // Enable Always Encrypted for the connection.
         // This is the only change specific to Always Encrypted
         connStringBuilder.ColumnEncryptionSetting =
             SqlConnectionColumnEncryptionSetting.Enabled;
         Console.WriteLine(Environment.NewLine + "Updated connection string with Always Encrypted enabled:");
         Console.WriteLine(connStringBuilder.ConnectionString);
         // Update the connection string with a password supplied at runtime.
         Console.WriteLine(Environment.NewLine + "Enter server password:");
         connStringBuilder.Password = Console.ReadLine();
         // Assign the updated connection string to our global variable.
         connectionString = connStringBuilder.ConnectionString;
         // Delete all records to restart this demo app.
         ResetPatientsTable();
         // Add sample data to the Patients table.
         Console.Write(Environment.NewLine + "Adding sample patient data to the database...");
         InsertPatient(new Patient()
         {
             SSN = "999-99-0001",
             FirstName = "Orlando",
             LastName = "Gee",
             BirthDate = DateTime.Parse("01/04/1964")
         });
         InsertPatient(new Patient()
         {
             SSN = "999-99-0002",
             FirstName = "Keith",
             LastName = "Harris",
             BirthDate = DateTime.Parse("06/20/1977")
         });
         InsertPatient(new Patient()
         {
             SSN = "999-99-0003",
             FirstName = "Donna",
             LastName = "Carreras",
             BirthDate = DateTime.Parse("02/09/1973")
         });
         InsertPatient(new Patient()
         {
             SSN = "999-99-0004",
             FirstName = "Janet",
             LastName = "Gates",
             BirthDate = DateTime.Parse("08/31/1985")
         });
         InsertPatient(new Patient()
         {
             SSN = "999-99-0005",
             FirstName = "Lucy",
             LastName = "Harrington",
             BirthDate = DateTime.Parse("05/06/1993")
         });
         // Fetch and display all patients.
         Console.WriteLine(Environment.NewLine + "All the records currently in the Patients table:");
         foreach (Patient patient in SelectAllPatients())
         {
             Console.WriteLine(patient.FirstName + " " + patient.LastName + "\tSSN: " + patient.SSN + "\tBirthdate: " + patient.BirthDate);
         }
         // Get patients by SSN.
         Console.WriteLine(Environment.NewLine + "Now lets locate records by searching the encrypted SSN column.");
         string ssn;
         // This very simple validation only checks that the user entered 11 characters.
         // In production be sure to check all user input and use the best validation for your specific application.
         do
         {
             Console.WriteLine("Please enter a valid SSN (ex. 999-99-0003):");
             ssn = Console.ReadLine()!;
         } while (ssn.Length != 11);
         // The example allows duplicate SSN entries so we will return all records
         // that match the provided value and store the results in selectedPatients.
         Patient selectedPatient = SelectPatientBySSN(ssn);
         // Check if any records were returned and display our query results.
         if (selectedPatient != null)
         {
             Console.WriteLine("Patient found with SSN = " + ssn);
             Console.WriteLine(selectedPatient.FirstName + " " + selectedPatient.LastName + "\tSSN: "
                 + selectedPatient.SSN + "\tBirthdate: " + selectedPatient.BirthDate);
         }
         else
         {
             Console.WriteLine("No patients found with SSN = " + ssn);
         }
         Console.WriteLine("Press Enter to exit...");
         Console.ReadLine();
     }
     private static ClientCredential _clientCredential;

     static void InitializeAzureKeyVaultProvider()
     {
         _clientCredential = new ClientCredential(clientId, clientSecret);
         TokenCredential tokenCredential = new ClientSecretCredential(tenantId, clientId, clientSecret);
         var azureKeyVaultProvider =
         new SqlColumnEncryptionAzureKeyVaultProvider(tokenCredential);
         Dictionary<string, SqlColumnEncryptionKeyStoreProvider> providers =
         new Dictionary<string, SqlColumnEncryptionKeyStoreProvider>();
         providers.Add(SqlColumnEncryptionAzureKeyVaultProvider.ProviderName, azureKeyVaultProvider);
         SqlConnection.RegisterColumnEncryptionKeyStoreProviders(providers);
     }

     /*public async static Task<string> GetToken(string authority, string resource, string scope)
     {
         var authContext = new AuthenticationContext(authority);
         AuthenticationResult result = await authContext.AcquireTokenAsync(resource, _clientCredential);
         if (result == null)
             throw new InvalidOperationException("Failed to obtain the access token");
         return result.AccessToken;
     }*/
     static int InsertPatient(Patient newPatient)
     {
         int returnValue = 0;
         string sqlCmdText = @"INSERT INTO [dbo].[Patients] ([SSN], [FirstName], [LastName], [BirthDate]) VALUES (@SSN, @FirstName, @LastName, @BirthDate);";
         SqlCommand sqlCmd = new SqlCommand(sqlCmdText);

         SqlParameter paramSSN = new SqlParameter(@"@SSN", newPatient.SSN);
         paramSSN.DbType = DbType.AnsiStringFixedLength;
         paramSSN.Direction = ParameterDirection.Input;
         paramSSN.Size = 11;
         SqlParameter paramFirstName = new SqlParameter(@"@FirstName", newPatient.FirstName);
         paramFirstName.DbType = DbType.String;
         paramFirstName.Direction = ParameterDirection.Input;
         SqlParameter paramLastName = new SqlParameter(@"@LastName", newPatient.LastName);
         paramLastName.DbType = DbType.String;
         paramLastName.Direction = ParameterDirection.Input;
         SqlParameter paramBirthDate = new SqlParameter(@"@BirthDate", newPatient.BirthDate);
         paramBirthDate.SqlDbType = SqlDbType.Date;
         paramBirthDate.Direction = ParameterDirection.Input;
         sqlCmd.Parameters.Add(paramSSN);
         sqlCmd.Parameters.Add(paramFirstName);
         sqlCmd.Parameters.Add(paramLastName);
         sqlCmd.Parameters.Add(paramBirthDate);
         using (sqlCmd.Connection = new SqlConnection(connectionString))
         {
             try
             {
                 sqlCmd.Connection.Open();
                 sqlCmd.ExecuteNonQuery();
             }
             catch (Exception ex)
             {
                 returnValue = 1;
                 Console.WriteLine("The following error was encountered: ");
                 Console.WriteLine(ex.Message);
                 Console.WriteLine(Environment.NewLine + "Press Enter key to exit");
                 Console.ReadLine();
                 Environment.Exit(0);
             }
         }
         return returnValue;
     }
     static List<Patient> SelectAllPatients()
     {
         List<Patient> patients = new List<Patient>();
         SqlCommand sqlCmd = new SqlCommand(
         "SELECT [SSN], [FirstName], [LastName], [BirthDate] FROM [dbo].[Patients]",
             new SqlConnection(connectionString));
         using (sqlCmd.Connection = new SqlConnection(connectionString))
         {
             try
             {
                 sqlCmd.Connection.Open();
                 SqlDataReader reader = sqlCmd.ExecuteReader();
                 if (reader.HasRows)
                 {
                     while (reader.Read())
                     {
                         patients.Add(new Patient()
                         {
                             SSN = reader[0].ToString(),
                             FirstName = reader[1].ToString(),
                             LastName = reader["LastName"].ToString(),
                             BirthDate = (DateTime)reader["BirthDate"]
                         });
                     }
                 }
             }
             catch (Exception ex)
             {
                 throw;
             }
         }
         return patients;
     }

     static Patient SelectPatientBySSN(string ssn)
     {
         Patient patient = new Patient();
         SqlCommand sqlCmd = new SqlCommand(
             "SELECT [SSN], [FirstName], [LastName], [BirthDate] FROM [dbo].[Patients] WHERE [SSN]=@SSN",
             new SqlConnection(connectionString));
         SqlParameter paramSSN = new SqlParameter(@"@SSN", ssn);
         paramSSN.DbType = DbType.AnsiStringFixedLength;
         paramSSN.Direction = ParameterDirection.Input;
         paramSSN.Size = 11;
         sqlCmd.Parameters.Add(paramSSN);
         using (sqlCmd.Connection = new SqlConnection(connectionString))
         {
             try
             {
                 sqlCmd.Connection.Open();
                 SqlDataReader reader = sqlCmd.ExecuteReader();
                 if (reader.HasRows)
                 {
                     while (reader.Read())
                     {
                         patient = new Patient()
                         {
                             SSN = reader[0].ToString(),
                             FirstName = reader[1].ToString(),
                             LastName = reader["LastName"].ToString(),
                             BirthDate = (DateTime)reader["BirthDate"]
                         };
                     }
                 }
                 else
                 {
                     patient = null;
                 }
             }
             catch (Exception ex)
             {
                 throw;
             }
         }
         return patient;
     }

     // This method simply deletes all records in the Patients table to reset our demo.
     static int ResetPatientsTable()
     {
         int returnValue = 0;
         SqlCommand sqlCmd = new SqlCommand("DELETE FROM Patients");
         using (sqlCmd.Connection = new SqlConnection(connectionString))
         {
             try
             {
                 sqlCmd.Connection.Open();
                 sqlCmd.ExecuteNonQuery();
             }
             catch (Exception ex)
             {
                 returnValue = 1;
             }
         }
         return returnValue;
 }
 }

 class Patient
 {
     public string SSN { get; set; }
     public string FirstName { get; set; }
     public string LastName { get; set; }
     public DateTime BirthDate { get; set; }
 }
}
```


20.  Instalar paquetes necesarios

```cmd
dotnet add package Microsoft.SqlServer.Management.AlwaysEncrypted.AzureKeyVaultProvider
dotnet add package Microsoft.IdentityModel.Clients.ActiveDirectory
dotnet add package Microsoft.Data.SqlClient
dotnet add package Microsoft.Data.SqlClient.AlwaysEncrypted.AzureKeyVaultProvider
dotnet add package Azure.Identity
```

21.  Modicar el codigo esta parte

``cs
     static string connectionString = @"<connection string noted earlier>";
     static string clientId = @"<client id noted earlier>";
     static string clientSecret = "<key value noted earlier>";``

21.  
   

