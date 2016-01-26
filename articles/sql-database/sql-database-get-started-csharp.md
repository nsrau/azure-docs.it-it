<properties 
	pageTitle="Prova del database SQL: usare C# per creare un database SQL | Microsoft Azure" 
	description="Provare il database SQL per lo sviluppo di app SQL e C# e creare un database SQL di Azure con C# usando la libreria di database SQL per .NET." 
	keywords="provare sql, sql c#"   
	services="sql-database" 
	documentationCenter="" 
	authors="stevestein" 
	manager="jeffreyg" 
	editor="cgronlun"/>

<tags
   ms.service="sql-database"
   ms.devlang="NA"
   ms.topic="get-started-article"
   ms.tgt_pltfrm="powershell"
   ms.workload="data-management" 
   ms.date="01/20/2016"
   ms.author="sstein"/>

# Prova del database SQL: usare C&#x23; per creare un database SQL con la libreria di database SQL per .NET 

**Database singolo**

> [AZURE.SELECTOR]
- [Azure portal](sql-database-get-started.md)
- [C#](sql-database-get-started-csharp.md)
- [PowerShell](sql-database-get-started-powershell.md)



Informazioni su come usare i comandi C# per creare un database SQL di Azure con la [libreria di database SQL di Azure per .NET](https://www.nuget.org/packages/Microsoft.Azure.Management.Sql).

La prova del database SQL consente di creare un database singolo usando SQL e C#. Per creare database elastici, vedere [Creare un pool di database elastici](sql-database-elastic-pool-portal.md).

I singoli frammenti di codice sono suddivisi per maggiore chiarezza e un'applicazione console di esempio riunisce tutti i comandi nella sezione nella parte inferiore di questo articolo.

La libreria di database SQL di Azure per .NET include un'API basata su [Gestione risorse di Azure](resource-group-overview.md) che esegue il wrapping dell'[API REST di database SQL basata su Gestione risorse](https://msdn.microsoft.com/library/azure/mt163571.aspx). La libreria client segue il modello comune per le librerie client basate su Gestione risorse. Gestione risorse richiede gruppi di risorse e l'autenticazione con [Azure Active Directory](https://msdn.microsoft.com/library/azure/mt168838.aspx) (AAD).

<br>

> [AZURE.NOTE]La libreria di database SQL per .NET è attualmente in anteprima.

<br>

Per seguire la procedura descritta in questo articolo, sono necessari gli elementi seguenti:

- Una sottoscrizione di Azure. Se è necessaria una sottoscrizione ad Azure, fare semplicemente clic su **VERSIONE DI PROVA GRATUITA** nella parte superiore della pagina, quindi tornare e proseguire fino alla fine di questo articolo.
- Visual Studio. Per una copia gratuita di Visual Studio, vedere la pagina [Download di Visual Studio](https://www.visualstudio.com/downloads/download-visual-studio-vs).


## Installare le librerie richieste

Per impostare un database SQL con C#, acquisire le librerie di gestione richieste installando i pacchetti seguenti mediante la [console di gestione pacchetti](http://docs.nuget.org/Consume/Package-Manager-Console):

    PM> Install-Package Microsoft.Azure.Management.Sql –Pre
    PM> Install-Package Microsoft.Azure.Management.Resources –Pre
    PM> Install-Package Microsoft.Azure.Common.Authentication –Pre


## Configurare l'autenticazione con Azure Active Directory

È innanzitutto necessario abilitare l'applicazione client per accedere all'API REST mediante la configurazione dell'autenticazione richiesta.

Le [API REST di Gestione risorse di Azure](https://msdn.microsoft.com/library/azure/dn948464.aspx) usano Azure Active Directory per l'autenticazione.

Per autenticare l'applicazione client in base all'utente corrente è necessario prima registrare l'applicazione nel dominio AAD associato alla sottoscrizione in cui sono state create le risorse di Azure. Se la sottoscrizione ad Azure è stata creata con un account Microsoft anziché un account aziendale o dell'istituto di istruzione si dispone già di un dominio AAD predefinito. La registrazione dell'applicazione può essere eseguita nel [portale di Azure classico](https://manage.windowsazure.com/).

Per creare una nuova applicazione e registrarla nell’active directory corrente procedere come descritto di seguito:

1. Scorrere il menu a sinistra per individuare il servizio **Active Directory** e aprirlo.

    ![Prova del database SQL: configurare Azure Active Directory (AAD).][1]

2. Selezionare la directory per l'autenticazione dell'applicazione e fare clic sul relativo **nome**.

    ![Selezionare la directory per l'autenticazione dell'applicazione SQL in C#.][4]

3. Nella pagina della directory fare clic su **APPLICAZIONI**.

    ![La pagina della directory con Applicazioni.][5]

4. Fare clic su **AGGIUNGI** per creare una nuova applicazione C# per il database SQL.

    ![Aggiungere l'applicazione SQL in C#.][6]

5. Selezionare **Aggiungi un'applicazione che l'organizzazione sta sviluppando**.

5. Fornire un **NOME** per l'app e selezionare **APPLICAZIONE CLIENT NATIVA**.

    ![Fornire informazioni sull'applicazione SQL in C#.][7]

6. Specificare un **URI DI REINDIRIZZAMENTO**. Non è necessario che sia un endpoint effettivo, è sufficiente un URI valido.

    ![Aggiungere un URL di reindirizzamento per l'applicazione SQL in C#.][8]

7. Completare la creazione dell'applicazione, fare clic su **CONFIGURA** e copiare l’**ID CLIENT** (l'ID del client sarà necessario nel codice).

    ![Ottenere un ID client per l'applicazione SQL in C#.][9]


1. Nella parte inferiore della pagina fare clic su **Aggiungi applicazione**.
1. Selezionare **App Microsoft**.
1. Selezionare **API di gestione del servizio Azure** e quindi completare la procedura guidata.
2. Con l'API selezionata è ora necessario concedere le autorizzazioni specifiche richieste per accedere a questa API selezionando **Accedi a gestione del servizio Azure (anteprima)**.

    ![Impostare le autorizzazioni.][2]

2. Fare clic su **SAVE**.



### Identificare il nome di dominio

Il nome di dominio è obbligatorio per il codice. Un modo semplice per identificare il nome di dominio corretto è:

1. Accedere al [portale di Azure](https://portal.azure.com).
2. Passare il mouse sul proprio nome nell'angolo superiore destro e osservare il dominio visualizzato nella finestra popup.

    ![Identificare il nome di dominio.][3]





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
            ("https://login.windows.net/" + domainName /* Tenant ID or AAD domain */);

        AuthenticationResult token = authContext.AcquireToken
            ("https://management.azure.com/"/* the Azure Resource Management endpoint */,
                clientId,
        new Uri(clientAppUri) /* redirect URI */,
        PromptBehavior.Auto /* with Auto user will not be prompted if an unexpired token is cached */);

        return token;
    }



> [AZURE.NOTE]Negli esempi inclusi in questo articolo viene utilizzata una forma asincrona di ogni richiesta di API e blocco fino al completamento della chiamata REST nel servizio sottostante. Sono disponibili metodi asincroni.



## Creare un gruppo di risorse

Con Gestione risorse, tutte le risorse devono essere create in un gruppo di risorse. Un gruppo di risorse è un contenitore che contiene risorse correlate per un'applicazione. Con il database SQL di Azure deve essere innanzitutto creato il server di database all'interno di un gruppo di risorse esistente e poi il database nel server. Prima che un'applicazione possa connettersi al server o al database è necessario anche creare una regola firewall sul server per aprire l'accesso dall'indirizzo IP del client.


    // Create a resource management client 
    ResourceManagementClient resourceClient = new ResourceManagementClient(new TokenCloudCredentials("XXXXXXXX-XXXX-XXXX-XXXX-XXXXXXXXXXXX" /*subscription id*/, token.AccessToken ));
    
    // Resource group parameters
    ResourceGroup resourceGroupParameters = new ResourceGroup()
    {
        Location = "South Central US"
    };
    
    //Create a resource group
    var resourceGroupResult = resourceClient.ResourceGroups.CreateOrUpdate("ResourceGroupName", resourceGroupParameters);



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
            AdministratorLoginPassword = securelyStoredPassword,
            Version = "12.0"
        }
    };

    var serverResult = sqlClient.Servers.CreateOrUpdate("ResourceGroupName", "ServerName", serverParameters);




## Creare una regola del firewall del server per consentire l'accesso al server

Per impostazione predefinita non è possibile connettersi a un server da qualsiasi posizione. Per connettersi a un server o a qualsiasi database nel server, è necessario definire una [regola firewall](https://msdn.microsoft.com/library/azure/ee621782.aspx) che consenta l'accesso dall'indirizzo IP del client.

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

    var firewallResult = sqlClient.FirewallRules.CreateOrUpdate("ResourceGroupName", "ServerName", "FirewallRuleName", firewallParameters);




Per consentire ad altri servizi di Azure di accedere a un server, aggiungere una regola del firewall e impostare StartIpAddress e EndIpAddress su 0.0.0.0. Si noti che ciò consente al traffico di Azure proveniente da *qualsiasi* sottoscrizione di Azure di accedere al server.


## Usare C&#x23; per creare un database SQL di base

Il comando seguente in C# crea un nuovo database SQL di base se nel server non esiste già un database con lo stesso nome. Se esiste un database con lo stesso nome questo verrà aggiornato.

        // Create a database

        // Retrieve the server on which the database will be created
        Server currentServer = sqlClient.Servers.Get("ResourceGroupName", "ServerName").Server;
 
        // Create a database: configure create or update parameters and properties explicitly
        DatabaseCreateOrUpdateParameters newDatabaseParameters = new DatabaseCreateOrUpdateParameters()
        {
            Location = currentServer.Location,
            Properties = new DatabaseCreateOrUpdateProperties()
            {
                Edition = "Standard",
                RequestedServiceObjectiveName = "S0",
            }
        };

        var dbResponse = sqlClient.Databases.CreateOrUpdate("ResourceGroupNname", "ServerName", "Database1", newDatabaseParameters);






## Applicazione console C&#x23; di esempio


    using Microsoft.Azure;
    using Microsoft.Azure.Management.Resources;
    using Microsoft.Azure.Management.Resources.Models;
    using Microsoft.Azure.Management.Sql;
    using Microsoft.Azure.Management.Sql.Models;
    using Microsoft.IdentityModel.Clients.ActiveDirectory;
    using System;
    
    
    namespace CreateSqlDatabase
    {
     class Program
     {

        #region user variables
        
        // Azure subscription id
        private static string subscriptionId = "XXXXXXXX-XXXX-XXXX-XXXX-XXXXXXXXXXXX";
     
        
        // application client ID from Azure Active Directory (AAD)
        private static string clientAppUri = "http://app1";
        private static string clientId = "XXXXXXXX-XXXX-XXXX-XXXX-XXXXXXXXXXXX";

        private static string resourcegroupName = "rg1";

        private static string dataCenterLocation = "Japan West";

        private static string databaseName = "newDatabaseName";
        private static string databaseEdition = "Standard";
        private static string databasePerformanceLevel = "S0";

        private static string serverName = "serverName";
        private static string serverAdmin = "admin";
        private static string serverAdminPassword = securelyStoredPassword;
        private static string serverVersion = "12.0";

        private static string firewallRuleName = "rule1";
        private static string firewallRuleStartIp = "0.0.0.0";
        private static string firewallRuleEndIp = "255.255.255.255";



        private static string domainName = "microsoft.onmicrosoft.com";

        private static string serverLocation = "Japan West";

        #endregion


        static void Main(string[] args)
        {
            var token = GetAccessToken();

            // Who am I?
            Console.WriteLine("Identity: {0} {1}", token.UserInfo.GivenName, token.UserInfo.FamilyName);
            Console.WriteLine("Token expires on {0}", token.ExpiresOn);
            Console.WriteLine("");

            // Create a resource management client 
            ResourceManagementClient resourceClient = new ResourceManagementClient(new TokenCloudCredentials(subscriptionId, token.AccessToken));

            // Resource group parameters
            ResourceGroup resourceGroupParameters = new ResourceGroup()
            {
                Location = dataCenterLocation
            };

            //Create a resource group
            var resourceGroupResult = resourceClient.ResourceGroups.CreateOrUpdate(resourcegroupName, resourceGroupParameters);

            Console.WriteLine("Resource group {0} create or update completed with status code {1} ", resourceGroupResult.ResourceGroup.Name, resourceGroupResult.StatusCode);

            //create a SQL Database management client
            TokenCloudCredentials tokenCredentials = new TokenCloudCredentials(subscriptionId, token.AccessToken);

            SqlManagementClient sqlClient = new SqlManagementClient(tokenCredentials);

            // Create a server
            ServerCreateOrUpdateParameters serverParameters = new ServerCreateOrUpdateParameters()
            {
                Location = serverLocation,
                Properties = new ServerCreateOrUpdateProperties()
                {
                    AdministratorLogin = serverAdmin,
                    AdministratorLoginPassword = serverAdminPassword,
                    Version = serverVersion
                }
            };

            var serverResult = sqlClient.Servers.CreateOrUpdate(resourcegroupName, serverName, serverParameters);

            var serverGetResult = sqlClient.Servers.Get(resourcegroupName, serverName);


            Console.WriteLine("Server {0} create or update completed with status code {1}", serverResult.Server.Name, serverResult.StatusCode);

            // Create a firewall rule on the server to allow TDS connection 

            FirewallRuleCreateOrUpdateParameters firewallParameters = new FirewallRuleCreateOrUpdateParameters()
            {
                Properties = new FirewallRuleCreateOrUpdateProperties()
                {
                    StartIpAddress = firewallRuleStartIp,
                    EndIpAddress = firewallRuleEndIp
                }
            };

            var firewallResult = sqlClient.FirewallRules.CreateOrUpdate(resourcegroupName, serverName, firewallRuleName, firewallParameters);

            Console.WriteLine("Firewall rule {0} create or update completed with status code: {1}", firewallResult.FirewallRule.Name, firewallResult.StatusCode);

            // Create a database

            // Retrieve the server on which the database will be created
            Server currentServer = sqlClient.Servers.Get(resourcegroupName, serverName).Server;

            // Create a database
            DatabaseCreateOrUpdateParameters newDatabaseParameters = new DatabaseCreateOrUpdateParameters()
            {
                Location = currentServer.Location,
                Properties = new DatabaseCreateOrUpdateProperties()
                {
                    Edition = databaseEdition,
                    RequestedServiceObjectiveName = databasePerformanceLevel,
                }
            };

            var dbResponse = sqlClient.Databases.CreateOrUpdate(resourcegroupName, serverName, databaseName, newDatabaseParameters);

            Console.WriteLine("Database {0} created with status code: {1}. Service Objective: {2} ", dbResponse.Database.Name, dbResponse.StatusCode, dbResponse.Database.Properties.ServiceObjective);
           
            Console.WriteLine(System.Environment.NewLine + "Press any key to exit.");
            Console.ReadKey();
        }

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
            new Uri(clientAppUri) /* redirect URI */,
            PromptBehavior.Auto /* with Auto user will not be prompted if an unexpired token is cached */);

            return token;
        }
      }
    }


## Passaggi successivi
Dopo aver provato il database SQL e aver impostato un database con C#, è possibile leggere gli articoli seguenti:

- [Connettersi al database SQL con SQL Server Management Studio ed eseguire una query T-SQL di esempio](sql-database-connect-query-ssms.md)

## Risorse aggiuntive

- [Database SQL](https://azure.microsoft.com/documentation/services/sql-database/)





<!--Image references-->
[1]: ./media/sql-database-get-started-csharp/aad.png
[2]: ./media/sql-database-get-started-csharp/permissions.png
[3]: ./media/sql-database-get-started-csharp/getdomain.png
[4]: ./media/sql-database-get-started-csharp/aad2.png
[5]: ./media/sql-database-get-started-csharp/aad-applications.png
[6]: ./media/sql-database-get-started-csharp/add.png
[7]: ./media/sql-database-get-started-csharp/add-application.png
[8]: ./media/sql-database-get-started-csharp/add-application2.png
[9]: ./media/sql-database-get-started-csharp/clientid.png

<!---HONumber=AcomDC_0121_2016-->