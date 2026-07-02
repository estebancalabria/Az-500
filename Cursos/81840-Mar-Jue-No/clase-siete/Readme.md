# Clase Siete - 2 de Julio del 2026

# Repaso

* Moitoring
  * Log Analycs Workspace
    * KQL (Kusto Query Language)
  * Monitoring de VMs
    * Cargarle Agentes (Extensiones)
      * AMA (Azure Monit Agent) : Logs de Windows y Performance Counters
      * DA (Depency Agent) : Procesos, Conexiones Abiertas
    * MDE (Microsoft Defender For Endpoint)


# Allways Encrypted para Columnas de laDB

* Crear el resource group
  * rg-az500-clase-siete
* Crear un key vault
  * kv-az500-clase-siete
  * Crear utilizando Vault Access Policy

* Crear una virtual
  * vm-az500-clase-siete
  * Que de paso cree la vnet, el nsg, la public IP, los discos

* Conectarse a la Virtual por RDP
  * Habilitar el check de clipboard
    
* Abrir Powershell en la VM
  
* Ejecutar en el Powertshell de la VM

```
Set-ExecutionPolicy -ExecutionPolicy Unrestricted
```

* Instalar por PowerShell el management Studio

```
$installer = "C:\SSMS-Setup-ENU.exe"

Invoke-WebRequest -URI "https://aka.ms/ssmsfullsetup" -OutFile $installer -UseBasicParsing

Start-Process -FilePath $installer -ArgumentList "/Install /Quiet" -Wait
```

* Instalar .net en la virtual

```
Invoke-WebRequest -Uri "https://dot.net/v1/dotnet-install.ps1" -OutFile "dotnet-install.ps1"

.\dotnet-install.ps1 -Channel 10.0
```

* Instalar el VSCode

```
Invoke-WebRequest -Uri "https://update.code.visualstudio.com/latest/win32-x64-user/stable" -OutFile "$env:TEMP\vscode-installer.exe"
Start-Process "$env:TEMP\vscode-installer.exe" -ArgumentList "/verysilent /mergetasks=!runcode" -Wait
```

* Instalar un sql server
  * sql-az500-clase-siete-esteban
  * Autenticacion mixta Entra y SQLServer
  * Usuario y Password de SQL Server
 
* Una vez creado anotarse la url del server
  * sql-az500-clase-siete-esteban.database.windows.net
 
* Habilitar en el servidor conexiones publicas
  * Habilitamos conexiones desde Mi ip
  * Habilitamos conexiones desde la IP de la VM (Lo vemos en el overview de la VM)
 
* Crear una base de datos en el SQL Server (En el overview +Create Database)
  * db-az500-clase-siete-esteban
  * Base de Development

* Una vez creada la base ir a settings-> Connection String y compiar el connection String que tiene que usar el programador
  * Server=tcp:sql-az500-clase-siete-esteban.database.windows.net,1433;Initial Catalog=db-az500-clase-siete-esteban;Persist Security Info=False;User ID=AzureUser;Password={your_password};MultipleActiveResultSets=False;Encrypt=True;TrustServerCertificate=False;Connection Timeout=30;
  * Notar que no figura la password
 
* Conectarse a la base
  * Desde el management Studio en la VM
  * DEsde el portal (query explorer)
 
* Ejecutar este SQL desde el portal o el SSMS

```
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

* Crear un secreto en el key vault con el ConnectionString y cambiarle el placeholder del password
  * Guardarrme el secret identifier
  * https://kv-az500-clase-siete.vault.azure.net/secrets/connectionString/e1c1af8c57964e998fed4844c95aa4d7

* Vamos a crearle un usuario a una aplicacion para que pueda leer ese connection string y por consiguiente se pueda conectar a la base de datos
 * Ir a Microsoft entra
 * Ir a App Registration
 * Register
   * Anotar del Overiview el Application (client) ID que es como el nombre de usuario de la applicacion en lugar seguro
   * Ir a Manage -> Certificate and Secret
     * Creamos una clave de aplicacion y la guardamos en un lugar seguro

* Darle permisos a la aplicacion para que pueda leer del key vault

> [!NOTE]
> La aplicacion se va a autenticar en el key vault con su clientID y clientSecret y mediante el Secret Identifier (URL) va a conectarse a la base de datos para hacer cosas en ella

* Pedir a Claude que me cree un programa en .net con este prompt

```
Queiro un programa en .net  de consola que me pregunte el clientID, el clientSecret, el Secret Identifier ylea del key vault el connectionString e inserte registros en una tabla que cree con este SQL " CREATE TABLE [dbo].[Patients](
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
 PRIMARY KEY CLUSTERED ([PatientId] ASC) ON [PRIMARY] );"
```

* Me genera este programa

```
using Azure.Identity;
using Azure.Security.KeyVault.Secrets;
using Microsoft.Data.SqlClient;

namespace PatientKeyVaultConsole;

