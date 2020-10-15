---
title: "Esercitazione: Esercitazione sulla distribuzione globale in Azure Cosmos DB per l'API SQL"
description: "Esercitazione: Informazioni su come configurare la distribuzione globale di Azure Cosmos DB usando l'API SQL con .NET, Java, Python e vari altri SDK"
author: markjbrown
ms.author: mjbrown
ms.service: cosmos-db
ms.topic: tutorial
ms.date: 11/05/2019
ms.reviewer: sngun
ms.custom: devx-track-python, devx-track-js, devx-track-csharp
ms.openlocfilehash: ebb6bdf6e5072d1024ba7abcd1db1cf7bebfcd3d
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 10/09/2020
ms.locfileid: "91334379"
---
# <a name="tutorial-set-up-azure-cosmos-db-global-distribution-using-the-sql-api"></a>Esercitazione: Configurare la distribuzione globale in Azure Cosmos DB usando l'API SQL

In questo articolo viene illustrato come usare il portale di Azure per configurare la distribuzione globale di Azure Cosmos DB e quindi connettersi tramite l'API SQL.

Questo articolo illustra le attività seguenti: 

> [!div class="checklist"]
> * Configurare la distribuzione globale tramite il portale di Azure
> * Configurare la distribuzione globale tramite le [API SQL](sql-api-introduction.md)

<a id="portal"></a>
[!INCLUDE [cosmos-db-tutorial-global-distribution-portal](../../includes/cosmos-db-tutorial-global-distribution-portal.md)]

## <a name="connecting-to-a-preferred-region-using-the-sql-api"></a><a id="preferred-locations"></a> Connessione a un'area preferita tramite l'API SQL

Per sfruttare la [distribuzione globale](distribute-data-globally.md), le applicazioni client possono specificare un elenco di aree, nell'ordine preferito, da usare per eseguire operazioni sui documenti. A seconda della configurazione dell'account Azure Cosmos DB, della disponibilità corrente delle aree e dell'elenco delle preferenze specificato, l'SDK SQL sceglierà l'endpoint ottimale per eseguire le operazioni di scrittura e lettura.

L'elenco delle preferenze viene specificato nella fase di inizializzazione di una connessione usando gli SDK SQL. Gli SDK accettano un parametro opzionale `PreferredLocations`, ovvero un elenco ordinato di aree di Azure.

L'SDK invia automaticamente tutte le scritture all'area di scrittura corrente. Tutte le letture verranno inviate alla prima area disponibile nell'elenco delle località preferite. Se la richiesta ha esito negativo, il client passerà all'area dell'elenco successiva.

L'SDK tenterà la lettura solo dalle aree specificate nelle località preferite. Si supponga ad esempio che l'account Azure Cosmos sia disponibile in quattro aree, ma il client specifichi solo due aree di lettura (non di scrittura) nell'elenco `PreferredLocations`.In questo caso, dall'area di lettura non specificata nell'elenco `PreferredLocations` non verrà distribuita alcuna lettura. Se le aree di letture specificate nell'elenco `PreferredLocations` non sono disponibili, le letture verranno distribuite dall'area di scrittura.

L'applicazione è in grado di verificare l'endpoint di scrittura e l'endpoint di lettura correnti scelti dall'SDK controllando due proprietà, `WriteEndpoint` e `ReadEndpoint`, disponibili nell'SDK versione 1.8 e versioni successive. Se la proprietà `PreferredLocations` non è impostata, tutte le richieste verranno distribuite dall'area di scrittura corrente.

Se non si specificano le località preferite, ma si usa il metodo `setCurrentLocation`, l'SDK specificherà automaticamente le località preferite in base all'area corrente in cui è in esecuzione il client. L'SDK ordina le aree in base alla prossimità di un'area a quella corrente.

## <a name="net-sdk"></a>.NET SDK

L'SDK può essere usato senza alcuna modifica del codice. In questo caso l'SDK reindirizza automaticamente sia le operazioni di lettura che di scrittura all'area di scrittura corrente.

Nella versione 1.8 e nelle versioni successive di .NET SDK, il parametro ConnectionPolicy per il costruttore DocumentClient dispone di una proprietà denominata Microsoft.Azure.Documents.ConnectionPolicy.PreferredLocations. Questa proprietà è di tipo raccolta `<string>` e deve contenere un elenco di nomi di aree. I valori stringa vengono formattati in base alla colonna dei nomi delle aree disponibile nella pagina [Aree di Azure][regions]. Prima del primo carattere e dopo l'ultimo carattere non viene immesso alcuno spazio.

