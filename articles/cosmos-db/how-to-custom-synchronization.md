---
title: Come implementare la sincronizzazione personalizzata per ottimizzare l'ambiente per disponibilità e prestazioni più elevate in Azure Cosmos DB
description: Informazioni su come implementare la sincronizzazione personalizzata per ottimizzare l'ambiente per disponibilità e prestazioni più elevate in Azure Cosmos DB.
author: markjbrown
ms.author: mjbrown
ms.service: cosmos-db
ms.topic: conceptual
ms.date: 05/23/2019
ms.openlocfilehash: 1fdd05f8a4757a49414a2a03c8f991a80186ed44
ms.sourcegitcommit: 8074f482fcd1f61442b3b8101f153adb52cf35c9
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 10/22/2019
ms.locfileid: "72755051"
---
# <a name="implement-custom-synchronization-to-optimize-for-higher-availability-and-performance"></a>Implementare la sincronizzazione personalizzata per ottimizzare l'ambiente per disponibilità e prestazioni più elevate

Azure Cosmos DB offre [cinque livelli di coerenza ben definiti](consistency-levels.md) tra cui scegliere per bilanciare il compromesso tra coerenza, prestazioni e disponibilità. La coerenza assoluta consente di assicurarsi che i dati vengano replicati in modo sincrono e resi persistenti in modo durevole in ogni area in cui è disponibile l'account Azure Cosmos. Questa configurazione offre il livello di durabilità più elevato, ma a discapito delle prestazioni e della disponibilità. Se si desidera controllare o diminuire la durabilità dei dati per soddisfare i requisiti dell'applicazione senza compromettere la disponibilità, è possibile usare la *sincronizzazione personalizzata* al livello dell'applicazione per ottenere il livello desiderato di durabilità.

L'immagine seguente mostra visivamente il modello di sincronizzazione personalizzata:

![Sincronizzazione personalizzata](./media/how-to-custom-synchronization/custom-synchronization.png)

In questo scenario, un contenitore Azure Cosmos viene replicato a livello globale su più aree e in più continenti. In questo scenario, l'uso della coerenza assoluta per tutte le aree influisce sulle prestazioni. Per assicurare un livello più elevato di durabilità dei dati senza compromettere la latenza in scrittura, l'applicazione può usare due client che condividono lo stesso [token di sessione](how-to-manage-consistency.md#utilize-session-tokens).

Il primo client può scrivere i dati nell'area locale, ad esempio Stati Uniti occidentali. Il secondo client, ad esempio negli Stati Uniti orientali, è un client di lettura usato per assicurare la sincronizzazione. Passando il token di sessione dalla risposta in scrittura alla lettura successiva, il client di lettura assicura la sincronizzazione delle scritture negli Stati Uniti orientali. Azure Cosmos DB garantisce che le scritture siano visibili per almeno un'area. Le scritture saranno conservate anche dopo un'interruzione, se l'area di scrittura originale diventa inattiva. In questo scenario, ogni scrittura viene sincronizzata con gli Stati Uniti orientali, riducendo la latenza derivante dall'uso della coerenza assoluta in tutte le aree. In uno scenario multimaster, in cui le scritture si verificano in ogni area, questo modello può essere esteso per eseguire la sincronizzazione con più aree in parallelo.

## <a name="configure-the-clients"></a>Configurare i client

L'esempio seguente mostra un livello di accesso ai dati che crea un'istanza di due client per la sincronizzazione personalizzata:

### <a name="net-v2-sdk"></a>.NET V2 SDK
```csharp
class MyDataAccessLayer
{
    private DocumentClient writeClient;
    private DocumentClient readClient;

    public async Task InitializeAsync(Uri accountEndpoint, string key)
    {
        ConnectionPolicy writeConnectionPolicy = new ConnectionPolicy
        {
            ConnectionMode = ConnectionMode.Direct,
            ConnectionProtocol = Protocol.Tcp,
            UseMultipleWriteLocations = true
        };
        writeConnectionPolicy.SetCurrentLocation(LocationNames.WestUS);

        ConnectionPolicy readConnectionPolicy = new ConnectionPolicy
        {
            ConnectionMode = ConnectionMode.Direct,
            ConnectionProtocol = Protocol.Tcp
        };
        readConnectionPolicy.SetCurrentLocation(LocationNames.EastUS);

        writeClient = new DocumentClient(accountEndpoint, key, writeConnectionPolicy);
        readClient = new DocumentClient(accountEndpoint, key, readConnectionPolicy, ConsistencyLevel.Session);

        await Task.WhenAll(new Task[]
        {
            writeClient.OpenAsync(),
            readClient.OpenAsync()
        });
    }
}
```

### <a name="net-v3-sdk"></a>.Net V3 SDK
```csharp
class MyDataAccessLayer
{
    private CosmosClient writeClient;
    private CosmosClient readClient;

    public void InitializeAsync(string accountEndpoint, string key)
    {
        CosmosClientOptions writeConnectionOptions = new CosmosClientOptions();
        writeConnectionOptions.ConnectionMode = ConnectionMode.Direct;
        writeConnectionOptions.ApplicationRegion = "West US";

        CosmosClientOptions readConnectionOptions = new CosmosClientOptions();
        readConnectionOptions.ConnectionMode = ConnectionMode.Direct;
        readConnectionOptions.ApplicationRegion = "East US";


        writeClient = new CosmosClient(accountEndpoint, key, writeConnectionOptions);
        readClient = new CosmosClient(accountEndpoint, key, readConnectionOptions);
    }
}
```

## <a name="implement-custom-synchronization"></a>Implementare la sincronizzazione personalizzata

Dopo l'inizializzazione dei client, l'applicazione può eseguire scritture nell'area locale (Stati Uniti occidentali) e forzarne la sincronizzazione con gli Stati Uniti orientali come segue.

### <a name="net-v2-sdk"></a>.NET V2 SDK
```csharp
class MyDataAccessLayer
{
    public async Task CreateItem(string containerLink, Document document)
    {
        ResourceResponse<Document> response = await writeClient.CreateDocumentAsync(
            containerLink, document);

        await readClient.ReadDocumentAsync(response.Resource.SelfLink,
            new RequestOptions { SessionToken = response.SessionToken });
    }
}
```

### <a name="net-v3-sdk"></a>.Net V3 SDK
```csharp
class MyDataAccessLayer
{
     public async Task CreateItem(string databaseId, string containerId, dynamic item)
     {
        ItemResponse<dynamic> response = await writeClient.GetContainer("containerId", "databaseId")
            .CreateItemAsync<dynamic>(
                item,
                new PartitionKey("test"));

        await readClient.GetContainer("containerId", "databaseId").ReadItemAsync<dynamic>(
            response.Resource.id,
            new PartitionKey("test"),
            new ItemRequestOptions { SessionToken = response.Headers.Session, ConsistencyLevel = ConsistencyLevel.Session });
    }
}
```

È possibile estendere il modello per eseguire la sincronizzazione con più aree in parallelo.

## <a name="next-steps"></a>Passaggi successivi

Per altre informazioni sulla distribuzione globale e sulla coerenza in Azure Cosmos DB, vedere gli articoli seguenti:

* [Scegliere il livello di coerenza ottimale in Azure Cosmos DB](consistency-levels-choosing.md)
* [Compromessi tra coerenza, disponibilità e prestazioni in Azure Cosmos DB](consistency-levels-tradeoffs.md)
* [Gestire la coerenza in Azure Cosmos DB](how-to-manage-consistency.md)
* [Partizionamento e distribuzione dei dati in Azure Cosmos DB](partition-data.md)
