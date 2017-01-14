---
title: Introduzione all&quot;Hub eventi in C# con Apache Storm | Documentazione Microsoft
description: Eseguire questa esercitazione per iniziare a usare gli hub eventi di Azure, inviare gli eventi in C# e riceverli in un cluster di Apache Storm.
services: event-hubs
documentationcenter: 
author: jtaubensee
manager: timlt
editor: 
ms.assetid: f64cb2bd-2e38-4c78-8d0d-9af14c000d9f
ms.service: event-hubs
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 12/07/2016
ms.author: jotaub;sethm
translationtype: Human Translation
ms.sourcegitcommit: f603a3c3304af3e64ef40f2cdef3745e3b2085b3
ms.openlocfilehash: 508b1390bee1e61514e6e7499071d0503a051c10


---
# <a name="get-started-with-event-hubs"></a>Introduzione all'Hub eventi
[!INCLUDE [service-bus-selector-get-started](../../includes/service-bus-selector-get-started.md)]

## <a name="introduction"></a>Introduzione
Hub eventi è un sistema di inserimento a scalabilità elevata, in grado di accettare milioni di eventi al secondo, che permette a un'applicazione di elaborare e analizzare le elevate quantità di dati prodotti dalle applicazioni e dai dispositivi connessi. Dopo la raccolta nell'hub eventi, i dati possono essere trasformati e archiviati tramite qualsiasi provider di analisi in tempo reale o qualsiasi cluster di archiviazione.

Per altre informazioni, vedere [Panoramica di Hub eventi].

Questa esercitazione illustra come inserire messaggi in Hub eventi usando un'applicazione console in C# e come recuperarli in parallelo usando Apache Storm.

Per completare questa esercitazione, sono necessari gli elementi seguenti:

* [Microsoft Visual Studio](http://visualstudio.com)
* Un ambiente di sviluppo Java configurato per l'esecuzione di [Maven](http://maven.apache.org/). Per questa esercitazione si presuppone l'uso di [Eclipse](https://www.eclipse.org/)
* Un account Azure attivo. <br/>Se non si ha un account, è possibile creare un account gratuito in pochi minuti. Per informazioni dettagliate, vedere la pagina relativa alla <a href="http://azure.microsoft.com/pricing/free-trial/?WT.mc_id=A0E0E5C02&amp;returnurl=http%3A%2F%2Fazure.microsoft.com%2Fen-us%2Fdevelop%2Fmobile%2Ftutorials%2Fget-started%2F" target="_blank">versione di valutazione gratuita di Azure</a>.

[!INCLUDE [event-hubs-create-event-hub](../../includes/event-hubs-create-event-hub.md)]

[!INCLUDE [service-bus-event-hubs-get-started-send-csharp](../../includes/service-bus-event-hubs-get-started-send-csharp.md)]

[!INCLUDE [service-bus-event-hubs-get-started-receive-storm](../../includes/service-bus-event-hubs-get-started-receive-storm.md)]

## <a name="run-the-applications"></a>Eseguire le applicazioni
A questo punto è possibile eseguire le applicazioni.

1. Eseguire la classe **LogTopology** da Eclipse, quindi attendere l'avvio dei ricevitori per tutte le partizioni.
2. Eseguire il progetto **Sender** e premere **Invio** nella finestra della console. Gli eventi verranno visualizzati nella finestra del ricevitore.
   
   ![][22]

## <a name="next-steps"></a>Passaggi successivi
Ora che è stata creata un'applicazione che crea un Hub eventi e invia e riceve i dati, è possibile passare ai seguenti scenari:

* Un' [applicazione di esempio completa che usa Hub eventi][sample application that uses Event Hubs].
* Esempio relativo alla [scalabilità orizzontale dell'elaborazione di eventi con l'Hub eventi][Scale out Event Processing with Event Hubs].

<!-- Images. -->
[22]: ./media/event-hubs-csharp-storm-getstarted/receive-storm1.png

<!-- Links -->
[Panoramica di Hub eventi]: event-hubs-overview.md
[sample application that uses Event Hubs]: https://code.msdn.microsoft.com/Service-Bus-Event-Hub-286fd097
[Scale out Event Processing with Event Hubs]: https://code.msdn.microsoft.com/Service-Bus-Event-Hub-45f43fc3



<!--HONumber=Dec16_HO2-->