Gli endpoint di lettura e scrittura correnti sono disponibili rispettivamente in DocumentClient.WriteEndpoint e DocumentClient.ReadEndpoint.

> [!NOTE]
> Gli URL per gli endpoint non devono essere considerati come costanti di lunga durata. Il servizio può aggiornare gli URL in qualsiasi momento. L'SDK gestisce la modifica in modo automatico.
>

# <a name="net-sdk-v2"></a>[.NET SDK V2](#tab/dotnetv2)

Se si usa .NET v2 SDK, impostare l'area preferita tramite la proprietà `PreferredLocations`.

```csharp
// Getting endpoints from application settings or other configuration location
Uri accountEndPoint = new Uri(Properties.Settings.Default.GlobalDatabaseUri);
string accountKey = Properties.Settings.Default.GlobalDatabaseKey;
  
ConnectionPolicy connectionPolicy = new ConnectionPolicy();

//Setting read region selection preference
connectionPolicy.PreferredLocations.Add(LocationNames.WestUS); // first preference
connectionPolicy.PreferredLocations.Add(LocationNames.EastUS); // second preference
connectionPolicy.PreferredLocations.Add(LocationNames.NorthEurope); // third preference

// initialize connection
DocumentClient docClient = new DocumentClient(
    accountEndPoint,
    accountKey,
    connectionPolicy);

// connect to DocDB
await docClient.OpenAsync().ConfigureAwait(false);
```

In alternativa, è possibile usare la proprietà `SetCurrentLocation` e consentire all'SDK di scegliere la località preferita in base alla prossimità.

```csharp
// Getting endpoints from application settings or other configuration location
Uri accountEndPoint = new Uri(Properties.Settings.Default.GlobalDatabaseUri);
string accountKey = Properties.Settings.Default.GlobalDatabaseKey;
  
ConnectionPolicy connectionPolicy = new ConnectionPolicy();

connectionPolicy.SetCurrentLocation("West US 2"); /

// initialize connection
DocumentClient docClient = new DocumentClient(
    accountEndPoint,
    accountKey,
    connectionPolicy);

// connect to DocDB
await docClient.OpenAsync().ConfigureAwait(false);
```

# <a name="net-sdk-v3"></a>[.NET SDK V3](#tab/dotnetv3)

Se si usa .NET V3 SDK, impostare l'area preferita tramite la proprietà `ApplicationPreferredRegions`.

```csharp

CosmosClientOptions options = new CosmosClientOptions();
options.ApplicationName = "MyApp";
options.ApplicationPreferredRegions = new List<string> {Regions.WestUS, Regions.WestUS2};

CosmosClient client = new CosmosClient(connectionString, options);

```

In alternativa, è possibile usare la proprietà `ApplicationRegion` e consentire all'SDK di scegliere la località preferita in base alla prossimità.

```csharp
CosmosClientOptions options = new CosmosClientOptions();
options.ApplicationName = "MyApp";
// If the application is running in West US
options.ApplicationRegion = Regions.WestUS;

CosmosClient client = new CosmosClient(connectionString, options);
```

---

## <a name="nodejsjavascript"></a>Node.js/JavaScript

> [!NOTE]
> Gli URL per gli endpoint non devono essere considerati come costanti di lunga durata. Il servizio può aggiornare gli URL in qualsiasi momento. L'SDK gestirà questa modifica in automatico.
>
>

Di seguito è riportato un esempio di codice per Node.js/Javascript.

```JavaScript
// Setting read region selection preference, in the following order -
// 1 - West US
// 2 - East US
// 3 - North Europe
const preferredLocations = ['West US', 'East US', 'North Europe'];

// initialize the connection
const client = new CosmosClient{ endpoint, key, connectionPolicy: { preferredLocations } });
```

## <a name="python-sdk"></a>Python SDK

Il codice seguente illustra come impostare le località preferite usando Python SDK:

```python
connectionPolicy = documents.ConnectionPolicy()
connectionPolicy.PreferredLocations = ['West US', 'East US', 'North Europe']
client = cosmos_client.CosmosClient(ENDPOINT, {'masterKey': MASTER_KEY}, connectionPolicy)

```

