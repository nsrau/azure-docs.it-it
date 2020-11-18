---
title: Configurare Always Encrypted tramite Azure Key Vault
description: Questa esercitazione illustra come proteggere i dati sensibili in un database nel database SQL di Azure con la crittografia dei dati tramite la procedura guidata di Always Encrypted in SQL Server Management Studio.
keywords: crittografia dei dati, chiave di crittografia, crittografia del cloud
services: sql-database
ms.service: sql-database
ms.subservice: security
ms.custom: sqldbrb=1, devx-track-azurecli
ms.devlang: ''
ms.topic: how-to
author: VanMSFT
ms.author: vanto
ms.reviewer: ''
ms.date: 11/02/2020
ms.openlocfilehash: 257abf03994c7006b1c3789174f550515dcd309a
ms.sourcegitcommit: 0a9df8ec14ab332d939b49f7b72dea217c8b3e1e
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 11/18/2020
ms.locfileid: "94841462"
---
# <a name="configure-always-encrypted-by-using-azure-key-vault"></a>Configurare Always Encrypted tramite Azure Key Vault 

[!INCLUDE[appliesto-sqldb](../includes/appliesto-sqldb-sqlmi.md)]

Questo articolo illustra come proteggere i dati sensibili in un database nel database SQL di Azure con la crittografia dei dati tramite la [procedura guidata di Always Encrypted](/sql/relational-databases/security/encryption/always-encrypted-wizard) in [SQL Server Management Studio (SSMS)](/sql/ssms/sql-server-management-studio-ssms). Include anche le istruzioni che illustrano come archiviare ogni chiave di crittografia nell'insieme di credenziali delle chiavi di Azure.

Always Encrypted è una tecnologia di crittografia dei dati che consente di proteggere i dati sensibili inattivi sul server, durante lo spostamento tra client e server e durante l'uso dei dati. La crittografia sempre attiva garantisce che i dati sensibili non vengano mai visualizzati come testo non crittografato all'interno del sistema di database. Dopo avere configurato la crittografia dei dati solo le applicazioni client o i server applicazioni, che hanno accesso alle chiavi, possono accedere ai dati di testo non crittografato. Per informazioni dettagliate, vedere l'articolo relativo alla [crittografia sempre attiva (motore di database)](/sql/relational-databases/security/encryption/always-encrypted-database-engine).

Dopo avere configurato il database per usare la crittografia sempre attiva, viene creata un'applicazione client in C# con Visual Studio per lavorare con i dati crittografati.

Seguire la procedura descritta in questo articolo per informazioni su come configurare Always Encrypted per il database nel database SQL di Azure o in SQL Istanza gestita. Questo articolo spiega come eseguire le attività seguenti:

