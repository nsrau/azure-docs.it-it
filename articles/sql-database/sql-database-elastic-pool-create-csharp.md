<properties
    pageTitle="Creare un pool di database elastici con C# | Microsoft Azure"
    description="Usare tecniche di sviluppo di database in C# per creare un pool di database elastici scalabile nel database SQL di Azure per poter condividere risorse tra più database."
    services="sql-database"
    documentationCenter=""
    authors="sidneyh"
    manager="jhubbard"
    editor=""/>

<tags
    ms.service="sql-database"
    ms.devlang="NA"
    ms.topic="get-started-article"
    ms.tgt_pltfrm="csharp"
    ms.workload="data-management"
    ms.date="04/28/2016"
    ms.author="sidneyh"/>

# Creare un nuovo pool di database elastici con C&#x23;

> [AZURE.SELECTOR]
- [Portale di Azure](sql-database-elastic-pool-create-portal.md)
- [PowerShell](sql-database-elastic-pool-create-powershell.md)
- [C#](sql-database-elastic-pool-create-csharp.md)


Informazioni su come creare un [pool di database elastici](sql-database-elastic-pool.md) con C&#x23;.

Per i codici di errore comuni, vedere [Codici di errore SQL per le applicazioni client del database SQL: errore di connessione e altri problemi del database](sql-database-develop-error-messages.md).

I pool di database elastici sono attualmente in anteprima e sono disponibili unicamente con i server di Database SQL V12. Se si usa un server di database SQL V11 è possibile [usare PowerShell per eseguire l'aggiornamento a V12 e creare un pool](sql-database-upgrade-server-portal.md) in un unico passaggio.

Poiché negli esempi si usa la [libreria di database SQL per .NET](https://msdn.microsoft.com/library/azure/mt349017.aspx), è necessario installarla. È possibile installarla con il comando seguente nella [Console di Gestione pacchetti](http://docs.nuget.org/Consume/Package-Manager-Console) in Visual Studio, scegliendo **Strumenti** > **Gestione pacchetti NuGet** > **Console di Gestione pacchetti**:

    PM> Install-Package Microsoft.Azure.Management.Sql –Pre

## Creare un nuovo pool

Creare un'istanza di [SqlManagementClient](https://msdn.microsoft.com/library/microsoft.azure.management.sql.sqlmanagementclient) con i valori di [Azure Active Directory](sql-database-client-id-keys.md). Creare un'istanza di [ElasticPoolCreateOrUpdateParameters](https://msdn.microsoft.com/library/microsoft.azure.management.sql.models.elasticpoolcreateorupdateparameters) e chiamare il metodo [CreateOrUpdate](https://msdn.microsoft.com/library/microsoft.azure.management.sql.databaseoperationsextensions.createorupdate). I valori per eDTU per pool, DTU min e max sono vincolati dal valore del livello di servizio (Basic, Standard o Premium). Vedere [Limiti di archiviazione e di eDTU dei pool elastici e dei database elastici](sql-database-elastic-pool.md#eDTU-and-storage-limits-for-elastic-pools-and-elastic-databases).


    ElasticPoolCreateOrUpdateParameters newPoolParameters = new ElasticPoolCreateOrUpdateParameters()
    {
        Location = "South Central US",
        Properties = new ElasticPoolCreateOrUpdateProperties()
        {
            Edition = "Standard",
            Dtu = 400,
            DatabaseDtuMin = 0,
            DatabaseDtuMax = 100
         }
    };

    // Create the pool
    var newPoolResponse = sqlClient.ElasticPools.CreateOrUpdate("resourcegroup-name", "server-name", "ElasticPool1", newPoolParameters);

## Creare un nuovo database in un pool

Creare un'istanza di [DataBaseCreateorUpdateProperties](https://msdn.microsoft.com/library/microsoft.azure.management.sql.models.databasecreateorupdateproperties) e impostare le proprietà del nuovo database. Chiamare quindi il metodo CreateOrUpdate con il gruppo di risorse, il nome del server e un nuovo nome del database.

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

Per spostare un database esistente in un pool, vedere [Spostare un database in un pool elastico](sql-database-elastic-pool-manage-csharp.md#Move-a-database-into-an-elastic-pool).

## Esempio: Creare un pool con C&#x23

Questo esempio crea un nuovo gruppo di risorse di Azure, una nuova istanza del server di Azure SQL e un nuovo pool elastico.
 

Le librerie seguenti sono necessarie per eseguire questo esempio. È possibile installarle con il comando seguente nella [Console di Gestione pacchetti](http://docs.nuget.org/Consume/Package-Manager-Console) in Visual Studio, scegliendo **Strumenti** > **Gestione pacchetti NuGet** > **Console di Gestione pacchetti**

    Install-Package Microsoft.Azure.Management.Sql –Pre
    Install-Package Microsoft.Azure.Management.Resources –Pre
    Install-Package Microsoft.Azure.Common.Authentication –Pre

Creare un'app console e sostituire il contenuto del file Program.cs con il codice seguente. Per ottenere l'ID client richiesto e i valori correlati, vedere l'articolo [Ottenere l'ID client e la chiave per la connessione al database SQL dal codice](sql-database-client-id-keys.md). Usare il cmdlet [Get-AzureRmSubscription](https://msdn.microsoft.com/library/mt619284.aspx) per recuperare il valore per l'ID sottoscrizione.

    using Microsoft.Azure;
    using Microsoft.Azure.Management.Resources;
    using Microsoft.Azure.Management.Resources.Models;
    using Microsoft.Azure.Management.Sql;
    using Microsoft.Azure.Management.Sql.Models;
    using Microsoft.IdentityModel.Clients.ActiveDirectory;
    using System;
    
    namespace SqlDbElasticPoolsSample
    {
    class Program
    {
        // authentication variables
        static string subscriptionId = "<your Azure subscription>";
        static string clientId = "<your client id>";
        static string redirectUri = "<your redirect URI>";
        static string domainName = "<i.e. microsoft.onmicrosoft.com>";
        static AuthenticationResult token;

        // resource group variables
        static string datacenterLocation = "<location>";
        static string resourceGroupName = "<resource group name>";

        // server variables
        static string serverName = "<server name>";
        static string adminLogin = "<server admin>";
        static string adminPassword = "<server password (store it securely!)>";
        static string serverVersion = "12.0";

        // pool variables
        static string elasticPoolName = "<pool name>";
        static string poolEdition = "Standard";
        static int poolDtus = 400;
        static int databaseMinDtus = 0;
        static int databaseMaxDtus = 100;


        static void Main(string[] args)
        {
            // Sign in to Azure
            token = GetAccessToken();
            Console.WriteLine("Logged in as: " + token.UserInfo.DisplayableId);

            // Create a resource group
            ResourceGroup rg = CreateResourceGroup();
            Console.WriteLine(rg.Name + " created.");

            // Create a server
            Console.WriteLine("Creating server... ");
            ServerGetResponse srvr = CreateServer();
            Console.WriteLine("Creation of server " + srvr.Server.Name + ": " + srvr.StatusCode.ToString());

            // Create a pool
            Console.WriteLine("Creating elastic database pool... ");
            ElasticPoolCreateOrUpdateResponse epool = CreateElasticDatabasePool();
            Console.WriteLine("Creation of pool " + epool.ElasticPool.Name + ": " + epool.Status.ToString());


            // keep the console open until Enter is pressed!
            Console.WriteLine("Press Enter to exit.");
            Console.ReadLine();
        }


        static ElasticPoolCreateOrUpdateResponse CreateElasticDatabasePool()
        {
            // Create a SQL Database management client
            SqlManagementClient sqlClient = new SqlManagementClient(new TokenCloudCredentials(subscriptionId, token.AccessToken));

            // Create elastic pool: configure create or update parameters and properties explicitly
            ElasticPoolCreateOrUpdateParameters newPoolParameters = new ElasticPoolCreateOrUpdateParameters()
            {
                Location = datacenterLocation,
                Properties = new ElasticPoolCreateOrUpdateProperties()
                {
                    Edition = poolEdition,
                    Dtu = poolDtus, 
                    DatabaseDtuMin = databaseMinDtus,
                    DatabaseDtuMax = databaseMaxDtus
                }
            };

            // Create the pool
            var newPoolResponse = sqlClient.ElasticPools.CreateOrUpdate(resourceGroupName, serverName, elasticPoolName, newPoolParameters);
            return newPoolResponse;
        }



        static ServerGetResponse CreateServer()
        {

            // Create a SQL Database management client
            SqlManagementClient sqlClient = new SqlManagementClient(new TokenCloudCredentials(subscriptionId, token.AccessToken));

            // Create a server
            ServerCreateOrUpdateParameters serverParameters = new ServerCreateOrUpdateParameters()
            {
                Location = datacenterLocation,
                Properties = new ServerCreateOrUpdateProperties()
                {
                    AdministratorLogin = adminLogin,
                    AdministratorLoginPassword = adminPassword,
                    Version = serverVersion
                }
            };

            var serverResult = sqlClient.Servers.CreateOrUpdate(resourceGroupName, serverName, serverParameters);
            return serverResult;

        }


        static ResourceGroup CreateResourceGroup()
        {
           
            Microsoft.Rest.TokenCredentials creds = new Microsoft.Rest.TokenCredentials(token.AccessToken);
            // Create a resource management client 
            ResourceManagementClient resourceClient = new ResourceManagementClient(creds);

            // Resource group parameters
            ResourceGroup resourceGroupParameters = new ResourceGroup()
            {
                Location = datacenterLocation,
            };

            //Create a resource group
            resourceClient.SubscriptionId = subscriptionId;
            var resourceGroupResult = resourceClient.ResourceGroups.CreateOrUpdate(resourceGroupName, resourceGroupParameters);
            return resourceGroupResult;
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

- [Gestire il pool](sql-database-elastic-pool-manage-csharp.md)
- [Creare processi elastici](sql-database-elastic-jobs-overview.md): i processi elastici consentono di eseguire script T-SQL su un numero qualsiasi di database nel pool.
- [Aumento del numero di istanze con il database SQL di Azure](sql-database-elastic-scale-introduction.md): usare gli strumenti di database elastici per aumentare il numero di istanze.

## Risorse aggiuntive

- [Database SQL](https://azure.microsoft.com/documentation/services/sql-database/)
- [API di Gestione risorse di Azure](https://msdn.microsoft.com/library/azure/dn948464.aspx)

<!---HONumber=AcomDC_0504_2016-->