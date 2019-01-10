---
title: Informazioni su come gestire gli account di database in Azure Cosmos DB
description: Informazioni su come gestire gli account di database in Azure Cosmos DB
author: markjbrown
ms.service: cosmos-db
ms.topic: sample
ms.date: 11/10/2018
ms.author: mjbrown
ms.openlocfilehash: c27cee4842c0e65e1737f100a215cff82a0fd439
ms.sourcegitcommit: 8330a262abaddaafd4acb04016b68486fba5835b
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 01/04/2019
ms.locfileid: "54033095"
---
# <a name="manage-indexing-in-azure-cosmos-db"></a>Gestire l'indicizzazione in Azure Cosmos DB

In Azure Cosmos DB è possibile scegliere se si vuole che un contenitore indicizzi automaticamente tutti gli elementi o meno. Per impostazione predefinita, tutti gli elementi in un contenitore di Azure Cosmos vengono indicizzati automaticamente, ma è possibile disattivare l'indicizzazione automatica. Quando l'indicizzazione è disattivata, gli elementi sono accessibili tramite i rispettivi collegamenti automatici o tramite query usando l'ID dell'elemento come ad esempio l'ID documento. È possibile richiedere in modo esplicito di rendere disponibili i risultati senza usare l'indice passando l'intestazione **x-ms-documentdb-enable-scan**  nell'API REST o l'opzione di richiesta **EnableScanInQuery** usando .NET SDK.

Con l'indicizzazione automatica disattivata, è comunque possibile aggiungere in modo selettivo elementi specifici all'indice. Al contrario, è possibile lasciare attiva l'indicizzazione automatica e scegliere di escludere selettivamente elementi specifici. Le configurazioni di attivazione o disattivazione dell'indicizzazione sono utili quando si ha un subset di elementi su cui eseguire query.  

La velocità effettiva in scrittura e le unità richiesta sono proporzionali al numero di valori che devono essere indicizzati, specificato dal set incluso nei criteri di indicizzazione. Se si ha una buona conoscenza dei modelli di query, è possibile scegliere in modo esplicito il subset di inclusione/esclusione di percorsi per migliorare la velocità effettiva in scrittura.

## <a name="manage-indexing-using-azure-portal"></a>Gestire l'indicizzazione usando il portale di Azure

1. Accedere al [portale di Azure](https://portal.azure.com/).

2. Creare un nuovo account Azure Cosmos o selezionare un account esistente.

3. Aprire il riquadro **Esplora dati**.

4. Selezionare un contenitore esistente, espanderlo e modificare i valori seguenti:

   * Aprire la finestra **Scalabilità e impostazioni**.
   * Modificare **indexingMode** da *consistent* a *none* o includere/escludere determinati percorsi dall'indicizzazione.
   * Fare clic su **OK** per salvare le modifiche.

   ![Gestire l'indicizzazione usando il portale di Azure](./media/how-to-manage-indexing/how-to-manage-indexing-portal.png)

## <a name="manage-indexing-using-azure-sdks"></a>Gestire l'indicizzazione usando Azure SDK

### <a id="dotnet"></a>.NET SDK

L'esempio seguente mostra come includere in modo esplicito un elemento usando [.NET SDK per l'API SQL](sql-api-sdk-dotnet.md) e la proprietà [RequestOptions.IndexingDirective](/dotnet/api/microsoft.azure.documents.client.requestoptions.indexingdirective).

```csharp
// To override the default behavior to exclude (or include) a document in indexing,
// use the RequestOptions.IndexingDirective property.
client.CreateDocumentAsync(UriFactory.CreateDocumentCollectionUri("myDatabaseName", "myCollectionName"),
    new { id = "myDocumentId", isRegistered = true },
    new RequestOptions { IndexingDirective = IndexingDirective.Include });
```

## <a name="next-steps"></a>Passaggi successivi

Altre informazioni sull'indicizzazione sono disponibili negli articoli seguenti:

* [Panoramica dell'indicizzazione](index-overview.md)
* [Criterio di indicizzazione](index-policy.md)
* [Tipi di indice](index-types.md)
* [Percorsi di indice](index-paths.md)
