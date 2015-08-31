<properties 
	pageTitle="Utilizzare Hub eventi di Azure con Apache Spark in HDInsight per l'elaborazione del flusso di dati | Microsoft Azure" 
	description="Istruzioni dettagliate su come inviare dati in un flusso a Hub eventi di Azure e quindi ricevere tali eventi in Spark utilizzando un notebook Zeppelin" 
	services="hdinsight" 
	documentationCenter="" 
	authors="nitinme" 
	manager="paulettm" 
	editor="cgronlun"
	tags="azure-portal"/>

<tags 
	ms.service="hdinsight" 
	ms.workload="big-data" 
	ms.tgt_pltfrm="na" 
	ms.devlang="na" 
	ms.topic="article" 
	ms.date="07/31/2015" 
	ms.author="nitinme"/>


# Streaming Spark: Elaborare eventi da Hub eventi di Azure con Apache Spark in HDInsight

Streaming Spark estende l'API di Spark per compilare applicazioni di elaborazione scalabili, a velocità effettiva elevata e tolleranza d'errore del flusso principale. I dati possono essere acquisiti da molte origini. In questo articolo utilizziamo Hub eventi per il caricamento dati. Hub eventi è un sistema altamente scalabile dell'inserimento tale che può assumere milioni di eventi al secondo.

In questa esercitazione si apprenderà come creare un Hub di eventi di Azure, come inserire i messaggi in un Hub di eventi utilizzando un'applicazione console in C# e recuperarli in parallelo utilizzando un Zeppelin notebook configurato per Spark Apache in HDInsight.

