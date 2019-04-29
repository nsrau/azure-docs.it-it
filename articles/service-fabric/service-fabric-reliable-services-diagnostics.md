---
title: Diagnostica per i servizi Reliable con stato in Azure Service Fabric | Microsoft Docs
description: Funzionalità di diagnostica per i servizi Reliable Services con stato in Azure Service Fabric
services: service-fabric
documentationcenter: .net
author: dkkapur
manager: chackdan
editor: ''
ms.assetid: ae0e8f99-69ab-4d45-896d-1fa80ed45659
ms.service: service-fabric
ms.devlang: dotnet
ms.topic: conceptual
ms.tgt_pltfrm: NA
ms.workload: NA
ms.date: 8/24/2018
ms.author: dekapur
ms.openlocfilehash: f49176f944aa2abfa1d355ce0bd207d1b544c275
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 04/23/2019
ms.locfileid: "60772959"
---
# <a name="diagnostic-functionality-for-stateful-reliable-services"></a>Funzionalità di diagnostica per i servizi Reliable con stato
La classe StatefulServiceBase dei servizi Reliable con stato in Azure Service Fabric genera eventi [EventSource](https://msdn.microsoft.com/library/system.diagnostics.tracing.eventsource.aspx) che possono essere usati per eseguire il debug del servizio, ottenere informazioni dettagliate sul funzionamento del runtime e ottenere assistenza per la risoluzione dei problemi.

## <a name="eventsource-events"></a>Eventi EventSource
Il nome EventSource per la classe StatefulServiceBase dei servizi Reliable con stato è "Microsoft-ServiceFabric-Services". Gli eventi provenienti da questa origine eventi vengono visualizzati nella finestra [Diagnostics Events](service-fabric-diagnostics-how-to-monitor-and-diagnose-services-locally.md#view-service-fabric-system-events-in-visual-studio) (Eventi di diagnostica) quando si esegue il [debug del servizio in Visual Studio](service-fabric-debugging-your-application.md).

Esempi di strumenti e tecnologie che consentono di raccogliere e/o visualizzare eventi EventSource sono [PerfView](https://www.microsoft.com/download/details.aspx?id=28567), [Diagnostica di Azure](../cloud-services/cloud-services-dotnet-diagnostics.md) e la [libreria TraceEvent di Microsoft](https://www.nuget.org/packages/Microsoft.Diagnostics.Tracing.TraceEvent).

## <a name="events"></a>Eventi
| Nome evento | ID evento | Level | Descrizione evento |
| --- | --- | --- | --- |
| StatefulRunAsyncInvocation |1 |Informazioni |Emesso quando l'attività RunAsync del servizio viene avviata |
| StatefulRunAsyncCancellation |2 |Informazioni |Emesso quando l'attività RunAsync del servizio viene annullata |
| StatefulRunAsyncCompletion |3 |Informazioni |Emesso quando l'attività RunAsync del servizio viene completata |
| StatefulRunAsyncSlowCancellation |4 |Avviso |Emesso quando l'annullamento dell'attività RunAsync del servizio richiede troppo tempo |
| StatefulRunAsyncFailure |5 |Tipi di errore |Emesso quando l'attività RunAsync del servizio genera un'eccezione |

## <a name="interpret-events"></a>Interpretazione degli eventi
Gli eventi StatefulRunAsyncInvocation, StatefulRunAsyncCompletion e StatefulRunAsyncCancellation consentono all'autore di un servizio di comprenderne il ciclo di vita e di definirne i tempi di avvio, annullamento e completamento. Queste informazioni possono esseri utili durante il debug dei problemi di un servizio o per la comprensione del ciclo di vita di quest'ultimo.

Gli sviluppatori devono prestare particolare attenzione agli eventi StatefulRunAsyncSlowCancellation e StatefulRunAsyncFailure in quanto indicano la presenza di problemi relativi al servizio.

L'evento StatefulRunAsyncFailure viene emesso ogni volta che l'attività RunAsync() del servizio genera un'eccezione. Normalmente, la generazione di un'eccezione indica la presenza di un errore o di un bug nel servizio. Inoltre, l'eccezione causa un esito negativo del servizio, con conseguente spostamento a un altro nodo. L'operazione di spostamento del servizio può risultare costosa e causare un ritardo per le richieste in ingresso. Gli sviluppatori dovrebbero determinare la causa dell'eccezione e, se possibile, limitare il problema.

L'evento StatefulRunAsyncSlowCancellation viene emesso ogni volta che una richiesta di annullamento dell'attività RunAsync richiede più di quattro secondi. L'eccessiva durata di un'operazione di annullamento influisce sulla possibilità di riavviare rapidamente il servizio su un altro nodo. Questo scenario può avere un impatto negativo sulla disponibilità complessiva del servizio.

## <a name="performance-counters"></a>Contatori delle prestazioni
Il runtime di Reliable Services definisce le categorie di contatori delle prestazioni seguenti:

| Categoria | DESCRIZIONE |
| --- | --- |
| Replicatore transazionale di Service Fabric |Contatori specifici di Replicatore transazionale di Service Fabric di Azure |
| Service Fabric TStore |Contatori specifici di Azure Service Fabric TStore |

Replicatore transazionale di Service Fabric viene usato da [Reliable State Manager](service-fabric-reliable-services-reliable-collections-internals.md) per replicare le transazioni all'interno di un determinato set di [repliche](service-fabric-concepts-replica-lifecycle.md).

Service Fabric TStore è un componente usato nelle raccolte [Reliable Collections](service-fabric-reliable-services-reliable-collections-internals.md) per archiviare e recuperare le coppie chiave-valore.

L'applicazione [Performance Monitor di Windows](https://technet.microsoft.com/library/cc749249.aspx) , disponibile per impostazione predefinita nel sistema operativo Windows, può essere usata per raccogliere e visualizzare i dati dei contatori delle prestazioni. [Diagnostica di Azure](../cloud-services/cloud-services-dotnet-diagnostics.md) rappresenta una valida alternativa per la raccolta di tali dati e il relativo caricamento nelle tabelle di Azure.

### <a name="performance-counter-instance-names"></a>Nomi delle istanze dei contatori delle prestazioni
Un cluster con un numero elevato di servizi Reliable Services o di partizioni di servizi Reliable Services disporrà di un numero considerevole di istanze di contatori delle prestazioni di Replicatore transazionale. Questo avviene anche per i contatori delle prestazioni TStore, ma viene moltiplicato anche per il numero di oggetti Reliable Dictionaries e Reliable Queues usati. I nomi delle istanze dei contatori delle prestazioni consentono di identificare la [partizione](service-fabric-concepts-partitioning.md), la replica del servizio e lo stato del provider specifici nel caso di TStore, a cui l'istanza del contatore delle prestazioni è associata.

#### <a name="service-fabric-transactional-replicator-category"></a>Categoria Replicatore transazionale di Service Fabric
Per la categoria `Service Fabric Transactional Replicator`, i nomi delle istanze dei contatori sono nel formato seguente:

`ServiceFabricPartitionId:ServiceFabricReplicaId`

*ServiceFabricPartitionId* è la rappresentazione sotto forma di stringa dell'ID partizione di Service Fabric a cui è associata l'istanza del contatore delle prestazioni. L'ID partizione è un GUID e la relativa rappresentazione di stringa viene generata tramite [`Guid.ToString`](https://msdn.microsoft.com/library/97af8hh4.aspx) con l'identificatore di formato "D".

*ServiceFabricReplicaId* è l'ID associato a una determinata replica di un servizio Reliable Services. L'ID replica viene incluso nel nome dell'istanza del contatore delle prestazioni per assicurarne l'univocità ed evitare conflitti con altre istanze di contatori delle prestazioni generate dalla stessa partizione. Informazioni dettagliate sulle repliche e il relativo ruolo nei servizi Reliable Services sono disponibili [qui](service-fabric-concepts-replica-lifecycle.md).

Di seguito è riportato un esempio di un nome di istanza per un contatore appartenente alla categoria `Service Fabric Transactional Replicator`:

`00d0126d-3e36-4d68-98da-cc4f7195d85e:131652217797162571`

Nell'esempio precedente, `00d0126d-3e36-4d68-98da-cc4f7195d85e` è la rappresentazione sotto forma di stringa dell'ID partizione di Service Fabric e `131652217797162571` è l'ID replica.

#### <a name="service-fabric-tstore-category"></a>Categoria Service Fabric TStore
Per la categoria `Service Fabric TStore`, i nomi delle istanze dei contatori sono nel formato seguente:

`ServiceFabricPartitionId:ServiceFabricReplicaId:ServiceFabricStateProviderId_PerformanceCounterInstanceDifferentiator`

*ServiceFabricPartitionId* è la rappresentazione sotto forma di stringa dell'ID partizione di Service Fabric a cui è associata l'istanza del contatore delle prestazioni. L'ID partizione è un GUID e la relativa rappresentazione di stringa viene generata tramite [`Guid.ToString`](https://msdn.microsoft.com/library/97af8hh4.aspx) con l'identificatore di formato "D".

*ServiceFabricReplicaId* è l'ID associato a una determinata replica di un servizio Reliable Services. L'ID replica viene incluso nel nome dell'istanza del contatore delle prestazioni per assicurarne l'univocità ed evitare conflitti con altre istanze di contatori delle prestazioni generate dalla stessa partizione. Informazioni dettagliate sulle repliche e il relativo ruolo nei servizi Reliable Services sono disponibili [qui](service-fabric-concepts-replica-lifecycle.md).

*ServiceFabricStateProviderId* è l'ID associato a un provider di stato all'interno di un servizio Reliable Services. L'ID del provider di stato è incluso nel nome dell'istanza del contatore delle prestazioni per differenziare un TStore da un altro.

*PerformanceCounterInstanceDifferentiator* è un ID di differenziazione associato a un'istanza del contatore delle prestazioni all'interno di un provider di stato. Il differenziatore viene incluso nel nome dell'istanza del contatore delle prestazioni per assicurarne l'univocità ed evitare conflitti con altre istanze di contatori delle prestazioni generate dallo stesso provider di stato.

Di seguito è riportato un esempio di un nome di istanza per un contatore appartenente alla categoria `Service Fabric TStore`:

`00d0126d-3e36-4d68-98da-cc4f7195d85e:131652217797162571:142652217797162571_1337`

Nell'esempio precedente, `00d0126d-3e36-4d68-98da-cc4f7195d85e` è la rappresentazione in formato stringa dell'ID della partizione di Service Fabric, `131652217797162571` è l'ID della replica, `142652217797162571` è l'ID del provider di stato e `1337` è il differenziatore dell'istanza del contatore delle prestazioni.

### <a name="transactional-replicator-performance-counters"></a>Contatori delle prestazioni di Replicatore transazionale

Il runtime di Reliable Services genera gli eventi seguenti nella categoria `Service Fabric Transactional Replicator`

 Nome contatore | DESCRIZIONE |
| --- | --- |
| Operazioni di inizio transazione/sec | Numero di nuove transazioni di scrittura create al secondo.|
| Operazioni di transazione/sec | Numero di operazioni di aggiunta/aggiornamento/eliminazione eseguite su raccolte Reliable Collections al secondo.|
| Byte scaricamento log/sec | Numero di byte scaricati sul disco da Replicatore transazionale al secondo. |
| Operazioni limitate/sec | Numero di operazioni rifiutate al secondo da Replicatore transazionale a causa della limitazione. |
| Avg. transazione/commit (ms) | Latenza media commit per transazione in millisecondi |
| Avg. media flush (ms) | Durata media delle operazioni di flush su disco avviate da Replicatore transazionale in millisecondi |

### <a name="tstore-performance-counters"></a>Contatori delle prestazioni di TStore

Il runtime di Reliable Services genera gli eventi seguenti nella categoria `Service Fabric TStore`

 Nome contatore | DESCRIZIONE |
| --- | --- |
| Item Count | Il numero di elementi nell'archivio.|
| Dimensione disco | Le dimensioni totali del disco, in byte, dei file di checkpoint per l'archivio.|
| Byte scritti/sec per il file di checkpoint | Il numero di byte scritti al secondo per il file di checkpoint più recente.|
| Byte trasferiti su disco/sec per la copia | Il numero di byte letti (nella replica primaria) o scritti (in una replica secondaria) al secondo durante la copia nell'archivio.|

## <a name="next-steps"></a>Passaggi successivi
[Provider di EventSource in PerfView](https://blogs.msdn.microsoft.com/vancem/2012/07/09/introduction-tutorial-logging-etw-events-in-c-system-diagnostics-tracing-eventsource/)
