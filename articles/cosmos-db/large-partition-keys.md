---
title: Creare contenitori di Azure Cosmos con una chiave di partizione di grandi dimensioni usando il portale di Azure e vari SDK.
description: Informazioni su come creare un contenitore in Azure Cosmos DB con una chiave di partizione di grandi dimensioni usando portale di Azure e SDK diversi.
author: markjbrown
ms.service: cosmos-db
ms.topic: conceptual
ms.date: 09/28/2019
ms.author: mjbrown
ms.openlocfilehash: be639a67f70ab40f8d7dcc0f3793cbbd4a2ec4a3
ms.sourcegitcommit: 77bfc067c8cdc856f0ee4bfde9f84437c73a6141
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 10/16/2019
ms.locfileid: "72436803"
---
# <a name="create-containers-with-large-partition-key"></a>Creare contenitori con una chiave di partizione grande

Azure Cosmos DB usa lo schema di partizionamento basato su hash per ottenere il ridimensionamento orizzontale dei dati. Tutti i contenitori di Azure Cosmos creati prima del 3 2019 maggio usano una funzione hash che calcola l'hash in base ai primi 100 byte della chiave di partizione. Se sono presenti più chiavi di partizione con lo stesso primo 100 byte, le partizioni logiche vengono considerate come la stessa partizione logica da parte del servizio. Questo può causare problemi quali la quota delle dimensioni della partizione non corretta e gli indici univoci applicati tra le chiavi di partizione. Per risolvere questo problema, sono state introdotte chiavi di partizione di grandi dimensioni. Azure Cosmos DB supporta ora chiavi di partizione di grandi dimensioni con valori fino a 2 KB.

Le chiavi di partizione di grandi dimensioni sono supportate usando la funzionalità di una versione migliorata della funzione hash, che può generare un hash univoco da chiavi di partizione di grandi dimensioni fino a 2 KB. Questa versione hash è consigliata anche per gli scenari con cardinalità a chiave di partizione elevata indipendentemente dalle dimensioni della chiave di partizione. Una cardinalità della chiave di partizione viene definita come il numero di partizioni logiche univoche, ad esempio nell'ordine di ~ 30000 partizioni logiche in un contenitore. Questo articolo descrive come creare un contenitore con una chiave di partizione di grandi dimensioni usando il portale di Azure e diversi SDK.

## <a name="create-a-large-partition-key-azure-portal"></a>Creare una chiave di partizione di grandi dimensioni (portale di Azure)

Per creare una chiave di partizione di grandi dimensioni, quando si crea un nuovo contenitore usando il portale di Azure, selezionare l'opzione **My Partition Key è maggiore di 100-bytes** . Deselezionare la casella di controllo se non sono necessarie chiavi di partizione di grandi dimensioni o se sono presenti applicazioni in esecuzione nella versione SDK precedente alla 1,18.

![Creazione di chiavi di partizione di grandi dimensioni con portale di Azure](./media/large-partition-keys/large-partition-key-with-portal.png)

## <a name="create-a-large-partition-key-powershell"></a>Creare una chiave di partizione di grandi dimensioni (PowerShell)

Per creare un contenitore con supporto di chiavi di partizione di grandi dimensioni, vedere

* [Creare un contenitore di Azure Cosmos con una chiave di partizione di grandi dimensioni](manage-with-powershell.md##create-container-big-pk)

## <a name="create-a-large-partition-key-net-sdk"></a>Creare una chiave di partizione di grandi dimensioni (.NET SDK)

Per creare un contenitore con una chiave di partizione di grandi dimensioni usando .NET SDK, specificare la proprietà `PartitionKeyDefinitionVersion.V2`. Nell'esempio seguente viene illustrato come specificare la proprietà Version all'interno dell'oggetto PartitionKeyDefinition e impostarla su PartitionKeyDefinitionVersion. v2.

### <a name="v3-net-sdk"></a>.NET SDK V3

```csharp
await database.CreateContainerAsync(
    new ContainerProperties(collectionName, $"/longpartitionkey")
    {
        PartitionKeyDefinitionVersion = PartitionKeyDefinitionVersion.V2,
    })
```

### <a name="v2-net-sdk"></a>SDK .NET v2

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

Le chiavi di partizione di grandi dimensioni sono supportate con le seguenti versioni minime degli SDK:

|Tipo di SDK  | Versione minima   |
|---------|---------|
|.NET     |    1,18     |
|Sincronizzazione Java     |   2.4.0      |
|Java asincrono   |  2.5.0        |
| API REST | versione superiore a `2017-05-03` usando l'intestazione della richiesta `x-ms-version`.|

Attualmente, non è possibile usare contenitori con una chiave di partizione di grandi dimensioni all'interno di Power BI e app per la logica di Azure. È possibile usare contenitori senza una chiave di partizione di grandi dimensioni da queste applicazioni.

## <a name="next-steps"></a>Passaggi successivi

* [Partitioning in Azure Cosmos DB](partitioning-overview.md) (Partizionamento in Azure Cosmos DB)
* [Unità richiesta in Azure Cosmos DB](request-units.md)
* [Effettuare il provisioning della velocità effettiva per contenitori e database](set-throughput.md)
* [Usare l'account Azure Cosmos](account-overview.md)
