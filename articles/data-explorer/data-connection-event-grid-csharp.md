---
title: Creare una connessione dati di griglia di eventi per Esplora dati di Azure usandoC#
description: Questo articolo illustra come creare una connessione dati di griglia di eventi per Esplora dati di Azure usando C#.
author: lucygoldbergmicrosoft
ms.author: lugoldbe
ms.reviewer: orspodek
ms.service: data-explorer
ms.topic: conceptual
ms.date: 10/07/2019
ms.openlocfilehash: 0accf502df3616a686a34fc6c96cb2cfc47e6db1
ms.sourcegitcommit: 3d4917ed58603ab59d1902c5d8388b954147fe50
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 12/02/2019
ms.locfileid: "74667832"
---
# <a name="create-an-event-grid-data-connection-for-azure-data-explorer-by-using-c"></a>Creare una connessione dati di griglia di eventi per Esplora dati di Azure usandoC#

> [!div class="op_single_selector"]
> * [di Microsoft Azure](ingest-data-event-grid.md)
> * [C#](data-connection-event-grid-csharp.md)
> * [Python](data-connection-event-grid-python.md)
> * [Modello di Azure Resource Manager](data-connection-event-grid-resource-manager.md)


Esplora dati di Azure è un servizio di esplorazione dati rapido e a scalabilità elevata per dati di log e di telemetria. Azure Esplora dati offre l'inserimento (caricamento dei dati) dagli hub eventi, dagli hub Internet e dai BLOB scritti nei contenitori BLOB. In questo articolo si crea una connessione dati di griglia di eventi per Esplora dati di Azure C#usando.

## <a name="prerequisites"></a>Prerequisiti

* Se Visual Studio 2019 non è installato, è possibile scaricare e usare l'edizione **gratuita** [Visual Studio 2019 Community Edition](https://www.visualstudio.com/downloads/). Durante l'installazione di Visual Studio abilitare **Sviluppo di Azure**.
* Se non si ha una sottoscrizione di Azure, prima di iniziare creare un [account Azure gratuito](https://azure.microsoft.com/free/).
* Creare [un cluster e un database](create-cluster-database-csharp.md)
* Creazione del [mapping di tabelle e colonne](net-standard-ingest-data.md#create-a-table-on-your-test-cluster)
* Impostare i [criteri di database e tabella](database-table-policies-csharp.md) (facoltativo)
* Creare un [account di archiviazione con una sottoscrizione di griglia di eventi](ingest-data-event-grid.md#create-an-event-grid-subscription-in-your-storage-account).

[!INCLUDE [data-explorer-data-connection-install-nuget-csharp](../../includes/data-explorer-data-connection-install-nuget-csharp.md)]

[!INCLUDE [data-explorer-authentication](../../includes/data-explorer-authentication.md)]

## <a name="add-an-event-grid-data-connection"></a>Aggiungere una connessione dati di griglia di eventi

Nell'esempio seguente viene illustrato come aggiungere una connessione dati di griglia di eventi a livello di codice. Per aggiungere una connessione dati di griglia di eventi usando il portale di Azure, vedere [creare una connessione dati di griglia di eventi in Azure Esplora dati](ingest-data-event-grid.md#create-an-event-grid-data-connection-in-azure-data-explorer) .

```csharp
var tenantId = "xxxxxxxx-xxxxx-xxxx-xxxx-xxxxxxxxx";//Directory (tenant) ID
var clientId = "xxxxxxxx-xxxxx-xxxx-xxxx-xxxxxxxxx";//Application ID
var clientSecret = "xxxxxxxxxxxxxx";//Client Secret
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
//The cluster and database that are created as part of the Prerequisites
var clusterName = "mykustocluster";
var databaseName = "mykustodatabase";
var dataConnectionName = "myeventhubconnect";
//The event hub and storage account that are created as part of the Prerequisites
var eventHubResourceId = "/subscriptions/xxxxxxxx-xxxxx-xxxx-xxxx-xxxxxxxxx/resourceGroups/xxxxxx/providers/Microsoft.EventHub/namespaces/xxxxxx/eventhubs/xxxxxx";
var storageAccountResourceId = "/subscriptions/xxxxxxxx-xxxxx-xxxx-xxxx-xxxxxxxxx/resourceGroups/xxxxxx/providers/Microsoft.Storage/storageAccounts/xxxxxx";
var consumerGroup = "$Default";
var location = "Central US";
//The table and column mapping are created as part of the Prerequisites
var tableName = "StormEvents";
var mappingRuleName = "StormEvents_CSV_Mapping";
var dataFormat = DataFormat.CSV;

await kustoManagementClient.DataConnections.CreateOrUpdateAsync(resourceGroupName, clusterName, databaseName, dataConnectionName,
    new EventGridDataConnection(storageAccountResourceId, eventHubResourceId, consumerGroup, tableName: tableName, location: location, mappingRuleName: mappingRuleName, dataFormat: dataFormat));
```

|**Impostazione** | **Valore consigliato** | **Descrizione campo**|
|---|---|---|
| TenantId | *xxxxxxxx-xxxxx-xxxx-xxxx-xxxxxxxxx* | ID tenant. Noto anche come ID directory.|
| subscriptionId | *xxxxxxxx-xxxxx-xxxx-xxxx-xxxxxxxxx* | ID sottoscrizione usato per la creazione di risorse.|
| clientId | *xxxxxxxx-xxxxx-xxxx-xxxx-xxxxxxxxx* | ID client dell'applicazione che può accedere alle risorse nel tenant.|
| clientSecret | *xxxxxxxxxxxxxx* | Il segreto client dell'applicazione che può accedere alle risorse nel tenant. |
| resourceGroupName | *testrg* | Nome del gruppo di risorse che contiene il cluster.|
| clusterName | *mykustocluster* | Nome del cluster.|
| databaseName | *mykustodatabase* | Nome del database di destinazione nel cluster.|
| dataconnectname | *myeventhubconnect* | Nome desiderato della connessione dati.|
| tableName | *StormEvents* | Nome della tabella di destinazione nel database di destinazione.|
| mappingRuleName | *StormEvents_CSV_Mapping* | Nome del mapping di colonne correlato alla tabella di destinazione.|
| dataFormat | *CSV* | Formato dati del messaggio.|
| eventHubResourceId | *ID risorsa* | ID risorsa dell'hub eventi in cui è configurata la griglia di eventi per l'invio di eventi. |
| storageAccountResourceId | *ID risorsa* | ID risorsa dell'account di archiviazione che include i dati per l'inserimento. |
| Gruppo Consumer | *$Default* | Il gruppo di consumer dell'hub eventi.|
| location | *Stati Uniti centrali* | Percorso della risorsa di connessione dati.|

[!INCLUDE [data-explorer-data-connection-clean-resources-csharp](../../includes/data-explorer-data-connection-clean-resources-csharp.md)]
