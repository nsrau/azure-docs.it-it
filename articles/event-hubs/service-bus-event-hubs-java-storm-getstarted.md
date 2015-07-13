<properties
	pageTitle="Introduzione all'Hub eventi"
	description="Seguire questa esercitazione per iniziare a usare gli hub eventi di Azure inviando gli eventi con Java e ricevendoli in un cluster di Apache Storm"
	services="event-hubs,service-bus"
	documentationCenter=""
	authors="fsautomata"
	manager="timlt"
	editor=""/>

<tags
	ms.service="event-hubs"
	ms.workload="core"
	ms.tgt_pltfrm="java"
	ms.devlang="java"
	ms.topic="article"
	ms.date="06/17/2015"
	ms.author="sethm"/>

# Introduzione all'Hub eventi

[AZURE.INCLUDE [service-bus-selector-get-started](../../includes/service-bus-selector-get-started.md)]

## Introduzione

Hub eventi è un sistema di inserimento a scalabilità elevata, in grado di accettare milioni di eventi al secondo, che permette a un'applicazione di elaborare e analizzare le elevate quantità di dati prodotti dalle applicazioni e dai dispositivi connessi. Dopo la raccolta nell'hub eventi, i dati possono essere trasformati e archiviati tramite qualsiasi provider di analisi in tempo reale o qualsiasi cluster di archiviazione.

Per ulteriori informazioni, vedere [Panoramica di Hub eventi].

Questa esercitazione illustra come raccogliere i messaggi in un hub eventi usando un'applicazione console in Java e come recuperarli in parallelo usando Apache Storm.

Per completare questa esercitazione, sono necessari i seguenti elementi:

+ Un ambiente di sviluppo Java configurato per l'esecuzione di [Maven](http://maven.apache.org/). Per questa esercitazione si presuppone l'uso di [Eclipse](https://www.eclipse.org/).

+ Un account Azure attivo. <br/>Se non si dispone di un account, è possibile creare un account di valutazione gratuita in pochi minuti. Per informazioni dettagliate, vedere la pagina relativa alla <a href="http://azure.microsoft.com/pricing/free-trial/?WT.mc_id=A0E0E5C02&amp;returnurl=http%3A%2F%2Fazure.microsoft.com%2Fit-it%2Fdevelop%2Fmobile%2Ftutorials%2Fget-started%2F" target="_blank">versione di valutazione gratuita di Azure</a>.

## Creare un hub eventi

1. Accedere al [portale di gestione di Azure] e fare clic su **NUOVO** nella parte inferiore della schermata.

2. Fare clic su **Servizi app**, selezionare **Bus di servizio**, quindi **Hub eventi** e infine **Creazione rapida**.

   	![][1]

3. Digitare un nome per l'hub eventi, selezionare la regione desiderata, quindi fare clic su **Crea un nuovo hub eventi**.

   	![][2]

4. Fare clic sullo spazio dei nomi appena creato (in genere ***nome hub eventi*-ns**).

   	![][3]

5. Fare clic sulla scheda **Hub eventi** nella parte superiore della pagina, quindi selezionare l'hub eventi appena creato.

   	![][4]

6. Fare clic sulla scheda **Configura** nella parte superiore della pagina, aggiungere una regola denominata **SendRule** con diritti di *Invio*, aggiungere un'altra regola denominata **ReceiveRule** con diritti di *Ascolto*, quindi fare clic su **Salva**.

   	![][5]

7. Nella stessa pagina prendere nota delle chiavi generate per **SendRule** e **ReceiveRule**.

   	![][6c]

L'hub eventi è stato creato e sono disponibili le stringhe di connessione necessarie per inviare e ricevere eventi.

[AZURE.INCLUDE [service-bus-event-hubs-get-started-send-java](../../includes/service-bus-event-hubs-get-started-send-java.md)]


[AZURE.INCLUDE [service-bus-event-hubs-get-started-receive-storm](../../includes/service-bus-event-hubs-get-started-receive-storm.md)]

## Eseguire le applicazioni

A questo punto è possibile eseguire le applicazioni.

1.	Eseguire la classe **LogTopology** da Eclipse, quindi attendere l'avvio dei ricevitori per tutte le partizioni.

2.	Eseguire il progetto **Sender** e scegliere **Invio** nella finestra della console. Gli eventi verranno visualizzati nella finestra del ricevitore.

   	![][22]

> [AZURE.NOTE]In questa esercitazione si usa Storm in modalità locale solo per finalità di sviluppo. Per altre informazioni sulle distribuzioni e sui modelli di Storm, vedere [Panoramica di HDinsight Storm] e la documentazione ufficiale su [Apache Storm].

## Passaggi successivi

Per lo sviluppo di applicazioni per l'integrazione di Hub eventi e Storm sono disponibili le seguenti risorse.

- [Analisi dei dati dei sensori con Storm e HDInsight] è un'esercitazione relativa a uno scenario completo che usa Hub eventi, Storm e HBase per inserire dati dei sensori in un cluster Hadoop.
- [Sviluppare applicazioni di elaborazione di flussi di dati con SCP.NET e C# tramite Storm e HDInsight] è un'esercitazione relativa alla scrittura di pipeline di Storm in C#.

<!-- Images. -->
[1]: ./media/service-bus-event-hubs-java-storm-getstarted/create-event-hub1.png
[2]: ./media/service-bus-event-hubs-java-storm-getstarted/create-event-hub2.png
[3]: ./media/service-bus-event-hubs-java-storm-getstarted/create-event-hub3.png
[4]: ./media/service-bus-event-hubs-java-storm-getstarted/create-event-hub4.png
[5]: ./media/service-bus-event-hubs-java-storm-getstarted/create-event-hub5.png
[6]: ./media/service-bus-event-hubs-getstarted/create-event-hub6.png
[6c]: ./media/service-bus-event-hubs-java-storm-getstarted/create-event-hub6c.png

[22]: ./media/service-bus-event-hubs-java-storm-getstarted/receive-storm2.png

<!-- Links -->
[portale di gestione di Azure]: https://manage.windowsazure.com/
[Event Processor Host]: https://www.nuget.org/packages/Microsoft.Azure.ServiceBus.EventProcessorHost
[Panoramica di Hub eventi]: http://msdn.microsoft.com/library/azure/dn836025.aspx

[Apache Storm]: https://storm.incubator.apache.org
[Panoramica di HDinsight Storm]: http://azure.microsoft.com/documentation/articles/hdinsight-storm-overview/
[Analisi dei dati dei sensori con Storm e HDInsight]: http://azure.microsoft.com/documentation/articles/hdinsight-storm-sensor-data-analysis/
[Sviluppare applicazioni di elaborazione di flussi di dati con SCP.NET e C# tramite Storm e HDInsight]: http://azure.microsoft.com/documentation/articles/hdinsight-hadoop-storm-scpdotnet-csharp-develop-streaming-data-processing-application/
 

<!---HONumber=July15_HO1-->