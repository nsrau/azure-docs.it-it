---
title: Creare contenitori Cosmos di Azure con chiave di partizione di grandi dimensioniCreate Azure Cosmos containers with large partition key
description: Informazioni su come creare un contenitore in Azure Cosmos DB con chiave di partizione di grandi dimensioni usando il portale di Azure e SDK diversi.
author: markjbrown
ms.service: cosmos-db
ms.topic: conceptual
ms.date: 09/28/2019
ms.author: mjbrown
ms.openlocfilehash: 7184a6b85e93c41dfe914813301a4b1a0c88f2cd
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/27/2020
ms.locfileid: "75887683"
---
# <a name="create-containers-with-large-partition-key"></a>Creare contenitori con chiave di partizione di grandi dimensioniCreate containers with large partition key

Azure Cosmos DB usa lo schema di partizionamento basato su hash per ottenere la scalabilità orizzontale dei dati. Tutti i contenitori di Azure Cosmos creati prima del 3 maggio 2019 usano una funzione hash che calcola l'hash in base ai primi 100 byte della chiave di partizione. Se sono presenti più chiavi di partizione che hanno lo stesso primo 100 byte, tali partizioni logiche vengono considerate come la stessa partizione logica dal servizio. Ciò può causare problemi come la quota delle dimensioni della partizione non sia corretta e l'applicazione di indici univoci tra le chiavi di partizione. Chiavi di partizione di grandi dimensioni sono introdotte per risolvere questo problema. Azure Cosmos DB ora supporta chiavi di partizione di grandi dimensioni con valori fino a 2 KB.

Le chiavi di partizione di grandi dimensioni sono supportate utilizzando la funzionalità di una versione avanzata della funzione hash, che può generare un hash univoco da chiavi di partizione di grandi dimensioni fino a 2 KB. Questa versione hash è consigliata anche per gli scenari con cardinalità chiave di partizione elevata indipendentemente dalle dimensioni della chiave di partizione. Una cardinalità della chiave di partizione è definita come il numero di partizioni logiche univoche, ad esempio nell'ordine di 30000 partizioni logiche in un contenitore. Questo articolo descrive come creare un contenitore con una chiave di partizione di grandi dimensioni usando il portale di Azure e SDK diversi.

## <a name="create-a-large-partition-key-azure-portal"></a>Creare una chiave di partizione di grandi dimensioni (portale di Azure)Create a large partition key (Azure portal)

Per creare una chiave di partizione di grandi dimensioni, quando si crea un nuovo contenitore usando il portale di Azure, verificare che l'opzione La chiave di partizione personale **sia maggiore di 100 byte.** Deselezionare la casella di controllo se non sono necessarie chiavi di partizione di grandi dimensioni o se sono in esecuzione applicazioni in versione SDK precedente alla 1.18.

![Creare chiavi di partizione di grandi dimensioni usando il portale di AzureCreate large partition keys using Azure portal](./media/large-partition-keys/large-partition-key-with-portal.png)

## <a name="create-a-large-partition-key-powershell"></a>Creare una chiave di partizione di grandi dimensioni (PowerShell)Create a large partition key (PowerShell)

Per creare un contenitore con supporto della chiave di partizione di grandi dimensioni, vedere

* [Creare un contenitore Cosmos di Azure con una dimensione di chiave di partizione di grandi dimensioniCreate an Azure Cosmos container with a large partition key size](manage-with-powershell.md#create-container-big-pk)

## <a name="create-a-large-partition-key-net-sdk"></a>Creare una chiave di partizione di grandi dimensioni (SDK di .Net)Create a large partition key (.Net SDK)

Per creare un contenitore con una chiave di `PartitionKeyDefinitionVersion.V2` partizione di grandi dimensioni usando .NET SDK, specificare la proprietà . The following example shows how to specify the Version property within the PartitionKeyDefinition object and set it to PartitionKeyDefinitionVersion.V2.

### <a name="v3-net-sdk"></a>SDK di .NET v3

```csharp
await database.CreateContainerAsync(
    new ContainerProperties(collectionName, $"/longpartitionkey")
    {
        PartitionKeyDefinitionVersion = PartitionKeyDefinitionVersion.V2,
    })
```

### <a name="v2-net-sdk"></a>SDK di .NET v2

```csharp
DocumentCollection collection = await newClient.CreateDocumentCollectionAsync(
database,
     new DocumentCollection
        {
           Id = Guid.NewGuid().ToString(),
           PartitionKey = new PartitionKeyDefinition
           {
             Paths = new Collection<string> {"/longpartitionkey" },
             Version = PartitionKeyDefinitionVersion.V2
           }
         },
      new RequestOptions { OfferThroughput = 400 });
```

## <a name="supported-sdk-versions"></a>Versioni di SDK supportate

Le chiavi di partizione grande sono supportate con le seguenti versioni minime degli SDK:

|Tipo di SDK  | Versione minima   |
|---------|---------|
|.Net     |    1.18     |
|Sincronizzazione Java     |   2.4.0      |
|Java Async   |  2.5.0        |
| API REST | versione superiore `2017-05-03` rispetto `x-ms-version` all'intestazione della richiesta.|
| Modello di Resource Manager | versione 2 utilizzando `"version":2` la `partitionKey` proprietà all'interno dell'oggetto. |

Attualmente, non è possibile usare contenitori con chiave di partizione di grandi dimensioni in Power BI e app per la logica di Azure.Currently, you cannot use containers with large partition key within in Power BI and Azure Logic Apps. È possibile usare i contenitori senza una chiave di partizione di grandi dimensioni da queste applicazioni.

## <a name="next-steps"></a>Passaggi successivi

* [Partizionamento in Azure Cosmos DB](partitioning-overview.md)
* [Unità di richiesta in Azure Cosmos DBRequest Units in Azure Cosmos DB](request-units.md)
* [Effettuare il provisioning della velocità effettiva per contenitori e database](set-throughput.md)
* [Usare l'account Azure Cosmos](account-overview.md)
