<properties 
   pageTitle="Creazione e gestione del database SQL di Azure con C#" 
   description="In questo articolo viene illustrato come creare e gestire un database SQL di Azure con C# utilizzando la libreria di database SQL di Azure per .NET." 
   services="sql-database" 
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
   ms.date="03/23/2016"
   ms.author="sstein"/>

# Creazione e gestione di database SQL con C&#x23;

> [AZURE.SELECTOR]
- [Portale di Azure](sql-database-elastic-pool-create-portal.md)
- [C#](sql-database-client-library.md)
- [PowerShell](sql-database-elastic-pool-powershell.md)


## Panoramica

In questo articolo vengono forniti i comandi per eseguire diverse attività di gestione del database SQL di Azure con C# utilizzando la [libreria di database SQL di Azure per .NET](https://www.nuget.org/packages/Microsoft.Azure.Management.Sql)

I singoli frammenti di codice sono suddivisi per maggiore chiarezza e un'applicazione console di esempio riunisce tutti i comandi nella sezione nella parte inferiore di questo articolo.

La libreria di database SQL di Azure per .NET include un'API basata su [Gestione risorse di Azure](../resource-group-overview.md) che esegue il wrapping dell'[API REST di database SQL basata su Gestione risorse](https://msdn.microsoft.com/library/azure/mt163571.aspx). La libreria client segue il modello comune per le librerie client basate su Gestione risorse. Gestione risorse richiede gruppi di risorse e l'autenticazione con [Azure Active Directory](https://msdn.microsoft.com/library/azure/mt168838.aspx) (AAD).

<br>

> [AZURE.NOTE] La libreria di database SQL per .NET è attualmente in anteprima.

<br>

Se non si dispone di una sottoscrizione di Azure, fare clic su **VERSIONE DI PROVA GRATUITA** nella parte superiore della pagina e quindi tornare a questo articolo. Per una copia gratuita di Visual Studio, vedere la pagina [Download di Visual Studio](https://www.visualstudio.com/downloads/download-visual-studio-vs).

## Installare le librerie richieste

Acquisire le librerie di gestione richieste installando i pacchetti seguenti mediante la [console di gestione pacchetti](http://docs.nuget.org/Consume/Package-Manager-Console):

    PM> Install-Package Microsoft.Azure.Management.Sql –Pre
    PM> Install-Package Microsoft.Azure.Management.Resources –Pre
    PM> Install-Package Microsoft.Azure.Common.Authentication –Pre


## Configurare l'autenticazione con Azure Active Directory

È innanzitutto necessario abilitare l'applicazione per accedere all'API REST mediante configurazione dell’autenticazione richiesta.

Le [API REST di Gestione risorse di Azure](https://msdn.microsoft.com/library/azure/dn948464.aspx) per l'autenticazione utilizzano Azure Active Directory anziché i certificati utilizzati dalle precedenti API REST di gestione del servizio Azure.

Per autenticare l'applicazione client in base all'utente corrente è necessario prima registrare l'applicazione nel dominio AAD associato alla sottoscrizione in cui sono state create le risorse di Azure. Se la sottoscrizione ad Azure è stata creata con un account Microsoft anziché un account aziendale o dell'istituto di istruzione si dispone già di un dominio AAD predefinito. La registrazione dell'applicazione può essere eseguita nel [portale classico](https://manage.windowsazure.com/).

Per creare una nuova applicazione e registrarla nell’active directory corrente procedere come descritto di seguito:

1. Scorrere il menu a sinistra per individuare il servizio **Active Directory** e aprirlo.

    ![AAD][1]

2. Selezionare la directory per l'autenticazione dell'applicazione e fare clic sul relativo **nome**.

    ![Directory][4]

3. Nella pagina della directory fare clic su **APPLICAZIONI**.

    ![Applicazioni][5]

4. Fare clic su **AGGIUNGI** per creare una nuova applicazione.

    ![Aggiunta di un'applicazione][6]

5. Selezionare **Aggiungi un'applicazione che l'organizzazione sta sviluppando**.

5. Fornire un **NOME** per l'app e selezionare **APPLICAZIONE CLIENT NATIVA**.

    ![Aggiunta di un'applicazione][7]

6. Specificare un **URI DI REINDIRIZZAMENTO**. Non è necessario che sia un endpoint effettivo, è sufficiente un URI valido.

    ![Aggiunta di un'applicazione][8]

7. Completare la creazione dell'applicazione, fare clic su **CONFIGURA** e copiare l’**ID CLIENT** (l'ID del client sarà necessario nel codice).

    ![acquisisci ID client][9]


1. Nella parte inferiore della pagina fare clic su **Aggiungi applicazione**.
1. Selezionare **App Microsoft**.
1. Selezionare **API di gestione del servizio Azure** e quindi completare la procedura guidata.
2. Con l'API selezionata è ora necessario concedere le autorizzazioni specifiche richieste per accedere a questa API selezionando **Accedi a gestione del servizio Azure (anteprima)**.

    ![autorizzazioni][2]

2. Fare clic su **SAVE**.



### Identificare il nome di dominio

Il nome di dominio è obbligatorio per il codice. Un modo semplice per identificare il nome di dominio corretto è:

1. Accedere al [portale di Azure](https://portal.azure.com).
2. Passare il mouse sul proprio nome nell'angolo superiore destro e osservare il dominio visualizzato nella finestra popup.

    ![Identificare il nome di dominio][3]





**Risorse aggiuntive di AAD**

Altre informazioni sull'uso di Azure Active Directory per l'autenticazione sono disponibili in [questo utile post di blog](http://www.cloudidentity.com/blog/2013/09/12/active-directory-authentication-library-adal-v1-for-net-general-availability/).


### Recuperare il token di accesso per l'utente corrente 

L'applicazione client deve recuperare il token di accesso all'applicazione per l'utente corrente. Quando un utente esegue per la prima volta il codice gli verrà richiesto di immettere le credenziali utente e il token risultante viene memorizzato nella cache in locale. Alle successive esecuzioni il token viene recuperato dalla cache e all’utente viene chiesto di effettuare l’accesso solo se il token è scaduto.


    /// <summary>
    /// Prompts for user credentials when first run or if the cached credentials have expired.
    /// </summary>
    /// <returns>The access token from AAD.</returns>
    private static AuthenticationResult GetAccessToken()
    {
        AuthenticationContext authContext = new AuthenticationContext
            ("https://login.windows.net/" /* AAD URI */ 
                + "domain.onmicrosoft.com" /* Tenant ID or AAD domain */);

        AuthenticationResult token = authContext.AcquireToken
            ("https://management.azure.com/"/* the Azure Resource Management endpoint */, 
                "aa00a0a0-a0a0-0000-0a00-a0a00000a0aa" /* application client ID from AAD*/, 
        new Uri("urn:ietf:wg:oauth:2.0:oob") /* redirect URI */, 
        PromptBehavior.Auto /* with Auto user will not be prompted if an unexpired token is cached */);

        return token;
    }

Per creare script automatizzati in cui non è richiesto alcun intervento dell'utente, è possibile eseguire l'autenticazione basata su un'entità servizio invece di un utente. Questo approccio richiede la creazione e l’invio di un oggetto credenziale.




> [AZURE.NOTE] Negli esempi inclusi in questo articolo viene utilizzata una forma asincrona di ogni richiesta di API e blocco fino al completamento della chiamata REST nel servizio sottostante. Sono disponibili metodi asincroni.



## Creare un gruppo di risorse

Con Gestione risorse, tutte le risorse devono essere create in un gruppo di risorse. Un gruppo di risorse è un contenitore che contiene risorse correlate per un'applicazione. Con il database SQL di Azure deve essere innanzitutto creato il server di database all'interno di un gruppo di risorse esistente e poi il database nel server. Prima che un'applicazione possa connettersi al server o al database tramite TDS per l'invio di T-SQL è necessario inoltre creare una regola del firewall sul server per aprire l'accesso dall'indirizzo IP del client.


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

I database SQL sono contenuti nei server. Il nome del server deve essere globalmente univoco tra tutti i server SQL Azure, quindi verrà visualizzato un messaggio di errore se il nome del server è già in uso. Vale inoltre la pena notare che il completamento di questo comando potrebbe richiedere alcuni minuti.


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

Per impostazione predefinita non è possibile connettersi a un server da qualsiasi posizione. Per connettersi a un server tramite TDS e inviare T-SQL al server o a qualsiasi database nel server, è necessario definire una [regola del firewall](https://msdn.microsoft.com/library/azure/ee621782.aspx) che consenta di accedere dall’indirizzo IP del client.

Nell'esempio seguente viene creata una regola che consente di aprire l'accesso al server da qualsiasi indirizzo IP. È consigliabile creare gli account di accesso SQL e le password appropriati per proteggere il database e non basarsi sulle regole del firewall come difesa primaria contro le intrusioni.


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

Il seguente comando crea un nuovo database di base se nel server non esiste un database con lo stesso nome. Se esiste un database con lo stesso nome questo verrà aggiornato.

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



## Aggiornare un database 

Per aggiornare un database (ad esempio, modificando il livello di prestazioni e il livello di servizio) è necessario chiamare il metodo **Databases.CreateOrUpdate** proprio come in precedenza per la creazione o l'aggiornamento di un database. Impostare le proprietà **Edition** e **RequestedServiceObjectiveName** al livello di servizio e prestazioni desiderato. Si noti che quando si modifica l'edizione su o dal livello **Premium**, l'aggiornamento può richiedere del tempo a seconda delle dimensioni del database.

Quanto riportato di seguito consente di aggiornare un database SQL al livello Standard (S0):

    // Retrieve current database properties 
    var currentDatabase = sqlClient.Databases.Get("resourecegroup-name", "server-name", "Database1").Database;

    // Configure create or update parameters with existing property values, override those to be changed.
    DatabaseCreateOrUpdateParameters updateDatabaseParameters = new DatabaseCreateOrUpdateParameters()
    {
        Location = currentDatabase.Location,
        Properties = new DatabaseCreateOrUpdateProperties()
        {
            Edition = "Standard",
            RequestedServiceObjectiveName = "S0", // alternatively set the RequestedServiceObjectiveId
            MaxSizeBytes = currentDatabase.Properties.MaxSizeBytes,
            Collation = currentDatabase.Properties.Collation
        }
     };

    // Update the database
    dbResponse = sqlClient.Databases.CreateOrUpdate("resourcegroup-name", "server-name", "Database1", updateDatabaseParameters);


## Elencare tutti i database in un server

Per elencare tutti i database in un server, passare i nomi dei server e dei gruppi di risorse al metodo Databases.List:

    // List databases on the server
    DatabaseListResponse dbListOnServer = sqlClient.Databases.List("resourcegroup-name", "server-name");
    Console.WriteLine("Databases on Server {0}", "server-name");
    foreach (Database db in dbListOnServer)
    {
        Console.WriteLine("  Database {0}, Service Objective {1}", db.Name, db.Properties.ServiceObjective);
    }



## Creare un pool di database elastici

Per creare un nuovo pool su un server:



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

*Dopo aver creato un pool è anche possibile usare Transact-SQL per lo spostamento dei database esistenti da e verso un pool. Per informazioni dettagliate, vedere [riferimento al pool di database elastici - Transact-SQL](sql-database-elastic-pool-reference.md#Transact-SQL).*

Per spostare un database esistente in un pool:

    
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

*Dopo la creazione di un pool è anche possibile usare Transact-SQL per la creazione di nuovi database elastici nel pool. Per informazioni dettagliate, vedere [riferimento al pool di database elastici - Transact-SQL](sql-database-elastic-pool-reference.md#Transact-SQL).*

Per creare un nuovo database direttamente in un pool:

    
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

Per elencare tutti i database in un pool:

    //List databases in the elastic pool
    DatabaseListResponse dbListInPool = sqlClient.ElasticPools.ListDatabases("resourcegroup-name", "server-name", "ElasticPool1");
    Console.WriteLine("Databases in Elastic Pool {0}", "server-name.ElasticPool1");
    foreach (Database db in dbListInPool)
    {
        Console.WriteLine("  Database {0}", db.Name);
    }

## Eliminazione di un server

Per eliminare un server (che consente inoltre di eliminare i database e qualsiasi pool di database elastici sul server), eseguire il codice seguente:

    var serverOperationResponse = sqlClient.Servers.Delete("resourcegroup-name", "server-name");


## Eliminare un gruppo di risorse

Per eliminare un gruppo di risorse:

    // Delete the resource group
    var resourceOperationResponse = resourceClient.ResourceGroups.Delete("resourcegroup-name");



## Applicazione console di esempio


    using Microsoft.Azure;
    using Microsoft.Azure.Insights;
    using Microsoft.Azure.Insights.Models;
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
                ("https://login.windows.net/" /* AAD URI */ 
                + "domain.onmicrosoft.com" /* Tenant ID or AAD domain */);

            AuthenticationResult token = authContext.AcquireToken
                ("https://management.azure.com/"/* the Azure Resource Management endpoint */, 
                "XXXXXXXX-XXXX-XXXX-XXXX-XXXXXXXXXXXX" /* application client ID from AAD*/, 
                new Uri("urn:ietf:wg:oauth:2.0:oob") /* redirect URI */, 
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

            Console.WriteLine("Database {0} create or update completed with status code {1}. Service Objective {2} ", dbResponse.Database.Name, dbResponse.StatusCode, dbResponse.Database.Properties.ServiceObjective);

            // ...
            // Update database: retrieve current database properties 
            var currentDatabase = sqlClient.Databases.Get("resourcegroup-name", "server-name", "Database1").Database;

            // Update database: configure create or update parameters with existing property values, override those to be changed.
            DatabaseCreateOrUpdateParameters updateDatabaseParameters = new DatabaseCreateOrUpdateParameters()
            {
                Location = currentDatabase.Location,
                Properties = new DatabaseCreateOrUpdateProperties()
                {
                    Edition = "Standard",
                    RequestedServiceObjectiveName = "S0", // alternatively set the RequestedServiceObjectiveId
                    MaxSizeBytes = currentDatabase.Properties.MaxSizeBytes,
                    Collation = currentDatabase.Properties.Collation
                }
            };

            // Update the database
            dbResponse = sqlClient.Databases.CreateOrUpdate("resourcegroup-name", "server-name", "Database1", updateDatabaseParameters);

            Console.WriteLine("Database {0} create or update completed with status code {1}. Service Objective: {2} ", dbResponse.Database.Name, dbResponse.StatusCode, dbResponse.Database.Properties.ServiceObjective);

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

            // Update database service objective to add the database to a pool

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

            // Update the database
            var dbUpdateResponse = sqlClient.Databases.CreateOrUpdate("resourcegroup-name", "server-name", "Database1", updatePooledDbParameters);

            Console.WriteLine("Database {0} create or update completed with status code {1}. Service Objective: {2}({3}) ", dbUpdateResponse.Database.Name, dbUpdateResponse.StatusCode, dbUpdateResponse.Database.Properties.ServiceObjective, dbUpdateResponse.Database.Properties.ElasticPoolName);

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

            // List databases on the server
            DatabaseListResponse dbListOnServer = sqlClient.Databases.List("resourcegroup-name", "server-name");
            Console.WriteLine("Databases on Server {0}", "server-name");
            foreach (Database db in dbListOnServer)
            {
                Console.WriteLine("  Database {0}, Service Objective {1}", db.Name, db.Properties.ServiceObjective);
            }

            // List all servers, pools and databases in the resource group
            ServerListResponse serverList = new ServerListResponse();
            ElasticPoolListResponse poolList = new ElasticPoolListResponse();
            DatabaseListResponse dbListInPool = new DatabaseListResponse();

            Console.WriteLine("Servers in resource group {0}", "resourcegroup-name");
            serverList = sqlClient.Servers.List("resourcegroup-name");
            foreach (Server server in serverList)
            {
                Console.WriteLine("  Server '{0}' location: {1}", server.Name, server.Location);
                poolList = sqlClient.ElasticPools.List("resourcegroup-name", server.Name);
                foreach (ElasticPool pool in poolList)
                {
                    Console.WriteLine("    Elastic Pool '{0}' edition: {1} eDTU: {2} storage GB: {3} database eDTU min: {4} database eDTU max: {5}", pool.Name, pool.Properties.Edition, pool.Properties.Dtu, (pool.Properties.StorageMB/1024), pool.Properties.DatabaseDtuMin, pool.Properties.DatabaseDtuMax);
                    dbListInPool = sqlClient.ElasticPools.ListDatabases("resourcegroup-name", server.Name, pool.Name);
                    foreach(Database db in dbListInPool)
                    {
                        Console.WriteLine("      Database '{0}'", db.Name);                       
                    }
                }
            }

            // Metrics

            var endTime = String.Format(DateTime.Now.ToUniversalTime().ToString("s")) + "Z"; // as UTC in sortable time format yyyy-mm-ddThh:mm:ssZ
            var duration = TimeSpan.FromHours(2);
            var startTime = String.Format(DateTime.Now.Subtract(duration).ToUniversalTime().ToString("s")) + "Z";  // as UTC in sortable time format yyyy-mm-ddThh:mm:ssZ

            Console.WriteLine("");
            Console.WriteLine("Elastic pool metrics for 'ElasticPool1'");

            ElasticPoolMetricDefinitions poolMetricDefinition = new ElasticPoolMetricDefinitions();
            poolMetricDefinition = sqlClient.ElasticPools.ListMetricDefinitions("resourcegroup-name", "server-name", "ElasticPool1");

            Console.WriteLine("  Metric definitions: ");
            foreach (Microsoft.Azure.Management.Sql.Models.MetricDefinition metricDefinition in poolMetricDefinition.MetricDefinitions)
            {
                Console.WriteLine("    '{0}' unit: {1} aggregation type: {2}", metricDefinition.Name.LocalizedValue, metricDefinition.Unit, metricDefinition.PrimaryAggregationType);
            }
            Console.WriteLine("  Metric values: ");
            ElasticPoolMetrics elasticPoolMetrics = new ElasticPoolMetrics();
            elasticPoolMetrics = sqlClient.ElasticPools.ListMetrics("resourcegroup-name", "server-name", "ElasticPool1", "name.value eq 'dtu_consumption_percent'", "PT5M", startTime, endTime);
            foreach (Microsoft.Azure.Management.Sql.Models.Metric metric in elasticPoolMetrics.Metrics)
            {
                Console.WriteLine("    '{0}' unit: {1} time grain: {2} start time: {3} end time: {4} values: {5}", metric.Name.LocalizedValue, metric.Unit, metric.TimeGrain, metric.StartTime, metric.EndTime, metric.Values.Count);
                foreach (Value metricValue in metric.Values)
                {
                    Console.WriteLine("      Timestamp: {0} average: {1} minimum: {2} maximum {3} total {4}", metricValue.Timestamp, metricValue.Average, metricValue.Maximum, metricValue.Minimum, metricValue.Total);
                }
            }

            // List database metrics
            Console.WriteLine("");
            Console.WriteLine("Database metrics for 'Database1'");
            Console.WriteLine("  Metric definitions"); 

            Microsoft.Azure.Insights.InsightsClient insightsClient = new InsightsClient(tokenCredentials);

            Microsoft.Azure.Insights.Models.MetricDefinitionListResponse metricDefinitionListResponse = insightsClient.MetricDefinitionOperations.GetMetricDefinitions("subscriptions/XXXXXXXX-XXXX-XXXX-XXXX-XXXXXXXXXXXX/resourcegroups/resourcegroup-name/providers/microsoft.sql/servers/server-name/databases/Database1/", "");
            foreach (Microsoft.Azure.Insights.Models.MetricDefinition metricDefinition in metricDefinitionListResponse.MetricDefinitionCollection.Value)
            {
                Console.WriteLine("    {0} unit: {1} aggregation: {2}" , metricDefinition.Name.LocalizedValue, metricDefinition.Unit, metricDefinition.PrimaryAggregationType);
            }
            var resourceURI = "subscriptions/XXXXXXXX-XXXX-XXXX-XXXX-XXXXXXXXXXXX/resourcegroups/resourcegroup-name/providers/microsoft.sql/servers/server-name/databases/Database1/";
            var filter = "(name.value eq 'dtu_consumption_percent') and startTime eq " + startTime + " and endTime eq " + endTime + " and timeGrain eq duration'PT5M'";

            Console.WriteLine("  Metric values");
            MetricListResponse mlr = insightsClient.MetricOperations.GetMetrics(resourceURI,filter);
            foreach (Microsoft.Azure.Insights.Models.Metric metric in mlr.MetricCollection.Value)
            {
                Console.WriteLine("    {0}", metric.Name.LocalizedValue);
                foreach (MetricValue metricValue in metric.MetricValues)
                {
                    Console.WriteLine("      Timestamp: {0} minimum: {1} maximum: {2} average: {3}", metricValue.Timestamp, metricValue.Minimum, metricValue.Maximum, metricValue.Average);
                }
            }

            Console.WriteLine("");
            Console.WriteLine("Press any key to delete the server and resource group, which will also delete the databases and the elastic pool.");
            Console.ReadKey();

            // Delete the server which deletes the databases and then the elastic pool
            var serverOperationResponse = sqlClient.Servers.Delete("resourcegroup-name", "server-name");
            Console.WriteLine("");
            Console.WriteLine("Server {0} delete completed with status code {1}.", "server-name", serverOperationResponse.StatusCode);

            // Delete the resource group
            var resourceOperationResponse = resourceClient.ResourceGroups.Delete("resourcegroup-name");
            Console.WriteLine("");
            Console.WriteLine("Resource {0} delete completed with status code {1}.", "resourcegroup-name", resourceOperationResponse.StatusCode);

            Console.WriteLine("");
            Console.WriteLine("Execution complete.  Press any key to continue.");
            Console.ReadKey();
        }
    }
    }





## Risorse aggiuntive

[Database SQL](https://azure.microsoft.com/documentation/services/sql-database/)

[API di Gestione risorse di Azure](https://msdn.microsoft.com/library/azure/dn948464.aspx)

[Riferimento al pool di database elastici](sql-database-elastic-pool-reference.md).


<!--Image references-->
[1]: ./media/sql-database-client-library/aad.png
[2]: ./media/sql-database-client-library/permissions.png
[3]: ./media/sql-database-client-library/getdomain.png
[4]: ./media/sql-database-client-library/aad2.png
[5]: ./media/sql-database-client-library/aad-applications.png
[6]: ./media/sql-database-client-library/add.png
[7]: ./media/sql-database-client-library/add-application.png
[8]: ./media/sql-database-client-library/add-application2.png
[9]: ./media/sql-database-client-library/clientid.png

<!---HONumber=AcomDC_0330_2016-->