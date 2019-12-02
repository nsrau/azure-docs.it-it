---
title: Creare criteri con Azure Esplora dati C# SDK
description: In questo articolo si apprenderà come creare i criteri usando C#.
author: lucygoldbergmicrosoft
ms.author: lugoldbe
ms.reviewer: orspodek
ms.service: data-explorer
ms.topic: conceptual
ms.date: 09/24/2019
ms.openlocfilehash: 17312840b0081056ad04723f2b2c241c47902021
ms.sourcegitcommit: 3d4917ed58603ab59d1902c5d8388b954147fe50
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 12/02/2019
ms.locfileid: "74667299"
---
# <a name="create-database-and-table-policies-for-azure-data-explorer-by-using-c"></a>Creare criteri di database e tabelle per Esplora dati di Azure usandoC#

> [!div class="op_single_selector"]
> * [C#](database-table-policies-csharp.md)
> * [Python](database-table-policies-python.md)
>

Esplora dati di Azure è un servizio di esplorazione dati rapido e a scalabilità elevata per dati di log e di telemetria. Questo articolo illustra come creare criteri di database e tabelle per Esplora dati di Azure usando C#.

## <a name="prerequisites"></a>Prerequisiti

* Visual Studio 2019. Se non si dispone di Visual Studio 2019, è possibile scaricare e usare la versione *gratuita* di [visual studio community 2019](https://www.visualstudio.com/downloads/). Assicurarsi di selezionare **sviluppo di Azure** durante l'installazione di Visual Studio.
* Una sottoscrizione di Azure. Se necessario, è possibile creare un [account Azure gratuito](https://azure.microsoft.com/free/) prima di iniziare.
* [Un cluster e un database di test](create-cluster-database-csharp.md).
* [Tabella di test](net-standard-ingest-data.md#create-a-table-on-your-test-cluster).

## <a name="install-c-nuget"></a>Installare C# NuGet

* Installare il [pacchetto NuGet Azure Esplora dati (kusto)](https://www.nuget.org/packages/Microsoft.Azure.Management.Kusto/).
* Installare il [pacchetto NuGet Microsoft. Azure. kusto. Data. NETStandard](https://www.nuget.org/packages/Microsoft.Azure.Kusto.Data.NETStandard/). Facoltativo, per la modifica dei criteri di tabella.
* Installare il [pacchetto NuGet Microsoft. IdentityModel. clients. ActiveDirectory](https://www.nuget.org/packages/Microsoft.IdentityModel.Clients.ActiveDirectory/), per l'autenticazione.

## <a name="authentication"></a>Authentication
Per eseguire gli esempi in questo articolo, è necessario disporre di un'applicazione Azure Active Directory (Azure AD) e di un'entità servizio che possano accedere alle risorse. È possibile utilizzare la stessa applicazione Azure AD per l'autenticazione da [un cluster e un database di test](create-cluster-database-csharp.md#authentication). Se si vuole usare un'applicazione Azure AD diversa, vedere [creare un'applicazione Azure ad](https://docs.microsoft.com/azure/active-directory/develop/howto-create-service-principal-portal) per creare un'applicazione di Azure ad gratuita e aggiungere un'assegnazione di ruolo nell'ambito della sottoscrizione. Questo articolo illustra anche come ottenere le `Directory (tenant) ID`, `Application ID`e `Client secret`. Potrebbe essere necessario aggiungere la nuova applicazione Azure AD come entità nel database. Per altre informazioni, vedere [gestire le autorizzazioni per il database Esplora dati di Azure](https://docs.microsoft.com/azure/data-explorer/manage-database-permissions).

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

## <a name="alter-database-cache-policy"></a>Alter database cache Policy
Imposta i criteri di cache per il database. I cinque giorni precedenti dei dati saranno sull'unità SSD del cluster.

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

## <a name="alter-table-cache-policy"></a>ALTER TABLE cache Policy
Imposta i criteri di cache per la tabella. I cinque giorni precedenti dei dati saranno sull'unità SSD del cluster.

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

## <a name="add-a-new-principal-for-the-database"></a>Aggiungere una nuova entità di protezione per il database
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
