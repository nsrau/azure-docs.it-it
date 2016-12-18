---
title: Introduzione agli hub eventi in Java | Documentazione Microsoft
description: Eseguire questa esercitazione per iniziare a usare gli hub eventi di Azure, inviare gli eventi in Java e riceverli in C# mediante EventProcessorHost.
services: event-hubs
documentationcenter: 
author: jtaubensee
manager: timlt
editor: 
ms.assetid: 1c9988ca-1473-4287-a033-f99122c429bc
ms.service: event-hubs
ms.workload: core
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 09/27/2016
ms.author: jotaub;sethm
translationtype: Human Translation
ms.sourcegitcommit: 2ea002938d69ad34aff421fa0eb753e449724a8f
ms.openlocfilehash: bcc75a6cdba08c7c94a556826e98ef24c199c37f


---
# <a name="get-started-with-event-hubs"></a>Introduzione all'Hub eventi
[!INCLUDE [service-bus-selector-get-started](../../includes/service-bus-selector-get-started.md)]

## <a name="introduction"></a>Introduzione
Hub eventi è un sistema di inserimento a scalabilità elevata, in grado di accettare milioni di eventi al secondo, che permette a un'applicazione di elaborare e analizzare le elevate quantità di dati prodotti dalle applicazioni e dai dispositivi connessi. Dopo la raccolta nell'hub eventi, i dati possono essere trasformati e archiviati tramite qualsiasi provider di analisi in tempo reale o qualsiasi cluster di archiviazione.

Per altre informazioni, vedere [Panoramica di Hub eventi][Panoramica di Hub eventi].

Questa esercitazione illustra come inserire messaggi in Hub eventi usando un'applicazione console in Java e come recuperarli in parallelo usando la libreria dell'[Host processore di eventi][Host processore di eventi] in C#.

Per completare questa esercitazione, sono necessari gli elementi seguenti:

* Ambiente di sviluppo in Java. Per questa esercitazione si presuppone l'uso di [Eclipse](https://www.eclipse.org/).
* [Microsoft Visual Studio](http://visualstudio.com)
* Un account Azure attivo. <br/>Se non si ha un account, è possibile creare un account gratuito in pochi minuti. Per informazioni dettagliate, vedere la pagina relativa alla <a href="http://azure.microsoft.com/pricing/free-trial/?WT.mc_id=A0E0E5C02&amp;returnurl=http%3A%2F%2Fazure.microsoft.com%2Fen-us%2Fdevelop%2Fmobile%2Ftutorials%2Fget-started%2F" target="_blank">versione di valutazione gratuita di Azure</a>.

[!INCLUDE [event-hubs-create-event-hub](../../includes/event-hubs-create-event-hub.md)]

[!INCLUDE [service-bus-event-hubs-get-started-send-java](../../includes/service-bus-event-hubs-get-started-send-java.md)]

[!INCLUDE [service-bus-event-hubs-get-started-receive-ephcs](../../includes/service-bus-event-hubs-get-started-receive-ephcs.md)]

## <a name="run-the-applications"></a>Eseguire le applicazioni
A questo punto è possibile eseguire le applicazioni.

1. Eseguire il progetto **Receiver** da Visual Studio, quindi attendere che il progetto avvii i ricevitori per tutte le partizioni.
   
   ![][21]
2. Eseguire il progetto **Sender** .
   
   ![][22]

## <a name="next-steps"></a>Passaggi successivi
Ora che è stata creata un'applicazione che crea un Hub eventi e invia e riceve i dati, è possibile passare ai seguenti scenari:

* [applicazione di esempio completa che usa Hub eventi][applicazione di esempio completa che usa Hub eventi]
* [scalabilità orizzontale dell'elaborazione di eventi con l'Hub eventi][scalabilità orizzontale dell'elaborazione di eventi con l'Hub eventi]

Per ulteriori informazioni, vedere il [Centro per sviluppatori di Java](/develop/java/).

<!-- Images. -->
[21]: ./media/event-hubs-java-ephcs-getstarted/run-csharp-ephcs1.png
[22]: ./media/event-hubs-java-ephcs-getstarted/java-send.png

<!-- Links -->
[portale di Azure classico]: https://manage.windowsazure.com/
[Host processore di eventi]: https://www.nuget.org/packages/Microsoft.Azure.ServiceBus.EventProcessorHost
[Panoramica di Hub eventi]: event-hubs-overview.md
[applicazione di esempio completa che usa Hub eventi]: https://code.msdn.microsoft.com/Service-Bus-Event-Hub-286fd097
[scalabilità orizzontale dell'elaborazione di eventi con l'Hub eventi]: https://code.msdn.microsoft.com/Service-Bus-Event-Hub-45f43fc3



<!--HONumber=Nov16_HO3-->


