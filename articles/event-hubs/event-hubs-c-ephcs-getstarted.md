---
title: Introduzione all&quot;Hub eventi in C e C# | Documentazione Microsoft
description: Eseguire questa esercitazione per iniziare a usare gli hub eventi di Azure, inviare gli eventi in C e riceverli in C# mediante EventProcessorHost.
services: event-hubs
documentationcenter: 
author: jtaubensee
manager: timlt
editor: 
ms.assetid: 5170c138-39ec-4eea-9925-e6902e5c425a
ms.service: event-hubs
ms.workload: na
ms.tgt_pltfrm: c
ms.devlang: csharp
ms.topic: article
ms.date: 11/30/2016
ms.author: jotaub;sethm
translationtype: Human Translation
ms.sourcegitcommit: fd7450985906dcf707a420d51b3a3f3b13786741
ms.openlocfilehash: 742bea46f7c4eba826523a49af4972dbb6db439b


---
# <a name="get-started-with-event-hubs"></a>Introduzione all'Hub eventi
[!INCLUDE [service-bus-selector-get-started](../../includes/service-bus-selector-get-started.md)]

## <a name="introduction"></a>Introduzione
Hub eventi è un sistema di inserimento a scalabilità elevata, in grado di accettare milioni di eventi al secondo, che permette a un'applicazione di elaborare e analizzare le elevate quantità di dati prodotti dalle applicazioni e dai dispositivi connessi. Dopo la raccolta nell'hub eventi, i dati possono essere trasformati e archiviati tramite qualsiasi provider di analisi in tempo reale o qualsiasi cluster di archiviazione.

Per altre informazioni, vedere [Panoramica di Hub eventi][Event Hubs overview].

Questa esercitazione illustra come inserire messaggi in Hub eventi usando un'applicazione console in C# e come recuperarli in parallelo usando la libreria dell'[host processore di eventi][Event Processor Host] in C#.

Per completare questa esercitazione, sono necessari gli elementi seguenti:

* Ambiente di sviluppo in C. Per questa esercitazione si presuppone l'uso di uno stack gcc in una [macchina virtuale Linux di Azure](../virtual-machines/virtual-machines-linux-quick-create-cli.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json) con Ubuntu 14.04. Le istruzioni per gli altri ambienti saranno fornite nei collegamenti esterni.
* Microsoft Visual Studio Express per Windows
* Un account Azure attivo. Se non si dispone di un account, è possibile creare un account di valutazione gratuita in pochi minuti. Per informazioni dettagliate, vedere la pagina relativa alla [versione di valutazione gratuita di Azure](https://azure.microsoft.com/pricing/free-trial/).

[!INCLUDE [event-hubs-create-event-hub](../../includes/event-hubs-create-event-hub.md)]

[!INCLUDE [service-bus-event-hubs-get-started-send-c](../../includes/service-bus-event-hubs-get-started-send-c.md)]

[!INCLUDE [service-bus-event-hubs-get-started-receive-ephcs](../../includes/service-bus-event-hubs-get-started-receive-ephcs.md)]

## <a name="run-the-applications"></a>Eseguire le applicazioni
A questo punto è possibile eseguire le applicazioni.

1. In Visual Studio eseguire il progetto **Receiver** , quindi attendere che il progetto avvii i ricevitori per tutte le partizioni.
   
   ![][21]
2. Eseguire il programma **Sender**. Gli eventi verranno visualizzati nella finestra del ricevitore.
   
   ![][24]

## <a name="next-steps"></a>Passaggi successivi
Ora che è stata creata un'applicazione che crea un Hub eventi e invia e riceve i dati, è possibile passare ai seguenti scenari:

* Un' [applicazione di esempio completa che usa Hub eventi][sample application that uses Event Hubs].
* Esempio relativo alla [scalabilità orizzontale dell'elaborazione di eventi con l'Hub eventi][Scale out Event Processing with Event Hubs].
* [Panoramica di Hub eventi][Event Hubs overview]

<!-- Images. -->
[21]: ./media/event-hubs-c-ephcs-getstarted/run-csharp-ephcs1.png
[24]: ./media/event-hubs-c-ephcs-getstarted/receive-eph-c.png

<!-- Links -->
[Azure classic portal]: https://manage.windowsazure.com/
[Event Processor Host]: https://www.nuget.org/packages/Microsoft.Azure.ServiceBus.EventProcessorHost
[Event Hubs overview]: event-hubs-overview.md
[sample application that uses Event Hubs]: https://code.msdn.microsoft.com/Service-Bus-Event-Hub-286fd097
[Scale out Event Processing with Event Hubs]: https://code.msdn.microsoft.com/Service-Bus-Event-Hub-45f43fc3



<!--HONumber=Dec16_HO1-->