> [AZURE.NOTE]Per seguire le istruzioni riportate in questo articolo, è necessario utilizzare entrambe le versioni del portale di Azure. Per creare un Hub eventi si utilizzerà il [portale di Azure](https://manage.windowsazure.com). Per utilizzare il cluster HDInsight Spark, si utilizzerà il [portale di anteprima di Azure](https://ms.portal.azure.com/).

**Prerequisiti:**

È necessario disporre di quanto segue:

- Una sottoscrizione di Azure. Vedere [Ottenere una versione di valutazione gratuita di Azure](http://azure.microsoft.com/documentation/videos/get-azure-free-trial-for-testing-hadoop-in-hdinsight/).
- Un cluster Apache Spark. Per istruzioni, vedere [Provisioning dei cluster Apache Spark in Azure HDInsight](hdinsight-apache-spark-provision-clusters.md).
- Uno [Hub eventi di Azure](service-bus-event-hubs-csharp-ephcs-getstarted.md).
- Una workstation con Microsoft Visual Studio 2013. Per istruzioni, vedere [Installazione di Visual Studio](https://msdn.microsoft.com/library/e2h7fzkw.aspx).

##<a name="createeventhub"></a>Creare Hub di eventi di Azure

1. Nel [Portale di Azure](https://manage.windowsazure.com) selezionare **NUOVO** > **Bus di servizio** > **Hub eventi** > **Creazione personalizzata**.

2. Nella schermata **Aggiungi hub eventi** immettere un nome in **Nome hub eventi**, selezionare l'area in cui creare l'hub in **Area geografica** e creare un nuovo spazio dei nomi o selezionarne uno esistente. Fare clic sulla **freccia** per continuare.

	![procedura guidata - pagina 1](./media/hdinsight-apache-spark-csharp-apache-zeppelin-eventhub-streaming/HDI.Spark.Streaming.Create.Event.Hub.png "Creare un Hub di eventi di Azure")

	> [AZURE.NOTE]È necessario selezionare lo stesso **Percorso** del cluster Apache Spark in HDInsight per ridurre i costi e la latenza.

3. Nella schermata di **Configurazione Hub di eventi** immettere i valori **Numero di partizione** e **Memorizzazione dei messaggi** e quindi fare clic sul segno di spunta. Per questo esempio usare un numero di partizioni pari a 10 e un valore di conservazione dei messaggi pari a 1. Prendere nota del numero di partizioni, perché questo valore sarà necessario in seguito.

	![procedura guidata - pagina 2](./media/hdinsight-apache-spark-csharp-apache-zeppelin-eventhub-streaming/HDI.Spark.Streaming.Create.Event.Hub2.png "Specificare i giorni di conservazione e la dimensione di partizione per Hub di eventi")

4. Fare clic sull'Hub di eventi che è stato creato, fare clic su **Configura** quindi creare due criteri di accesso per l'hub di eventi.

	<table>
<tr><th>Nome</th><th>Autorizzazioni</th></tr>
<tr><td>mysendpolicy</td><td>Invio</td></tr>
<tr><td>myreceivepolicy</td><td>Attesa</td></tr>
</table>Dopo avere creato le autorizzazioni, selezionare l'icona **Salva** nella parte inferiore della pagina. Ciò consente di creare criteri di accesso condiviso che verranno utilizzati per inviare (**mysendpolicy**) e di ascolto (**myreceivepolicy**) per l'Hub eventi.

	![criteri](./media/hdinsight-apache-spark-csharp-apache-zeppelin-eventhub-streaming/HDI.Spark.Streaming.Event.Hub.Policies.png "Creare criteri di Hub di eventi")

	
5. Nella stessa pagina, prendere nota delle chiavi di criteri generati per i due criteri. Salvare queste informazioni perché verranno usate in seguito.

	![chiavi dei criteri](./media/hdinsight-apache-spark-csharp-apache-zeppelin-eventhub-streaming/HDI.Spark.Streaming.Event.Hub.Policy.Keys.png "Salvare le chiavi dei criteri")

6. Nella pagina **Dashboard** fare clic su **Informazioni di connessione** dal basso per recuperare e salvare le stringhe di connessione per l'Hub di eventi utilizzando i due criteri.

	![chiavi dei criteri](./media/hdinsight-apache-spark-csharp-apache-zeppelin-eventhub-streaming/HDI.Spark.Streaming.Event.Hub.Policy.Connection.Strings.png "Salvare le stringhe di connessione criteri")

[AZURE.INCLUDE [service-bus-event-hubs-get-started-send-csharp](../../includes/service-bus-event-hubs-get-started-send-csharp.md)]

##<a name="receivezeppelin"></a>Ricevere messaggi in Spark in HDInsight tramite Zeppelin

In questa sezione, è possibile creare un notebook [Zeppelin](https://zeppelin.incubator.apache.org) per ricevere messaggi dall'Hub eventi nel cluster Spark in HDInsight.

1. Dal [portale di anteprima di Azure](https://ms.portal.azure.com/), dalla schermata iniziale fare clic sul riquadro per il cluster Spark (se lo si è bloccato alla schermata iniziale). È inoltre possibile passare al cluster in **Esplora tutto** > **Cluster HDInsight**.   

2. Avviare il notebook Zeppelin. Dal pannello del cluster Spark fare clic su **Collegamenti rapidi**, e dal pannello **Dashboard del Cluster**, fare clic su **Notebook Zeppelin**. Quando richiesto, immettere le credenziali per il cluster. Seguire le istruzioni nella pagina visualizzata per avviare il notebook.

2. Creare un nuovo notebook. Dal riquadro intestazione fare clic su **Notebook** e dall'elenco a discesa, fare clic su **Crea una nuova nota**.

	![Creare un nuovo notebook Zeppelin](./media/hdinsight-apache-spark-csharp-apache-zeppelin-eventhub-streaming/HDI.Spark.CreateNewNote.png "Creare un nuovo notebook Zeppelin")

	Nella stessa pagina, sotto l’intestazione **Notebook**, verrà visualizzato un nuovo blocco appunti con il nome che inizia con **Nota XXXXXXXXX**. Fare clic su nuovo notebook.

3. Nella pagina web per il nuovo notebook, fare clic sull'intestazione e se si vuole modificare il nome del notebook. Premere INVIO per salvare la modifica del nome. Inoltre, assicurarsi che l'intestazione del notebook mostri lo stato **Connesso** nell'angolo in alto a destra.

	![Stato di notebook Zeppelin](./media/hdinsight-apache-spark-csharp-apache-zeppelin-eventhub-streaming/HDI.Spark.NewNote.Connected.png "Stato di notebook Zeppelin")

4. Nel paragrafo vuoto creato per impostazione predefinita del nuovo notebook, incollare il frammento seguente e sostituire i segnaposto per utilizzare la configurazione di Hub eventi. In questo frammento, è possibile ricevere il flusso da Hub di eventi e registrare il flusso in una tabella temporanea denominata **mytemptable**. Nella sezione successiva, si inizierà l'applicazione mittente. È quindi possibile leggere i dati direttamente dalla tabella.

		import org.apache.spark.streaming.{Seconds, StreamingContext}
		import org.apache.spark.streaming.eventhubs.EventHubsUtils
		import sqlContext.implicits._
		
		val ehParams = Map[String, String](
		  "eventhubs.policyname" -> "<name of policy with listen permissions>",
		  "eventhubs.policykey" -> "<key of policy with listen permissions>",
		  "eventhubs.namespace" -> "<service bus namespace>",
		  "eventhubs.name" -> "<event hub in the service bus namespace>",
		  "eventhubs.partition.count" -> "1",
		  "eventhubs.consumergroup" -> "$default",
		  "eventhubs.checkpoint.dir" -> "/<check point directory in your storage account>",
		  "eventhubs.checkpoint.interval" -> "10"
		)
		
		val ssc =  new StreamingContext(sc, Seconds(10))
		val stream = EventHubsUtils.createUnionStream(ssc, ehParams)
		
		case class Message(msg: String)
		stream.map(msg=>Message(new String(msg))).foreachRDD(rdd=>rdd.toDF().registerTempTable("mytemptable"))

		stream.print
		ssc.start


##<a name="runapps"></a>Eseguire le applicazioni

1. Dal notebook Zeppelin eseguire il paragrafo con il frammento. Premere il pulsante **MAIUSC + INVIO** o **Riprodurre** nell'angolo in alto a destra.

	Lo stato nell’angolo destro del paragrafo deve passare da PRONTO, IN ATTESA, IN ESECUZIONE, a COMPLETATO. L'output verrà visualizzato nella parte inferiore dello stesso paragrafo. Nella schermata è simile al seguente:

	![Output frammento](./media/hdinsight-apache-spark-csharp-apache-zeppelin-eventhub-streaming/HDI.Spark.Streaming.Event.Hub.Zeppelin.Code.Output.png "Output del frammento")

2. Eseguire il progetto **Mittente** e premere **Invio** nella finestra della console per iniziare a inviare messaggi all'Hub di eventi.

3. Dal notebook Zeppelin in un nuovo paragrafo, immettere il seguente frammento per leggere i messaggi ricevuti in Spark.

		%sql 
		select * from mytemptable limit 10

	La schermata seguente mostra i messaggi ricevuti nel **mytemptable**.

	![Ricevere i messaggi in Zeppelin](./media/hdinsight-apache-spark-csharp-apache-zeppelin-eventhub-streaming/HDI.Spark.Streaming.Event.Hub.Zeppelin.Output.png "Ricezione di messaggi nel notebook Zeppelin")

4. Riavviare l’interprete Spark SQL per uscire dall'applicazione. Scegliere la scheda **Interprete** nella parte superiore e per l'interprete Spark fare clic su **Riavvia**.

	![Riavviare l’inteprete Zeppelin](./media/hdinsight-apache-spark-csharp-apache-zeppelin-eventhub-streaming/HDI.Spark.Zeppelin.Restart.Interpreter.png "Riavviare l’inteprete Zeppelin")

##<a name="sparkstreamingha"></a>Eseguire l'applicazione di flusso con disponibilità elevata

Utilizzare Zeppelin per ricevere il flusso di dati in cluster Spark in HDInsight è un buon approccio al prototipo dell'applicazione. Tuttavia, per eseguire l'applicazione di flusso in un programma di installazione di produzione con elevata disponibilità e resilienza, è necessario eseguire le operazioni seguenti:

1. Copiare il file jar di dipendenza all'account di archiviazione associato al cluster.
2. Creazione di un'applicazione di flusso.
3. RDP nel cluster e copiare il file jar di applicazione nel nodo head del cluster.
3. RDP nel cluster ed eseguire l'applicazione nel nodo del cluster.

Le istruzioni su come eseguire questi passaggi e un’applicazione di flusso di esempio possono essere scaricati da GitHub all'indirizzo [https://github.com/hdinsight/hdinsight-spark-examples](https://github.com/hdinsight/hdinsight-spark-examples).


##<a name="seealso"></a>Vedere anche


* [Panoramica: Apache Spark su Azure HDInsight](hdinsight-apache-spark-overview.md)
* [Avvio rapido: eseguire il provisioning Apache Spark e avviare query interattive usando SQL Spark in HDInsight](hdinsight-apache-spark-zeppelin-notebook-jupyter-spark-sql.md)
* [Utilizzare Spark in HDInsight per la creazione di applicazioni di Machine Learning](hdinsight-apache-spark-ipython-notebook-machine-learning.md)
* [Eseguire l’analisi interattiva dei dati con strumenti di Business Intelligence mediante Spark in HDInsight](hdinsight-apache-spark-use-bi-tools.md)
* [Gestire le risorse del cluster Apache Spark in Azure HDInsight](hdinsight-apache-spark-resource-manager.md)


[hdinsight-versions]: ../hdinsight-component-versioning/
[hdinsight-upload-data]: ../hdinsight-upload-data/
[hdinsight-storage]: ../hdinsight-use-blob-storage/

[azure-purchase-options]: http://azure.microsoft.com/pricing/purchase-options/
[azure-member-offers]: http://azure.microsoft.com/pricing/member-offers/
[azure-free-trial]: http://azure.microsoft.com/pricing/free-trial/
[azure-management-portal]: https://manage.windowsazure.com/
[azure-create-storageaccount]: ../storage-create-storage-account/

<!---HONumber=August15_HO8-->