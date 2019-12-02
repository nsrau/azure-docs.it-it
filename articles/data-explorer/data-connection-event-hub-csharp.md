---
title: Creare una connessione dati di hub eventi per Esplora dati di Azure usandoC#
description: Questo articolo illustra come creare una connessione dati di hub eventi per Esplora dati di Azure usando C#.
author: lucygoldbergmicrosoft
ms.author: lugoldbe
ms.reviewer: orspodek
ms.service: data-explorer
ms.topic: conceptual
ms.date: 10/07/2019
ms.openlocfilehash: cf2a274b4f48b31739d6abba5cf87fa2a10d4ca1
ms.sourcegitcommit: 3d4917ed58603ab59d1902c5d8388b954147fe50
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 12/02/2019
ms.locfileid: "74667685"
---
# <a name="create-an-event-hub-data-connection-for-azure-data-explorer-by-using-c"></a>Creare una connessione dati di hub eventi per Esplora dati di Azure usandoC#

> [!div class="op_single_selector"]
> * [di Microsoft Azure](ingest-data-event-hub.md)
> * [C#](data-connection-event-hub-csharp.md)
> * [Python](data-connection-event-hub-python.md)
> * [Modello di Azure Resource Manager](data-connection-event-hub-resource-manager.md)

Esplora dati di Azure è un servizio di esplorazione dati rapido e a scalabilità elevata per dati di log e di telemetria. Azure Esplora dati offre l'inserimento (caricamento dei dati) dagli hub eventi, dagli hub Internet e dai BLOB scritti nei contenitori BLOB. In questo articolo si crea una connessione dati di hub eventi per Esplora dati di Azure usando C#.

## <a name="prerequisites"></a>Prerequisiti

* Se Visual Studio 2019 non è installato, è possibile scaricare e usare l'edizione **gratuita** [Visual Studio 2019 Community Edition](https://www.visualstudio.com/downloads/). Durante l'installazione di Visual Studio abilitare **Sviluppo di Azure**.
* Se non si ha una sottoscrizione di Azure, prima di iniziare creare un [account Azure gratuito](https://azure.microsoft.com/free/).
* Creare [un cluster e un database](create-cluster-database-csharp.md)
* Creazione del [mapping di tabelle e colonne](net-standard-ingest-data.md#create-a-table-on-your-test-cluster)
* Impostare i [criteri di database e tabella](database-table-policies-csharp.md) (facoltativo)
* Creare un [Hub eventi con i dati per](ingest-data-event-hub.md#create-an-event-hub)l'inserimento. 

[!INCLUDE [data-explorer-data-connection-install-nuget-csharp](../../includes/data-explorer-data-connection-install-nuget-csharp.md)]

[!INCLUDE [data-explorer-authentication](../../includes/data-explorer-authentication.md)]

## <a name="add-an-event-hub-data-connection"></a>Aggiungere una connessione dati dell'hub eventi

L'esempio seguente illustra come aggiungere una connessione dati di hub eventi a livello di codice. Vedere [connettersi all'hub eventi](ingest-data-event-hub.md#connect-to-the-event-hub) per aggiungere una connessione dati di hub eventi usando il portale di Azure.

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
//The event hub that is created as part of the Prerequisites
var eventHubResourceId = "/subscriptions/xxxxxxxx-xxxxx-xxxx-xxxx-xxxxxxxxx/resourceGroups/xxxxxx/providers/Microsoft.EventHub/namespaces/xxxxxx/eventhubs/xxxxxx";
var consumerGroup = "$Default";
var location = "Central US";
//The table and column mapping are created as part of the Prerequisites
var tableName = "StormEvents";
var mappingRuleName = "StormEvents_CSV_Mapping";
var dataFormat = DataFormat.CSV;
await kustoManagementClient.DataConnections.CreateOrUpdateAsync(resourceGroupName, clusterName, databaseName, dataConnectionName, 
    new EventHubDataConnection(eventHubResourceId, consumerGroup, location: location, tableName: tableName, mappingRuleName: mappingRuleName, dataFormat: dataFormat));
```

|**Impostazione** | **Valore consigliato** | **Descrizione campo**|
|---|---|---|
| TenantId | *xxxxxxxx-xxxxx-xxxx-xxxx-xxxxxxxxx* | ID tenant. Noto anche come ID directory.|
| subscriptionId | *xxxxxxxx-xxxxx-xxxx-xxxx-xxxxxxxxx* | ID sottoscrizione usato per la creazione di risorse.|
| clientId | *xxxxxxxx-xxxxx-xxxx-xxxx-xxxxxxxxx* | ID client dell'applicazione che può accedere alle risorse nel tenant.|
| clientSecret | *xxxxxxxxxxxxxx* | Il segreto client dell'applicazione che può accedere alle risorse nel tenant.|
| resourceGroupName | *testrg* | Nome del gruppo di risorse che contiene il cluster.|
| clusterName | *mykustocluster* | Nome del cluster.|
| databaseName | *mykustodatabase* | Nome del database di destinazione nel cluster.|
| dataconnectname | *myeventhubconnect* | Nome desiderato della connessione dati.|
| tableName | *StormEvents* | Nome della tabella di destinazione nel database di destinazione.|
| mappingRuleName | *StormEvents_CSV_Mapping* | Nome del mapping di colonne correlato alla tabella di destinazione.|
| dataFormat | *CSV* | Formato dati del messaggio.|
| eventHubResourceId | *ID risorsa* | ID risorsa dell'hub eventi che include i dati per l'inserimento. |
| Gruppo Consumer | *$Default* | Il gruppo di consumer dell'hub eventi.|
| location | *Stati Uniti centrali* | Percorso della risorsa di connessione dati.|

[!INCLUDE [data-explorer-data-connection-clean-resources-csharp](../../includes/data-explorer-data-connection-clean-resources-csharp.md)]
