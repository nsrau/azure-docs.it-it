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
   ms.topic="hero-article"
   ms.tgt_pltfrm="powershell"
   ms.workload="data-management" 
   ms.date="01/22/2016"
   ms.author="sstein"/>

# Prova del database SQL: usare C&#x23; per creare un database SQL con la libreria di database SQL per .NET 

**Database singolo**

> [AZURE.SELECTOR]
- [Portale di Azure](sql-database-get-started.md)
- [C#](sql-database-get-started-csharp.md)
- [PowerShell](sql-database-get-started-powershell.md)



Informazioni su come usare i comandi C# per creare un database SQL di Azure con la [libreria di database SQL di Azure per .NET](https://www.nuget.org/packages/Microsoft.Azure.Management.Sql).

La prova del database SQL consente di creare un database singolo usando SQL e C#. Per creare database elastici, vedere [Creare un pool di database elastici](sql-database-elastic-pool-portal.md).

I singoli frammenti di codice sono suddivisi per maggiore chiarezza e un'applicazione console di esempio riunisce tutti i comandi nella sezione nella parte inferiore di questo articolo.

La libreria di database SQL di Azure per .NET include un'API basata su [Gestione risorse di Azure](../resource-group-overview.md) che esegue il wrapping dell'[API REST di database SQL basata su Gestione risorse](https://msdn.microsoft.com/library/azure/mt163571.aspx). La libreria client segue il modello comune per le librerie client basate su Gestione risorse. Gestione risorse richiede gruppi di risorse e l'autenticazione con [Azure Active Directory](https://msdn.microsoft.com/library/azure/mt168838.aspx) (AAD).

<br>

> [AZURE.NOTE] La libreria di database SQL per .NET è attualmente in anteprima.

<br>

Per seguire la procedura descritta in questo articolo, sono necessari gli elementi seguenti:

- Una sottoscrizione di Azure. Se è necessaria una sottoscrizione ad Azure, fare semplicemente clic su **VERSIONE DI PROVA GRATUITA** nella parte superiore della pagina, quindi tornare e proseguire fino alla fine di questo articolo.
- Visual Studio. Per una copia gratuita di Visual Studio, vedere la pagina [Download di Visual Studio](https://www.visualstudio.com/downloads/download-visual-studio-vs).


## Installare le librerie richieste

Per configurare un database SQL con C#, ottenere le librerie di gestione necessarie installando i pacchetti seguenti tramite la [Console di Gestione pacchetti](http://docs.nuget.org/Consume/Package-Manager-Console) in Visual Studio (**Strumenti** > **Gestione pacchetti NuGet** > **Console di Gestione pacchetti**):

    Install-Package Microsoft.Azure.Management.Sql –Pre
    Install-Package Microsoft.Azure.Management.Resources –Pre
    Install-Package Microsoft.Azure.Common.Authentication –Pre


## Configurare l'autenticazione con Azure Active Directory

È prima di tutto necessario abilitare l'applicazione client per accedere al servizio di database SQL mediante la configurazione dell'autenticazione richiesta.

Per autenticare l'applicazione client in base all'utente corrente è necessario prima registrare l'applicazione nel dominio AAD associato alla sottoscrizione in cui sono state create le risorse di Azure. Se la sottoscrizione ad Azure è stata creata con un account Microsoft anziché un account aziendale o dell'istituto di istruzione si dispone già di un dominio AAD predefinito.

Per creare una nuova applicazione e registrarla nell’active directory corrente procedere come descritto di seguito:

1. Passare al [portale di Azure classico](https://manage.windowsazure.com/).
1. Sul lato sinistro selezionare il servizio **Active Directory**, quindi selezionare la directory per autenticare l'applicazione e fare clic sul relativo **Nome**.

    ![Prova del database SQL: configurare Azure Active Directory (AAD).][1]

2. Nella pagina della directory fare clic su **APPLICAZIONI**.

    ![La pagina della directory con Applicazioni.][5]

4. Fare clic su **AGGIUNGI** per creare una nuova applicazione.

5. Selezionare **Aggiungi un'applicazione che l'organizzazione sta sviluppando**.

5. Fornire un **NOME** per l'app e selezionare **APPLICAZIONE CLIENT NATIVA**.

    ![Fornire informazioni sull'applicazione SQL in C#.][7]

6. Specificare un **URI DI REINDIRIZZAMENTO**. Non è necessario che sia un endpoint effettivo, è sufficiente un URI valido.

    ![Aggiungere un URL di reindirizzamento per l'applicazione SQL in C#.][8]

7. Completare la creazione dell'app, fare clic su **CONFIGURA** e copiare l'**ID CLIENT**, che sarà necessario in seguito nel codice.

    ![Ottenere un ID client per l'applicazione SQL in C#.][9]


1. Nella parte inferiore della pagina fare clic su **Aggiungi applicazione**.
1. Selezionare **App Microsoft**.
1. Selezionare **API di gestione del servizio Azure** e quindi completare la procedura guidata.
2. Con l'API selezionata è ora necessario concedere le autorizzazioni specifiche richieste per accedere a questa API selezionando **Accedi a gestione del servizio Azure (anteprima)**.

    ![Impostare le autorizzazioni.][2]

2. Fare clic su **SAVE**.



### Identificare il nome di dominio

Il nome di dominio è obbligatorio per il codice. Un modo semplice per identificare il nome di dominio corretto è:

1. Accedere al [portale di Azure](http://portal.azure.com).
2. Passare il mouse sul proprio nome nell'angolo superiore destro e osservare il dominio visualizzato nella finestra popup.

    ![Identificare il nome di dominio.][3]





**Risorse aggiuntive di AAD**

Altre informazioni sull'uso di Azure Active Directory per l'autenticazione sono disponibili in [questo utile post di blog](http://www.cloudidentity.com/blog/2013/09/12/active-directory-authentication-library-adal-v1-for-net-general-availability/).


### Recuperare il token di accesso per l'utente corrente 

L'applicazione client deve recuperare il token di accesso all'applicazione per l'utente corrente. Quando un utente esegue per la prima volta il codice gli verrà richiesto di immettere le credenziali utente e il token risultante viene memorizzato nella cache in locale. Alle successive esecuzioni il token viene recuperato dalla cache e all’utente viene chiesto di effettuare l’accesso solo se il token è scaduto.

Questo codice restituisce Microsoft.IdentityModel.Clients.ActiveDirectory.AuthenticationResult, che sarà necessario in altri frammenti di codice più avanti.

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

Con Gestione risorse, tutte le risorse devono essere create in un gruppo di risorse. Un gruppo di risorse è un contenitore che contiene risorse correlate per un'applicazione. Con il database SQL di Azure è necessario creare il server di database all'interno di un gruppo di risorse esistente.

        static void CreateResourceGroup()
        {
            creds = new Microsoft.Rest.TokenCredentials(token.AccessToken);

            // Create a resource management client 
            ResourceManagementClient resourceClient = new ResourceManagementClient(creds);

            // Resource group parameters
            ResourceGroup resourceGroupParameters = new ResourceGroup()
            {
                Location = location,
            };

            //Create a resource group
            resourceClient.SubscriptionId = subscriptionId;
            var resourceGroupResult = resourceClient.ResourceGroups.CreateOrUpdate(resourceGroupName, resourceGroupParameters);
        }


## Creare un server 

I database SQL sono contenuti nei server. Il nome del server deve essere globalmente univoco tra tutti i server SQL Azure, quindi verrà visualizzato un messaggio di errore se il nome del server è già in uso. Vale inoltre la pena notare che il completamento di questo comando potrebbe richiedere alcuni minuti.

    static void CreateServer()
    {
        // Create a SQL Database management client
        SqlManagementClient sqlClient = new SqlManagementClient(new TokenCloudCredentials(subscriptionId, token.AccessToken));

        // Create a server
        ServerCreateOrUpdateParameters serverParameters = new ServerCreateOrUpdateParameters()
        {
            Location = location,
            Properties = new ServerCreateOrUpdateProperties()
            {
                AdministratorLogin = administratorLogin,
                AdministratorLoginPassword = administratorPassword,
                Version = serverVersion
            }
        };
            var serverResult = sqlClient.Servers.CreateOrUpdate(resourceGroupName, serverName, serverParameters);
    }


### Creare un amministratore del server esterno


    // Create a server external admin
    ServerAdministratorCreateOrUpdateParameters aadAdminParameters = new ServerAdministratorCreateOrUpdateParameters()
    {
        Properties = new ServerAdministratorCreateOrUpdateProperties()
        {
            AdministratorType = "ActiveDirectory",
            Login = "<login>",
            Sid = new Guid("<Azure AD admin sid>"),
            TenantId = new Guid("<Azure AD tenant id>")
        }
    };

    var adminResult = sqlClient.ServerAdministrators.CreateOrUpdate(resourceGroupName, serverName, "ActiveDirectory", aadAdminParameters);
    var adminGetResult = sqlClient.ServerAdministrators.Get(resourceGroupName, serverName, "ActiveDirectory");




## Creare una regola del firewall del server per consentire l'accesso al server

Per impostazione predefinita non è possibile connettersi a un server da qualsiasi posizione. Per connettersi a un server o a qualsiasi database nel server, è necessario definire una [regola firewall](https://msdn.microsoft.com/library/azure/ee621782.aspx) che consenta l'accesso dall'indirizzo IP del client.

Nell'esempio seguente viene creata una regola che consente di aprire l'accesso al server da qualsiasi indirizzo IP. È consigliabile creare gli account di accesso SQL e le password appropriati per proteggere il database e non basarsi sulle regole del firewall come difesa primaria contro le intrusioni.


        static void CreateFirewallRule()
        {
            // Create a firewall rule on the server 
            FirewallRuleCreateOrUpdateParameters firewallParameters = new FirewallRuleCreateOrUpdateParameters()
            {
                Properties = new FirewallRuleCreateOrUpdateProperties()
                {
                    StartIpAddress = "0.0.0.0",
                    EndIpAddress = "255.255.255.255"
                }
            };
            var firewallResult = sqlClient.FirewallRules.CreateOrUpdate(resourceGroupName, serverName, firewallRuleName, firewallParameters);
        }




Per consentire ad altri servizi di Azure di accedere a un server, aggiungere una regola del firewall e impostare StartIpAddress e EndIpAddress su 0.0.0.0. Si noti che ciò consente al traffico di Azure proveniente da *qualsiasi* sottoscrizione di Azure di accedere al server.


## Usare C&#x23; per creare un database SQL

Il comando C# seguente crea un nuovo database SQL se nel server non esiste già un database con lo stesso nome. Se esiste un database con lo stesso nome, verrà aggiornato.


        static void CreateDatabase()
        {
            // Create a database

            // Retrieve the server on which the database will be created
            Server currentServer = sqlClient.Servers.Get(resourceGroupName, serverName).Server;

            // Create a database: configure create or update parameters and properties explicitly
            DatabaseCreateOrUpdateParameters newDatabaseParameters = new DatabaseCreateOrUpdateParameters()
            {
                Location = currentServer.Location,
                Properties = new DatabaseCreateOrUpdateProperties()
                {
                    Edition = databaseEdition,
                    RequestedServiceObjectiveName = databasePerfLevel,
                }
            };
            var dbResponse = sqlClient.Databases.CreateOrUpdate(resourceGroupName, serverName, databaseName, newDatabaseParameters);
        }



## Applicazione console C&#x23; di esempio

L'esempio seguente consente di creare un gruppo di risorse, un server, una regola del firewall e un database SQL. La sezione *Configurare l'autenticazione con Azure Active Directory* all'inizio di questo articolo illustra dove è possibile ottenere i valori per le variabili clientId, redirectUri e domainName.


    using Microsoft.Azure;
    using Microsoft.Azure.Management.Resources;
    using Microsoft.Azure.Management.Resources.Models;
    using Microsoft.Azure.Management.Sql;
    using Microsoft.Azure.Management.Sql.Models;
    using Microsoft.IdentityModel.Clients.ActiveDirectory;
    using System;
    using System.Collections.Generic;
    using System.Linq;
    using System.Text;
    using System.Threading.Tasks;
    
    namespace SqlDbConsoleApp
    {
    class Program
    {
        // Get these values from the Azure portal
        static string subscriptionId = "<Azure subscription id>";
        static string clientId = "<Azure App clientId>";
        static string redirectUri = "<Azure App redirectURI>";
        static string domainName = "<domain>";

        // You create these values 
        static string resourceGroupName = "<your resource group name>";
        static string location = "<Azure data center location>";

        static string serverName = "<your server name>";
        static string administratorLogin = "<your server admin>";
        
        // store your password securely!
        static string administratorPassword = "<your server admin password>";
        static string serverVersion = "12.0";

        static string firewallRuleName = "<your firewall rule name>";

        static string databaseName = "dbfromcsharparticle";
        static string databaseEdition = "Basic"; // Basic, Standard, or Premium
        static string databasePerfLevel = "";

        static AuthenticationResult token;
        static Microsoft.Rest.TokenCredentials creds;

        static SqlManagementClient sqlClient;


        static void Main(string[] args)
        {
            Console.WriteLine("Logging in...");

            token = GetAccessToken();

            Console.WriteLine("Logged in as: " + token.UserInfo.DisplayableId);

            Console.WriteLine("Creating resource group...");
            CreateResourceGroup();

            sqlClient = new SqlManagementClient(new TokenCloudCredentials(subscriptionId, token.AccessToken));

            Console.WriteLine("Creating server...");
            CreateServer();

            Console.WriteLine("Creating firewall rule...");
            CreateFirewallRule();

            Console.WriteLine("Creating database...");

            DatabaseCreateOrUpdateResponse dbResponse = CreateDatabase();
            Console.WriteLine("Status: " + dbResponse.Status.ToString() 
                + " Code: " + dbResponse.StatusCode.ToString());

            Console.WriteLine("Press enter to exit...");
            Console.ReadLine();
        }

        static void CreateResourceGroup()
        {
            creds = new Microsoft.Rest.TokenCredentials(token.AccessToken);

            // Create a resource management client 
            ResourceManagementClient resourceClient = new ResourceManagementClient(creds);

            // Resource group parameters
            ResourceGroup resourceGroupParameters = new ResourceGroup()
            {
                Location = location,
            };

            //Create a resource group
            resourceClient.SubscriptionId = subscriptionId;
            var resourceGroupResult = resourceClient.ResourceGroups.CreateOrUpdate(resourceGroupName, resourceGroupParameters);
        }

        static void CreateServer()
        {
            // Create a SQL Database management client
            //SqlManagementClient sqlClient = new SqlManagementClient(new TokenCloudCredentials(subscriptionId, token.AccessToken));

            // Create a server
            ServerCreateOrUpdateParameters serverParameters = new ServerCreateOrUpdateParameters()
            {
                Location = location,
                Properties = new ServerCreateOrUpdateProperties()
                {
                    AdministratorLogin = administratorLogin,
                    AdministratorLoginPassword = administratorPassword,
                    Version = serverVersion
                }
            };
            var serverResult = sqlClient.Servers.CreateOrUpdate(resourceGroupName, serverName, serverParameters);
        }

        static void CreateFirewallRule()
        {
            // Create a firewall rule on the server 
            FirewallRuleCreateOrUpdateParameters firewallParameters = new FirewallRuleCreateOrUpdateParameters()
            {
                Properties = new FirewallRuleCreateOrUpdateProperties()
                {
                    // replace with your client IP address
                    StartIpAddress = "0.0.0.0",
                    EndIpAddress = "255.255.255.255"
                }
            };
            var firewallResult = sqlClient.FirewallRules.CreateOrUpdate(resourceGroupName, serverName, firewallRuleName, firewallParameters);
        }

        static DatabaseCreateOrUpdateResponse CreateDatabase()
        {
            // Create a database

            // Retrieve the server on which the database will be created
            Server currentServer = sqlClient.Servers.Get(resourceGroupName, serverName).Server;

            // Create a database: configure create or update parameters and properties explicitly
            DatabaseCreateOrUpdateParameters newDatabaseParameters = new DatabaseCreateOrUpdateParameters()
            {
                Location = currentServer.Location,
                Properties = new DatabaseCreateOrUpdateProperties()
                {
                    Edition = databaseEdition,
                    RequestedServiceObjectiveName = databasePerfLevel,
                }
            };
            var dbResponse = sqlClient.Databases.CreateOrUpdate(resourceGroupName, serverName, databaseName, newDatabaseParameters);
            return dbResponse;
        }

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

<!---HONumber=AcomDC_0302_2016-->