## <a name="java-v4-sdk"></a><a id="java4-preferred-locations"></a> Java V4 SDK

Il codice seguente illustra come impostare le località preferite usando Java SDK:

# <a name="async"></a>[Asincrona](#tab/api-async)

   API asincrona [Java SDK V4](sql-api-sdk-java-v4.md) (Maven [com.azure::azure-cosmos](https://mvnrepository.com/artifact/com.azure/azure-cosmos))

   [!code-java[](~/azure-cosmos-java-sql-api-samples/src/main/java/com/azure/cosmos/examples/documentationsnippets/async/SampleDocumentationSnippetsAsync.java?name=TutorialGlobalDistributionPreferredLocationAsync)]

# <a name="sync"></a>[Sincronizza](#tab/api-sync)

   API sincrona [Java SDK V4](sql-api-sdk-java-v4.md) (Maven [com.azure::azure-cosmos](https://mvnrepository.com/artifact/com.azure/azure-cosmos))

   [!code-java[](~/azure-cosmos-java-sql-api-samples/src/main/java/com/azure/cosmos/examples/documentationsnippets/sync/SampleDocumentationSnippets.java?name=TutorialGlobalDistributionPreferredLocationSync)]

--- 

## <a name="rest"></a>REST

Dopo aver reso disponibile un account di database in più aree, i client possono eseguire query sulla relativa disponibilità tramite una richiesta GET all'URI seguente `https://{databaseaccount}.documents.azure.com/`

Il servizio restituirà un elenco delle aree e i relativi URI dell'endpoint Azure Cosmos DB per le repliche. L'area di scrittura corrente verrà indicata nella risposta. Il client può quindi selezionare l'endpoint appropriato per tutte le altre richieste dell'API REST come indicato di seguito.

Risposta di esempio

```json
{
    "_dbs": "//dbs/",
    "media": "//media/",
    "writableLocations": [
        {
            "Name": "West US",
            "DatabaseAccountEndpoint": "https://globaldbexample-westus.documents.azure.com:443/"
        }
    ],
    "readableLocations": [
        {
            "Name": "East US",
            "DatabaseAccountEndpoint": "https://globaldbexample-eastus.documents.azure.com:443/"
        }
    ],
    "MaxMediaStorageUsageInMB": 2048,
    "MediaStorageUsageInMB": 0,
    "ConsistencyPolicy": {
        "defaultConsistencyLevel": "Session",
        "maxStalenessPrefix": 100,
        "maxIntervalInSeconds": 5
    },
    "addresses": "//addresses/",
    "id": "globaldbexample",
    "_rid": "globaldbexample.documents.azure.com",
    "_self": "",
    "_ts": 0,
    "_etag": null
}
```

* Tutte le richieste PUT, POST e DELETE devono passare all'URI di scrittura indicato.
* Tutte le richieste di sola lettura e GET, ad esempio le query, possono essere indirizzate a qualsiasi endpoint scelto dal client

Le richieste di scrittura per le aree di sola lettura avranno esito negativo con codice di errore HTTP 403 ("Non consentito").

Se l'area di scrittura viene modificata dopo la fase di individuazione iniziale del client, le scritture successive sull'area di scrittura precedente avranno esito negativo con codice errore HTTP 403 ("Non consentito"). Il client deve quindi OTTENERE nuovamente l'elenco delle aree per aggiornare l'area di scrittura.

L'esercitazione è terminata. Per informazioni su come gestire la coerenza dell'account con replica globale, vedere [Livelli di coerenza in Azure Cosmos DB](consistency-levels.md). Per informazioni sul funzionamento della replica di database globale in Azure Cosmos DB, vedere [Distribuire i dati a livello globale con Azure Cosmos DB](distribute-data-globally.md).

## <a name="next-steps"></a>Passaggi successivi

In questa esercitazione sono state eseguite le operazioni seguenti:

> [!div class="checklist"]
> * Configurare la distribuzione globale tramite il portale di Azure
> * Configurare la distribuzione globale tramite le API SQL

È ora possibile passare all'esercitazione successiva per imparare a sviluppare in locale usando l'emulatore locale di Azure Cosmos DB.

> [!div class="nextstepaction"]
> [Sviluppare in locale con l'emulatore](local-emulator.md)

[regions]: https://azure.microsoft.com/regions/

