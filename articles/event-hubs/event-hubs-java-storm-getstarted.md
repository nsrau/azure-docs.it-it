<properties
	pageTitle="Introduzione all'Hub eventi in Java con Apache Storm | Microsoft Azure"
	description="Eseguire questa esercitazione per iniziare a usare gli hub eventi di Azure inviando gli eventi con Java e ricevendoli in un cluster di Apache Storm."
	services="event-hubs"
	documentationCenter=""
	authors="fsautomata"
	manager="timlt"
	editor=""/>

<tags
	ms.service="event-hubs"
	ms.workload="na"
	ms.tgt_pltfrm="na"
	ms.devlang="na"
	ms.topic="article"
	ms.date="09/06/2016"
	ms.author="sethm"/>

# Introduzione all'Hub eventi

[AZURE.INCLUDE [service-bus-selector-get-started](../../includes/service-bus-selector-get-started.md)]

## Introduzione

Hub eventi è un sistema di inserimento a scalabilità elevata, in grado di accettare milioni di eventi al secondo, che permette a un'applicazione di elaborare e analizzare le elevate quantità di dati prodotti dalle applicazioni e dai dispositivi connessi. Dopo la raccolta nell'hub eventi, i dati possono essere trasformati e archiviati tramite qualsiasi provider di analisi in tempo reale o qualsiasi cluster di archiviazione.

Per altre informazioni, vedere [Panoramica di Hub eventi][].

Questa esercitazione illustra come raccogliere i messaggi in un hub eventi usando un'applicazione console in Java e come recuperarli in parallelo usando Apache Storm.

Per completare questa esercitazione, sono necessari gli elementi seguenti:

+ Un ambiente di sviluppo Java configurato per l'esecuzione di [Maven](http://maven.apache.org/). Per questa esercitazione si presuppone l'uso di [Eclipse](https://www.eclipse.org/).

+ Un account Azure attivo. Se non si dispone di un account, è possibile creare un account di valutazione gratuita in pochi minuti. Per informazioni dettagliate, vedere la pagina relativa alla [versione di valutazione gratuita di Azure](https://azure.microsoft.com/pricing/free-trial/).

[AZURE.INCLUDE [event-hubs-create-event-hub](../../includes/event-hubs-create-event-hub.md)]

[AZURE.INCLUDE [service-bus-event-hubs-get-started-send-java](../../includes/service-bus-event-hubs-get-started-send-java.md)]


[AZURE.INCLUDE [service-bus-event-hubs-get-started-receive-storm](../../includes/service-bus-event-hubs-get-started-receive-storm.md)]

## Eseguire le applicazioni

A questo punto è possibile eseguire le applicazioni.

1.	Eseguire la classe **LogTopology** da Eclipse, quindi attendere l'avvio dei ricevitori per tutte le partizioni.

2.	Eseguire il progetto **Sender** e scegliere **Invio** nella finestra della console. Gli eventi verranno visualizzati nella finestra del ricevitore.

   	![][22]

> [AZURE.NOTE] In questa esercitazione si usa Storm in modalità locale solo per finalità di sviluppo. Per altre informazioni sulle distribuzioni e sui modelli di Storm, vedere [Panoramica di HDinsight Storm][] e la documentazione ufficiale su [Apache Storm][].

## Passaggi successivi

Per lo sviluppo di applicazioni per l'integrazione di Hub eventi e Storm sono disponibili le seguenti risorse.

- [Analisi dei dati dei sensori con Storm e HDInsight] è un'esercitazione relativa a uno scenario completo che usa Hub eventi, Storm e HBase per inserire dati dei sensori in un cluster Hadoop.
- [Sviluppare applicazioni di elaborazione di flussi di dati con SCP.NET e C# tramite Storm e HDInsight][] è un'esercitazione relativa alla scrittura di pipeline di Storm in C#.

<!-- Images. -->
[22]: ./media/event-hubs-java-storm-getstarted/receive-storm2.png

<!-- Links -->
[portale di Azure classico]: https://manage.windowsazure.com/
[Event Processor Host]: https://www.nuget.org/packages/Microsoft.Azure.ServiceBus.EventProcessorHost
[Panoramica di Hub eventi]: event-hubs-overview.md

[Apache Storm]: https://storm.incubator.apache.org
[Panoramica di HDinsight Storm]: ../hdinsight/hdinsight-storm-overview.md
[Analisi dei dati dei sensori con Storm e HDInsight]: ../hdinsight/hdinsight-storm-sensor-data-analysis.md
[Sviluppare applicazioni di elaborazione di flussi di dati con SCP.NET e C# tramite Storm e HDInsight]: ../hdinsight/hdinsight-storm-develop-csharp-visual-studio-topology.md
 

<!---HONumber=AcomDC_0907_2016-->