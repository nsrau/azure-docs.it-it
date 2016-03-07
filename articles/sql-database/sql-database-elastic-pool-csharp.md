<properties
    pageTitle="Sviluppo di database C#: Pool di database elastici | Microsoft Azure"
    description="Usare tecniche di sviluppo di database in C# per creare un pool di database elastici per database SQL di Microsoft Azure in modo da poter condividere risorse tra più database."
    services="sql-database"
    keywords="database c#,sviluppo sql"
    documentationCenter=""
    authors="stevestein"
    manager="jeffreyg"
    editor=""/>

<tags
    ms.service="sql-database"
    ms.devlang="NA"
    ms.topic="article"
    ms.tgt_pltfrm="powershell"
    ms.workload="data-management"
    ms.date="02/23/2016"
    ms.author="sstein"/>

# Sviluppo di database in C&#x23;: Creare e configurare un pool di database elastici per database SQL

> [AZURE.SELECTOR]
- [Azure portal](sql-database-elastic-pool-portal.md)
- [C#](sql-database-elastic-pool-csharp.md)
- [PowerShell](sql-database-elastic-pool-powershell.md)


Questo articolo illustra come creare un [pool di database elastici](sql-database-elastic-pool.md) per database SQL da un'applicazione che usa tecniche di sviluppo di database C#.

> [AZURE.NOTE] I pool di database elastici sono attualmente in anteprima e sono disponibili unicamente con i server di Database SQL V12. Se si usa un server di database SQL V11 è possibile [usare PowerShell per eseguire l'aggiornamento a V12 e creare un pool](sql-database-upgrade-server-powershell.md) in un unico passaggio.

Negli esempi viene usata la [libreria di database SQL di Azure per .NET](https://www.nuget.org/packages/Microsoft.Azure.Management.Sql). I singoli frammenti di codice sono suddivisi per maggiore chiarezza e un'applicazione console di esempio riunisce tutti i comandi nella sezione nella parte inferiore di questo articolo.


> [AZURE.NOTE] La libreria di database SQL per .NET è attualmente in anteprima.



Se non si dispone di una sottoscrizione di Azure, fare clic su **VERSIONE DI PROVA GRATUITA** nella parte superiore della pagina e quindi tornare a questo articolo. Per una copia gratuita di Visual Studio, vedere la pagina [Download di Visual Studio](https://www.visualstudio.com/downloads/download-visual-studio-vs).

## Installare le librerie richieste

Acquisire le librerie di gestione richieste installando i pacchetti seguenti mediante la [console di gestione pacchetti](http://docs.nuget.org/Consume/Package-Manager-Console) per lo sviluppo in ambiente SQL:

    Install-Package Microsoft.Azure.Management.Sql –Pre
    Install-Package Microsoft.Azure.Management.Resources –Pre
    Install-Package Microsoft.Azure.Common.Authentication –Pre


## Configurare l'autenticazione con Azure Active Directory

Prima di iniziare lo sviluppo di SQL in C#, è necessario completare alcune attività nel portale di Azure. È prima necessario abilitare l'applicazione per accedere all'API REST mediante la configurazione dell'autenticazione richiesta.

Le [API REST di Gestione risorse di Azure](https://msdn.microsoft.com/library/azure/dn948464.aspx) per l'autenticazione usano Azure Active Directory anziché i certificati usati dalle precedenti API REST di gestione del servizio Azure.

Per autenticare l'applicazione client in base all'utente corrente è necessario prima registrare l'applicazione nel dominio AAD associato alla sottoscrizione in cui sono state create le risorse di Azure. Se la sottoscrizione ad Azure è stata creata con un account Microsoft anziché un account aziendale o dell'istituto di istruzione si dispone già di un dominio AAD predefinito. La registrazione dell'applicazione può essere eseguita nel [portale classico](https://manage.windowsazure.com/).

Per creare una nuova applicazione e registrarla nell’active directory corrente procedere come descritto di seguito:

1. Scorrere il menu a sinistra per individuare il servizio **Active Directory** e aprirlo.

    ![Sviluppo di database SQL in C#: configurazione di Active Directory][1]

2. Selezionare la directory per l'autenticazione dell'applicazione e fare clic sul relativo **nome**.

    ![Selezionare una directory.][4]

3. Nella pagina della directory fare clic su **APPLICAZIONI**.

    ![Fare clic su Applicazioni.][5]

4. Fare clic su **AGGIUNGI** per creare una nuova applicazione.

    ![Fare clic sul pulsante Aggiungi: Creare un'applicazione C#.][6]

5. Selezionare **Aggiungi un'applicazione che l'organizzazione sta sviluppando**.

5. Fornire un **NOME** per l'app e selezionare **APPLICAZIONE CLIENT NATIVA**.

    ![Aggiunta di un'applicazione][7]

6. Specificare un **URI DI REINDIRIZZAMENTO**. Non è necessario che sia un endpoint effettivo, è sufficiente un URI valido.

    ![Aggiunta di un'applicazione][8]

7. Completare la creazione dell'applicazione, fare clic su **CONFIGURA** e copiare l’**ID CLIENT** (l'ID del client sarà necessario nel codice).

    ![Acquisire l'ID client][9]


1. Nella parte inferiore della pagina fare clic su **Aggiungi applicazione**.
1. Selezionare **App Microsoft**.
1. Selezionare **API di gestione del servizio Azure** e quindi completare la procedura guidata.
2. Con l'API selezionata è ora necessario concedere le autorizzazioni specifiche richieste per accedere a questa API selezionando **Accedi a gestione del servizio Azure (anteprima)**.

    ![Impostare le autorizzazioni][2]

2. Fare clic su **SAVE**.



### Identificare il nome di dominio

Il nome di dominio è obbligatorio per il codice. Un modo semplice per identificare il nome di dominio corretto è:

1. Accedere al [portale di Azure](https://portal.azure.com).
2. Passare il mouse sul proprio nome nell'angolo superiore destro e osservare il dominio visualizzato nella finestra popup. Sostituire **domain.onmicrosoft.com** nel frammento di codice seguente con il valore per l'account.

    ![Identificare il nome di dominio][3]



**Risorse aggiuntive di AAD**

Altre informazioni sull'uso di Azure Active Directory per l'autenticazione sono disponibili in [questo utile post di blog](http://www.cloudidentity.com/blog/2013/09/12/active-directory-authentication-library-adal-v1-for-net-general-availability/).


### Recuperare il token di accesso per l'utente corrente

L'applicazione client deve recuperare il token di accesso all'applicazione per l'utente corrente. Quando un utente esegue per la prima volta il codice gli verrà richiesto di immettere le credenziali utente e il token risultante viene memorizzato nella cache in locale. Alle successive esecuzioni il token viene recuperato dalla cache e all’utente viene chiesto di effettuare l’accesso solo se il token è scaduto.


    private static AuthenticationResult GetAccessToken()
    {
        AuthenticationContext authContext = new AuthenticationContext
            ("https://login.windows.net/" + domainName /* Tenant ID or AAD domain */);

        AuthenticationResult token = authContext.AcquireToken
            ("https://management.azure.com/"/* the Azure Resource Management endpoint */,
                clientId,
        new Uri(redirectUri) /* redirect URI */,
        PromptBehavior.Auto /* with Auto user will not be prompted if an unexpired token is cached */);

        return token;
    }



> [AZURE.NOTE] Negli esempi inclusi in questo articolo viene utilizzata una forma asincrona di ogni richiesta di API e blocco fino al completamento della chiamata REST nel servizio sottostante. Sono disponibili metodi asincroni.



## Creare un gruppo di risorse

Con Gestione risorse, tutte le risorse devono essere create in un gruppo di risorse. Un gruppo di risorse è un contenitore che contiene risorse correlate per un'applicazione. Per creare un pool di database elastici è necessario un server di Database SQL di Azure in un gruppo di risorse esistente. Eseguire il codice C# seguente per creare il gruppo di risorse:


    // Create a resource management client
    ResourceManagementClient resourceClient = new ResourceManagementClient(new TokenCloudCredentials("XXXXXXXX-XXXX-XXXX-XXXX-XXXXXXXXXXXX" /*subscription id*/, token.AccessToken ));

    // Resource group parameters
    ResourceGroup resourceGroupParameters = new ResourceGroup()
    {
        Location = "South Central US"
    };

    //Create a resource group
    var resourceGroupResult = resourceClient.ResourceGroups.CreateOrUpdate("resourcegroup-name", resourceGroupParameters);



## Creare un server

I pool di database elastici sono contenuti all'interno di server di Database SQL di Azure perciò il prossimo passaggio è quello di creare un server. Il nome del server deve essere globalmente univoco tra tutti i server SQL Azure, quindi verrà visualizzato un messaggio di errore se il nome del server è già in uso. Vale inoltre la pena notare che il completamento di questo comando potrebbe richiedere alcuni minuti. Per permettere a un'applicazione di connettersi al server è necessario anche creare una regola firewall sul server per aprire l'accesso dall'indirizzo IP del client.


    // Create a SQL Database management client
    SqlManagementClient sqlClient = new SqlManagementClient(new TokenCloudCredentials("XXXXXXXX-XXXX-XXXX-XXXX-XXXXXXXXXXXX" /* Subscription id*/, token.AccessToken));

    // Create a server
    ServerCreateOrUpdateParameters serverParameters = new ServerCreateOrUpdateParameters()
    {
        Location = "South Central US",
        Properties = new ServerCreateOrUpdateProperties()
        {
            AdministratorLogin = "ServerAdmin",
            AdministratorLoginPassword = "P@ssword1",
            Version = "12.0"
        }
    };

    var serverResult = sqlClient.Servers.CreateOrUpdate("resourcegroup-name", "server-name", serverParameters);




## Creare una regola del firewall del server per consentire l'accesso al server

Per impostazione predefinita un server non ha regole di firewall, quindi non è possibile connettersi al server stesso da qualsiasi posizione. Per connettersi a un server o a qualsiasi database nel server, è necessario definire una [regola firewall](sql-database-firewall-configure.md) che consenta l'accesso dall'indirizzo IP del client.

Nell'esempio seguente viene creata una regola firewall che consente di aprire l'accesso al server da qualsiasi indirizzo IP. È consigliabile creare gli account di accesso SQL e le password appropriati per proteggere il database e non basarsi sulle regole del firewall come difesa primaria contro le intrusioni. Per dettagli, vedere [Gestione di database e account di accesso in database SQL di Azure](sql-database-manage-logins.md).


    // Create a firewall rule on the server to allow TDS connection
    FirewallRuleCreateOrUpdateParameters firewallParameters = new FirewallRuleCreateOrUpdateParameters()
    {
        Properties = new FirewallRuleCreateOrUpdateProperties()
        {
            StartIpAddress = "0.0.0.0",
            EndIpAddress = "255.255.255.255"
        }
    };

    var firewallResult = sqlClient.FirewallRules.CreateOrUpdate("resourcegroup-name", "server-name", "FirewallRule1", firewallParameters);




Per consentire ad altri servizi di Azure di accedere a un server, aggiungere una regola del firewall e impostare StartIpAddress e EndIpAddress su 0.0.0.0. Si noti che ciò consente al traffico di Azure proveniente da *qualsiasi* sottoscrizione di Azure di accedere al server.


## Creare un database

Nell'esempio seguente viene creato un nuovo database Basic; se un database con lo stesso nome esiste nel server, il database esistente verrà aggiornato.

        // Create a database

        // Retrieve the server on which the database will be created
        Server currentServer = sqlClient.Servers.Get("resourcegroup-name", "server-name").Server;

        // Create a database: configure create or update parameters and properties explicitly
        DatabaseCreateOrUpdateParameters newDatabaseParameters = new DatabaseCreateOrUpdateParameters()
        {
            Location = currentServer.Location,
            Properties = new DatabaseCreateOrUpdateProperties()
            {
                Edition = "Basic",
                RequestedServiceObjectiveName = "Basic",
                MaxSizeBytes = 2147483648,
                Collation = "SQL_Latin1_General_CP1_CI_AS"
            }
        };

        var dbResponse = sqlClient.Databases.CreateOrUpdate("resourcegroup-name", "server-name", "Database1", newDatabaseParameters);



## Creare un pool di database elastici

Nell'esempio seguente viene creato un nuovo pool di database elastici:



    // Create elastic pool: configure create or update parameters and properties explicitly
    ElasticPoolCreateOrUpdateParameters newPoolParameters = new ElasticPoolCreateOrUpdateParameters()
    {
        Location = "South Central US",
        Properties = new ElasticPoolCreateOrUpdateProperties()
        {
            Edition = "Standard",
            Dtu = 100,  // alternatively set StorageMB, if both are specified they must agree based on the eDTU:storage ratio of the edition
            DatabaseDtuMin = 0,
            DatabaseDtuMax = 100
         }
    };

    // Create the pool
    var newPoolResponse = sqlClient.ElasticPools.CreateOrUpdate("resourcegroup-name", "server-name", "ElasticPool1", newPoolParameters);


## Aggiornare un pool di database elastici

L'esempio seguente consente di aggiornare le caratteristiche delle prestazioni di un pool di database elastici esistente:

    // Retrieve existing pool properties
    var currentPool = sqlClient.ElasticPools.Get("resourcegroup-name", "server-name", "ElasticPool1").ElasticPool;

    // Configure create or update parameters with existing property values, override those to be changed.
    ElasticPoolCreateOrUpdateParameters updatePoolParameters = new ElasticPoolCreateOrUpdateParameters()
    {
        Location = currentPool.Location,
        Properties = new ElasticPoolCreateOrUpdateProperties()
        {
            Edition = currentPool.Properties.Edition,
            DatabaseDtuMax = 50, /* Setting DatabaseDtuMax to 50 limits the eDTUs that any one database can consume */
            DatabaseDtuMin = 10, /* Setting DatabaseDtuMin above 0 limits the number of databases that can be stored in the pool */
            Dtu = (int)currentPool.Properties.Dtu,
            StorageMB = currentPool.Properties.StorageMB,  /* For a Standard pool there is 1 GB of storage per eDTU. */
        }
    };

    newPoolResponse = sqlClient.ElasticPools.CreateOrUpdate("resourcegroup-name", "server-name", "ElasticPool1", newPoolParameters);



## Spostare un database esistente in un pool di database elastici

*Dopo aver creato un pool è anche possibile usare Transact-SQL per lo spostamento dei database esistenti da e verso un pool. Per informazioni dettagliate, vedere il [riferimento al pool di database elastici - Transact-SQL](sql-database-elastic-pool-reference.md#Transact-SQL).*

Nell'esempio seguente si sposta un database SQL di Azure esistente in un pool:


    // Update database service objective to add the database to a pool

    // Retrieve current database properties
    currentDatabase = sqlClient.Databases.Get("resourcegroup-name", "server-name", "Database1").Database;

    // Configure create or update parameters with existing property values, override those to be changed.
    DatabaseCreateOrUpdateParameters updatePooledDbParameters = new DatabaseCreateOrUpdateParameters()
    {
        Location = currentDatabase.Location,
        Properties = new DatabaseCreateOrUpdateProperties()
        {
            Edition = "Standard",
            RequestedServiceObjectiveName = "ElasticPool",
            ElasticPoolName = "ElasticPool1",
            MaxSizeBytes = currentDatabase.Properties.MaxSizeBytes,
            Collation = currentDatabase.Properties.Collation,
        }
    };

    // Update the database
    var dbUpdateResponse = sqlClient.Databases.CreateOrUpdate("resourcegroup-name", "server-name", "Database1", updatePooledDbParameters);




## Creare un nuovo database in un pool di database elastici

*Dopo la creazione di un pool è anche possibile usare Transact-SQL per la creazione di nuovi database elastici nel pool. Per informazioni dettagliate, vedere il [riferimento al pool di database elastici - Transact-SQL](sql-database-elastic-pool-reference.md#Transact-SQL).*

Nell'esempio seguente viene creato un nuovo database direttamente in un pool:


    // Create a new database in the pool

    // Create a database: configure create or update parameters and properties explicitly
    DatabaseCreateOrUpdateParameters newPooledDatabaseParameters = new DatabaseCreateOrUpdateParameters()
    {
        Location = currentServer.Location,
        Properties = new DatabaseCreateOrUpdateProperties()
        {
            Edition = "Standard",
            RequestedServiceObjectiveName = "ElasticPool",
            ElasticPoolName = "ElasticPool1",
            MaxSizeBytes = 268435456000, // 250 GB,
            Collation = "SQL_Latin1_General_CP1_CI_AS"
        }
    };

    var poolDbResponse = sqlClient.Databases.CreateOrUpdate("resourcegroup-name", "server-name", "Database2", newPooledDatabaseParameters);



## Elencare tutti i database in un pool di database elastici

Nell'esempio seguente vengono elencati tutti i database in un pool:

    //List databases in the elastic pool
    DatabaseListResponse dbListInPool = sqlClient.ElasticPools.ListDatabases("resourcegroup-name", "server-name", "ElasticPool1");
    Console.WriteLine("Databases in Elastic Pool {0}", "server-name.ElasticPool1");
    foreach (Database db in dbListInPool)
    {
        Console.WriteLine("  Database {0}", db.Name);
    }




## Applicazione console di esempio


    using Microsoft.Azure;
    using Microsoft.Azure.Management.Resources;
    using Microsoft.Azure.Management.Resources.Models;
    using Microsoft.Azure.Management.Sql;
    using Microsoft.Azure.Management.Sql.Models;
    using Microsoft.IdentityModel.Clients.ActiveDirectory;
    using System;
    using System.Security;

    namespace AzureSqlDatabaseRestApiExamples
    {
    class Program
    {
        /// <summary>
        /// Prompts for user credentials when first run or if the cached credentials have expired.
        /// </summary>
        /// <returns>The access token from AAD.</returns>
        private static AuthenticationResult GetAccessToken()
        {
            AuthenticationContext authContext = new AuthenticationContext
                ("https://login.windows.net/" + domainName /* Tenant ID or AAD domain */);

            AuthenticationResult token = authContext.AcquireToken
                ("https://management.azure.com/"/* the Azure Resource Management endpoint */,
                    clientId,
            new Uri(redirectUri) /* redirect URI */,
            PromptBehavior.Auto /* with Auto user will not be prompted if an unexpired token is cached */);

            return token;
        }

        private static AuthenticationResult GetAccessTokenUsingUserCredentials(UserCredential userCredential)
        {
            AuthenticationContext authContext = new AuthenticationContext
                ("https://login.windows.net/" /* AAD URI */
                + "YOU.onmicrosoft.com" /* Tenant ID or AAD domain */);

            AuthenticationResult token = authContext.AcquireToken(
                "https://management.azure.com/"/* the Azure Resource Management endpoint */,
                "XXXXXXXX-XXXX-XXXX-XXXX-XXXXXXXXXXXX" /* application client ID from AAD*/,
                userCredential);

            return token;
        }
        private static SecureString convertToSecureString(string secret)
        {
            var secureStr = new SecureString();
            if (secret.Length > 0)
            {
                foreach (var c in secret.ToCharArray()) secureStr.AppendChar(c);
            }
            return secureStr;
        }

        static void Main(string[] args)
        {
            var token = GetAccessToken();

            // Who am I?
            Console.WriteLine("Identity is {0} {1}", token.UserInfo.GivenName, token.UserInfo.FamilyName);
            Console.WriteLine("Token expires on {0}", token.ExpiresOn);
            Console.WriteLine("");

            // Create a resource management client
            ResourceManagementClient resourceClient = new ResourceManagementClient(new TokenCloudCredentials("XXXXXXXX-XXXX-XXXX-XXXX-XXXXXXXXXXXX" /*subscription id*/, token.AccessToken));

            // Resource group parameters
            ResourceGroup resourceGroupParameters = new ResourceGroup()
            {
                Location = "South Central US"
            };

            //Create a resource group
            var resourceGroupResult = resourceClient.ResourceGroups.CreateOrUpdate("resourcegroup-name", resourceGroupParameters);

            Console.WriteLine("Resource group {0} create or update completed with status code {1} ", resourceGroupResult.ResourceGroup.Name, resourceGroupResult.StatusCode);

            //create a SQL Database management client
            TokenCloudCredentials tokenCredentials = new TokenCloudCredentials("XXXXXXXX-XXXX-XXXX-XXXX-XXXXXXXXXXXX" /* Subscription id*/, token.AccessToken);

            SqlManagementClient sqlClient = new SqlManagementClient(tokenCredentials);

            // Create a server
            ServerCreateOrUpdateParameters serverParameters = new ServerCreateOrUpdateParameters()
            {
                Location = "South Central US",
                Properties = new ServerCreateOrUpdateProperties()
                {
                    AdministratorLogin = "ServerAdmin",
                    AdministratorLoginPassword = "P@ssword1",
                    Version = "12.0"
                }
            };

            var serverResult = sqlClient.Servers.CreateOrUpdate("resourcegroup-name", "server-name", serverParameters);

            var serverGetResult = sqlClient.Servers.Get("resourcegroup-name", "server-name");


            Console.WriteLine("Server {0} create or update completed with status code {1}", serverResult.Server.Name, serverResult.StatusCode);

            // Create a firewall rule on the server to allow TDS connection

            FirewallRuleCreateOrUpdateParameters firewallParameters = new FirewallRuleCreateOrUpdateParameters()
            {
                Properties = new FirewallRuleCreateOrUpdateProperties()
                {
                    StartIpAddress = "0.0.0.0",
                    EndIpAddress = "255.255.255.255"
                }
            };

            var firewallResult = sqlClient.FirewallRules.CreateOrUpdate("resourcegroup-name", "server-name", "FirewallRule1", firewallParameters);

            Console.WriteLine("Firewall rule {0} create or update completed with status code {1}", firewallResult.FirewallRule.Name, firewallResult.StatusCode);


            var dbResponse = sqlClient.Databases.CreateOrUpdate("resourcegroup-name", "server-name", "Database1", newDatabaseParameters);

            Console.WriteLine("Database {0} create or update completed with status code {1}. Service Objective {2} ", dbResponse.Database.Name, dbResponse.StatusCode, dbResponse.Database.Properties.ServiceObjective);


           // Create an elastic database pool
            var newPoolResponse = sqlClient.ElasticPools.CreateOrUpdate("resourcegroup-name", "server-name", "ElasticPool1", newPoolParameters);

            Console.WriteLine("Elastic pool {0} create or update completed with status code {1}.", newPoolResponse.ElasticPool.Name, newPoolResponse.StatusCode);

            // Update pool: retrieve existing pool properties
            var currentPool = sqlClient.ElasticPools.Get("resourcegroup-name", "server-name", "ElasticPool1").ElasticPool;

            // Update pool: configure create or update parameters with existing property values, override those to be changed.
            ElasticPoolCreateOrUpdateParameters updatePoolParameters = new ElasticPoolCreateOrUpdateParameters()
            {
                Location = currentPool.Location,
                Properties = new ElasticPoolCreateOrUpdateProperties()
                {
                    Edition = currentPool.Properties.Edition,
                    DatabaseDtuMax = 50, /* Setting DatabaseDtuMax to 50 limits the eDTUs that any one database can consume */
                    DatabaseDtuMin = 10, /* Setting DatabaseDtuMin above 0 limits the number of databases that can be stored in the pool */
                    Dtu = (int)currentPool.Properties.Dtu,
                    StorageMB = currentPool.Properties.StorageMB,  /* For a Standard pool there is 1 GB of storage per eDTU. */
                }
            };
            newPoolResponse = sqlClient.ElasticPools.CreateOrUpdate("resourcegroup-name", "server-name", "ElasticPool1", newPoolParameters);

            Console.WriteLine("Elastic pool {0} create or update completed with status code {1}.", newPoolResponse.ElasticPool.Name, newPoolResponse.StatusCode);

            // Update a databases service objective to add the database to a pool

            // Update database: retrieve current database properties
            currentDatabase = sqlClient.Databases.Get("resourcegroup-name", "server-name", "Database1").Database;

            // Update database: configure create or update parameters with existing property values, override those to be changed.
            DatabaseCreateOrUpdateParameters updatePooledDbParameters = new DatabaseCreateOrUpdateParameters()
            {
                Location = currentDatabase.Location,
                Properties = new DatabaseCreateOrUpdateProperties()
                {
                    Edition = "Standard",
                    RequestedServiceObjectiveName = "ElasticPool",
                    ElasticPoolName = "ElasticPool1",
                    MaxSizeBytes = currentDatabase.Properties.MaxSizeBytes,
                    Collation = currentDatabase.Properties.Collation,
                }
            };


            // Create a new database in the pool

            // Create a database: configure create or update parameters and properties explicitly
            DatabaseCreateOrUpdateParameters newPooledDatabaseParameters = new DatabaseCreateOrUpdateParameters()
            {
                Location = currentServer.Location,
                Properties = new DatabaseCreateOrUpdateProperties()
                {
                    Edition = "Standard",
                    RequestedServiceObjectiveName = "ElasticPool",
                    ElasticPoolName = "ElasticPool1",
                    MaxSizeBytes = 268435456000, // 250 GB,
                    Collation = "SQL_Latin1_General_CP1_CI_AS"
                }
            };

            var poolDbResponse = sqlClient.Databases.CreateOrUpdate("resourcegroup-name", "server-name", "Database2", newPooledDatabaseParameters);

            Console.WriteLine("Database {0} create or update completed with status code {1}. Service Objective: {2}({3}) ", poolDbResponse.Database.Name, poolDbResponse.StatusCode, poolDbResponse.Database.Properties.ServiceObjective, poolDbResponse.Database.Properties.ElasticPoolName);
      }
    }







## Risorse aggiuntive


[Database SQL](https://azure.microsoft.com/documentation/services/sql-database/)

[API di Gestione risorse di Azure](https://msdn.microsoft.com/library/azure/dn948464.aspx)

[Riferimento al pool di database elastici](sql-database-elastic-pool-reference.md).


<!--Image references-->
[1]: ./media/sql-database-elastic-pool-csharp/aad.png
[2]: ./media/sql-database-elastic-pool-csharp/permissions.png
[3]: ./media/sql-database-elastic-pool-csharp/getdomain.png
[4]: ./media/sql-database-elastic-pool-csharp/aad2.png
[5]: ./media/sql-database-elastic-pool-csharp/aad-applications.png
[6]: ./media/sql-database-elastic-pool-csharp/add.png
[7]: ./media/sql-database-elastic-pool-csharp/add-application.png
[8]: ./media/sql-database-elastic-pool-csharp/add-application2.png
[9]: ./media/sql-database-elastic-pool-csharp/clientid.png

<!---HONumber=AcomDC_0224_2016-->