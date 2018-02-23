---
title: Diagnostica per i servizi Reliable con stato in Azure Service Fabric | Microsoft Docs
description: "Funzionalità di diagnostica per i servizi Reliable Services con stato in Azure Service Fabric"
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
ms.date: 10/15/2017
ms.author: dekapur
ms.openlocfilehash: edcaaaf8f1619082b33195aedf1fb1abf32e85b1
ms.sourcegitcommit: 804db51744e24dca10f06a89fe950ddad8b6a22d
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 10/30/2017
---
# <a name="diagnostic-functionality-for-stateful-reliable-services"></a>Funzionalità di diagnostica per i servizi Reliable con stato
La classe StatefulServiceBase dei servizi Reliable con stato in Azure Service Fabric genera eventi [EventSource](https://msdn.microsoft.com/library/system.diagnostics.tracing.eventsource.aspx) che possono essere usati per eseguire il debug del servizio, ottenere informazioni dettagliate sul funzionamento del runtime e ottenere assistenza per la risoluzione dei problemi.

## <a name="eventsource-events"></a>Eventi EventSource
Il nome EventSource per la classe StatefulServiceBase dei servizi Reliable con stato è "Microsoft-ServiceFabric-Services". Gli eventi provenienti da questa origine eventi vengono visualizzati nella finestra [Diagnostics Events](service-fabric-diagnostics-how-to-monitor-and-diagnose-services-locally.md#view-service-fabric-system-events-in-visual-studio) (Eventi di diagnostica) quando si esegue il [debug del servizio in Visual Studio](service-fabric-debugging-your-application.md).

Esempi di strumenti e tecnologie che consentono di raccogliere e/o visualizzare eventi EventSource sono [PerfView](http://www.microsoft.com/download/details.aspx?id=28567), [Diagnostica di Azure](../cloud-services/cloud-services-dotnet-diagnostics.md) e la [libreria TraceEvent di Microsoft](http://www.nuget.org/packages/Microsoft.Diagnostics.Tracing.TraceEvent).

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

## <a name="next-steps"></a>Passaggi successivi
[Provider di EventSource in PerfView](https://blogs.msdn.microsoft.com/vancem/2012/07/09/introduction-tutorial-logging-etw-events-in-c-system-diagnostics-tracing-eventsource/)
