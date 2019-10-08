---
title: Creare una connessione dati dell'hub Internet per la Esplora dati di Azure usandoC#
description: Questo articolo illustra come creare una connessione dati dell'hub Internet per Azure Esplora dati usando C#.
author: lucygoldbergmicrosoft
ms.author: lugoldbe
ms.reviewer: orspodek
ms.service: data-explorer
ms.topic: conceptual
ms.date: 10/07/2019
ms.openlocfilehash: bcc80000be5e061a37601f05a2a245aac031fc15
ms.sourcegitcommit: 11265f4ff9f8e727a0cbf2af20a8057f5923ccda
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 10/08/2019
ms.locfileid: "72031656"
---
# <a name="create-an-iot-hub-data-connection-for-azure-data-explorer-by-using-c-preview"></a>Creare una connessione dati dell'hub Internet per Azure Esplora dati usando C# (anteprima)

> [!div class="op_single_selector"]
> * [Portale](ingest-data-iot-hub.md)
> * [C#](data-connection-iot-hub-csharp.md)
> * [Python](data-connection-iot-hub-python.md)

Esplora dati di Azure è un servizio di esplorazione dati rapido e a scalabilità elevata per dati di log e di telemetria. Azure Esplora dati offre l'inserimento (caricamento dei dati) dagli hub eventi, dagli hub Internet e dai BLOB scritti nei contenitori BLOB. In questo articolo si crea una connessione dati dell'hub Internet per Azure Esplora dati usando C#.

## <a name="prerequisites"></a>Prerequisiti

* Se Visual Studio 2019 non è installato, è possibile scaricare e usare l'edizione **gratuita** [Visual Studio 2019 Community Edition](https://www.visualstudio.com/downloads/). Durante l'installazione di Visual Studio abilitare **Sviluppo di Azure**.

* Se non si ha una sottoscrizione di Azure, creare un [account Azure gratuito](https://azure.microsoft.com/free/) prima di iniziare.

* Creare [un cluster e un database](create-cluster-database-csharp.md)

* Creazione del [mapping di tabelle e colonne](net-standard-ingest-data.md#create-a-table-on-your-test-cluster)

* Impostare i [criteri di database e tabella](database-table-policies-csharp.md) (facoltativo)

* Creare un [hub delle cose con un criterio di accesso condiviso configurato](ingest-data-iot-hub.md#create-an-iot-hub).

[!INCLUDE [data-explorer-data-connection-install-nuget-csharp](../../includes/data-explorer-data-connection-install-nuget-csharp.md)]

[!INCLUDE [data-explorer-authentication](../../includes/data-explorer-authentication.md)]

## <a name="add-an-iot-hub-data-connection"></a>Aggiungere una connessione dati dell'hub Internet 

L'esempio seguente illustra come aggiungere una connessione dati dell'hub Internet a livello di codice. Vedere [connettere Azure Esplora dati table all'hub](ingest-data-iot-hub.md#connect-azure-data-explorer-table-to-iot-hub) Internet per l'aggiunta di una connessione dati dell'hub Internet con l'portale di Azure.

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
//The IoT hub that is created as part of the Prerequisites
var iotHubResourceId = "/subscriptions/xxxxxxxx-xxxxx-xxxx-xxxx-xxxxxxxxx/resourceGroups/xxxxxx/providers/Microsoft.Devices/IotHubs/xxxxxx";
var sharedAccessPolicyName = "iothubforread";
var consumerGroup = "$Default";
var location = "Central US";
//The table and column mapping are created as part of the Prerequisites
var tableName = "StormEvents";
var mappingRuleName = "StormEvents_CSV_Mapping";
var dataFormat = DataFormat.CSV;

await kustoManagementClient.DataConnections.CreateOrUpdate(resourceGroupName, clusterName, databaseName, dataConnectionName,
            new IotHubDataConnection(iotHubResourceId, consumerGroup, sharedAccessPolicyName, tableName: tableName, location: location, mappingRuleName: mappingRuleName, dataFormat: dataFormat));
```

|**Impostazione** | **Valore consigliato** | **Descrizione campo**|
|---|---|---|
| tenantId | *xxxxxxxx-xxxxx-xxxx-xxxx-xxxxxxxxx* | ID tenant. Noto anche come ID directory.|
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
| iotHubResourceId | *ID risorsa* | ID risorsa dell'hub Internet che include i dati per l'inserimento. |
| sharedAccessPolicyName | *iothubforread* | Nome dei criteri di accesso condiviso che definiscono le autorizzazioni per i dispositivi e i servizi per la connessione all'hub Internet. |
| Gruppo Consumer | *$Default* | Il gruppo di consumer dell'hub eventi.|
| location | *Stati Uniti centrali* | Percorso della risorsa di connessione dati.|

[!INCLUDE [data-explorer-data-connection-clean-resources-csharp](../../includes/data-explorer-data-connection-clean-resources-csharp.md)]
