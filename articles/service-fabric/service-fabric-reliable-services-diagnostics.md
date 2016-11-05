---
title: Diagnostica di Reliable Services con stato | Microsoft Docs
description: Funzionalità di diagnostica per Reliable Services con stato
services: service-fabric
documentationcenter: .net
author: AlanWarwick
manager: timlt
editor: ''

ms.service: Service-Fabric
ms.devlang: dotnet
ms.topic: article
ms.tgt_pltfrm: NA
ms.workload: NA
ms.date: 05/17/2016
ms.author: alanwar

---
# Funzionalità di diagnostica per Reliable Services con stato
La classe StatefulServiceBase di Reliable Services con stato emette eventi [EventSource](https://msdn.microsoft.com/library/system.diagnostics.tracing.eventsource.aspx) che possono essere usati per il debug del servizio, per fornire informazioni approfondite sul funzionamento del runtime e per facilitare la risoluzione dei problemi.

## Eventi EventSource
Il nome EventSource per la classe StatefulServiceBase di Reliable Services con stato è "Microsoft-ServiceFabric-Services". Gli eventi provenienti da questa origine eventi vengono visualizzati nella finestra degli [eventi di diagnostica](service-fabric-diagnostics-how-to-monitor-and-diagnose-services-locally.md#view-service-fabric-system-events-in-visual-studio) quando si esegue il [debug del servizio in Visual Studio](service-fabric-debugging-your-application.md).

Alcuni esempi di strumenti e tecnologie che consentono di raccogliere e/o visualizzare eventi EventSource sono [PerfView](http://www.microsoft.com/download/details.aspx?id=28567), [Diagnostica di Microsoft Azure](../cloud-services/cloud-services-dotnet-diagnostics.md) e [Microsoft TraceEvent Library](http://www.nuget.org/packages/Microsoft.Diagnostics.Tracing.TraceEvent).

## Eventi
| Nome evento | ID evento | Level | Descrizione evento |
| --- | --- | --- | --- |
| StatefulRunAsyncInvocation |1 |Informazioni |Emesso quando viene avviata l'attività RunAsync del servizio |
| StatefulRunAsyncCancellation |2 |Informazioni |Emesso quando viene annullata l'attività RunAsync del servizio |
| StatefulRunAsyncCompletion |3 |Informazioni |Emesso quando l'attività RunAsync del servizio è completata |
| StatefulRunAsyncSlowCancellation |4 |Avviso |Emesso quando l'annullamento dell'attività del servizio RunAsync richiede troppo tempo |
| StatefulRunAsyncFailure |5 |Errore |Emesso quando l'attività RunAsync del servizio genera un'eccezione |

## Interpretazione degli eventi
Gli eventi StatefulRunAsyncInvocation, StatefulRunAsyncCompletion e StatefulRunAsyncCancellation consentono allo sviluppatore di comprendere il ciclo di vita di un servizio e di definire la durata delle operazioni di avvio, annullamento e completamento del servizio stesso. Questo può essere utile per il debug dei problemi di un servizio e per la comprensione del ciclo di vita di quest'ultimo.

Gli sviluppatori devono prestare particolare attenzione agli eventi StatefulRunAsyncSlowCancellation e StatefulRunAsyncFailure in quanto indicano la presenza di problemi relativi al servizio.

L'evento StatefulRunAsyncFailure viene emesso ogni volta che l'attività RunAsync() del servizio genera un'eccezione. Normalmente, la generazione di un'eccezione indica la presenza di un errore o di un bug nel servizio. Inoltre, l'eccezione causa un esito negativo del servizio, con conseguente spostamento a un altro nodo. Lo spostamento del servizio può essere un'operazione costosa che può ritardare le richieste in ingresso. Gli sviluppatori dovrebbero determinare la causa dell'eccezione e, se possibile, limitare il problema.

L'evento StatefulRunAsyncSlowCancellation viene emesso ogni volta che una richiesta di annullamento dell'attività RunAsync richiede più di quattro secondi. L'eccessiva durata di un'operazione di annullamento influisce sulla possibilità di riavviare rapidamente il servizio su un altro nodo. Questo può avere un impatto negativo sulla disponibilità complessiva del servizio.

<!---HONumber=AcomDC_0518_2016-->