- Usare la procedura guidata per la crittografia sempre attiva in SSMS per creare [chiavi con crittografia sempre attiva](/sql/relational-databases/security/encryption/always-encrypted-database-engine#Anchor_3).
  - Creare una [chiave master di colonna (CMK, Column Master Key)](/sql/t-sql/statements/create-column-master-key-transact-sql).
  - Creare una [chiave di crittografia di colonna (CEK, Column Encryption Key)](/sql/t-sql/statements/create-column-encryption-key-transact-sql).
- Creare una tabella di database e crittografare le colonne.
- Creare un'applicazione che inserisce, seleziona e visualizza i dati delle colonne crittografate.

## <a name="prerequisites"></a>Prerequisiti


- Un account e una sottoscrizione di Azure. Se non è disponibile, iscriversi per ottenere una [versione di valutazione gratuita](https://azure.microsoft.com/pricing/free-trial/).
- Un database nel [database SQL di Azure](single-database-create-quickstart.md) o in [Azure SQL istanza gestita](../managed-instance/instance-create-quickstart.md).
- [SQL Server Management Studio](/sql/ssms/download-sql-server-management-studio-ssms) versione 13.0.700.242 o successive.
- [.NET Framework 4.6](/dotnet/framework/) o versioni successive (nel computer client).
- [Visual Studio](https://www.visualstudio.com/downloads/download-visual-studio-vs.aspx).
- [Azure PowerShell](/powershell/azure/) o [interfaccia](/cli/azure/install-azure-cli) della riga di comando di Azure

## <a name="enable-client-application-access"></a>Abilitare l'accesso alle applicazioni client

È necessario abilitare l'applicazione client per accedere al database nel database SQL impostando un'applicazione Azure Active Directory (Azure AD) e copiando l'ID e la *chiave* *dell'applicazione* necessarie per autenticare l'applicazione.

Per ottenere l'*ID applicazione* e la *chiave*, eseguire la procedura descritta in [Usare il portale per creare un'applicazione Azure Active Directory e un'entità servizio che possano accedere alle risorse](../../active-directory/develop/howto-create-service-principal-portal.md).

## <a name="create-a-key-vault-to-store-your-keys"></a>Creare un insieme di credenziali delle chiavi per archiviare le chiavi

Quando l'app client è configurata e si dispone dell'ID applicazione, è necessario creare un insieme di credenziali delle chiavi e configurare il criterio di accesso per consentire all'utente e all'applicazione di accedere alle chiavi private dell'insieme di credenziali, ovvero le chiavi Always Encrypted. Sono necessarie le autorizzazioni *create*, *get*, *list*, *sign*, *verify*, *wrapKey* e *unwrapKey* per creare una nuova chiave master di colonna e per configurare la crittografia con SQL Server Management Studio.

È possibile creare rapidamente un insieme di credenziali delle chiavi eseguendo lo script seguente. Per una spiegazione dettagliata di questi comandi e per altre informazioni sulla creazione e sulla configurazione di un insieme di credenziali delle chiavi, vedere [che cos'è Azure Key Vault?](../../key-vault/general/overview.md).

# <a name="powershell"></a>[PowerShell](#tab/azure-powershell)

> [!IMPORTANT]
> Il modulo Azure Resource Manager di PowerShell (RM) è ancora supportato dal database SQL di Azure, ma tutte le attività di sviluppo future sono per il modulo AZ. SQL. Il modulo AzureRM continuerà a ricevere correzioni di bug almeno fino a dicembre 2020.  Gli argomenti per i comandi nei moduli Az e AzureRm sono sostanzialmente identici. Per altre informazioni sulla compatibilità, vedere [Introduzione del nuovo modulo Az di Azure PowerShell](/powershell/azure/new-azureps-module-az).

```powershell
$subscriptionName = '<subscriptionName>'
$userPrincipalName = '<username@domain.com>'
$applicationId = '<applicationId from AAD application>'
$resourceGroupName = '<resourceGroupName>' # use the same resource group name when creating your SQL Database below
$location = '<datacenterLocation>'
$vaultName = '<vaultName>'

Connect-AzAccount
$subscriptionId = (Get-AzSubscription -SubscriptionName $subscriptionName).Id
Set-AzContext -SubscriptionId $subscriptionId

New-AzResourceGroup -Name $resourceGroupName -Location $location
New-AzKeyVault -VaultName $vaultName -ResourceGroupName $resourceGroupName -Location $location

Set-AzKeyVaultAccessPolicy -VaultName $vaultName -ResourceGroupName $resourceGroupName -PermissionsToKeys create,get,wrapKey,unwrapKey,sign,verify,list -UserPrincipalName $userPrincipalName
Set-AzKeyVaultAccessPolicy  -VaultName $vaultName  -ResourceGroupName $resourceGroupName -ServicePrincipalName $applicationId -PermissionsToKeys get,wrapKey,unwrapKey,sign,verify,list
```

# <a name="azure-cli"></a>[Interfaccia della riga di comando di Azure](#tab/azure-cli)

```azurecli
$subscriptionName = '<subscriptionName>'
$userPrincipalName = '<username@domain.com>'
$applicationId = '<applicationId from AAD application>'
$resourceGroupName = '<resourceGroupName>' # use the same resource group name when creating your database in Azure SQL Database below
$location = '<datacenterLocation>'
$vaultName = '<vaultName>'

az login
az account set --subscription $subscriptionName

az group create --location $location --name $resourceGroupName

az keyvault create --name $vaultName --resource-group $resourceGroupName --location $location

az keyvault set-policy --name $vaultName --key-permissions create get list sign unwrapKey verify wrapKey --resource-group $resourceGroupName --upn $userPrincipalName
az keyvault set-policy --name $vaultName --key-permissions get list sign unwrapKey verify wrapKey --resource-group $resourceGroupName --spn $applicationId
```

---

## <a name="connect-with-ssms"></a>Connettersi a SSMS

Aprire SQL Server Management Studio (SSMS) e connettersi al server o gestito con il database.

1. Aprire SSMS. (Vai a **Connetti**  >  **Motore di database** per aprire la finestra **Connetti al server** , se non è aperta.

2. Immettere il nome del server o il nome dell'istanza e le credenziali. 

    ![Copia della stringa di connessione](./media/always-encrypted-azure-key-vault-configure/ssms-connect.png)

Se viene visualizzata la finestra **Nuova regola firewall** , accedere ad Azure e lasciare che SSMS crei una nuova regola firewall per l'utente.

## <a name="create-a-table"></a>Creare una tabella

Questa sezione contiene istruzioni per creare una tabella con i dati dei pazienti. Non è crittografata inizialmente e la crittografia verrà configurata nella sezione successiva.

1. Espandere **Database**.
2. Fare clic con il pulsante destro del mouse sul database e scegliere **nuova query**.
3. Incollare il comando Transact-SQL (T-SQL) seguente nella finestra della nuova query ed **eseguirlo** .

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
GO
```

## <a name="encrypt-columns-configure-always-encrypted"></a>Crittografare le colonne configurando la crittografia sempre attiva

SSMS offre una procedura guidata per configurare facilmente la crittografia sempre attiva impostando la chiave master di colonna, la chiave di crittografia di colonna e le colonne crittografate automaticamente.

1. Espandere **database**  >  **Clinic**  >  **tabelle**.
2. Fare clic con il pulsante destro del mouse sulla tabella **Patients** e selezionare **Crittografa colonne** per aprire la procedura guidata Always Encrypted:

    ![Screenshot che evidenzia le colonne Encrypt... opzione di menu.](./media/always-encrypted-azure-key-vault-configure/encrypt-columns.png)

La procedura guidata Always Encrypted include le sezioni seguenti: **Selezione colonne**, **Configurazione della chiave master**, **Convalida** e **Riepilogo**.

### <a name="column-selection"></a>Selezione colonne

Fare clic su **Avanti** nella pagina **Introduzione** per aprire la pagina **Selezione colonne**. In questa pagina verranno selezionate le colonne da crittografare, [il tipo di crittografia e la chiave di crittografia di colonna (CEK)](/sql/relational-databases/security/encryption/always-encrypted-wizard#Anchor_2) da usare.

Crittografare il **CF** e la **data di nascita** per ogni paziente. La colonna relativa al codice fiscale usa la crittografia deterministica, che supporta ricerche di uguaglianza, join e raggruppamenti. La colonna relativa alla data di nascita usa la crittografia casuale, che non supporta le operazioni.

Impostare il **Tipo di crittografia** per la colonna CF su **Deterministica** e la colonna Data di nascita su **Casuale**. Fare clic su **Avanti**.

![Crittografa colonne](./media/always-encrypted-azure-key-vault-configure/column-selection.png)

### <a name="master-key-configuration"></a>Configurazione della chiave master

La pagina **Configurazione della chiave master** consente di impostare la CMK e selezionare il provider dell'archivio chiavi in cui verrà archiviata la CMK. Attualmente è possibile archiviare una chiave master di colonna nell'archivio certificati di Windows, nell'insieme di credenziali delle chiavi di Azure o in un modulo di protezione hardware.

Questa esercitazione illustra come archiviare le chiavi nell'insieme di credenziali delle chiavi di Azure.

1. Selezionare **Insieme di credenziali delle chiavi di Azure**.
2. Selezionare l'insieme di credenziali delle chiavi desiderato dall'elenco a discesa.
3. Fare clic su **Avanti**.

![Configurazione della chiave master](./media/always-encrypted-azure-key-vault-configure/master-key-configuration.png)

### <a name="validation"></a>Convalida

È attualmente possibile crittografare le colonne o salvare uno script di PowerShell da eseguire in un secondo momento. Per questa esercitazione selezionare **Procedi per completare ora** e fare clic su **Avanti**.

### <a name="summary"></a>Riepilogo

Verificare che tutte le impostazioni siano corrette e fare clic su **Fine** per completare la configurazione della crittografia sempre attiva.

![Screenshot mostra la pagina risultati con attività contrassegnate come passate.](./media/always-encrypted-azure-key-vault-configure/summary.png)

### <a name="verify-the-wizards-actions"></a>Confermare le azioni della procedura guidata

Al termine della procedura guidata, il database è configurato per la crittografia sempre attiva. La procedura guidata esegue le azioni seguenti:

- È stata creata una chiave master di colonna ed è stata archiviata nell'insieme di credenziali delle chiavi di Azure.
- È stata creata una chiave di crittografia di colonna ed è stata archiviata nell'insieme di credenziali delle chiavi di Azure.
- Configurazione delle colonne selezionate per la crittografia. La tabella Patients attualmente è vuota, ma eventuali dati esistenti nelle colonne selezionate sono ora crittografati.

È possibile verificare la creazione di chiavi in SSMS espandendo le **Clinic**  >  chiavi di **sicurezza**  >  **Always Encrypted** di Clinic.

## <a name="create-a-client-application-that-works-with-the-encrypted-data"></a>Creare un'applicazione client che funziona con i dati crittografati

Ora che la crittografia Always Encrypted è configurata, è possibile creare un'applicazione che esegua *inserimenti* e *selezioni* nelle colonne crittografate.  

> [!IMPORTANT]
> L'applicazione deve usare oggetti [SqlParameter](/dotnet/api/system.data.sqlclient.sqlparameter) per trasferire dati di testo non crittografato al server con colonne con la crittografia sempre attiva. Il trasferimento di valori letterali senza usare oggetti SqlParameter genererà un'eccezione.

1. Aprire Visual Studio e creare una nuova **Applicazione console** C# (Visual Studio 2015 e versioni precedenti) o **App console (.NET Framework)** (Visual Studio 2017 e versioni successive). Verificare che il progetto sia impostato su **.NET Framework 4.6** o versione successiva.
2. Denominare il progetto **AlwaysEncryptedConsoleAKVApp** e fare clic su **OK**.
3. Installare i pacchetti NuGet seguenti passando a **strumenti**  >  **Gestione pacchetti NuGet**  >  **console di gestione pacchetti**.

Eseguire queste due righe di codice nella console di gestione pacchetti:

   ```powershell
   Install-Package Microsoft.SqlServer.Management.AlwaysEncrypted.AzureKeyVaultProvider
   Install-Package Microsoft.IdentityModel.Clients.ActiveDirectory
   ```

## <a name="modify-your-connection-string-to-enable-always-encrypted"></a>Modificare la stringa di connessione per abilitare la crittografia sempre attiva

Questa sezione descrive come abilitare la crittografia sempre attiva nella stringa di connessione del database.

Per abilitare Always Encrypted è necessario aggiungere la parola chiave di **Column Encryption Setting** alla stringa di connessione e impostarla su **Abilitata**.

È possibile impostarla direttamente nella stringa di connessione o tramite [SqlConnectionStringBuilder](/dotnet/api/system.data.sqlclient.sqlconnectionstringbuilder). L'applicazione di esempio nella sezione successiva mostra come usare **SqlConnectionStringBuilder**.

### <a name="enable-always-encrypted-in-the-connection-string"></a>Abilitare la crittografia sempre attiva nella stringa di connessione

Aggiungere la parola chiave seguente alla stringa di connessione.

   `Column Encryption Setting=Enabled`

### <a name="enable-always-encrypted-with-sqlconnectionstringbuilder"></a>Abilitare la crittografia sempre attiva con SqlConnectionStringBuilder

Il codice seguente mostra come abilitare Always Encrypted impostando [SqlConnectionStringBuilder.ColumnEncryptionSetting](/dotnet/api/system.data.sqlclient.sqlconnectionstringbuilder.columnencryptionsetting) su [Abilitata](/dotnet/api/system.data.sqlclient.sqlconnectioncolumnencryptionsetting).

```csharp
// Instantiate a SqlConnectionStringBuilder.
SqlConnectionStringBuilder connStringBuilder = new SqlConnectionStringBuilder("replace with your connection string");

// Enable Always Encrypted.
connStringBuilder.ColumnEncryptionSetting = SqlConnectionColumnEncryptionSetting.Enabled;
```

## <a name="register-the-azure-key-vault-provider"></a>Registrare il provider dell'insieme di credenziali delle chiavi di Azure
Il codice seguente mostra come registrare il provider dell'insieme di credenziali delle chiavi di Azure con il driver ADO.NET.

```csharp
private static ClientCredential _clientCredential;

static void InitializeAzureKeyVaultProvider() {
    _clientCredential = new ClientCredential(applicationId, clientKey);

    SqlColumnEncryptionAzureKeyVaultProvider azureKeyVaultProvider = new SqlColumnEncryptionAzureKeyVaultProvider(GetToken);

    Dictionary<string, SqlColumnEncryptionKeyStoreProvider> providers = new Dictionary<string, SqlColumnEncryptionKeyStoreProvider>();

    providers.Add(SqlColumnEncryptionAzureKeyVaultProvider.ProviderName, azureKeyVaultProvider);
    SqlConnection.RegisterColumnEncryptionKeyStoreProviders(providers);
}
```

## <a name="always-encrypted-sample-console-application"></a>Applicazione console di esempio della crittografia sempre attiva

Questo esempio dimostra come:

- Modificare la stringa di connessione per abilitare la crittografia sempre attiva.
- Registrare l'insieme di credenziali delle chiavi di Azure come provider dell'archivio chiavi dell'applicazione.  
- Inserire dati nelle colonne crittografate.
- Selezionare un record filtrando in base a un valore specifico in una colonna crittografata.

Sostituire il contenuto di *Program.cs* con il codice seguente. Sostituire la stringa di connessione per la variabile globale connectionString nella riga che precede immediatamente il metodo Main con la stringa di connessione valida del portale di Azure. Questa è l'unica modifica che è necessario apportare al codice.

Eseguire l'app per vedere in azione la crittografia sempre attiva.

```csharp
using System;
using System.Collections.Generic;
using System.Linq;
using System.Text;
using System.Threading.Tasks;
using System.Data;
using System.Data.SqlClient;
using Microsoft.IdentityModel.Clients.ActiveDirectory;
using Microsoft.SqlServer.Management.AlwaysEncrypted.AzureKeyVaultProvider;

namespace AlwaysEncryptedConsoleAKVApp {
    class Program {
        // Update this line with your Clinic database connection string from the Azure portal.
        static string connectionString = @"<connection string from the portal>";
        static string applicationId = @"<application ID from your AAD application>";
        static string clientKey = "<key from your AAD application>";

        static void Main(string[] args) {
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

            InsertPatient(new Patient() {
                SSN = "999-99-0001",
                FirstName = "Orlando",
                LastName = "Gee",
                BirthDate = DateTime.Parse("01/04/1964")
            });
            InsertPatient(new Patient() {
                SSN = "999-99-0002",
                FirstName = "Keith",
                LastName = "Harris",
                BirthDate = DateTime.Parse("06/20/1977")
            });
            InsertPatient(new Patient() {
                SSN = "999-99-0003",
                FirstName = "Donna",
                LastName = "Carreras",
                BirthDate = DateTime.Parse("02/09/1973")
            });
            InsertPatient(new Patient() {
                SSN = "999-99-0004",
                FirstName = "Janet",
                LastName = "Gates",
                BirthDate = DateTime.Parse("08/31/1985")
            });
            InsertPatient(new Patient() {
                SSN = "999-99-0005",
                FirstName = "Lucy",
                LastName = "Harrington",
                BirthDate = DateTime.Parse("05/06/1993")
            });

            // Fetch and display all patients.
            Console.WriteLine(Environment.NewLine + "All the records currently in the Patients table:");

            foreach (Patient patient in SelectAllPatients()) {
                Console.WriteLine(patient.FirstName + " " + patient.LastName + "\tSSN: " + patient.SSN + "\tBirthdate: " + patient.BirthDate);
            }

            // Get patients by SSN.
            Console.WriteLine(Environment.NewLine + "Now lets locate records by searching the encrypted SSN column.");

            string ssn;

            // This very simple validation only checks that the user entered 11 characters.
            // In production be sure to check all user input and use the best validation for your specific application.
            do {
                Console.WriteLine("Please enter a valid SSN (ex. 999-99-0003):");
                ssn = Console.ReadLine();
            } while (ssn.Length != 11);

            // The example allows duplicate SSN entries so we will return all records
            // that match the provided value and store the results in selectedPatients.
            Patient selectedPatient = SelectPatientBySSN(ssn);

            // Check if any records were returned and display our query results.
            if (selectedPatient != null) {
                Console.WriteLine("Patient found with SSN = " + ssn);
                Console.WriteLine(selectedPatient.FirstName + " " + selectedPatient.LastName + "\tSSN: "
                    + selectedPatient.SSN + "\tBirthdate: " + selectedPatient.BirthDate);
            }
            else {
                Console.WriteLine("No patients found with SSN = " + ssn);
            }

            Console.WriteLine("Press Enter to exit...");
            Console.ReadLine();
        }

        private static ClientCredential _clientCredential;

        static void InitializeAzureKeyVaultProvider() {
            _clientCredential = new ClientCredential(applicationId, clientKey);

            SqlColumnEncryptionAzureKeyVaultProvider azureKeyVaultProvider =
              new SqlColumnEncryptionAzureKeyVaultProvider(GetToken);

            Dictionary<string, SqlColumnEncryptionKeyStoreProvider> providers =
              new Dictionary<string, SqlColumnEncryptionKeyStoreProvider>();

            providers.Add(SqlColumnEncryptionAzureKeyVaultProvider.ProviderName, azureKeyVaultProvider);
            SqlConnection.RegisterColumnEncryptionKeyStoreProviders(providers);
        }

        public async static Task<string> GetToken(string authority, string resource, string scope) {
            var authContext = new AuthenticationContext(authority);
            AuthenticationResult result = await authContext.AcquireTokenAsync(resource, _clientCredential);

            if (result == null)
                throw new InvalidOperationException("Failed to obtain the access token");
            return result.AccessToken;
        }

        static int InsertPatient(Patient newPatient) {
            int returnValue = 0;

            string sqlCmdText = @"INSERT INTO [dbo].[Patients] ([SSN], [FirstName], [LastName], [BirthDate])
     VALUES (@SSN, @FirstName, @LastName, @BirthDate);";

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

            using (sqlCmd.Connection = new SqlConnection(connectionString)) {
                try {
                    sqlCmd.Connection.Open();
                    sqlCmd.ExecuteNonQuery();
                }
                catch (Exception ex) {
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


        static List<Patient> SelectAllPatients() {
            List<Patient> patients = new List<Patient>();

            SqlCommand sqlCmd = new SqlCommand(
              "SELECT [SSN], [FirstName], [LastName], [BirthDate] FROM [dbo].[Patients]",
                new SqlConnection(connectionString));

            using (sqlCmd.Connection = new SqlConnection(connectionString))

            using (sqlCmd.Connection = new SqlConnection(connectionString)) {
                try {
                    sqlCmd.Connection.Open();
                    SqlDataReader reader = sqlCmd.ExecuteReader();

                    if (reader.HasRows) {
                        while (reader.Read()) {
                            patients.Add(new Patient() {
                                SSN = reader[0].ToString(),
                                FirstName = reader[1].ToString(),
                                LastName = reader["LastName"].ToString(),
                                BirthDate = (DateTime)reader["BirthDate"]
                            });
                        }
                    }
                }
                catch (Exception ex) {
                    throw;
                }
            }

            return patients;
        }

        static Patient SelectPatientBySSN(string ssn) {
            Patient patient = new Patient();

            SqlCommand sqlCmd = new SqlCommand(
                "SELECT [SSN], [FirstName], [LastName], [BirthDate] FROM [dbo].[Patients] WHERE [SSN]=@SSN",
                new SqlConnection(connectionString));

            SqlParameter paramSSN = new SqlParameter(@"@SSN", ssn);
            paramSSN.DbType = DbType.AnsiStringFixedLength;
            paramSSN.Direction = ParameterDirection.Input;
            paramSSN.Size = 11;

            sqlCmd.Parameters.Add(paramSSN);

            using (sqlCmd.Connection = new SqlConnection(connectionString)) {
                try {
                    sqlCmd.Connection.Open();
                    SqlDataReader reader = sqlCmd.ExecuteReader();

                    if (reader.HasRows) {
                        while (reader.Read()) {
                            patient = new Patient() {
                                SSN = reader[0].ToString(),
                                FirstName = reader[1].ToString(),
                                LastName = reader["LastName"].ToString(),
                                BirthDate = (DateTime)reader["BirthDate"]
                            };
                        }
                    }
                    else {
                        patient = null;
                    }
                }
                catch (Exception ex) {
                    throw;
                }
            }
            return patient;
        }

        // This method simply deletes all records in the Patients table to reset our demo.
        static int ResetPatientsTable() {
            int returnValue = 0;

            SqlCommand sqlCmd = new SqlCommand("DELETE FROM Patients");
            using (sqlCmd.Connection = new SqlConnection(connectionString)) {
                try {
                    sqlCmd.Connection.Open();
                    sqlCmd.ExecuteNonQuery();

                }
                catch (Exception ex) {
                    returnValue = 1;
                }
            }
            return returnValue;
        }
    }

    class Patient {
        public string SSN { get; set; }
        public string FirstName { get; set; }
        public string LastName { get; set; }
        public DateTime BirthDate { get; set; }
    }
}
```

## <a name="verify-that-the-data-is-encrypted"></a>Verificare che i dati siano crittografati

È possibile verificare rapidamente che i dati effettivi del server siano crittografati eseguendo una query dei dati della tabella Patients con SSMS tramite la connessione corrente in cui l'impostazione **Column Encryption Setting** non è ancora abilitata.

Eseguire la query seguente nel database Clinic.

```sql
SELECT FirstName, LastName, SSN, BirthDate FROM Patients;
```

È possibile osservare che le colonne crittografate non contengono dati di testo non crittografato.

   ![Screenshot che indica che le colonne crittografate non contengono dati in testo non crittografato.](./media/always-encrypted-azure-key-vault-configure/ssms-encrypted.png)

Per usare SSMS per accedere ai dati di testo non crittografato, è prima necessario assicurarsi che l'utente abbia le autorizzazioni appropriate per Azure Key Vault: *get*, *unwrapKey* e *verify*. Per informazioni dettagliate, vedere [Creare e archiviare chiavi master della colonna (Always Encrypted)](/sql/relational-databases/security/encryption/create-and-store-column-master-keys-always-encrypted).

Aggiungere quindi il parametro *Column Encryption Setting=enabled* durante la connessione.

1. In SSMS fare clic con il pulsante destro del mouse sul server in **Esplora oggetti** e scegliere **Disconnetti**.
2. Fare clic su **Connetti**  >  **motore di database** per aprire la finestra **Connetti al server** e fare clic su **Opzioni**.
3. Fare clic su **Parametri aggiuntivi per la connessione** e digitare **Column Encryption Setting=Enabled**.

    ![Screenshot che mostra la scheda parametri aggiuntivi per la correzione.](./media/always-encrypted-azure-key-vault-configure/ssms-connection-parameter.png)

4. Eseguire la query seguente nel database Clinic.

   ```sql
   SELECT FirstName, LastName, SSN, BirthDate FROM Patients;
   ```

   È ora possibile visualizzare i dati non crittografati nelle colonne crittografate.
   
   ![Nuova applicazione console](./media/always-encrypted-azure-key-vault-configure/ssms-plaintext.png)

## <a name="next-steps"></a>Passaggi successivi

Quando il database è configurato per l'utilizzo di Always Encrypted, è possibile eseguire le operazioni seguenti:

- [Ruotare e pulire le chiavi](/sql/relational-databases/security/encryption/configure-always-encrypted-using-sql-server-management-studio).
- [Eseguire la migrazione dei dati già crittografati con la crittografia sempre attiva](/sql/relational-databases/security/encryption/migrate-sensitive-data-protected-by-always-encrypted).

## <a name="related-information"></a>Informazioni correlate

- [Crittografia sempre attiva (sviluppo client)](/sql/relational-databases/security/encryption/always-encrypted-client-development)
- [Transparent Data Encryption](/sql/relational-databases/security/encryption/transparent-data-encryption)
- [Crittografia SQL Server](/sql/relational-databases/security/encryption/sql-server-encryption)
- [Procedura guidata Always Encrypted](/sql/relational-databases/security/encryption/always-encrypted-wizard)
- [Blog sulla Crittografia sempre attiva](/archive/blogs/sqlsecurity/always-encrypted-key-metadata)
