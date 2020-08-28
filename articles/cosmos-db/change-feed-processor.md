---
title: Processore dei feed di modifiche in Azure Cosmos DB
description: Informazioni su come usare il processore dei feed di modifiche Azure Cosmos DB per leggere il feed delle modifiche, i componenti del processore dei feed di modifiche
author: timsander1
ms.author: tisande
ms.service: cosmos-db
ms.devlang: dotnet
ms.topic: conceptual
ms.date: 05/13/2020
ms.reviewer: sngun
ms.custom: devx-track-csharp
ms.openlocfilehash: 3a802cc3d6178302445e0c31c52785d00207d0bd
ms.sourcegitcommit: 419cf179f9597936378ed5098ef77437dbf16295
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 08/27/2020
ms.locfileid: "88998544"
---
# <a name="change-feed-processor-in-azure-cosmos-db"></a>Processore dei feed di modifiche in Azure Cosmos DB

Il processore del feed di modifiche è incluso in [Azure Cosmos DB SDK V3](https://github.com/Azure/azure-cosmos-dotnet-v3). Semplifica il processo di lettura del feed di modifiche e la distribuzione efficace dell'elaborazione di eventi tra più consumer.

Il vantaggio principale della libreria del processore dei feed di modifiche è il comportamento a tolleranza di errore che garantisce un recapito di tipo "at-least-once" di tutti gli eventi nel feed di modifiche.

## <a name="components-of-the-change-feed-processor"></a>Componenti del processore dei feed di modifiche

Ci sono quattro componenti principali per l'implementazione del processore del feed di modifiche:

1. **Contenitore monitorato:** il contenitore monitorato include i dati da cui viene generato il feed di modifiche. Eventuali inserimenti e aggiornamenti nel contenitore monitorato si riflettono nel feed di modifiche del contenitore.

1. **Contenitore di lease:** Il contenitore di lease agisce come una risorsa di archiviazione di stato e coordina l'elaborazione dei feed di modifiche tra più ruoli di lavoro. Il contenitore di lease può essere archiviato nello stesso account del contenitore monitorato o in un account diverso.

1. **Host**: Un host è un'istanza dell'applicazione che usa il processore del feed di modifiche per restare in ascolto delle modifiche. È possibile eseguire in parallelo più istanze con la stessa configurazione di lease, ma ogni istanza deve avere un nome di istanza **diverso**.

1. **Delegato**: Il delegato è il codice che definisce le operazioni che lo sviluppatore desidera eseguire con ogni batch di modifiche letto dal processore del feed di modifiche. 

Per una migliore comprensione dell'interazione tra i quattro elementi del processore dei feed di modifiche, è consigliabile esaminare un esempio nel diagramma seguente. Il contenitore monitorato archivia i documenti e usa "City" come chiave di partizione. Si noterà che i valori della chiave di partizione sono distribuiti in intervalli che contengono elementi. Sono disponibili due istanze dell'host e il processore dei feed di modifiche assegna diversi intervalli di valori di chiave di partizione a ogni istanza per ottimizzare la distribuzione di calcolo. Ogni intervallo viene letto in parallelo e lo stato di avanzamento viene mantenuto separatamente dagli altri intervalli nel contenitore di lease.

:::image type="content" source="./media/change-feed-processor/changefeedprocessor.png" alt-text="Esempio di processore dei feed di modifiche" border="false":::

## <a name="implementing-the-change-feed-processor"></a>Implementazione del processore dei feed di modifiche

Il punto di ingresso è sempre il contenitore monitorato, da un'istanza `Container` chiamata `GetChangeFeedProcessorBuilder`:

[!code-csharp[Main](~/samples-cosmosdb-dotnet-change-feed-processor/src/Program.cs?name=DefineProcessor)]

dove il primo parametro è un nome distinto che descrive l'obiettivo del processore e il secondo nome è l'implementazione del delegato che gestirà le modifiche. 

Un esempio di un delegato è:


[!code-csharp[Main](~/samples-cosmosdb-dotnet-change-feed-processor/src/Program.cs?name=Delegate)]

Infine, definire un nome per l'istanza del processore con `WithInstanceName` e che è il contenitore per mantenere lo stato del lease con `WithLeaseContainer`.

Chiamando `Build`, si otterrà l'istanza del processore che è possibile avviare chiamando `StartAsync`.

## <a name="processing-life-cycle"></a>Ciclo di vita dell'elaborazione

Il normale ciclo di vita di un'istanza dell'host è:

1. Leggere il feed di modifiche.
1. Se non sono state apportate modifiche, sospendere per un periodo di tempo predefinito personalizzabile con `WithPollInterval` nel generatore e passare a #1.
1. Se ci sono modifiche, inviarle al **delegato**.
1. Quando il delegato termina **correttamente** l'elaborazione delle modifiche, aggiornare l'archivio dei lease con l'ultimo punto nel tempo elaborato e passare a #1.

## <a name="error-handling"></a>Gestione degli errori

Il processore dei feed di modifiche è resiliente agli errori del codice utente. Ciò significa che se l'implementazione del delegato presenta un'eccezione non gestita (passaggio #4), il thread che elabora tale batch di modifiche verrà arrestato e verrà creato un nuovo thread. Il nuovo thread verificherà quale sia l'ultimo punto nel tempo dell'archivio dei lease per l'intervallo di valori della chiave di partizione e il riavvio da tale intervallo, inviando efficacemente lo stesso batch di modifiche al delegato. Questo comportamento continuerà fino a quando il delegato non elabora correttamente le modifiche ed è il motivo per cui il processore dei feed di modifiche ha una garanzia di tipo "at least once", perché se il codice del delegato genera un'eccezione, vi sarà un nuovo tentativo di quel batch.

Per evitare che il processore dei feed di modifiche venga bloccato continuamente durante il nuovo tentativo dello stesso batch di modifiche, è necessario aggiungere la logica nel codice del delegato per scrivere documenti, in caso di eccezione, in una coda di messaggi non recapitabili. Questa progettazione garantisce la possibilità di tenere traccia delle modifiche non elaborate continuando comunque a elaborare le modifiche future. La coda dei messaggi non recapitabili potrebbe essere semplicemente un altro contenitore Cosmos. L'archivio dati esatto non è rilevante, semplicemente le modifiche non elaborate sono persistenti.

Inoltre, è possibile usare lo [strumento di stima del feed di modifiche](how-to-use-change-feed-estimator.md) per monitorare lo stato di avanzamento delle istanze durante la lettura del feed di modifiche. Oltre a monitorare se il processore di feed di modifiche viene bloccato continuamente durante il nuovo tentativo dello stesso batch di modifiche, è anche possibile comprendere se il processore di feed di modifiche è in ritardo a causa di risorse disponibili come CPU, memoria e larghezza di banda di rete.

## <a name="deployment-unit"></a>Unità di distribuzione

Una singola unità di distribuzione del processore di feed di modifiche è costituita da una o più istanze con lo stesso `processorName` e la stessa configurazione del contenitore di lease. È possibile disporre di numerose unità di distribuzione in cui ciascuna di esse dispone di un flusso aziendale diverso per le modifiche e di ogni unità di distribuzione costituita da una o più istanze. 

Ad esempio, si potrebbe avere un'unità di distribuzione che attiva un'API esterna in qualsiasi momento in cui viene apportata una modifica al contenitore. Un'altra unità di distribuzione potrebbe spostare i dati in tempo reale ogni volta che viene apportata una modifica. Quando si verifica una modifica nel contenitore monitorato, tutte le unità di distribuzione riceveranno una notifica.

## <a name="dynamic-scaling"></a>Scalabilità dinamica

Come indicato in precedenza, all'interno di un'unità di distribuzione è possibile disporre di una o più istanze. Per sfruttare i vantaggi della distribuzione di calcolo all'interno dell'unità di distribuzione, gli unici requisiti principali sono i seguenti:

1. Tutte le istanze devono avere la stessa configurazione del contenitore di lease.
1. Tutte le istanze devono avere lo stesso `processorName`.
1. Ogni istanza deve avere un nome di istanza diverso (`WithInstanceName`).

Se si applicano queste tre condizioni, il processore di feed di modifiche userà un algoritmo di distribuzione uguale, distribuirà tutti i lease nel contenitore di lease in tutte le istanze in esecuzione di tale unità di distribuzione e parallelizzerà il calcolo. Un lease può essere di proprietà di un'istanza in un determinato momento, quindi il numero massimo di istanze equivale al numero di lease.

È possibile aumentare e ridurre il numero di istanze e il processore di feed di modifiche regola dinamicamente il carico ridistribuendolo di conseguenza.

Inoltre, il processore di feed di modifiche può adattarsi dinamicamente alla scalabilità dei contenitori a causa dell'aumento della velocità effettiva o dell'archiviazione. Quando il contenitore si espande, il processore di feed di modifiche gestisce in modo trasparente questi scenari aumentando dinamicamente i lease e distribuendo i nuovi lease tra le istanze esistenti.

## <a name="change-feed-and-provisioned-throughput"></a>Feed di modifiche e velocità effettiva di cui viene effettuato il provisioning

Vengono addebitati i costi per le UR utilizzate, in quanto lo spostamento dei dati da e verso i contenitori Cosmos comporta sempre l'utilizzo di UR. Vengono addebitati i costi per le UR utilizzate dal contenitore di lease.

## <a name="where-to-host-the-change-feed-processor"></a>Posizione in cui ospitare il processore del feed delle modifiche

Il processore del feed delle modifiche può essere ospitato in qualsiasi piattaforma che supporti processi o attività a esecuzione prolungata:

* Una processo Web di [Azure](https://docs.microsoft.com/learn/modules/run-web-app-background-task-with-webjobs/)con esecuzione continua.
* Un processo in una [macchina virtuale di Azure](https://docs.microsoft.com/azure/architecture/best-practices/background-jobs#azure-virtual-machines).
* Un processo in background nel [servizio Azure Kubernetes](https://docs.microsoft.com/azure/architecture/best-practices/background-jobs#azure-kubernetes-service).
* [Servizio ospitato ASP.NET](https://docs.microsoft.com/aspnet/core/fundamentals/host/hosted-services).

Mentre il processore del feed delle modifiche può essere eseguito in ambienti di breve durata, perché il contenitore di lease gestisce lo stato, il ciclo di avvio e arresto di questi ambienti aggiungerà un ritardo alla ricezione delle notifiche (a causa dell'overhead di avvio del processore a ogni avvio dell'ambiente).

## <a name="additional-resources"></a>Risorse aggiuntive

* [SDK di Azure Cosmos DB](sql-api-sdk-dotnet.md)
* [Completare l'applicazione di esempio su GitHub](https://github.com/Azure-Samples/cosmos-dotnet-change-feed-processor)
* [Esempi di utilizzo aggiuntivi su GitHub](https://github.com/Azure/azure-cosmos-dotnet-v3/tree/master/Microsoft.Azure.Cosmos.Samples/Usage/ChangeFeed)
* [Laboratori di Cosmos DB workshop per il processore di feed di modifiche](https://azurecosmosdb.github.io/labs/dotnet/labs/08-change_feed_with_azure_functions.html#consume-cosmos-db-change-feed-via-the-change-feed-processor)

## <a name="next-steps"></a>Passaggi successivi

È ora possibile procedere ad acquisire altre informazioni sul processore di feed di modifiche negli articoli seguenti:

* [Panoramica del feed di modifiche](change-feed.md)
* [Modello di pull del feed di modifiche](change-feed-pull-model.md)
* [Come eseguire la migrazione della libreria del processore di feed di modifiche](how-to-migrate-from-change-feed-library.md)
* [Uso dello strumento di stima di feed di modifiche](how-to-use-change-feed-estimator.md)
* [Ora di avvio del processore di feed di modifiche](how-to-configure-change-feed-start-time.md)
