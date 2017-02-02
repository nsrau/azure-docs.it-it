---
title: Introduzione agli hub di eventi in Java con Apache Storm | Microsoft Docs
description: Eseguire questa esercitazione per iniziare a usare gli hub eventi di Azure inviando gli eventi con Java e ricevendoli in un cluster di Apache Storm.
services: event-hubs
documentationcenter: 
author: sethmanheim
manager: timlt
editor: 
ms.assetid: 385869bd-1ebe-44ae-8113-cc4679a568eb
ms.service: event-hubs
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 12/07/2016
ms.author: sethm
translationtype: Human Translation
ms.sourcegitcommit: 52c9ded8af99c5ae0836be8e71394dd1e5072aff
ms.openlocfilehash: 7566a8d0643607ee80c056ed4410aefd930226b8


---
# <a name="get-started-with-event-hubs"></a>Introduzione all'Hub eventi
[!INCLUDE [service-bus-selector-get-started](../../includes/service-bus-selector-get-started.md)]

## <a name="introduction"></a>Introduzione
Hub eventi è un sistema di inserimento a scalabilità elevata, in grado di accettare milioni di eventi al secondo, che permette a un'applicazione di elaborare e analizzare le elevate quantità di dati prodotti dalle applicazioni e dai dispositivi connessi. Dopo la raccolta nell'hub eventi, i dati possono essere trasformati e archiviati tramite qualsiasi provider di analisi in tempo reale o qualsiasi cluster di archiviazione.

Per altre informazioni, vedere [Panoramica di Hub eventi][Event Hubs Overview].

Questa esercitazione illustra come raccogliere i messaggi in un hub eventi usando un'applicazione console in Java e come recuperarli in parallelo usando Apache Storm.

Per completare questa esercitazione, sono necessari gli elementi seguenti:

* Un ambiente di sviluppo Java configurato per l'esecuzione di [Maven](http://maven.apache.org/). Per questa esercitazione si presuppone l'uso di [Eclipse](https://www.eclipse.org/).
* Un account Azure attivo. Se non si dispone di un account, è possibile creare un account di valutazione gratuita in pochi minuti. Per informazioni dettagliate, vedere la pagina relativa alla [versione di valutazione gratuita di Azure](https://azure.microsoft.com/pricing/free-trial/).

[!INCLUDE [event-hubs-create-event-hub](../../includes/event-hubs-create-event-hub.md)]

[!INCLUDE [service-bus-event-hubs-get-started-send-java](../../includes/service-bus-event-hubs-get-started-send-java.md)]

[!INCLUDE [service-bus-event-hubs-get-started-receive-storm](../../includes/service-bus-event-hubs-get-started-receive-storm.md)]

## <a name="run-the-applications"></a>Eseguire le applicazioni
A questo punto è possibile eseguire le applicazioni.

1. Eseguire la classe **LogTopology** da Eclipse, quindi attendere l'avvio dei ricevitori per tutte le partizioni.
2. Eseguire il progetto **Sender** e premere **Invio** nella finestra della console. Gli eventi verranno visualizzati nella finestra del ricevitore.
   
    ![][22]

> [!NOTE]
> In questa esercitazione si usa Storm in modalità locale solo per finalità di sviluppo. Per altre informazioni sulle distribuzioni e sui modelli di Storm, vedere [Panoramica di HDInsight Storm][HDInsight Storm Overview] e la documentazione ufficiale su [Apache Storm][Apache Storm].
> 
> 

## <a name="next-steps"></a>Passaggi successivi
Per lo sviluppo di applicazioni per l'integrazione di Hub eventi e Storm sono disponibili le seguenti risorse.

* [Analisi dei dati dei sensori con Storm e HDInsight] è un'esercitazione relativa a uno scenario completo che usa Hub eventi, Storm e HBase per inserire dati dei sensori in un cluster Hadoop.
* [Sviluppare applicazioni di elaborazione di flussi di dati con SCP.NET e C# tramite Storm e HDInsight][Develop streaming data processing applications with SCP.NET and C# on Storm and HDInsight] è un'esercitazione relativa alla scrittura di pipeline di Storm in C#.

<!-- Images. -->
[22]: ./media/event-hubs-java-storm-getstarted/receive-storm2.png

<!-- Links -->
[Event Processor Host]: https://www.nuget.org/packages/Microsoft.Azure.ServiceBus.EventProcessorHost
[Event Hubs Overview]: event-hubs-overview.md

[Apache Storm]: https://storm.incubator.apache.org
[HDInsight Storm Overview]: ../hdinsight/hdinsight-storm-overview.md
[Analisi dei dati dei sensori con Storm e HDInsight]: ../hdinsight/hdinsight-storm-sensor-data-analysis.md
[Develop streaming data processing applications with SCP.NET and C# on Storm and HDInsight]: ../hdinsight/hdinsight-storm-develop-csharp-visual-studio-topology.md



<!--HONumber=Dec16_HO2-->


