---
title: Diagnostica di Reliable Services con stato | Documentazione Microsoft
description: "Funzionalità di diagnostica per i servizi Reliable con stato"
services: service-fabric
documentationcenter: .net
author: dkkapur
manager: timlt
editor: 
ms.assetid: ae0e8f99-69ab-4d45-896d-1fa80ed45659
ms.service: Service-Fabric
ms.devlang: dotnet
ms.topic: article
ms.tgt_pltfrm: NA
ms.workload: NA
ms.date: 06/30/2017
ms.author: dekapur
ms.translationtype: Human Translation
ms.sourcegitcommit: 3ed67788fbbe0c4fb820cfd1525d8c9ee5154446
ms.openlocfilehash: 5da5707900b35be69018ba4e9c8efb05d8a74a9d
ms.contentlocale: it-it
ms.lasthandoff: 01/05/2017


---
# <a name="diagnostic-functionality-for-stateful-reliable-services"></a>Funzionalità di diagnostica per Reliable Services con stato
La classe StatefulServiceBase di Reliable Services con stato emette eventi [EventSource](https://msdn.microsoft.com/library/system.diagnostics.tracing.eventsource.aspx) che possono essere usati per il debug del servizio, per fornire informazioni approfondite sul funzionamento del runtime e per facilitare la risoluzione dei problemi.

## <a name="eventsource-events"></a>Eventi EventSource
Il nome EventSource per la classe StatefulServiceBase di Reliable Services con stato è "Microsoft-ServiceFabric-Services". Gli eventi provenienti da questa origine eventi vengono visualizzati nella finestra [Diagnostics Events](service-fabric-diagnostics-how-to-monitor-and-diagnose-services-locally.md#view-service-fabric-system-events-in-visual-studio) (Eventi di diagnostica) quando si esegue il [debug del servizio in Visual Studio](service-fabric-debugging-your-application.md).

Alcuni esempi di strumenti e tecnologie che consentono di raccogliere e/o visualizzare eventi EventSource sono [PerfView](http://www.microsoft.com/download/details.aspx?id=28567), [Diagnostica di Microsoft Azure](../cloud-services/cloud-services-dotnet-diagnostics.md) e [Microsoft TraceEvent Library](http://www.nuget.org/packages/Microsoft.Diagnostics.Tracing.TraceEvent).

## <a name="events"></a>Events
| Nome evento | ID evento | Level | Descrizione evento |
| --- | --- | --- | --- |
| StatefulRunAsyncInvocation |1 |Informazioni |Emesso quando viene avviata l'attività RunAsync del servizio |
| StatefulRunAsyncCancellation |2 |Informazioni |Emesso quando viene annullata l'attività RunAsync del servizio |
| StatefulRunAsyncCompletion |3 |Informazioni |Emesso quando l'attività RunAsync del servizio è completata |
| StatefulRunAsyncSlowCancellation |4 |Avviso |Emesso quando l'annullamento dell'attività del servizio RunAsync richiede troppo tempo |
| StatefulRunAsyncFailure |5 |Errore |Emesso quando l'attività RunAsync del servizio genera un'eccezione |

## <a name="interpret-events"></a>Interpretazione degli eventi
Gli eventi StatefulRunAsyncInvocation, StatefulRunAsyncCompletion e StatefulRunAsyncCancellation consentono allo sviluppatore di comprendere il ciclo di vita di un servizio e di definire la durata delle operazioni di avvio, annullamento e completamento del servizio stesso. Questo può essere utile per il debug dei problemi di un servizio e per la comprensione del ciclo di vita di quest'ultimo.

Gli sviluppatori devono prestare particolare attenzione agli eventi StatefulRunAsyncSlowCancellation e StatefulRunAsyncFailure in quanto indicano la presenza di problemi relativi al servizio.

L'evento StatefulRunAsyncFailure viene emesso ogni volta che l'attività RunAsync() del servizio genera un'eccezione. Normalmente, la generazione di un'eccezione indica la presenza di un errore o di un bug nel servizio. Inoltre, l'eccezione causa un esito negativo del servizio, con conseguente spostamento a un altro nodo. Lo spostamento del servizio può essere un'operazione costosa che può ritardare le richieste in ingresso. Gli sviluppatori dovrebbero determinare la causa dell'eccezione e, se possibile, limitare il problema.

L'evento StatefulRunAsyncSlowCancellation viene emesso ogni volta che una richiesta di annullamento dell'attività RunAsync richiede più di quattro secondi. L'eccessiva durata di un'operazione di annullamento influisce sulla possibilità di riavviare rapidamente il servizio su un altro nodo. Questo può avere un impatto negativo sulla disponibilità complessiva del servizio.

## <a name="next-steps"></a>Passaggi successivi
* [Provider di EventSource in PerfView](https://blogs.msdn.microsoft.com/vancem/2012/07/09/introduction-tutorial-logging-etw-events-in-c-system-diagnostics-tracing-eventsource/)

