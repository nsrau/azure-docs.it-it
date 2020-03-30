---
title: Eseguire la migrazione dalla libreria del processore del feed di modifiche a Azure Cosmos DB .NET V3 SDKMigrate from the change feed processor library to the Azure Cosmos DB .NET V3 SDK
description: Informazioni su come eseguire la migrazione dell'applicazione dall'uso della libreria del processore del feed di modifiche a Azure Cosmos DB SDK V3
author: ealsur
ms.service: cosmos-db
ms.topic: conceptual
ms.date: 09/17/2019
ms.author: maquaran
ms.openlocfilehash: 9570a8512e3437b12ecce2ef0c708a74a8806482
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/28/2020
ms.locfileid: "77588884"
---
# <a name="migrate-from-the-change-feed-processor-library-to-the-azure-cosmos-db-net-v3-sdk"></a>Eseguire la migrazione dalla libreria del processore del feed di modifiche a Azure Cosmos DB .NET V3 SDKMigrate from the change feed processor library to the Azure Cosmos DB .NET V3 SDK

In questo articolo vengono descritti i passaggi necessari per eseguire la migrazione del codice di un'applicazione esistente che utilizza la libreria del processore del feed di [modifiche](https://github.com/Azure/azure-documentdb-changefeedprocessor-dotnet) alla funzionalità del feed di [modifiche](change-feed.md) nella versione più recente di .NET SDK (denominata anche .NET V3 SDK).

## <a name="required-code-changes"></a>Modifiche al codice richieste

I componenti di configurazione di .NET V3 SDK hanno diverse modifiche di rilievo, i passaggi chiave per eseguire la migrazione dell'applicazione sono i passaggi principali per eseguire la migrazione dell'applicazione:

1. Convertire `DocumentCollectionInfo` le `Container` istanze in riferimenti per i contenitori monitorati e di lease.
1. Le personalizzazioni `WithProcessorOptions` che utilizzano `WithLeaseConfiguration` `WithPollInterval` devono essere `WithStartTime` aggiornate per l'utilizzo e per gli intervalli, per l'ora di [inizio](how-to-configure-change-feed-start-time.md)e `WithMaxItems` per definire il numero massimo di elementi.
1. Impostare `processorName` `GetChangeFeedProcessorBuilder` l'opzione on `ChangeFeedProcessorOptions.LeasePrefix`in modo `string.Empty` che corrisponda al valore configurato su o utilizzare in caso contrario.
1. Le modifiche non vengono più `IReadOnlyList<Document>`recapitate come `IReadOnlyCollection<T>` , `T` è un dove è un tipo che è necessario definire, non esiste più una classe di elementi di base.
1. Per gestire le modifiche, non è più necessaria un'implementazione, ma è necessario [definire un delegato](change-feed-processor.md#implementing-the-change-feed-processor). Il delegato può essere una funzione statica o, se è necessario mantenere lo stato tra le esecuzioni, è possibile creare una classe personalizzata e passare un metodo di istanza come delegato.

Ad esempio, se il codice originale per compilare il processore del feed di modifiche ha il seguente aspetto:

[!code-csharp[Main](~/samples-cosmosdb-dotnet-v3/Microsoft.Azure.Cosmos.Samples/Usage/ChangeFeed/Program.cs?name=ChangeFeedProcessorLibrary)]

Il codice migrato sarà simile al seguente:The migrated code will look like:

[!code-csharp[Main](~/samples-cosmosdb-dotnet-v3/Microsoft.Azure.Cosmos.Samples/Usage/ChangeFeed/Program.cs?name=ChangeFeedProcessorMigrated)]

E il delegato, può essere un metodo statico:

[!code-csharp[Main](~/samples-cosmosdb-dotnet-v3/Microsoft.Azure.Cosmos.Samples/Usage/ChangeFeed/Program.cs?name=Delegate)]

## <a name="state-and-lease-container"></a>Contenitore di stato e lease

Analogamente alla libreria del processore del feed di modifiche, la funzionalità del feed di modifiche in .NET V3 SDK usa un [contenitore di lease](change-feed-processor.md#components-of-the-change-feed-processor) per archiviare lo stato. Tuttavia, gli schemi sono diversi.

Il processore del feed di modifiche SDK V3 rileverà qualsiasi stato precedente della libreria ed eseguirà automaticamente la migrazione al nuovo schema alla prima esecuzione del codice dell'applicazione migrato. 

È possibile arrestare in modo sicuro l'applicazione utilizzando il codice precedente, eseguire la migrazione del codice alla nuova versione, avviare l'applicazione migrata e tutte le modifiche apportate durante l'arresto dell'applicazione verranno prelevate ed elaborate dalla nuova versione.

> [!NOTE]
> Le migrazioni dalle applicazioni che utilizzano la libreria a .NET V3 SDK sono unidirezionali, poiché lo stato (lease) verrà migrato nel nuovo schema. La migrazione non è compatibile con le versioni precedenti.


## <a name="additional-resources"></a>Risorse aggiuntive

* [SDK di Azure Cosmos DB](sql-api-sdk-dotnet.md)
* [Esempi di utilizzo su GitHub](https://github.com/Azure/azure-cosmos-dotnet-v3/tree/master/Microsoft.Azure.Cosmos.Samples/Usage/ChangeFeed)
* [Altri esempi in GitHub](https://github.com/Azure-Samples/cosmos-dotnet-change-feed-processor)

## <a name="next-steps"></a>Passaggi successivi

È ora possibile procedere ad acquisire altre informazioni sul processore di feed di modifiche negli articoli seguenti:

* [Panoramica del processore di feed di modifiche](change-feed-processor.md)
* [Uso dello strumento di stima dei feed di modifiche](how-to-use-change-feed-estimator.md)
* [Ora di avvio del processore dei feed di modifiche](how-to-configure-change-feed-start-time.md)
