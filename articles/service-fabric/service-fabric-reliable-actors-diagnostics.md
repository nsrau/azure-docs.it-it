---
title: Monitoraggio e diagnostica degli attori | Documentazione Microsoft
description: "Questo articolo illustra le funzionalità di diagnostica e monitoraggio delle prestazioni disponibili nel runtime di Service Fabric Reliable Actors, inclusi gli eventi e i contatori delle prestazioni emessi dal runtime."
services: service-fabric
documentationcenter: .net
author: abhishekram
manager: timlt
editor: vturecek
ms.assetid: 1c229923-670a-4634-ad59-468ff781ad18
ms.service: service-fabric
ms.devlang: dotnet
ms.topic: article
ms.tgt_pltfrm: NA
ms.workload: NA
ms.date: 01/04/2017
ms.author: abhisram
translationtype: Human Translation
ms.sourcegitcommit: 3ed67788fbbe0c4fb820cfd1525d8c9ee5154446
ms.openlocfilehash: 1a8a36d3b6f14c75965afca970be808015059dbb


---
# <a name="diagnostics-and-performance-monitoring-for-reliable-actors"></a>Diagnostica e monitoraggio delle prestazioni per Reliable Actors
Il runtime di Reliable Actors rende disponibili eventi [EventSource](https://msdn.microsoft.com/library/system.diagnostics.tracing.eventsource.aspx) e [contatori delle prestazioni](https://msdn.microsoft.com/library/system.diagnostics.performancecounter.aspx), che forniscono informazioni utili per comprendere il funzionamento del runtime e per eseguire la risoluzione dei problemi e il monitoraggio delle prestazioni.

## <a name="eventsource-events"></a>Eventi EventSource
Il nome provider di EventSource per il runtime di Reliable Actors è "Microsoft-ServiceFabric-Actors". Gli eventi provenienti da questa origine vengono visualizzati nella finestra degli [eventi di diagnostica](service-fabric-diagnostics-how-to-monitor-and-diagnose-services-locally.md#view-service-fabric-system-events-in-visual-studio) quando l'applicazione attore viene sottoposta a [debug in Visual Studio](service-fabric-debugging-your-application.md).

Alcuni esempi di strumenti e tecnologie che consentono di raccogliere e/o visualizzare eventi EventSource sono [PerfView](http://www.microsoft.com/download/details.aspx?id=28567), [Diagnostica di Azure](../cloud-services/cloud-services-dotnet-diagnostics.md), [Semantic Logging](https://msdn.microsoft.com/library/dn774980.aspx) e [Microsoft TraceEvent Library](http://www.nuget.org/packages/Microsoft.Diagnostics.Tracing.TraceEvent).

### <a name="keywords"></a>Parole chiave
Tutti gli eventi appartenenti a EventSource di Reliable Actors sono associati a una o più parole chiave. Questo consente di filtrare gli eventi che vengono raccolti. Sono definiti i bit delle parole chiave seguenti:

| Bit | Descrizione |
| --- | --- |
| 0x1 |Set di eventi importanti che riepilogano il funzionamento del runtime di Fabric Actors. |
| 0x2 |Set di eventi che descrivono le chiamate ai metodi degli attori. Per altre informazioni, vedere l' [argomento introduttivo sugli attori](service-fabric-reliable-actors-introduction.md). |
| 0x4 |Set di eventi relativi allo stato dell'attore. Per altre informazioni, vedere l'argomento relativo alla [gestione dello stato degli attori](service-fabric-reliable-actors-state-management.md). |
| 0x8 |Set di eventi correlati alla concorrenza basata su turni nell'attore. Per altre informazioni, vedere l'argomento relativo alla [concorrenza](service-fabric-reliable-actors-introduction.md#concurrency). |

## <a name="performance-counters"></a>Contatori delle prestazioni
Il runtime di Reliable Actors definisce le categorie di contatori delle prestazioni seguenti.

| Categoria | Descrizione |
| --- | --- |
| Service Fabric Actor |Contatori specifici degli attori di Azure Service Fabric, ad esempio per il tempo necessario per salvare lo stato di un attore |
| Service Fabric Actor Method |Contatori specifici dei metodi implementati dagli attori di Service Fabric, ad esempio per la frequenza con cui viene richiamato un metodo di un attore |

Ognuna di queste categorie include uno o più contatori.

L'applicazione [Performance Monitor di Windows](https://technet.microsoft.com/library/cc749249.aspx) , disponibile per impostazione predefinita nel sistema operativo Windows, può essere usata per raccogliere e visualizzare i dati dei contatori delle prestazioni. [Diagnostica di Azure](../cloud-services/cloud-services-dotnet-diagnostics.md) rappresenta una valida alternativa per la raccolta di tali dati e il relativo caricamento nelle tabelle di Azure.

### <a name="performance-counter-instance-names"></a>Nomi delle istanze dei contatori delle prestazioni
Un cluster con un numero elevato di servizi attore o di partizioni di servizi attore disporrà di un numero considerevole di istanze di contatori delle prestazioni degli attori. I nomi delle istanze possono facilitare l'identificazione della [partizione](service-fabric-reliable-actors-platform.md#service-fabric-partition-concepts-for-actors) specifica e del metodo dell'attore (se applicabile) a cui l'istanza è associata.

#### <a name="service-fabric-actor-category"></a>Categoria Service Fabric Actor
Per la categoria `Service Fabric Actor`, i nomi delle istanze dei contatori sono nel formato seguente:

`ServiceFabricPartitionID_ActorsRuntimeInternalID`

*ServiceFabricPartitionID* è la rappresentazione sotto forma di stringa dell'ID partizione di Service Fabric a cui è associata l'istanza del contatore delle prestazioni. L'ID partizione è un GUID e la relativa rappresentazione di stringa viene generata tramite il metodo [`Guid.ToString`](https://msdn.microsoft.com/library/97af8hh4.aspx) con l'identificatore di formato "D".

*ActorRuntimeInternalID* è la rappresentazione sotto forma di stringa di un numero intero a 64 bit generato dal runtime di Fabric Actors per l'uso interno. Viene incluso nel nome dell'istanza del contatore delle prestazioni per assicurarne l'univocità ed evitare conflitti con altri nomi di istanze di contatori delle prestazioni. Agli utenti non serve tentare di interpretare questa parte del nome dell'istanza del contatore delle prestazioni.

Di seguito è riportato un esempio di un nome di istanza per un contatore appartenente alla categoria `Service Fabric Actor` :

`2740af29-78aa-44bc-a20b-7e60fb783264_635650083799324046`

Nell'esempio precedente `2740af29-78aa-44bc-a20b-7e60fb783264` è la rappresentazione di stringa dell'ID partizione di Service Fabric e `635650083799324046` è l'ID a 64 bit generato per l'uso interno del runtime.

#### <a name="service-fabric-actor-method-category"></a>Categoria Service Fabric Actor Method
Per la categoria `Service Fabric Actor Method`, i nomi delle istanze dei contatori sono nel formato seguente:

`MethodName_ActorsRuntimeMethodId_ServiceFabricPartitionID_ActorsRuntimeInternalID`

*MethodName* è il nome del metodo dell'attore a cui è associata l'istanza del contatore delle prestazioni. Il formato del nome del metodo è determinato in base a una parte della logica del runtime di Fabric Actors che cerca di trovare un compromesso tra la leggibilità del nome e i vincoli relativi alla lunghezza massima dei nomi delle istanze dei contatori delle prestazioni in Windows.

*ActorsRuntimeMethodId* è la rappresentazione sotto forma di stringa di un numero intero a 32 bit generato dal runtime di Fabric Actors per l'uso interno. Viene incluso nel nome dell'istanza del contatore delle prestazioni per assicurarne l'univocità ed evitare conflitti con altri nomi di istanze di contatori delle prestazioni. Agli utenti non serve tentare di interpretare questa parte del nome dell'istanza del contatore delle prestazioni.

*ServiceFabricPartitionID* è la rappresentazione sotto forma di stringa dell'ID partizione di Service Fabric a cui è associata l'istanza del contatore delle prestazioni. L'ID partizione è un GUID e la relativa rappresentazione di stringa viene generata tramite il metodo [`Guid.ToString`](https://msdn.microsoft.com/library/97af8hh4.aspx) con l'identificatore di formato "D".

*ActorRuntimeInternalID* è la rappresentazione sotto forma di stringa di un numero intero a 64 bit generato dal runtime di Fabric Actors per l'uso interno. Viene incluso nel nome dell'istanza del contatore delle prestazioni per assicurarne l'univocità ed evitare conflitti con altri nomi di istanze di contatori delle prestazioni. Agli utenti non serve tentare di interpretare questa parte del nome dell'istanza del contatore delle prestazioni.

Di seguito è riportato un esempio di un nome di istanza per un contatore appartenente alla categoria `Service Fabric Actor Method` :

`ivoicemailboxactor.leavemessageasync_2_89383d32-e57e-4a9b-a6ad-57c6792aa521_635650083804480486`

Nell'esempio precedente `ivoicemailboxactor.leavemessageasync` è il nome del metodo, `2` è l'ID a 32 bit generato per l'uso interno del runtime, `89383d32-e57e-4a9b-a6ad-57c6792aa521` è la rappresentazione di stringa dell'ID partizione di Service Fabric e `635650083804480486` è l'ID a 64 bit generato per l'uso interno del runtime.

## <a name="list-of-events-and-performance-counters"></a>Elenco degli eventi e dei contatori delle prestazioni
### <a name="actor-method-events-and-performance-counters"></a>Eventi dei metodi degli attori e relativi contatori delle prestazioni
Il runtime di Reliable Actors rende disponibili gli eventi seguenti relativi ai [metodi degli attori](service-fabric-reliable-actors-introduction.md).

| Nome evento | ID evento | Level | Parole chiave | Descrizione |
| --- | --- | --- | --- | --- |
| ActorMethodStart |7 |Dettagliato |0x2 |Il runtime di Actors sta per richiamare un metodo di un attore. |
| ActorMethodStop |8 |Dettagliato |0x2 |È terminata l'esecuzione di un metodo di un attore, ovvero la chiamata asincrona del runtime al metodo ha restituito il controllo e l'attività restituita dal metodo stesso è stata completata. |
| ActorMethodThrewException |9 |Avviso |0x3 |È stata generata un'eccezione durante l'esecuzione di un metodo di un attore durante la chiamata asincrona del runtime al metodo o durante l'esecuzione dell'attività restituita dal metodo stesso. Questo evento indica un problema relativo al codice dell'attore che deve essere analizzato. |

Il runtime di Reliable Actors pubblica i contatori delle prestazioni seguenti relativi all'esecuzione dei metodi degli attori.

| Nome categoria | Nome contatore | Descrizione |
| --- | --- | --- |
| Service Fabric Actor Method |Invocations/Sec |Numero di volte in cui il metodo del servizio attore viene richiamato al secondo |
| Service Fabric Actor Method |Average milliseconds per invocation |Tempo necessario per eseguire il metodo del servizio attore in millisecondi |
| Service Fabric Actor Method |Exceptions thrown/Sec |Numero di volte in cui il metodo del servizio attore ha generato un'eccezione al secondo |

### <a name="concurrency-events-and-performance-counters"></a>Eventi di concorrenza e relativi contatori delle prestazioni
Il runtime di Reliable Actors emette gli eventi seguenti relativi alla [concorrenza](service-fabric-reliable-actors-introduction.md#concurrency).

| Nome evento | ID evento | Level | Parole chiave | Descrizione |
| --- | --- | --- | --- | --- |
| ActorMethodCallsWaitingForLock |12 |Dettagliato |0x8 |Questo evento viene scritto all'inizio di ogni nuovo turno in un attore. Include il numero di chiamate dell'attore in sospeso, in attesa di acquisire il blocco per attore che applica la concorrenza basata su turni. |

Il runtime di Reliable Actors pubblica i contatori delle prestazioni seguenti relativi alla concorrenza.

| Nome categoria | Nome contatore | Descrizione |
| --- | --- | --- |
| Service Fabric Actor |# of actor calls waiting for actor lock |Numero di chiamate dell'attore in sospeso, in attesa di acquisire il blocco per attore che applica la concorrenza basata su turni. |
| Service Fabric Actor |Average milliseconds per lock wait |Tempo richiesto (in millisecondi) per acquisire il blocco per attore che applica la concorrenza basata su turni. |
| Service Fabric Actor |Average milliseconds actor lock held |Tempo (in millisecondi) per il quale il blocco per attore è attivato |

### <a name="actor-state-management-events-and-performance-counters"></a>Eventi di gestione dello stato degli attori e relativi contatori delle prestazioni
Il runtime di Reliable Actors emette gli eventi seguenti relativi alla [gestione dello stato degli attori](service-fabric-reliable-actors-state-management.md).

| Nome evento | ID evento | Level | Parole chiave | Descrizione |
| --- | --- | --- | --- | --- |
| ActorSaveStateStart |10 |Dettagliato |0x4 |Il runtime di Actors sta per salvare lo stato di un attore. |
| ActorSaveStateStop |11 |Dettagliato |0x4 |Il runtime di Actors ha finito di salvare lo stato di un attore. |

Il runtime di Reliable Actors pubblica i contatori delle prestazioni seguenti relativi alla gestione dello stato degli attori.

| Nome categoria | Nome contatore | Descrizione |
| --- | --- | --- |
| Service Fabric Actor |Average milliseconds per save state operation |Tempo necessario per salvare lo stato dell'attore in millisecondi |
| Service Fabric Actor |Average milliseconds per load state operation |Tempo richiesto per caricare lo stato dell'attore in millisecondi |

### <a name="events-related-to-actor-replicas"></a>Eventi relativi alle repliche di attori
Il runtime di Reliable Actors rende disponibili gli eventi seguenti relativi alle [repliche di attori](service-fabric-reliable-actors-platform.md#service-fabric-partition-concepts-for-actors).

| Nome evento | ID evento | Level | Parole chiave | Descrizione |
| --- | --- | --- | --- | --- |
| ReplicaChangeRoleToPrimary |1 |Informazioni |0x1 |Il ruolo della replica dell'attore è cambiato e la replica è diventata primaria. Ciò significa che gli attori per questa partizione verranno creati all'interno della replica. |
| ReplicaChangeRoleFromPrimary |2 |Informazioni |0x1 |Il ruolo della replica dell'attore è cambiato e la replica non è più primaria. Ciò significa che gli attori per questa partizione non verranno più creati all'interno della replica. Non verranno recapitate nuove richieste agli attori già creati all'interno della replica. Gli attori verranno distrutti dopo il completamento di qualsiasi richiesta in corso. |

### <a name="actor-activation-and-deactivation-events-and-performance-counters"></a>Eventi di attivazione e disattivazione degli attori e contatori delle prestazioni
Il runtime di Reliable Actors emette gli eventi seguenti relativi all' [attivazione e disattivazione degli attori](service-fabric-reliable-actors-lifecycle.md).

| Nome evento | ID evento | Level | Parole chiave | Descrizione |
| --- | --- | --- | --- | --- |
| ActorActivated |5 |Informazioni |0x1 |Un attore è stato attivato. |
| ActorDeactivated |6 |Informazioni |0x1 |Un attore è stato disattivato. |

Il runtime di Reliable Actors pubblica i contatori delle prestazioni seguenti relativi all’attivazione e alla disattivazione degli attori.

| Nome categoria | Nome contatore | Descrizione |
| --- | --- | --- |
| Service Fabric Actor |Average OnActivateAsync milliseconds |Tempo necessario per eseguire il metodo OnActivateAsync in millisecondi |

### <a name="actor-request-processing-performance-counters"></a>Contatori delle prestazioni di elaborazione delle richieste degli attori
Quando un client richiama un metodo tramite un oggetto proxy di attore, viene generato un messaggio di richiesta inviato in rete al servizio attore. Il servizio elabora il messaggio di richiesta e invia una risposta al client. Il runtime di Reliable Actors pubblica i contatori delle prestazioni seguenti relativi all’elaborazione delle richieste degli attori.

| Nome categoria | Nome contatore | Descrizione |
| --- | --- | --- |
| Service Fabric Actor |# of outstanding requests |Numero di richieste elaborate nel servizio |
| Service Fabric Actor |Average milliseconds per request |Tempo richiesto (in millisecondi) dal servizio per elaborare una richiesta |
| Service Fabric Actor |Average milliseconds for request deserialization |Tempo richiesto (in millisecondi) per deserializzare il messaggio di richiesta dell’attore quando viene ricevuto nel servizio |
| Service Fabric Actor |Average milliseconds for response serialization |Tempo richiesto (in millisecondi) per serializzare il messaggio di risposta dell’attore nel servizio prima dell’invio della risposta al client |

## <a name="next-steps"></a>Passaggi successivi
* [Modalità d'uso della piattaforma Service Fabric da parte di Reliable Actors](service-fabric-reliable-actors-platform.md)
* [Documentazione di riferimento delle API di Actors](https://msdn.microsoft.com/library/azure/dn971626.aspx)
* [Codice di esempio](https://github.com/Azure/servicefabric-samples)
* [Provider di EventSource in PerfView](https://blogs.msdn.microsoft.com/vancem/2012/07/09/introduction-tutorial-logging-etw-events-in-c-system-diagnostics-tracing-eventsource/)



<!--HONumber=Jan17_HO1-->


