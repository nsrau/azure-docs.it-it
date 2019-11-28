---
title: "Esercitazione: Esercitazione sulla distribuzione globale in Azure Cosmos DB per l'API SQL"
description: "Esercitazione: Informazioni su come configurare la distribuzione globale in Azure Cosmos DB usando l'API SQL."
author: markjbrown
ms.author: mjbrown
ms.service: cosmos-db
ms.topic: tutorial
ms.date: 11/05/2019
ms.reviewer: sngun
ms.openlocfilehash: 7c7ea4ca8fc28ef47e60d5010c804223c3cfab3b
ms.sourcegitcommit: 4821b7b644d251593e211b150fcafa430c1accf0
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 11/19/2019
ms.locfileid: "74173844"
---
# <a name="tutorial-set-up-azure-cosmos-db-global-distribution-using-the-sql-api"></a>Esercitazione: Configurare la distribuzione globale in Azure Cosmos DB usando l'API SQL

In questo articolo viene illustrato come usare il portale di Azure per configurare la distribuzione globale di Azure Cosmos DB e quindi connettersi tramite l'API SQL.

Questo articolo illustra le attività seguenti: 

> [!div class="checklist"]
> * Configurare la distribuzione globale tramite il portale di Azure
> * Configurare la distribuzione globale tramite le [API SQL](sql-api-introduction.md)

<a id="portal"></a>
[!INCLUDE [cosmos-db-tutorial-global-distribution-portal](../../includes/cosmos-db-tutorial-global-distribution-portal.md)]


## <a name="connecting-to-a-preferred-region-using-the-sql-api"></a>Connessione a un'area preferita tramite l'API SQL

Per sfruttare la [distribuzione globale](distribute-data-globally.md), le applicazioni client possono specificare un elenco di aree, nell'ordine preferito, da usare per eseguire operazioni sui documenti. Questa operazione può essere eseguita impostando il criterio di connessione. A seconda della configurazione dell'account Azure Cosmos DB, della disponibilità corrente delle aree e dell'elenco delle preferenze specificato, l'SDK SQL sceglierà l'endpoint ottimale per eseguire le operazioni di scrittura e lettura.

L'elenco delle preferenze viene specificato nella fase di inizializzazione di una connessione usando gli SDK SQL. Gli SDK accettano il parametro facoltativo "PreferredLocations", ovvero un elenco ordinato di aree di Azure.

L'SDK invia automaticamente tutte le scritture all'area di scrittura corrente.

Tutte le letture verranno inviate alla prima area disponibile nell'elenco PreferredLocations. Se la richiesta ha esito negativo, il client trasferisce l'elenco all'area successiva e così via.

Gli SDK tenteranno di leggere solo dalle aree specificate nell'elenco PreferredLocations. Perciò, se l'account di database è disponibile, ad esempio, in quattro aree, ma il client specifica solo due aree di lettura (non scrittura) per PreferredLocations, le letture non verranno distribuite dall'area di lettura che non è specificata in PreferredLocations. Se le aree di lettura specificate in PreferredLocations non sono disponibili, le operazioni di lettura verranno distribuite dalle area di scrittura.

L'applicazione può verificare l'endpoint di scrittura e lettura corrente scelto dall'SDK controllando due proprietà, WriteEndpoint e ReadEndpoint, disponibili nella versione dell'SDK 1.8 e nelle versioni successive.

Se la proprietà PreferredLocations non è impostata, tutte le richieste verranno distribuite dall'area di scrittura corrente.

## <a name="net-sdk"></a>.NET SDK
L'SDK può essere usato senza alcuna modifica del codice. In questo caso l'SDK reindirizza automaticamente sia le operazioni di lettura che di scrittura all'area di scrittura corrente.

Nella versione 1.8 e nelle versioni successive di .NET SDK, il parametro ConnectionPolicy per il costruttore DocumentClient dispone di una proprietà denominata Microsoft.Azure.Documents.ConnectionPolicy.PreferredLocations. Questa proprietà è di tipo raccolta `<string>` e deve contenere un elenco di nomi di aree. I valori di stringa vengono formattati in base alla colonna Nome dell'area nella pagina [Aree di Azure][regions], senza spazi prima o dopo il primo e l'ultimo carattere.

Gli endpoint di lettura e scrittura correnti sono disponibili rispettivamente in DocumentClient.WriteEndpoint e DocumentClient.ReadEndpoint.

> [!NOTE]
> Gli URL per gli endpoint non devono essere considerati come costanti di lunga durata. Il servizio può aggiornare gli URL in qualsiasi momento. L'SDK gestisce la modifica in modo automatico.
>
>

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

## <a name="java-v2-sdk"></a>Java V2 SDK

Il codice seguente illustra come impostare le località preferite usando Java SDK:

```java
ConnectionPolicy policy = new ConnectionPolicy();
policy.setUsingMultipleWriteLocations(true);
policy.setPreferredLocations(Arrays.asList("East US", "West US", "Canada Central"));
AsyncDocumentClient client =
        new AsyncDocumentClient.Builder()
                .withMasterKeyOrResourceToken(this.accountKey)
                .withServiceEndpoint(this.accountEndpoint)
                .withConnectionPolicy(policy)
                .build();
```

## <a name="rest"></a>REST
Dopo aver reso disponibile un account di database in più aree, i client possono eseguire query sulla relativa disponibilità tramite una richiesta GET all'URI seguente.

    https://{databaseaccount}.documents.azure.com/

Il servizio restituirà un elenco delle aree e i relativi URI dell'endpoint Azure Cosmos DB per le repliche. L'area di scrittura corrente verrà indicata nella risposta. Il client può quindi selezionare l'endpoint appropriato per tutte le altre richieste dell'API REST come indicato di seguito.

Risposta di esempio

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


* Tutte le richieste PUT, POST e DELETE devono passare all'URI di scrittura indicato.
* Tutte le richieste di sola lettura e GET, ad esempio le query, possono passare a qualsiasi endpoint scelto dal client.

Le richieste di scrittura per le aree di sola lettura avranno esito negativo con codice di errore HTTP 403 ("Non consentito").

Se l'area di scrittura viene modificata dopo la fase di individuazione iniziale del client, le scritture successive all'area di scrittura precedente avranno esito negativo con codice di errore HTTP 403 ("Non consentito"). Il client deve quindi OTTENERE nuovamente l'elenco delle aree per aggiornare l'area di scrittura.

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

