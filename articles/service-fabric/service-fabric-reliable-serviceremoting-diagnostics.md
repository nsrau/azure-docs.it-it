---
title: Monitoraggio e diagnostica di Azure ServiceFabric | Microsoft Docs
description: "Questo articolo illustra le funzionalità monitoraggio delle prestazioni disponibili nel runtime di Service Fabric Reliable ServiceRemoting, inclusi i contatori delle prestazioni emessi dal runtime."
services: service-fabric
documentationcenter: .net
author: suchiagicha
manager: timlt
editor: suchiagicha
ms.assetid: 1c229923-670a-4634-ad59-468ff781ad18
ms.service: service-fabric
ms.devlang: dotnet
ms.topic: article
ms.tgt_pltfrm: NA
ms.workload: NA
ms.date: 06/29/2017
ms.author: suchiagicha
ms.translationtype: HT
ms.sourcegitcommit: 7bf5d568e59ead343ff2c976b310de79a998673b
ms.openlocfilehash: f54e157654fb15d2f7ff48ddc666c6c8803c75a2
ms.contentlocale: it-it
ms.lasthandoff: 08/01/2017

---
# <a name="diagnostics-and-performance-monitoring-for-reliable-service-remoting"></a>Diagnostica e monitoraggio delle prestazioni per Reliable Service Remoting
Il runtime Reliable ServiceRemoting genera [i contatori delle prestazioni](https://msdn.microsoft.com/library/system.diagnostics.performancecounter.aspx) che forniscono informazioni utili per comprendere il funzionamento di ServiceRemoting e per eseguire la risoluzione dei problemi e il monitoraggio delle prestazioni.


## <a name="performance-counters"></a>Contatori delle prestazioni
Il runtime di Reliable ServiceRemoting definisce le categorie di contatori delle prestazioni seguenti.

| Categoria | Descrizione |
| --- | --- |
| Servizio Service Fabric |Contatori specifici per Azure Service Fabric Serivce Remoting, ad esempio tempo medio impiegato per elaborare la richiesta |
| Metodo del servizio Service Fabric |Contatori specifici dei metodi implementati da Service Fabric Remoting Service, ad esempio per la frequenza con cui viene richiamato un metodo di un servizio |

Ognuna di queste categorie include uno o più contatori.

L'applicazione [Performance Monitor di Windows](https://technet.microsoft.com/library/cc749249.aspx) , disponibile per impostazione predefinita nel sistema operativo Windows, può essere usata per raccogliere e visualizzare i dati dei contatori delle prestazioni. [Diagnostica di Azure](../cloud-services/cloud-services-dotnet-diagnostics.md) rappresenta una valida alternativa per la raccolta di tali dati e il relativo caricamento nelle tabelle di Azure.

### <a name="performance-counter-instance-names"></a>Nomi delle istanze dei contatori delle prestazioni
Un cluster con un numero elevato di servizi o partizioni di ServiceRemoting disporrà di un numero considerevole di istanze di contatori delle prestazioni. I nomi delle istanze possono facilitare l'identificazione della partizione specifica e del metodo del servizio (se applicabile) a cui l'istanza è associata.

#### <a name="service-fabric-service-category"></a>Categoria del servizio Service Fabric
Per la categoria `Service Fabric Service`, i nomi delle istanze dei contatori sono nel formato seguente:

`ServiceFabricPartitionID_ServiceReplicaOrInstanceId_ServiceRuntimeInternalID`

*ServiceFabricPartitionID* è la rappresentazione sotto forma di stringa dell'ID partizione di Service Fabric a cui è associata l'istanza del contatore delle prestazioni. L'ID partizione è un GUID e la relativa rappresentazione di stringa viene generata tramite il metodo [`Guid.ToString`](https://msdn.microsoft.com/library/97af8hh4.aspx) con l'identificatore di formato "D".

*ServiceReplicaOrInstanceId* è la rappresentazione sotto forma di stringa di Service Fabric Replica/ID dell'istanza a cui è associata l'istanza del contatore delle prestazioni.

*ServiceRuntimeInternalID* è la rappresentazione sotto forma di stringa di un numero intero a 64 bit generato dal runtime di Fabric Service per l'uso interno. Viene incluso nel nome dell'istanza del contatore delle prestazioni per assicurarne l'univocità ed evitare conflitti con altri nomi di istanze di contatori delle prestazioni. Agli utenti non serve tentare di interpretare questa parte del nome dell'istanza del contatore delle prestazioni.

Di seguito è riportato un esempio di un nome di istanza per un contatore appartenente alla categoria `Service Fabric Service` :

`2740af29-78aa-44bc-a20b-7e60fb783264_635650083799324046_5008379932`

Nell'esempio precedente `2740af29-78aa-44bc-a20b-7e60fb783264` è la rappresentazione sotto forma di stringa dell'ID partizione di Service Fabric, `635650083799324046` è la rappresentazione sotto forma di stringa di Replica/InstanceId e `5008379932` è l'ID a 64 bit generato per l'uso interno del runtime.

#### <a name="service-fabric-service-method-category"></a>Categoria del metodo del servizio Service Fabric
Per la categoria `Service Fabric Service Method`, i nomi delle istanze dei contatori sono nel formato seguente:

`MethodName_ServiceRuntimeMethodId_ServiceFabricPartitionID_ServiceReplicaOrInstanceId_ServiceRuntimeInternalID`

*MethodName* è il nome del metodo del servizio a cui è associata l'istanza del contatore delle prestazioni. Il formato del nome del metodo è determinato in base a una parte della logica del runtime di Fabric Service che cerca di trovare un compromesso tra la leggibilità del nome e i vincoli relativi alla lunghezza massima dei nomi delle istanze dei contatori delle prestazioni in Windows.

*ServiceRuntimeMethodId* è la rappresentazione sotto forma di stringa di un numero intero a 32 bit generato dal runtime di Fabric Service per l'uso interno. Viene incluso nel nome dell'istanza del contatore delle prestazioni per assicurarne l'univocità ed evitare conflitti con altri nomi di istanze di contatori delle prestazioni. Agli utenti non serve tentare di interpretare questa parte del nome dell'istanza del contatore delle prestazioni.

*ServiceFabricPartitionID* è la rappresentazione sotto forma di stringa dell'ID partizione di Service Fabric a cui è associata l'istanza del contatore delle prestazioni. L'ID partizione è un GUID e la relativa rappresentazione di stringa viene generata tramite il metodo [`Guid.ToString`](https://msdn.microsoft.com/library/97af8hh4.aspx) con l'identificatore di formato "D".

*ServiceReplicaOrInstanceId* è la rappresentazione sotto forma di stringa di Service Fabric Replica/ID dell'istanza a cui è associata l'istanza del contatore delle prestazioni.

*ServiceRuntimeInternalID* è la rappresentazione sotto forma di stringa di un numero intero a 64 bit generato dal runtime di Fabric Service per l'uso interno. Viene incluso nel nome dell'istanza del contatore delle prestazioni per assicurarne l'univocità ed evitare conflitti con altri nomi di istanze di contatori delle prestazioni. Agli utenti non serve tentare di interpretare questa parte del nome dell'istanza del contatore delle prestazioni.

Di seguito è riportato un esempio di un nome di istanza per un contatore appartenente alla categoria `Service Fabric Service Method` :

`ivoicemailboxservice.leavemessageasync_2_89383d32-e57e-4a9b-a6ad-57c6792aa521_635650083804480486_5008380`

Nell'esempio precedente `ivoicemailboxservice.leavemessageasync` è il nome del metodo, `2` è l'ID a 32 bit generato per l'uso interno del runtime, `89383d32-e57e-4a9b-a6ad-57c6792aa521` è la rappresentazione sotto forma di stringa dell'ID partizione di Service Fabric, `635650083804480486` è la rappresentazione sotto forma di stringa di Service Fabric Replica/ID istanza e `5008380` è l'ID a 64 bit generato per l'uso interno del runtime.

## <a name="list-of-performance-counters"></a>Elenco di contatori delle prestazioni
### <a name="service-method-performance-counters"></a>Contatori delle prestazioni dei metodi servizi

Il runtime di Reliable Service pubblica i contatori delle prestazioni seguenti relativi all'esecuzione dei metodi dei servizi.

| Nome categoria | Nome contatore | Descrizione |
| --- | --- | --- |
| Metodo del servizio Service Fabric |Invocations/Sec |Numero di volte in cui il metodo del servizio viene richiamato al secondo |
| Metodo del servizio Service Fabric |Average milliseconds per invocation |Tempo necessario per eseguire il metodo del servizio in millisecondi |
| Metodo del servizio Service Fabric |Exceptions thrown/Sec |Numero di volte in cui il metodo del servizio attore ha generato un'eccezione al secondo |

### <a name="service-request-processing-performance-counters"></a>Contatori delle prestazioni di elaborazione delle richieste dei servizi
Quando un client richiama un metodo tramite un oggetto proxy di servizio, viene generato un messaggio di richiesta inviato in rete al servizio remoting. Il servizio elabora il messaggio di richiesta e invia una risposta al client. Il runtime di Reliable ServiceRemoting pubblica i contatori delle prestazioni seguenti relativi all’elaborazione delle richieste dei servizi.

| Nome categoria | Nome contatore | Descrizione |
| --- | --- | --- |
| Servizio Service Fabric |# of outstanding requests |Numero di richieste elaborate nel servizio |
| Servizio Service Fabric |Average milliseconds per request |Tempo richiesto (in millisecondi) dal servizio per elaborare una richiesta |
| Servizio Service Fabric |Average milliseconds for request deserialization |Tempo richiesto (in millisecondi) per deserializzare il messaggio di richiesta del servizio quando viene ricevuto nel servizio |
| Servizio Service Fabric |Average milliseconds for response serialization |Tempo richiesto (in millisecondi) per serializzare il messaggio di risposta del servizio nel servizio prima dell’invio della risposta al client |

## <a name="next-steps"></a>Passaggi successivi
* [Codice di esempio](https://github.com/Azure/servicefabric-samples)
* [Provider di EventSource in PerfView](https://blogs.msdn.microsoft.com/vancem/2012/07/09/introduction-tutorial-logging-etw-events-in-c-system-diagnostics-tracing-eventsource/)

