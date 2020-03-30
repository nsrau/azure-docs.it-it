---
title: "Creare una connessione dati dell'hub IoT per Azure Data Explorer usando CCreate an IoT Hub data connection for Azure Data Explorer by using C #"
description: In questo articolo viene illustrato come creare una connessione dati all'hub IoT per Azure Data Explorer usando C .
author: lucygoldbergmicrosoft
ms.author: lugoldbe
ms.reviewer: orspodek
ms.service: data-explorer
ms.topic: conceptual
ms.date: 10/07/2019
ms.openlocfilehash: 0cac03e50bf46910f8430b745803107b60905769
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/27/2020
ms.locfileid: "74667375"
---
# <a name="create-an-iot-hub-data-connection-for-azure-data-explorer-by-using-c-preview"></a>Creare una connessione dati dell'hub IoT per Esplora dati di Azure usando C 'Preview

> [!div class="op_single_selector"]
> * [Portale](ingest-data-iot-hub.md)
> * [C #](data-connection-iot-hub-csharp.md)
> * [Python](data-connection-iot-hub-python.md)
> * [Modello di Azure Resource ManagerAzure Resource Manager template](data-connection-iot-hub-resource-manager.md)

Esplora dati di Azure è un servizio di esplorazione dati rapido e a scalabilità elevata per dati di log e di telemetria. Azure Data Explorer offre l'inserimento (caricamento dei dati) da hub eventi, hub IoT e BLOB scritti in contenitori BLOB. In questo articolo si crea una connessione dati dell'hub IoT per Azure Data Explorer usando C .

## <a name="prerequisites"></a>Prerequisiti

* Se Visual Studio 2019 non è installato, è possibile scaricare e usare l'edizione **gratuita** [Visual Studio 2019 Community Edition](https://www.visualstudio.com/downloads/). Assicurarsi di abilitare lo sviluppo di Azure durante l'installazione di Visual Studio.Make sure that you enable **Azure development** during the Visual Studio setup.
* Se non si ha una sottoscrizione di Azure, creare un [account Azure gratuito](https://azure.microsoft.com/free/) prima di iniziare.
* Creare [un cluster e un databaseCreate a cluster and database](create-cluster-database-csharp.md)
* Creare il [mapping di tabelle e colonne](net-standard-ingest-data.md#create-a-table-on-your-test-cluster)
* Impostare [i criteri di database e tabella](database-table-policies-csharp.md) (facoltativo)Set database and table policies (optional)
* Creare un [hub IoT con un criterio](ingest-data-iot-hub.md#create-an-iot-hub)di accesso condiviso configurato.

[!INCLUDE [data-explorer-data-connection-install-nuget-csharp](../../includes/data-explorer-data-connection-install-nuget-csharp.md)]

[!INCLUDE [data-explorer-authentication](../../includes/data-explorer-authentication.md)]

## <a name="add-an-iot-hub-data-connection"></a>Aggiungere una connessione dati all'hub IoTAdd an IoT Hub data connection 

Nell'esempio seguente viene illustrato come aggiungere una connessione dati all'hub IoT a livello di codice. Vedere Connettere la tabella di [Azure Data Explorer all'hub IoT](ingest-data-iot-hub.md#connect-azure-data-explorer-table-to-iot-hub) per aggiungere una connessione dati dell'hub Iot usando il portale di Azure.See connect Azure Data Explorer table to IoT Hub for adding an Iot Hub data connection using the Azure portal.

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
| TenantId | *xxxxxxxx-xxxxx-xxxx-xxxx-xxxxxxxxx* | ID tenant. Noto anche come ID directory.|
| subscriptionId | *xxxxxxxx-xxxxx-xxxx-xxxx-xxxxxxxxx* | ID sottoscrizione utilizzato per la creazione delle risorse.|
| clientId | *xxxxxxxx-xxxxx-xxxx-xxxx-xxxxxxxxx* | ID client dell'applicazione che può accedere alle risorse nel tenant.|
| clientSecret | *xxxxxxxxxxxxxx* | Segreto client dell'applicazione che può accedere alle risorse nel tenant. |
| resourceGroupName | *testrg* | Nome del gruppo di risorse contenente il cluster.|
| clusterName | *mykustocluster* | Nome del cluster.|
| databaseName | *mykustodatabase* | Nome del database di destinazione nel cluster.|
| DataConnectionName (Nome connessione dati) | *myeventhubconnect* | Il nome desiderato della connessione dati.|
| tableName | *Eventi Storm* | Nome della tabella di destinazione nel database di destinazione.|
| mappingRuleName | *StormEvents_CSV_Mapping* | Nome del mapping di colonna correlato alla tabella di destinazione.|
| Dataformat | *Csv* | Formato dati del messaggio.|
| iotHubResourceId | *ID risorsa* | ID risorsa dell'hub IoT che contiene i dati per l'inserimento. |
| sharedAccessPolicyName (nome di criterisharedAccessPolicyName) | *iothubforread* | Nome dei criteri di accesso condiviso che definisce le autorizzazioni per la connessione di dispositivi e servizi all'hub IoT. |
| consumerGroup | *$Default* | Gruppo di consumer dell'hub eventi.|
| posizione | *Stati Uniti centrali* | Percorso della risorsa di connessione dati.|

[!INCLUDE [data-explorer-data-connection-clean-resources-csharp](../../includes/data-explorer-data-connection-clean-resources-csharp.md)]
