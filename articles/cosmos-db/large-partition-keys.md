---
title: Creare contenitori di Azure Cosmos con una chiave di partizione di grandi dimensioni
description: Informazioni su come creare un contenitore in Azure Cosmos DB con una chiave di partizione di grandi dimensioni usando portale di Azure e SDK diversi.
author: markjbrown
ms.service: cosmos-db
ms.topic: how-to
ms.date: 09/28/2019
ms.author: mjbrown
ms.custom: devx-track-csharp
ms.openlocfilehash: a93bf4fe92cc7b2240a7411b093a7bab5a1a275e
ms.sourcegitcommit: 3bdeb546890a740384a8ef383cf915e84bd7e91e
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 10/30/2020
ms.locfileid: "93098825"
---
# <a name="create-containers-with-large-partition-key"></a>Creare contenitori con una chiave di partizione grande
[!INCLUDE[appliesto-sql-api](includes/appliesto-sql-api.md)]

Azure Cosmos DB usa lo schema di partizionamento basato su hash per ottenere il ridimensionamento orizzontale dei dati. Tutti i contenitori di Azure Cosmos creati prima del 3 2019 maggio usano una funzione hash che calcola l'hash in base ai primi 100 byte della chiave di partizione. Se sono presenti più chiavi di partizione con lo stesso primo 100 byte, le partizioni logiche vengono considerate come la stessa partizione logica da parte del servizio. Questo può causare problemi quali la quota delle dimensioni della partizione non corretta e gli indici univoci applicati tra le chiavi di partizione. Per risolvere questo problema, sono state introdotte chiavi di partizione di grandi dimensioni. Azure Cosmos DB supporta ora chiavi di partizione di grandi dimensioni con valori fino a 2 KB.

Le chiavi di partizione di grandi dimensioni sono supportate usando la funzionalità di una versione migliorata della funzione hash, che può generare un hash univoco da chiavi di partizione di grandi dimensioni fino a 2 KB. Questa versione hash è consigliata anche per gli scenari con cardinalità a chiave di partizione elevata indipendentemente dalle dimensioni della chiave di partizione. Una cardinalità della chiave di partizione viene definita come il numero di partizioni logiche univoche, ad esempio nell'ordine di ~ 30000 partizioni logiche in un contenitore. Questo articolo descrive come creare un contenitore con una chiave di partizione di grandi dimensioni usando il portale di Azure e diversi SDK.

## <a name="create-a-large-partition-key-azure-portal"></a>Creare una chiave di partizione di grandi dimensioni (portale di Azure)

Per creare una chiave di partizione di grandi dimensioni, quando si crea un nuovo contenitore usando il portale di Azure, selezionare l'opzione **My Partition Key è maggiore di 100-bytes** . Deselezionare la casella di controllo se non sono necessarie chiavi di partizione di grandi dimensioni o se sono presenti applicazioni in esecuzione nella versione SDK precedente alla 1,18.

:::image type="content" source="./media/large-partition-keys/large-partition-key-with-portal.png" alt-text="Creazione di chiavi di partizione di grandi dimensioni con portale di Azure":::

## <a name="create-a-large-partition-key-powershell"></a>Creare una chiave di partizione di grandi dimensioni (PowerShell)

Per creare un contenitore con supporto di chiavi di partizione di grandi dimensioni, vedere

* [Creare un contenitore di Azure Cosmos con una chiave di partizione di grandi dimensioni](manage-with-powershell.md#create-container-big-pk)

## <a name="create-a-large-partition-key-net-sdk"></a>Creare una chiave di partizione di grandi dimensioni (.NET SDK)

Per creare un contenitore con una chiave di partizione di grandi dimensioni usando .NET SDK, specificare la `PartitionKeyDefinitionVersion.V2` Proprietà. Nell'esempio seguente viene illustrato come specificare la proprietà Version all'interno dell'oggetto PartitionKeyDefinition e impostarla su PartitionKeyDefinitionVersion. v2.

# <a name="net-sdk-v3"></a>[.NET SDK V3](#tab/dotnetv3)

```csharp
await database.CreateContainerAsync(
    new ContainerProperties(collectionName, $"/longpartitionkey")
    {
        PartitionKeyDefinitionVersion = PartitionKeyDefinitionVersion.V2,
    })
```

# <a name="net-sdk-v2"></a>[.NET SDK V2](#tab/dotnetv2)

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
---

## <a name="supported-sdk-versions"></a>Versioni di SDK supportate

Le chiavi di partizione di grandi dimensioni sono supportate con le seguenti versioni minime degli SDK:

|Tipo di SDK  | Versione minima   |
|---------|---------|
|.Net     |    1.18     |
|Sincronizzazione Java     |   2.4.0      |
|Java Async   |  2.5.0        |
| API REST | versione superiore `2017-05-03` a tramite l' `x-ms-version` intestazione della richiesta.|
| Modello di Resource Manager | versione 2 usando la `"version":2` proprietà all'interno dell' `partitionKey` oggetto. |

Attualmente, non è possibile usare contenitori con una chiave di partizione di grandi dimensioni all'interno di Power BI e app per la logica di Azure. È possibile usare contenitori senza una chiave di partizione di grandi dimensioni da queste applicazioni.

## <a name="next-steps"></a>Passaggi successivi

* [Partitioning in Azure Cosmos DB](partitioning-overview.md) (Partizionamento in Azure Cosmos DB)
* [Unità richiesta in Azure Cosmos DB](request-units.md)
* [Effettuare il provisioning della velocità effettiva per contenitori e database](set-throughput.md)
* [Usare l'account Azure Cosmos](./account-databases-containers-items.md)