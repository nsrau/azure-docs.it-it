---
title: Come implementare la sincronizzazione personalizzata per ottimizzare l'ambiente per disponibilità e prestazioni più elevate in Azure Cosmos DB
description: Informazioni su come implementare la sincronizzazione personalizzata per ottimizzare l'ambiente per disponibilità e prestazioni più elevate in Azure Cosmos DB
author: markjbrown
ms.service: cosmos-db
ms.topic: sample
ms.date: 2/12/2019
ms.author: mjbrown
ms.openlocfilehash: 43cb73784806358bccb9758be2923d3df5e9badd
ms.sourcegitcommit: 79038221c1d2172c0677e25a1e479e04f470c567
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 02/19/2019
ms.locfileid: "56414882"
---
# <a name="how-to-implement-custom-synchronization-to-optimize-for-higher-availability-and-performance"></a>Come implementare la sincronizzazione personalizzata per ottimizzare l'ambiente per disponibilità e prestazioni più elevate

Azure Cosmos DB offre cinque livelli di coerenza ben definiti tra cui scegliere per bilanciare il compromesso tra coerenza, prestazioni e disponibilità. La coerenza assoluta assicura che i dati vengano replicati in modo sincrono e resi persistenti in modo durevole in ogni area in cui è disponibile l'account Azure Cosmos. Pur garantendo il massimo livello di durabilità, questa configurazione ha un costo in termini di prestazioni e disponibilità. Se si vuole controllare/diminuire la durabilità dei dati per soddisfare i requisiti di un'applicazione senza compromettere la disponibilità, è possibile usare la sincronizzazione personalizzata al livello di applicazione per ottenere il livello desiderato di durabilità.

Il diagramma seguente mostra visivamente il modello di sincronizzazione personalizzata.

![Sincronizzazione personalizzata](./media/how-to-custom-synchronization/custom-synchronization.png)

In questo scenario un contenitore Azure Cosmos viene replicato a livello globale tra più aree e in più continenti. In questo scenario l'uso della coerenza assoluta per tutte le aree influirebbe sulle prestazioni. Per assicurare un livello più elevato di durabilità dei dati senza compromettere la latenza in scrittura, l'applicazione può usare due client che condividono lo stesso token di sessione.

Il primo client può scrivere i dati nell'area locale, ad esempio Stati Uniti occidentali. Il secondo client, ad esempio negli Stati Uniti orientali, è un client di lettura usato per assicurare la sincronizzazione. Passando il token di sessione dalla risposta in scrittura alla lettura successiva, il client di lettura assicurerà la sincronizzazione delle scritture negli Stati Uniti orientali. Azure Cosmos DB assicurerà che le scritture vengano viste da almeno un'area e che ne sia garantita la sopravvivenza in caso di interruzione locale, se l'area di scrittura originale diventasse non disponibile. In questo scenario ogni scrittura viene sincronizzata con gli Stati Uniti orientali, riducendo la latenza derivante dall'uso della coerenza assoluta in tutte le aree. In uno scenario multimaster, in cui le scritture si verificano in ogni area, questo modello può essere esteso per eseguire la sincronizzazione con più aree in parallelo.

## <a name="configure-the-clients"></a>Configurare i client

L'esempio seguente mostra il livello di accesso ai dati che crea un'istanza di due client per questo scopo.

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

## <a name="implement-custom-synchronization"></a>Implementare la sincronizzazione personalizzata

Dopo l'inizializzazione dei client, l'applicazione può eseguire scritture nell'area locale (Stati Uniti occidentali) e forzarne la sincronizzazione con gli Stati Uniti orientali come segue.

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

Questo modello può essere esteso per eseguire la sincronizzazione con più aree in parallelo.

## <a name="next-steps"></a>Passaggi successivi

Per altre informazioni sulla distribuzione globale e sulla coerenza in Azure Cosmo DB, vedere gli articoli seguenti:

* [Scelta del livello di coerenza ottimale in Azure Cosmos DB](consistency-levels-choosing.md)

* [Compromessi tra coerenza, disponibilità e prestazioni in Azure Cosmos DB](consistency-levels-tradeoffs.md)

* [Come gestire la coerenza in Azure Cosmos DB](how-to-manage-consistency.md)

* [Partizionamento e distribuzione dei dati in Azure Cosmos DB](partition-data.md)
