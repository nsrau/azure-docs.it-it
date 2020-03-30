---
title: Modificare la libreria del processore del feed in Azure Cosmos DBChange feed processor library in Azure Cosmos DB
description: Informazioni su come usare la libreria del processore del feed di modifiche di Azure Cosmos DB per leggere il feed di modifiche, i componenti del processore del feed di modifiche
author: markjbrown
ms.author: mjbrown
ms.service: cosmos-db
ms.devlang: dotnet
ms.topic: conceptual
ms.date: 12/03/2019
ms.reviewer: sngun
ms.openlocfilehash: e71b2807595aebeb1f0c8682fde119f4e267e55d
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/28/2020
ms.locfileid: "78273315"
---
# <a name="change-feed-processor-in-azure-cosmos-db"></a>Processore dei feed di modifiche in Azure Cosmos DB 

Il processore del feed di modifiche fa parte di [Azure Cosmos DB SDK V3.](https://github.com/Azure/azure-cosmos-dotnet-v3) Semplifica il processo di lettura del feed di modifiche e distribuisce l'elaborazione degli eventi tra più consumer in modo efficace.

Il vantaggio principale della libreria del processore del feed di modifiche è il suo comportamento a tolleranza di errore che assicura un recapito "almeno una volta" di tutti gli eventi nel feed di modifiche.

## <a name="components-of-the-change-feed-processor"></a>Componenti del processore del feed di modifiche

Esistono quattro componenti principali dell'implementazione del processore del feed di modifiche: 

1. **Contenitore monitorato:** il contenitore monitorato include i dati da cui viene generato il feed di modifiche. Eventuali inserimenti e aggiornamenti al contenitore monitorato si riflettono nel feed di modifiche del contenitore.

1. **Contenitore di lease:** Il contenitore di lease funge da archivio dello stato e coordina l'elaborazione del feed di modifiche tra più worker. Il contenitore lease può essere archiviato nello stesso account del contenitore monitorato o in un account separato. 

1. **L'ospitante:** Un host è un'istanza dell'applicazione che usa il processore del feed di modifiche per l'ascolto delle modifiche. Più istanze con la stessa configurazione di lease possono essere eseguite in parallelo, ma ogni istanza deve avere un nome di **istanza**diverso. 

1. **Delegato:** Il delegato è il codice che definisce le operazioni che si desidera eseguire con ogni batch di modifiche letto dal processore del feed di modifiche. 

Per una migliore comprensione dell'interazione tra i quattro elementi del processore dei feed di modifiche, è consigliabile esaminare un esempio nel diagramma seguente. Il contenitore monitorato archivia i documenti e utilizza 'City' come chiave di partizione. Vediamo che i valori della chiave di partizione sono distribuiti in intervalli che contengono elementi. Esistono due istanze host e il processore del feed di modifiche assegna intervalli diversi di valori di chiave di partizione a ogni istanza per ottimizzare la distribuzione di calcolo. Ogni intervallo viene letto in parallelo e il relativo stato di avanzamento viene mantenuto separatamente dagli altri intervalli nel contenitore di lease.

![Esempio di processore dei feed di modifiche](./media/change-feed-processor/changefeedprocessor.png)

## <a name="implementing-the-change-feed-processor"></a>Implementazione del processore del feed di modificheImplementing the change feed processor

Il punto di ingresso è sempre il `Container` contenitore `GetChangeFeedProcessorBuilder`monitorato, da un'istanza chiamata :

[!code-csharp[Main](~/samples-cosmosdb-dotnet-change-feed-processor/src/Program.cs?name=DefineProcessor)]

Dove il primo parametro è un nome distinto che descrive l'obiettivo di questo processore e il secondo nome è l'implementazione del delegato che gestirà le modifiche. 

Un esempio di delegato potrebbe essere:An example of a delegate would be:


[!code-csharp[Main](~/samples-cosmosdb-dotnet-change-feed-processor/src/Program.cs?name=Delegate)]

Infine si definisce un nome `WithInstanceName` per questa istanza del processore `WithLeaseContainer`con e che è il contenitore per mantenere lo stato di lease con .

La `Build` chiamata ti darà l'istanza del `StartAsync`processore che puoi avviare chiamando .

## <a name="processing-life-cycle"></a>Ciclo di vita dell'elaborazione

Il normale ciclo di vita di un'istanza dell'host è:The normal life cycle of a host instance is:

1. Leggere il feed di modifiche.
1. Se non ci sono modifiche, sormi per un `WithPollInterval` periodo di tempo predefinito (personalizzabile con nel Costruttore) e vai a #1.
1. Se sono presenti modifiche, inviarle al **delegato**.
1. Quando il delegato termina l'elaborazione delle modifiche **correttamente,** aggiornare l'archivio lease con l'ultimo punto elaborato nel tempo e passare a #1.

## <a name="error-handling"></a>Gestione degli errori

Il processore del feed di modifiche è resiliente agli errori del codice utente. Ciò significa che se l'implementazione del delegato presenta un'eccezione non gestita (passaggio #4), l'elaborazione del thread che specifico batch di modifiche verrà arrestato e verrà creato un nuovo thread. Il nuovo thread controllerà quale era l'ultimo punto nel tempo dell'archivio lease per tale intervallo di valori di chiave di partizione e verrà riavviato da lì, inviando in modo efficace lo stesso batch di modifiche al delegato. Questo comportamento continuerà fino a quando il delegato elabora correttamente le modifiche ed è il motivo per cui il processore del feed di modifiche ha una garanzia "almeno una volta", perché se il codice delegato genera un'eccezione, verrà eseguito un nuovo tentativo di tale batch.

## <a name="dynamic-scaling"></a>Scalabilità dinamica

Come accennato durante l'introduzione, il processore del feed di modifiche può distribuire automaticamente il calcolo tra più istanze. È possibile distribuire più istanze dell'applicazione usando il processore del feed di modifiche e sfruttarla, gli unici requisiti chiave sono:

1. Tutte le istanze devono avere la stessa configurazione del contenitore di lease.
1. Tutte le istanze devono avere lo stesso nome del flusso di lavoro.
1. Ogni istanza deve avere un`WithInstanceName`nome di istanza diverso ( ).

Se si applicano queste tre condizioni, il processore del feed di modifiche, utilizzando un algoritmo di distribuzione uguale, distribuirà tutti i lease nel contenitore di lease tra tutte le istanze in esecuzione e parallelizzerà il calcolo. Un lease può essere di proprietà di una sola istanza alla volta, pertanto il numero massimo di istanze è uguale al numero di lease.

Il numero di istanze può aumentare e ridurre e il processore del feed di modifiche regolerà dinamicamente il carico ridistribuendo di conseguenza.

Inoltre, il processore del feed di modifiche può adattarsi dinamicamente alla scalabilità dei contenitori a causa della velocità effettiva o degli aumenti di archiviazione. Quando il contenitore aumenta, il processore del feed di modifiche gestisce in modo trasparente questi scenari aumentando dinamicamente i lease e distribuendo i nuovi lease tra le istanze esistenti.

## <a name="change-feed-and-provisioned-throughput"></a>Feed di modifiche e velocità effettiva di cui viene effettuato il provisioning

Vengono addebitati i costi per le UR utilizzate, in quanto lo spostamento dei dati da e verso i contenitori Cosmos comporta sempre l'utilizzo di UR. Vengono addebitati i costi per le UR utilizzate dal contenitore di lease.

## <a name="additional-resources"></a>Risorse aggiuntive

* [SDK di Azure Cosmos DB](sql-api-sdk-dotnet.md)
* [Esempi di utilizzo su GitHub](https://github.com/Azure/azure-cosmos-dotnet-v3/tree/master/Microsoft.Azure.Cosmos.Samples/Usage/ChangeFeed)
* [Altri esempi in GitHub](https://github.com/Azure-Samples/cosmos-dotnet-change-feed-processor)

## <a name="next-steps"></a>Passaggi successivi

È ora possibile procedere ad acquisire altre informazioni sul processore di feed di modifiche negli articoli seguenti:

* [Panoramica del feed di modifiche](change-feed.md)
* [Come eseguire la migrazione dalla libreria del processore del feed di modificheHow to migrate from the change feed processor library](how-to-migrate-from-change-feed-library.md)
* [Uso dello strumento di stima dei feed di modifiche](how-to-use-change-feed-estimator.md)
* [Ora di avvio del processore dei feed di modifiche](how-to-configure-change-feed-start-time.md)
