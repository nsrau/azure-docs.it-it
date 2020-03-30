---
title: 'Creare una connessione dati di Griglia di eventi per Esplora dati di Azure usando CCreate an Event Grid data connection for Azure Data Explorer by using C #'
description: In questo articolo viene illustrato come creare una connessione dati di Griglia di eventi per Esplora dati di Azure usando C.In this article, you learn how to create an Event Grid data connection for Azure Data Explorer by using C'è.
author: lucygoldbergmicrosoft
ms.author: lugoldbe
ms.reviewer: orspodek
ms.service: data-explorer
ms.topic: conceptual
ms.date: 10/07/2019
ms.openlocfilehash: 03963f60cc364dd36ad55c0a28e92e3b585bb38d
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/28/2020
ms.locfileid: "78255069"
---
# <a name="create-an-event-grid-data-connection-for-azure-data-explorer-by-using-c"></a>Creare una connessione dati di Griglia di eventi per Esplora dati di Azure usando CCreate an Event Grid data connection for Azure Data Explorer by using C #

> [!div class="op_single_selector"]
> * [Portale](ingest-data-event-grid.md)
> * [C #](data-connection-event-grid-csharp.md)
> * [Python](data-connection-event-grid-python.md)
> * [Modello di Azure Resource ManagerAzure Resource Manager template](data-connection-event-grid-resource-manager.md)


Esplora dati di Azure è un servizio di esplorazione dati rapido e a scalabilità elevata per dati di log e di telemetria. Azure Data Explorer offre l'inserimento (caricamento dei dati) da hub eventi, hub IoT e BLOB scritti in contenitori BLOB. In questo articolo si crea una connessione dati griglia di eventi per Esplora dati di Azure usando C .

## <a name="prerequisites"></a>Prerequisiti

* Se Visual Studio 2019 non è installato, è possibile scaricare e usare l'edizione **gratuita** [Visual Studio 2019 Community Edition](https://www.visualstudio.com/downloads/). Assicurarsi di abilitare lo sviluppo di Azure durante l'installazione di Visual Studio.Make sure that you enable **Azure development** during the Visual Studio setup.
* Se non si ha una sottoscrizione di Azure, creare un [account Azure gratuito](https://azure.microsoft.com/free/) prima di iniziare.
* Creare [un cluster e un databaseCreate a cluster and database](create-cluster-database-csharp.md)
* Creare il [mapping di tabelle e colonne](net-standard-ingest-data.md#create-a-table-on-your-test-cluster)
* Impostare [i criteri di database e tabella](database-table-policies-csharp.md) (facoltativo)Set database and table policies (optional)
* Creare un account di archiviazione con una [sottoscrizione griglia di eventi.](ingest-data-event-grid.md#create-an-event-grid-subscription-in-your-storage-account)

[!INCLUDE [data-explorer-data-connection-install-nuget-csharp](../../includes/data-explorer-data-connection-install-nuget-csharp.md)]

[!INCLUDE [data-explorer-authentication](../../includes/data-explorer-authentication.md)]

## <a name="add-an-event-grid-data-connection"></a>Aggiungere una connessione dati alla griglia di eventiAdd an Event Grid data connection

Nell'esempio seguente viene illustrato come aggiungere una connessione dati griglia di eventi a livello di codice. Vedere Creare una connessione dati griglia di eventi in Esplora dati di Azure per aggiungere una connessione dati griglia di eventi tramite il portale di Azure.See create an [Event Grid data connection in Azure Data Explorer](ingest-data-event-grid.md#create-an-event-grid-data-connection-in-azure-data-explorer) for adding an Event Grid data connection using the Azure portal.

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
| EventHubResourceId | *ID risorsa* | ID risorsa dell'hub eventi in cui la griglia di eventi è configurata per l'invio di eventi. |
| storageAccountResourceId (informazioni in questo caso) | *ID risorsa* | ID risorsa dell'account di archiviazione che contiene i dati per l'inserimento. |
| consumerGroup | *$Default* | Gruppo di consumer dell'Hub eventi.|
| posizione | *Stati Uniti centrali* | Percorso della risorsa di connessione dati.|

## <a name="generate-sample-data"></a>Generare i dati di esempio

Dopo aver connesso Esplora dati di Azure e l'account di archiviazione, è possibile creare i dati di esempio e caricarli nella risorsa di archiviazione BLOB.

Questo script crea un nuovo contenitore nell'account di archiviazione, carica un file esistente (come BLOB) in tale contenitore e quindi elenca gli oggetti BLOB nel contenitore.

```csharp
var azureStorageAccountConnectionString=<storage_account_connection_string>;

var containerName=<container_name>;
var blobName=<blob_name>;
var localFileName=<file_to_upload>;

// Creating the container
var azureStorageAccount = CloudStorageAccount.Parse(azureStorageAccountConnectionString);
var blobClient = azureStorageAccount.CreateCloudBlobClient();
var container = blobClient.GetContainerReference(containerName);
container.CreateIfNotExists();

// Set metadata and upload file to blob
var blob = container.GetBlockBlobReference(blobName);
blob.Metadata.Add("rawSizeBytes", "4096‬"); // the uncompressed size is 4096 bytes
blob.Metadata.Add("kustoIngestionMappingReference", "mapping_v2‬");
blob.UploadFromFile(localFileName);

// List blobs
var blobs = container.ListBlobs();
```

> [!NOTE]
> Azure Data Explorer non eliminerà i BLOB dopo l'inserimento. Conservare i BLOB per tre o cinque giorni usando il ciclo di vita [dell'archiviazione BLOB](https://docs.microsoft.com/azure/storage/blobs/storage-lifecycle-management-concepts?tabs=azure-portal) di Azure per gestire l'eliminazione dei BLOB.

[!INCLUDE [data-explorer-data-connection-clean-resources-csharp](../../includes/data-explorer-data-connection-clean-resources-csharp.md)]
