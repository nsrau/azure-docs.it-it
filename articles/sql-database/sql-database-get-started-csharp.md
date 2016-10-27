<properties
    pageTitle="Prova del database SQL: usare C# per creare un database SQL | Microsoft Azure"
    description="Provare il database SQL per lo sviluppo di app SQL e C# e creare un database SQL di Azure con C# usando la libreria di database SQL per .NET."
    keywords="provare sql, sql c#"   
    services="sql-database"
    documentationCenter=""
    authors="stevestein"
    manager="jhubbard"
    editor="cgronlun"/>

<tags
   ms.service="sql-database"
   ms.devlang="NA"
   ms.topic="hero-article"
   ms.tgt_pltfrm="csharp"
   ms.workload="data-management"
   ms.date="10/04/2016"
   ms.author="sstein"/>


# <a name="try-sql-database:-use-c#-to-create-a-sql-database-with-the-sql-database-library-for-.net"></a>Prova del database SQL: usare C# per creare un database SQL con la libreria del database SQL per .NET


> [AZURE.SELECTOR]
- [Portale di Azure](sql-database-get-started.md)
- [C#](sql-database-get-started-csharp.md)
- [PowerShell](sql-database-get-started-powershell.md)

Informazioni su come usare C# per creare un database SQL di Azure con la [libreria di gestione di Microsoft Azure SQL per .NET](https://www.nuget.org/packages/Microsoft.Azure.Management.Sql). Questo articolo descrive come creare un database singolo con SQL e C#. Per creare pool di database elastici, vedere [Creare un pool di database elastici](sql-database-elastic-pool-create-csharp.md).

La libreria di gestione di database SQL di Azure per .NET include un'API basata su [Azure Resource Manager](../resource-group-overview.md) che esegue il wrapping dell'[API REST di database SQL basata su Resource Manager](https://msdn.microsoft.com/library/azure/mt163571.aspx).

>[AZURE.NOTE] Molte nuove funzionalità del database SQL sono supportate solo con il [modello di distribuzione Azure Resource Manager](../resource-group-overview.md). È quindi consigliabile usare sempre la **libreria di gestione di database SQL di Azure per .NET più recente ([documentazione](https://msdn.microsoft.com/library/azure/mt349017.aspx) | [pacchetto NuGet](https://www.nuget.org/packages/Microsoft.Azure.Management.Sql))**. Le [librerie basate sul modello di distribuzione classica](https://www.nuget.org/packages/Microsoft.WindowsAzure.Management.Sql) meno recenti sono supportate solo per la compatibilità con le versioni precedenti ed è quindi consigliabile usare le nuove librerie basate su Resource Manager.

Per seguire la procedura descritta in questo articolo, sono necessari gli elementi seguenti:

- Una sottoscrizione di Azure. Se è necessaria una sottoscrizione di Azure, è sufficiente fare clic su **ACCOUNT GRATUITO** nella parte superiore della pagina, quindi tornare e proseguire fino alla fine di questo articolo.
- Visual Studio. Per una copia gratuita di Visual Studio, vedere la pagina [Download di Visual Studio](https://www.visualstudio.com/downloads/download-visual-studio-vs) .

>[AZURE.NOTE] Questo articolo illustra la creazione di un nuovo database SQL vuoto. Per copiare e ridimensionare database, creare un database in un pool e così via, modificare il metodo *CreateOrUpdateDatabase(...)* dell'esempio riportato di seguito. Per altre informazioni, vedere le classi [DatabaseCreateMode](https://msdn.microsoft.com/library/microsoft.azure.management.sql.models.databasecreatemode.aspx) e [DatabaseProperties](https://msdn.microsoft.com/library/microsoft.azure.management.sql.models.databaseproperties.aspx).



## <a name="create-a-console-app-and-install-the-required-libraries"></a>Creare un'app console e installare le librerie richieste

1. Avviare Visual Studio.
2. Fare clic su **File** > **Nuovo** > **Progetto**.
3. Creare un' **applicazione console** in C# e denominarla *SqlDbConsoleApp*


Per creare un database SQL con C#, caricare le librerie di gestione richieste tramite la [console di Gestione pacchetti](http://docs.nuget.org/Consume/Package-Manager-Console):

1. Fare clic su **Strumenti** > **Gestione pacchetti NuGet** > **Console di Gestione pacchetti**.
2. Digitare `Install-Package Microsoft.Azure.Management.Sql –Pre` per installare la [libreria di gestione di Microsoft Azure SQL](https://www.nuget.org/packages/Microsoft.Azure.Management.Sql) più recente.
3. Digitare `Install-Package Microsoft.Azure.Management.ResourceManager –Pre` per installare la [libreria di Microsoft Azure Resource Manager](https://www.nuget.org/packages/Microsoft.Azure.Management.ResourceManager).
4. Digitare `Install-Package Microsoft.Azure.Common.Authentication –Pre` per installare la [libreria di autenticazione comune di Microsoft Azure](https://www.nuget.org/packages/Microsoft.Azure.Common.Authentication). 



> [AZURE.NOTE] Negli esempi inclusi in questo articolo viene utilizzata una forma asincrona di ogni richiesta di API e blocco fino al completamento della chiamata REST nel servizio sottostante. Sono disponibili metodi asincroni.


## <a name="create-a-sql-database-server,-firewall-rule,-and-sql-database---c#-example"></a>Creare un server di database SQL, una regola firewall e un database SQL: esempio di C#

L'esempio seguente consente di creare un gruppo di risorse, un server, una regola del firewall e un database SQL. Per ottenere le variabili `_subscriptionId, _tenantId, _applicationId, and _applicationSecret`, vedere la sezione [Creare un'entità servizio per accedere alle risorse](#create-a-service-principal-to-access-resources).

Sostituire il contenuto di **Program.cs** con quanto segue e aggiornare `{variables}` con i valori dell'app, senza includere `{}`.


    using Microsoft.Azure;
    using Microsoft.Azure.Management.ResourceManager;
    using Microsoft.Azure.Management.ResourceManager.Models;
    using Microsoft.Azure.Management.Sql;
    using Microsoft.Azure.Management.Sql.Models;
    using Microsoft.IdentityModel.Clients.ActiveDirectory;
    using System;
    
    namespace SqlDbConsoleApp
    {
    class Program
       {
        // For details about these four (4) values, see
        // https://azure.microsoft.com/documentation/articles/resource-group-authenticate-service-principal/
        static string _subscriptionId = "{xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx}";
        static string _tenantId = "{xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx}";
        static string _applicationId = "{xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx}";
        static string _applicationSecret = "{your-password}";

        // Create management clients for the Azure resources your app needs to work with.
        // This app works with Resource Groups, and Azure SQL Database.
        static ResourceManagementClient _resourceMgmtClient;
        static SqlManagementClient _sqlMgmtClient;

        // Authentication token
        static AuthenticationResult _token;

        // Azure resource variables
        static string _resourceGroupName = "{resource-group-name}";
        static string _resourceGrouplocation = "{Azure-region}";

        static string _serverlocation = _resourceGrouplocation;
        static string _serverName = "{server-name}";
        static string _serverAdmin = "{server-admin-login}";
        static string _serverAdminPassword = "{server-admin-password}";

        static string _firewallRuleName = "{firewall-rule-name}";
        static string _startIpAddress = "{0.0.0.0}";
        static string _endIpAddress = "{255.255.255.255}";

        static string _databaseName = "{dbfromcsarticle}";
        static string _databaseEdition = DatabaseEditions.Basic;
        static string _databasePerfLevel = ""; // "S0", "S1", and so on here for other tiers


        static void Main(string[] args)
        {
            // Authenticate:
            _token = GetToken(_tenantId, _applicationId, _applicationSecret);
            Console.WriteLine("Token acquired. Expires on:" + _token.ExpiresOn);

            // Instantiate management clients:
            _resourceMgmtClient = new ResourceManagementClient(new Microsoft.Rest.TokenCredentials(_token.AccessToken));
            _sqlMgmtClient = new SqlManagementClient(new TokenCloudCredentials(_subscriptionId, _token.AccessToken));


            Console.WriteLine("Resource group...");
            ResourceGroup rg = CreateOrUpdateResourceGroup(_resourceMgmtClient, _subscriptionId, _resourceGroupName, _resourceGrouplocation);
            Console.WriteLine("Resource group: " + rg.Id);


            Console.WriteLine("Server...");
            ServerGetResponse sgr = CreateOrUpdateServer(_sqlMgmtClient, _resourceGroupName, _serverlocation, _serverName, _serverAdmin, _serverAdminPassword);
            Console.WriteLine("Server: " + sgr.Server.Id);

            Console.WriteLine("Server firewall...");
            FirewallRuleGetResponse fwr = CreateOrUpdateFirewallRule(_sqlMgmtClient, _resourceGroupName, _serverName, _firewallRuleName, _startIpAddress, _endIpAddress);
            Console.WriteLine("Server firewall: " + fwr.FirewallRule.Id);

            Console.WriteLine("Database...");
            DatabaseCreateOrUpdateResponse dbr = CreateOrUpdateDatabase(_sqlMgmtClient, _resourceGroupName, _serverName, _databaseName, _databaseEdition, _databasePerfLevel);
            Console.WriteLine("Database: " + dbr.Database.Id);


            Console.WriteLine("Press any key to continue...");
            Console.ReadKey();
        }

        static ResourceGroup CreateOrUpdateResourceGroup(ResourceManagementClient resourceMgmtClient, string subscriptionId, string resourceGroupName, string resourceGroupLocation)
        {
            ResourceGroup resourceGroupParameters = new ResourceGroup()
            {
                Location = resourceGroupLocation,
            };
            resourceMgmtClient.SubscriptionId = subscriptionId;
            ResourceGroup resourceGroupResult = resourceMgmtClient.ResourceGroups.CreateOrUpdate(resourceGroupName, resourceGroupParameters);
            return resourceGroupResult;
        }

        static ServerGetResponse CreateOrUpdateServer(SqlManagementClient sqlMgmtClient, string resourceGroupName, string serverLocation, string serverName, string serverAdmin, string serverAdminPassword)
        {
            ServerCreateOrUpdateParameters serverParameters = new ServerCreateOrUpdateParameters()
            {
                Location = serverLocation,
                Properties = new ServerCreateOrUpdateProperties()
                {
                    AdministratorLogin = serverAdmin,
                    AdministratorLoginPassword = serverAdminPassword,
                    Version = "12.0"
                }
            };
            ServerGetResponse serverResult = sqlMgmtClient.Servers.CreateOrUpdate(resourceGroupName, serverName, serverParameters);
            return serverResult;
        }


        static FirewallRuleGetResponse CreateOrUpdateFirewallRule(SqlManagementClient sqlMgmtClient, string resourceGroupName, string serverName, string firewallRuleName, string startIpAddress, string endIpAddress)
        {
            FirewallRuleCreateOrUpdateParameters firewallParameters = new FirewallRuleCreateOrUpdateParameters()
            {
                Properties = new FirewallRuleCreateOrUpdateProperties()
                {
                    StartIpAddress = startIpAddress,
                    EndIpAddress = endIpAddress
                }
            };
            FirewallRuleGetResponse firewallResult = sqlMgmtClient.FirewallRules.CreateOrUpdate(resourceGroupName, serverName, firewallRuleName, firewallParameters);
            return firewallResult;
        }



        static DatabaseCreateOrUpdateResponse CreateOrUpdateDatabase(SqlManagementClient sqlMgmtClient, string resourceGroupName, string serverName, string databaseName, string databaseEdition, string databasePerfLevel)
        {
            // Retrieve the server that will host this database
            Server currentServer = sqlMgmtClient.Servers.Get(resourceGroupName, serverName).Server;

            // Create a database: configure create or update parameters and properties explicitly
            DatabaseCreateOrUpdateParameters newDatabaseParameters = new DatabaseCreateOrUpdateParameters()
            {
                Location = currentServer.Location,
                Properties = new DatabaseCreateOrUpdateProperties()
                {
                    CreateMode = DatabaseCreateMode.Default,
                    Edition = databaseEdition,
                    RequestedServiceObjectiveName = databasePerfLevel
                }
            };
            DatabaseCreateOrUpdateResponse dbResponse = sqlMgmtClient.Databases.CreateOrUpdate(resourceGroupName, serverName, databaseName, newDatabaseParameters);
            return dbResponse;
        }



        private static AuthenticationResult GetToken(string tenantId, string applicationId, string applicationSecret)
        {
            AuthenticationContext authContext = new AuthenticationContext("https://login.windows.net/" + tenantId);
            _token = authContext.AcquireToken("https://management.core.windows.net/", new ClientCredential(applicationId, applicationSecret));
            return _token;
        }
      }
    }





## <a name="create-a-service-principal-to-access-resources"></a>Creare un'entità servizio per accedere alle risorse

Lo script di PowerShell seguente permette di creare l'applicazione Active Directory (AD) e l'entità servizio necessarie per autenticare l'app C#. Lo script restituisce i valori necessari per l'esempio di C# precedente. Per informazioni dettagliate, vedere [Usare Azure PowerShell per creare un'entità servizio per accedere alle risorse](../resource-group-authenticate-service-principal.md).

   
    # Sign in to Azure.
    Add-AzureRmAccount
    
    # If you have multiple subscriptions, uncomment and set to the subscription you want to work with.
    #$subscriptionId = "{xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx}"
    #Set-AzureRmContext -SubscriptionId $subscriptionId
    
    # Provide these values for your new AAD app.
    # $appName is the display name for your app, must be unique in your directory.
    # $uri does not need to be a real uri.
    # $secret is a password you create.
    
    $appName = "{app-name}"
    $uri = "http://{app-name}"
    $secret = "{app-password}"
    
    # Create a AAD app
    $azureAdApplication = New-AzureRmADApplication -DisplayName $appName -HomePage $Uri -IdentifierUris $Uri -Password $secret
    
    # Create a Service Principal for the app
    $svcprincipal = New-AzureRmADServicePrincipal -ApplicationId $azureAdApplication.ApplicationId
    
    # To avoid a PrincipalNotFound error, I pause here for 15 seconds.
    Start-Sleep -s 15
    
    # If you still get a PrincipalNotFound error, then rerun the following until successful. 
    $roleassignment = New-AzureRmRoleAssignment -RoleDefinitionName Contributor -ServicePrincipalName $azureAdApplication.ApplicationId.Guid
    
    
    # Output the values we need for our C# application to successfully authenticate
    
    Write-Output "Copy these values into the C# sample app"
    
    Write-Output "_subscriptionId:" (Get-AzureRmContext).Subscription.SubscriptionId
    Write-Output "_tenantId:" (Get-AzureRmContext).Tenant.TenantId
    Write-Output "_applicationId:" $azureAdApplication.ApplicationId.Guid
    Write-Output "_applicationSecret:" $secret



## <a name="next-steps"></a>Passaggi successivi
Dopo aver provato il database SQL e aver impostato un database con C#, è possibile leggere gli articoli seguenti:

- [Connettersi al database SQL con SQL Server Management Studio ed eseguire una query T-SQL di esempio](sql-database-connect-query-ssms.md)

## <a name="additional-resources"></a>Risorse aggiuntive

- [Database SQL](https://azure.microsoft.com/documentation/services/sql-database/)
- [Classe di database](https://msdn.microsoft.com/library/azure/microsoft.azure.management.sql.models.database.aspx)




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



<!--HONumber=Oct16_HO2-->