internal class Program
{
    static async Task Main()
    {
        Console.WriteLine("=== Insercion de pacientes via Key Vault ===");

        string tenantId = ReadInput("Tenant ID: ");
        string clientId = ReadInput("Client ID: ");
        string clientSecret = ReadSecret("Client Secret: ");
        string secretIdentifier = ReadInput("Secret Identifier (URI completa del secreto en Key Vault): ");

        string connectionString;
        try
        {
            connectionString = await GetConnectionStringFromKeyVaultAsync(tenantId, clientId, clientSecret, secretIdentifier);
        }
        catch (Exception ex)
        {
            Console.WriteLine($"Error al leer el secreto de Key Vault: {ex.Message}");
            return;
        }

        try
        {
            await using var connection = new SqlConnection(connectionString);
            await connection.OpenAsync();
            Console.WriteLine("Conexion a la base de datos establecida.");

            bool seguir = true;
            while (seguir)
            {
                var patient = ReadPatientFromConsole();
                await InsertPatientAsync(connection, patient);
                Console.WriteLine("Registro insertado.");

                Console.Write("¿Insertar otro paciente? (s/n): ");
                seguir = (Console.ReadLine() ?? "n").Trim().Equals("s", StringComparison.OrdinalIgnoreCase);
            }
        }
        catch (Exception ex)
        {
            Console.WriteLine($"Error al conectar o insertar en la base de datos: {ex.Message}");
        }
    }

    private static async Task<string> GetConnectionStringFromKeyVaultAsync(
        string tenantId, string clientId, string clientSecret, string secretIdentifier)
    {
        var identifier = new KeyVaultSecretIdentifier(new Uri(secretIdentifier));
        var credential = new ClientSecretCredential(tenantId, clientId, clientSecret);
        var client = new SecretClient(identifier.VaultUri, credential);

        KeyVaultSecret secret = await client.GetSecretAsync(identifier.Name, identifier.Version);
        return secret.Value;
    }

    private static Patient ReadPatientFromConsole()
    {
        Console.WriteLine("--- Datos del paciente ---");
        var patient = new Patient
        {
            SSN = ReadInput("SSN (11 caracteres, ej. 123-45-6789): "),
            FirstName = ReadInput("Nombre: "),
            LastName = ReadInput("Apellido: "),
            MiddleName = ReadInput("Segundo nombre (opcional): "),
            StreetAddress = ReadInput("Direccion: "),
            City = ReadInput("Ciudad: "),
            ZipCode = ReadInput("Codigo postal (5 caracteres): "),
            State = ReadInput("Estado (2 caracteres): "),
            BirthDate = ReadDate("Fecha de nacimiento (yyyy-MM-dd): ")
        };
        return patient;
    }

    private static async Task InsertPatientAsync(SqlConnection connection, Patient patient)
    {
        const string sql = @"
INSERT INTO [dbo].[Patients]
    ([SSN], [FirstName], [LastName], [MiddleName], [StreetAddress], [City], [ZipCode], [State], [BirthDate])
VALUES
    (@SSN, @FirstName, @LastName, @MiddleName, @StreetAddress, @City, @ZipCode, @State, @BirthDate);";

        await using var command = new SqlCommand(sql, connection);
        command.Parameters.Add("@SSN", System.Data.SqlDbType.Char, 11).Value = patient.SSN;
        command.Parameters.Add("@FirstName", System.Data.SqlDbType.NVarChar, 50).Value = (object?)patient.FirstName ?? DBNull.Value;
        command.Parameters.Add("@LastName", System.Data.SqlDbType.NVarChar, 50).Value = (object?)patient.LastName ?? DBNull.Value;
        command.Parameters.Add("@MiddleName", System.Data.SqlDbType.NVarChar, 50).Value = string.IsNullOrEmpty(patient.MiddleName) ? DBNull.Value : patient.MiddleName;
        command.Parameters.Add("@StreetAddress", System.Data.SqlDbType.NVarChar, 50).Value = (object?)patient.StreetAddress ?? DBNull.Value;
        command.Parameters.Add("@City", System.Data.SqlDbType.NVarChar, 50).Value = (object?)patient.City ?? DBNull.Value;
        command.Parameters.Add("@ZipCode", System.Data.SqlDbType.Char, 5).Value = (object?)patient.ZipCode ?? DBNull.Value;
        command.Parameters.Add("@State", System.Data.SqlDbType.Char, 2).Value = (object?)patient.State ?? DBNull.Value;
        command.Parameters.Add("@BirthDate", System.Data.SqlDbType.Date).Value = patient.BirthDate;

        await command.ExecuteNonQueryAsync();
    }

    private static string ReadInput(string prompt)
    {
        Console.Write(prompt);
        return Console.ReadLine() ?? string.Empty;
    }

    private static DateTime ReadDate(string prompt)
    {
        while (true)
        {
            Console.Write(prompt);
            var input = Console.ReadLine();
            if (DateTime.TryParse(input, out var date))
                return date;
            Console.WriteLine("Fecha invalida, intente de nuevo.");
        }
    }

    private static string ReadSecret(string prompt)
    {
        Console.Write(prompt);
        var secret = string.Empty;
        ConsoleKeyInfo key;
        do
        {
            key = Console.ReadKey(intercept: true);
            if (key.Key == ConsoleKey.Backspace && secret.Length > 0)
            {
                secret = secret[..^1];
                Console.Write("\b \b");
            }
            else if (!char.IsControl(key.KeyChar))
            {
                secret += key.KeyChar;
                Console.Write("*");
            }
        } while (key.Key != ConsoleKey.Enter);
        Console.WriteLine();
        return secret;
    }
}

internal class Patient
{
    public string SSN { get; set; } = string.Empty;
    public string? FirstName { get; set; }
    public string? LastName { get; set; }
    public string? MiddleName { get; set; }
    public string? StreetAddress { get; set; }
    public string? City { get; set; }
    public string? ZipCode { get; set; }
    public string? State { get; set; }
    public DateTime BirthDate { get; set; }
}
```
