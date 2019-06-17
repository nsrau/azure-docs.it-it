---
title: Creare contenitori di Azure Cosmos con la chiave di partizione di grandi dimensioni usando il portale di Azure e vari SDK.
description: Informazioni su come creare un contenitore in Azure Cosmos DB con la chiave di partizione di grandi dimensioni usando il portale di Azure e diversi SDK.
author: markjbrown
ms.service: cosmos-db
ms.topic: conceptual
ms.date: 05/23/2019
ms.author: mjbrown
ms.openlocfilehash: 33f871564b7c8435395db6b97122ba6a75800271
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 06/13/2019
ms.locfileid: "66225989"
---
# <a name="create-containers-with-large-partition-key"></a>Creare contenitori con la chiave di partizione di grandi dimensioni

Azure Cosmos DB Usa lo schema di partizionamento basato su hash per conseguire la scalabilità orizzontale dei dati. Tutti i contenitori di Azure Cosmos creati prima di maggio 2019 3 usano una funzione hash che calcola l'hash in base i primi 100 byte della chiave di partizione. Se sono presenti più chiavi di partizione con i primi 100 byte stessi, quindi tali partizioni logiche vengono considerati della stessa partizione logica dal servizio. Questo può causare problemi come la quota delle dimensioni della partizione viene corretto e gli indici univoci, viene applicati tra le chiavi di partizione. Le chiavi di partizione di grandi dimensioni vengono introdotte per risolvere questo problema. Azure Cosmos DB è ora supporta le chiavi di partizione di grandi dimensioni con i valori fino a 2 KB. 

Partizione di grandi dimensioni utilizzando la funzionalità di una versione migliorata della funzione hash, che può generare un hash univoco dalla partizione di grandi dimensioni sono supportate chiavi tasti fino a 2 KB. Questa versione di hash è consigliata anche per scenari con cardinalità elevata partizione della chiave indipendentemente dalle dimensioni della chiave di partizione. Una cardinalità di chiave di partizione è definita come il numero di partizioni logiche univoche, ad esempio in base all'ordine ~ 30000 partizioni logiche in un contenitore. Questo articolo descrive come creare un contenitore con una chiave di partizione di grandi dimensioni usando il portale di Azure e diversi SDK. 

## <a name="create-a-large-partition-key-net-sdk-v2"></a>Creare una chiave di partizione di grandi dimensioni (.Net SDK versione 2)

Quando si usa .net SDK per creare un contenitore con la chiave di partizione di grandi dimensioni, è necessario specificare il `PartitionKeyDefinitionVersion.V2` proprietà. Nell'esempio seguente viene illustrato come specificare la proprietà della versione all'interno dell'oggetto PartitionKeyDefinition e impostarlo su PartitionKeyDefinitionVersion.V2:

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

## <a name="create-a-large-partition-key-azure-portal"></a>Creare una chiave di partizione di grandi dimensioni (portale di Azure) 

Per creare una chiave di partizione di grandi dimensioni, mentre si crea un nuovo contenitore nel portale di Azure, selezionare la **la chiave di partizione è superiore a 100 byte** opzione. Per impostazione predefinita, tutti i nuovi contenitori sono acconsentire usando le chiavi di partizione di grandi dimensioni. Se non sono necessarie le chiavi di partizione di grandi dimensioni o se sono presenti applicazioni in esecuzione nella versione di SDK precedente a 1.18, deselezionare la casella di controllo.

![Creare le chiavi di partizione di grandi dimensioni usando il portale di Azure](./media/large-partition-keys/large-partition-key-with-portal.png)


## <a name="supported-sdk-versions"></a>Versioni di SDK supportate

Le chiavi di partizione di grandi dimensioni sono supportate con le versioni minime seguenti degli SDK:

|Tipo di SDK  | Versione minima   |
|---------|---------|
|.Net     |    1.18     |
|Sincronizzazione di Java     |   2.4.0      |
|Java Async   |  2.5.0        |
| API REST | versione maggiore `2017-05-03` utilizzando il `x-ms-version` intestazione della richiesta.|

Attualmente, è possibile usare i contenitori con la chiave di partizione di grandi dimensioni all'interno di Power BI e le App per la logica di Azure. È possibile usare i contenitori senza una chiave di partizione di grandi dimensioni da queste applicazioni. 
 
## <a name="next-steps"></a>Passaggi successivi

* [Partitioning in Azure Cosmos DB](partitioning-overview.md) (Partizionamento in Azure Cosmos DB)
* [Unità richiesta in Azure Cosmos DB](request-units.md)
* [Effettuare il provisioning della velocità effettiva per contenitori e database](set-throughput.md)
* [Usare l'account Azure Cosmos](account-overview.md)


