---
title: Creare criteri usando l'SDK di Azure Data Explorer in C
description: In questo articolo verrà illustrato come creare i criteri utilizzando C .
author: lucygoldbergmicrosoft
ms.author: lugoldbe
ms.reviewer: orspodek
ms.service: data-explorer
ms.topic: conceptual
ms.date: 09/24/2019
ms.openlocfilehash: 17312840b0081056ad04723f2b2c241c47902021
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/27/2020
ms.locfileid: "74667299"
---
# <a name="create-database-and-table-policies-for-azure-data-explorer-by-using-c"></a>Creare criteri di database e tabelle per Azure Data Explorer usando CCreate database and table policies for Azure Data Explorer by using C #

> [!div class="op_single_selector"]
> * [C #](database-table-policies-csharp.md)
> * [Python](database-table-policies-python.md)
>

Esplora dati di Azure è un servizio di esplorazione dati rapido e a scalabilità elevata per dati di log e di telemetria. In questo articolo verranno creati i criteri di database e di tabella per Azure Data Explorer usando C.

## <a name="prerequisites"></a>Prerequisiti

* Visual Studio 2019. Se non si dispone di Visual Studio 2019, è possibile scaricare e utilizzare la community di [Visual Studio 2019](https://www.visualstudio.com/downloads/) *gratuita.* Assicurarsi di selezionare lo sviluppo di Azure durante l'installazione di Visual Studio.Sure sure to select **Azure development** during the Visual Studio setup.
* Una sottoscrizione di Azure. Se necessario, è possibile creare un [account Azure gratuito](https://azure.microsoft.com/free/) prima di iniziare.
* [Un cluster di test e](create-cluster-database-csharp.md)un database .
* [Una tabella di prova](net-standard-ingest-data.md#create-a-table-on-your-test-cluster).

## <a name="install-c-nuget"></a>Installare NuGet in C

* Installare il pacchetto NuGet di [Azure Data Explorer (Kusto).](https://www.nuget.org/packages/Microsoft.Azure.Management.Kusto/)
* Installare il [pacchetto Microsoft.Azure.Kusto.Data.NETStandard NuGet](https://www.nuget.org/packages/Microsoft.Azure.Kusto.Data.NETStandard/). (Facoltativo, per la modifica dei criteri di tabella.)
* Installare il [pacchetto Microsoft.IdentityModel.Clients.ActiveDirectory NuGet](https://www.nuget.org/packages/Microsoft.IdentityModel.Clients.ActiveDirectory/)per l'autenticazione.

## <a name="authentication"></a>Authentication
Per eseguire gli esempi in questo articolo, sono necessarie un'applicazione di Azure Active Directory (Azure AD) e un'entità servizio in grado di accedere alle risorse. È possibile usare la stessa applicazione Azure AD per l'autenticazione da un cluster e un database di [test.](create-cluster-database-csharp.md#authentication) Se si vuole usare un'applicazione Azure AD diversa, vedere [Creare un'applicazione Azure AD](https://docs.microsoft.com/azure/active-directory/develop/howto-create-service-principal-portal) per creare un'applicazione Azure AD gratuita e aggiungere un'assegnazione di ruolo nell'ambito della sottoscrizione. In questo articolo viene `Directory (tenant) ID`inoltre `Application ID`illustrato `Client secret`come ottenere i , e . Potrebbe essere necessario aggiungere la nuova applicazione Azure AD come entità nel database. Per altre informazioni, vedere Gestire le autorizzazioni del database di Azure Data Explorer.For more information, see [Manage Azure Data Explorer database permissions.](https://docs.microsoft.com/azure/data-explorer/manage-database-permissions)

## <a name="alter-database-retention-policy"></a>Modificare i criteri di conservazione del database
Imposta un criterio di conservazione con un periodo di eliminazione temporanea di 10 giorni.
    
```csharp
var tenantId = "xxxxxxxx-xxxxx-xxxx-xxxx-xxxxxxxxx";//Directory (tenant) ID
var clientId = "xxxxxxxx-xxxxx-xxxx-xxxx-xxxxxxxxx";//Application ID
var clientSecret = "xxxxxxxxxxxxxx";//Client secret
var subscriptionId = "xxxxxxxx-xxxxx-xxxx-xxxx-xxxxxxxxx";
var authenticationContext = new AuthenticationContext($"https://login.windows.net/{tenantId}");
var credential = new ClientCredential(clientId, clientSecret);
var result = await authenticationContext.AcquireTokenAsync(resource: "https://management.core.windows.net/", clientCredential: credential);

var credentials = new TokenCredentials(result.AccessToken, result.AccessTokenType);

var kustoManagementClient = new KustoManagementClient(credentials)
{
    SubscriptionId = subscriptionId
};

var resourceGroupName = "testrg";
//The cluster and database that are created as part of the prerequisites
var clusterName = "mykustocluster";
var databaseName = "mykustodatabase";
await kustoManagementClient.Databases.UpdateAsync(resourceGroupName, clusterName, databaseName, new DatabaseUpdate(softDeletePeriod: TimeSpan.FromDays(10)));
```

## <a name="alter-database-cache-policy"></a>Modificare i criteri di cache del database
Imposta un criterio di cache per il database. I cinque giorni precedenti di dati saranno sul cluster SSD.

```csharp
var tenantId = "xxxxxxxx-xxxxx-xxxx-xxxx-xxxxxxxxx";//Directory (tenant) ID
var clientId = "xxxxxxxx-xxxxx-xxxx-xxxx-xxxxxxxxx";//Application ID
var clientSecret = "xxxxxxxxxxxxxx";//Client secret
var subscriptionId = "xxxxxxxx-xxxxx-xxxx-xxxx-xxxxxxxxx";
var authenticationContext = new AuthenticationContext($"https://login.windows.net/{tenantId}");
var credential = new ClientCredential(clientId, clientSecret);
var result = await authenticationContext.AcquireTokenAsync(resource: "https://management.core.windows.net/", clientCredential: credential);

var credentials = new TokenCredentials(result.AccessToken, result.AccessTokenType);

var kustoManagementClient = new KustoManagementClient(credentials)
{
    SubscriptionId = subscriptionId
};

var resourceGroupName = "testrg";
//The cluster and database that are created as part of the prerequisites
var clusterName = "mykustocluster";
var databaseName = "mykustodatabase";
await kustoManagementClient.Databases.UpdateAsync(resourceGroupName, clusterName, databaseName, new DatabaseUpdate(hotCachePeriod: TimeSpan.FromDays(5)));
```

## <a name="alter-table-cache-policy"></a>Modificare i criteri di cache delle tabelle
Imposta un criterio di cache per la tabella. I cinque giorni precedenti di dati saranno sul cluster SSD.

```csharp
var kustoUri = "https://<ClusterName>.<Region>.kusto.windows.net:443/";
var databaseName = "<DatabaseName>";
var tenantId = "xxxxxxxx-xxxxx-xxxx-xxxx-xxxxxxxxx";//Directory (tenant) ID
var clientId = "xxxxxxxx-xxxxx-xxxx-xxxx-xxxxxxxxx";//Application ID
var clientSecret = "xxxxxxxxxxxxxx";//Client secret
var tableName = "<TableName>"

var kustoConnectionStringBuilder =
    new KustoConnectionStringBuilder(kustoUri)
    {
        FederatedSecurity = true,
        InitialCatalog = databaseName,
        ApplicationClientId = clientId,
        ApplicationKey = clientSecret,
        Authority = tenantId
    };

using (var kustoClient = KustoClientFactory.CreateCslAdminProvider(kustoConnectionStringBuilder))
{
    //dataHotSpan and indexHotSpan should have the same value
    var hotSpan = TimeSpan.FromDays(5);
    var command1 = CslCommandGenerator.GenerateAlterTableCachingPolicyCommand(tableName: tableName,
                    dataHotSpan: hotSpan, indexHotSpan: hotSpan);

    kustoClient.ExecuteControlCommand(command);
}
```

## <a name="add-a-new-principal-for-the-database"></a>Aggiungere una nuova entità per il databaseAdd a new principal for the database
Aggiunge una nuova applicazione Azure AD come entità di amministrazione per il database.

```csharp
var tenantId = "xxxxxxxx-xxxxx-xxxx-xxxx-xxxxxxxxx";//Directory (tenant) ID
var clientId = "xxxxxxxx-xxxxx-xxxx-xxxx-xxxxxxxxx";//Application ID
var clientSecret = "xxxxxxxxxxxxxx";//Client Secret
var clientIdToAdd = "xxxxxxxx-xxxxx-xxxx-xxxx-xxxxxxxxx";//Application ID
var subscriptionId = "xxxxxxxx-xxxxx-xxxx-xxxx-xxxxxxxxx";
var authenticationContext = new AuthenticationContext($"https://login.windows.net/{tenantId}");
var credential = new ClientCredential(clientId, clientSecret);
var result = await authenticationContext.AcquireTokenAsync(resource: "https://management.core.windows.net/", clientCredential: credential);

var credentials = new TokenCredentials(result.AccessToken, result.AccessTokenType);

var kustoManagementClient = new KustoManagementClient(credentials)
{
    SubscriptionId = subscriptionId
};

var resourceGroupName = "testrg";
//The cluster and database that are created as part of the prerequisites
var clusterName = "mykustocluster";
var databaseName = "mykustodatabase";
await kustoManagementClient.Databases.AddPrincipalsAsync(resourceGroupName, clusterName, databaseName,
                new DatabasePrincipalListRequest()
                {
                    Value = new List<DatabasePrincipal>()
                    {
                        new DatabasePrincipal("Admin", "<database_principle_name>", "App", appId: clientIdToAdd, tenantName:tenantId)
                    }
                });
```
## <a name="next-steps"></a>Passaggi successivi

* [Altre informazioni sui criteri di database e tabelle](https://docs.microsoft.com/azure/kusto/management/policies)
