<properties
	pageTitle="Introduzione all'Hub eventi con C e Apache Storm | Microsoft Azure"
	description="Eseguire questa esercitazione per iniziare a usare gli hub eventi di Azure, inviare gli eventi in C e riceverli in un cluster di Apache Storm."
	services="event-hubs"
	documentationCenter=""
	authors="fsautomata"
	manager="timlt"
	editor=""/>

<tags
	ms.service="event-hubs"
	ms.workload="na"
	ms.tgt_pltfrm="c"
	ms.devlang="java"
	ms.topic="article"
	ms.date="08/16/2016"
	ms.author="sethm"/>

# Introduzione all'Hub eventi

[AZURE.INCLUDE [service-bus-selector-get-started](../../includes/service-bus-selector-get-started.md)]

## Introduzione

Hub eventi è un sistema di inserimento a scalabilità elevata, in grado di accettare milioni di eventi al secondo, che permette a un'applicazione di elaborare e analizzare le elevate quantità di dati prodotti dalle applicazioni e dai dispositivi connessi. Dopo la raccolta nell'hub eventi, i dati possono essere trasformati e archiviati tramite qualsiasi provider di analisi in tempo reale o qualsiasi cluster di archiviazione.

Per ulteriori informazioni, vedere [Panoramica di Hub eventi].

Questa esercitazione illustra come inserire messaggi in Hub eventi usando un'applicazione console in C e come recuperarli in parallelo usando Apache Storm.

Per completare questa esercitazione, sono necessari gli elementi seguenti:

+ Ambiente di sviluppo in C. Per questa esercitazione si presuppone l'uso di uno stack gcc in una [macchina virtuale Linux di Azure ](../virtual-machines/virtual-machines-linux-quick-create-cli.md) con Ubuntu 14.04. Le istruzioni per gli altri ambienti saranno fornite nei collegamenti esterni.

+ Un ambiente di sviluppo Java configurato per l'esecuzione di [Maven](http://maven.apache.org/). Per questa esercitazione si presuppone l'uso di [Eclipse](https://www.eclipse.org/).

+ Un account Azure attivo. Se non si ha un account, è possibile creare un account gratuito in pochi minuti. Per informazioni dettagliate, vedere la pagina relativa alla [versione di valutazione gratuita di Azure](https://azure.microsoft.com/pricing/free-trial/).

[AZURE.INCLUDE [event-hubs-create-event-hub](../../includes/event-hubs-create-event-hub.md)]

[AZURE.INCLUDE [service-bus-event-hubs-get-started-send-c](../../includes/service-bus-event-hubs-get-started-send-c.md)]

[AZURE.INCLUDE [service-bus-event-hubs-get-started-receive-storm](../../includes/service-bus-event-hubs-get-started-receive-storm.md)]

## Eseguire le applicazioni

A questo punto è possibile eseguire le applicazioni.

1.	Eseguire la classe **LogTopology** da Eclipse, quindi attendere l'avvio dei ricevitori per tutte le partizioni.

2.	Eseguire il programma **sender**. Gli eventi verranno visualizzati nella finestra del ricevitore.

	![][23]

> [AZURE.NOTE] In questa esercitazione si usa Storm in modalità locale solo per finalità di sviluppo. Per altre informazioni sulle distribuzioni e sui modelli di Storm, vedere [Panoramica di HDinsight Storm] e la documentazione ufficiale su [Apache Storm].

## Passaggi successivi

Per lo sviluppo di applicazioni per l'integrazione di Hub eventi e Storm sono disponibili le seguenti risorse.

- [Analisi dei dati dei sensori con Storm e HDInsight][] è un'esercitazione relativa a uno scenario completo che usa Hub eventi, Storm e HBase per inserire dati dei sensori in un cluster Hadoop.
- [Sviluppare applicazioni di elaborazione di flussi di dati con SCP.NET e C# tramite Storm e HDInsight][] è un'esercitazione relativa alla scrittura di pipeline di Storm in C#.

<!-- Images. -->
[23]: ./media/event-hubs-c-storm-getstarted/receive-storm3.png

<!-- Links -->
[Azure classic portal]: https://manage.windowsazure.com/
[Event Processor Host]: https://www.nuget.org/packages/Microsoft.Azure.ServiceBus.EventProcessorHost
[Panoramica di Hub eventi]: event-hubs-overview.md

[Apache Storm]: https://storm.incubator.apache.org
[Panoramica di HDinsight Storm]: ../hdinsight/hdinsight-storm-overview.md/
[Analisi dei dati dei sensori con Storm e HDInsight]: ../hdinsight/hdinsight-storm-sensor-data-analysis.md
[Sviluppare applicazioni di elaborazione di flussi di dati con SCP.NET e C# tramite Storm e HDInsight]: ../hdinsight/hdinsight-storm-develop-csharp-visual-studio-topology.md

<!---HONumber=AcomDC_0817_2016-->