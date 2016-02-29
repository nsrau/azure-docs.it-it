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
	ms.date="12/08/2015" 
	ms.author="nitinme"/>


# Streaming Spark: elaborare eventi dall’Hub eventi di Azure con Apache Spark in HDInsight (Windows)

> [AZURE.NOTE] HDInsight offre ora cluster Spark su Linux. Per informazioni su come eseguire un'applicazione di streaming nei cluster HDInsight Spark Linux, vedere [Streaming Spark: elaborare eventi dall'Hub eventi di Azure con Apache Spark in HDInsight (Linux)](hdinsight-apache-spark-eventhub-streaming.md).

Streaming Spark estende l'API di Spark per compilare applicazioni di elaborazione scalabili, a velocità effettiva elevata e tolleranza d'errore del flusso principale. I dati possono essere acquisiti da molte origini. In questo articolo utilizziamo Hub eventi per il caricamento dati. Hub eventi è un sistema altamente scalabile dell'inserimento tale che può assumere milioni di eventi al secondo.

In questa esercitazione si apprenderà come creare un Hub di eventi di Azure, come inserire i messaggi in un Hub di eventi utilizzando un'applicazione console in C# e recuperarli in parallelo utilizzando un Zeppelin notebook configurato per Apache Spark in HDInsight.

> [AZURE.NOTE] Per seguire le istruzioni riportate in questo articolo, è necessario utilizzare entrambe le versioni del portale di Azure. Per creare un Hub eventi si utilizzerà il [portale di Azure](https://manage.windowsazure.com). Per utilizzare il cluster HDInsight Spark, si utilizzerà il [portale di anteprima di Azure](https://ms.portal.azure.com/).

**Prerequisiti:**

È necessario disporre di quanto segue:

- Una sottoscrizione di Azure. Vedere [Ottenere una versione di valutazione gratuita di Azure](https://azure.microsoft.com/documentation/videos/get-azure-free-trial-for-testing-hadoop-in-hdinsight/).
- Un cluster Apache Spark. Per istruzioni, vedere [Creazione dei cluster Apache Spark in Azure HDInsight](hdinsight-apache-spark-provision-clusters.md).
- Un [Hub eventi di Azure](../event-hubs/event-hubs-csharp-ephcs-getstarted.md).
- Una workstation con Microsoft Visual Studio 2013. Per istruzioni, vedere [Installazione di Visual Studio](https://msdn.microsoft.com/library/e2h7fzkw.aspx).

##<a name="createeventhub"></a>Creare Hub di eventi di Azure

1. Nel [Portale di Azure](https://manage.windowsazure.com) selezionare **NUOVO** > **Bus di servizio** > **Hub eventi** > **Creazione personalizzata**.

2. Nella schermata **Aggiungi hub eventi** immettere un nome in **Nome hub eventi**, selezionare l'area in cui creare l'hub in **Area geografica** e creare un nuovo spazio dei nomi o selezionarne uno esistente. Fare clic sulla **freccia** per continuare.

	![procedura guidata - pagina 1](./media/hdinsight-apache-spark-csharp-apache-zeppelin-eventhub-streaming/hdispark.streaming.create.event.hub.png "Creare un Hub di eventi di Azure")

	> [AZURE.NOTE] È necessario selezionare lo stesso **Percorso** del cluster Apache Spark in HDInsight per ridurre i costi e la latenza.

3. Nella schermata **Configura hub eventi** immettere i valori **Conteggio partizioni** e **Conservazione messaggi** e quindi fare clic sul segno di spunta. Per questo esempio usare un numero di partizioni pari a 10 e un valore di conservazione dei messaggi pari a 1. Prendere nota del numero di partizioni, perché questo valore sarà necessario in seguito.

	![procedura guidata - pagina 2](./media/hdinsight-apache-spark-csharp-apache-zeppelin-eventhub-streaming/hdispark.streaming.create.event.hub2.png "Specificare i giorni di conservazione e la dimensione di partizione per Hub di eventi")

4. Fare clic sull'hub eventi creato, su **Configura** e quindi creare due criteri di accesso per l'hub eventi.

	<table>
<tr><th>Nome</th><th>Autorizzazioni</th></tr>
<tr><td>mysendpolicy</td><td>Invio</td></tr>
<tr><td>myreceivepolicy</td><td>Attesa</td></tr>
</table>Dopo avere creato le autorizzazioni, selezionare l'icona **Salva** nella parte inferiore della pagina. Ciò consente di creare criteri di accesso condiviso che saranno usati per l'invio (**mysendpolicy**) e l'ascolto (**myreceivepolicy**) in questo hub eventi.

	![criteri](./media/hdinsight-apache-spark-csharp-apache-zeppelin-eventhub-streaming/hdispark.streaming.event.hub.policies.png "Creare criteri di Hub di eventi")

	
5. Nella stessa pagina, prendere nota delle chiavi di criteri generati per i due criteri. Salvare queste informazioni perché verranno usate in seguito.

	![chiavi dei criteri](./media/hdinsight-apache-spark-csharp-apache-zeppelin-eventhub-streaming/hdispark.streaming.event.hub.policy.keys.png "Salvare le chiavi dei criteri")

6. Nella pagina **Dashboard** fare clic su **Informazioni di connessione** in basso per recuperare e salvare le stringhe di connessione per l'hub eventi usando i due criteri.

	![chiavi dei criteri](./media/hdinsight-apache-spark-csharp-apache-zeppelin-eventhub-streaming/hdispark.streaming.event.hub.policy.connection.strings.png "Salvare le stringhe di connessione criteri")

[AZURE.INCLUDE [service-bus-event-hubs-get-started-send-csharp](../../includes/service-bus-event-hubs-get-started-send-csharp.md)]

##<a name="receivezeppelin"></a>Ricevere messaggi in Spark in HDInsight tramite Zeppelin

In questa sezione, è possibile creare un notebook [Zeppelin](https://zeppelin.incubator.apache.org) per ricevere messaggi dall'Hub eventi nel cluster Spark in HDInsight.

### Allocazione di risorse a Zeppelin per l'applicazione di flusso

Tenere conto delle considerazioni seguenti quando si crea un'applicazione di flusso con Zeppelin:

* **Partizioni di Hub eventi e core allocati a Zeppelin**. Nei passaggi precedenti è stato creato un hub eventi con alcune partizioni. Nell'applicazione di flusso Zeppelin che viene creata più avanti sarà necessario specificare di nuovo lo stesso numero di partizioni. Per trasmettere correttamente i dati dall'hub eventi con Zeppelin, il numero di core allocati a Zeppelin deve essere il doppio del numero di partizioni dell'hub eventi.
* **Numero minimo di core da allocare a Zeppelin**. Nell'applicazione di flusso creata più avanti viene creata una tabella temporanea in cui vengono archiviati i messaggi trasmessi dall'applicazione. Viene usata quindi un'istruzione Spark SQL per leggere i messaggi da questa tabella temporanea. Per eseguire l'istruzione Spark SQL, assicurarsi che siano allocati almeno due core per Zeppelin.

Se si combinano i due requisiti precedenti, questo è il risultato:

* Il numero minimo di core che è necessario allocare per Zeppelin è 2.
* Il numero di core allocati deve sempre essere il doppio del numero di partizioni dell'hub eventi. 

Per istruzioni su come allocare risorse in un cluster Spark, vedere [Gestire le risorse del cluster Apache Spark in HDInsight](hdinsight-apache-spark-resource-manager-v1.md).

### Creare un'applicazione di flusso con Zeppelin

1. Dalla Schermata iniziale del [portale di anteprima di Azure](https://portal.azure.com/) fare clic sul riquadro per il cluster Spark (se è stato bloccato sulla Schermata iniziale). È anche possibile passare al cluster in **Sfoglia tutto** > **Cluster HDInsight**.   

2. Dal pannello del cluster Spark fare clic su **Collegamenti rapidi**, e dal pannello **Dashboard del Cluster**, fare clic su **Notebook Zeppelin**. Se richiesto, immettere le credenziali per il cluster.

	> [AZURE.NOTE] È anche possibile raggiungere il notebook di Zeppelin per il cluster aprendo l'URL seguente nel browser. Sostituire __CLUSTERNAME__ con il nome del cluster:
	>
	> `https://CLUSTERNAME.azurehdinsight.net/zeppelin`

2. Creare un nuovo notebook. Dal riquadro intestazione fare clic su **Notebook** e dall'elenco a discesa, fare clic su **Crea una nuova nota**.

	![Creare un nuovo notebook Zeppelin](./media/hdinsight-apache-spark-csharp-apache-zeppelin-eventhub-streaming/hdispark.createnewnote.png "Creare un nuovo notebook Zeppelin")

	Nella stessa pagina, sotto l'intestazione **Notebook**, verrà visualizzato un nuovo notebook con il nome che inizia con **Nota XXXXXXXXX**. Fare clic su nuovo notebook.

3. Nella pagina web per il nuovo notebook, fare clic sull'intestazione e se si vuole modificare il nome del notebook. Premere INVIO per salvare la modifica del nome. Verificare anche che l'intestazione del notebook mostri lo stato **Connesso** nell'angolo in alto a destra.

	![Stato di notebook Zeppelin](./media/hdinsight-apache-spark-csharp-apache-zeppelin-eventhub-streaming/hdispark.newnote.connected.png "Stato di notebook Zeppelin")

4. Nel paragrafo vuoto creato per impostazione predefinita del nuovo notebook, incollare il frammento seguente e sostituire i segnaposto per utilizzare la configurazione di Hub eventi. In questo frammento di codice è possibile ricevere il flusso dall'hub eventi e registrare il flusso in una tabella temporanea denominata **mytemptable**. Nella sezione successiva, si inizierà l'applicazione mittente. È quindi possibile leggere i dati direttamente dalla tabella.

	> [AZURE.NOTE] Nel frammento di codice riportato di seguito, **eventhubs.checkpoint.dir** deve essere impostato su una directory del contenitore di archiviazione predefinito. Se la directory non esiste, verrà creata dall'applicazione di flusso. È possibile specificare il percorso completo della directory come "**wasb://container@storageaccount.blob.core.windows.net/mycheckpointdir/**" o semplicemente il percorso relativo alla directory, ad esempio "**/mycheckpointdir**".

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

1. Dal notebook Zeppelin eseguire il paragrafo con il frammento. Premere il pulsante **MAIUSC + INVIO** o **riprodurre** nell'angolo in alto a destra.

	Lo stato nell’angolo destro del paragrafo deve passare da PRONTO, IN ATTESA, IN ESECUZIONE, a COMPLETATO. L'output verrà visualizzato nella parte inferiore dello stesso paragrafo. Nella schermata è simile al seguente:

	![Output frammento](./media/hdinsight-apache-spark-csharp-apache-zeppelin-eventhub-streaming/hdispark.streaming.event.hub.zeppelin.code.output.png "Output del frammento")

2. Eseguire il progetto **Mittente** e premere **Invio** nella finestra della console per iniziare a inviare messaggi all'hub eventi.

3. Dal notebook Zeppelin in un nuovo paragrafo, immettere il seguente frammento per leggere i messaggi ricevuti in Spark.

		%sql 
		select * from mytemptable limit 10

	La schermata seguente mostra i messaggi ricevuti in **mytemptable**.

	![Ricevere i messaggi in Zeppelin](./media/hdinsight-apache-spark-csharp-apache-zeppelin-eventhub-streaming/hdispark.streaming.event.hub.zeppelin.output.png "Ricezione di messaggi nel notebook Zeppelin")

4. Riavviare l’interprete Spark SQL per uscire dall'applicazione. Scegliere la scheda **Interprete** nella parte superiore e per l'interprete Spark fare clic su **Riavvia**.

	![Riavviare l'interprete Zeppelin](./media/hdinsight-apache-spark-csharp-apache-zeppelin-eventhub-streaming/hdispark.zeppelin.restart.interpreter.png "Riavviare l'interprete Zeppelin")

##<a name="sparkstreamingha"></a>Eseguire l'applicazione di flusso con disponibilità elevata

Utilizzare Zeppelin per ricevere il flusso di dati in cluster Spark in HDInsight è un buon approccio al prototipo dell'applicazione. Tuttavia, per eseguire l'applicazione di flusso in un programma di installazione di produzione con elevata disponibilità e resilienza, è necessario eseguire le operazioni seguenti:

1. Copiare il file jar di dipendenza all'account di archiviazione associato al cluster.
2. Creazione di un'applicazione di flusso.
3. RDP nel cluster e copiare il file jar di applicazione nel nodo head del cluster.
3. RDP nel cluster ed eseguire l'applicazione nel nodo del cluster.

È possibile scaricare le istruzioni su come eseguire questi passaggi e un'applicazione di flusso di esempio da GitHub all'indirizzo [https://github.com/hdinsight/hdinsight-spark-examples](https://github.com/hdinsight/hdinsight-spark-examples).


##<a name="seealso"></a>Vedere anche


* [Panoramica: Apache Spark su Azure HDInsight](hdinsight-apache-spark-overview-v1.md)
* [Guida introduttiva: creare Apache Spark in HDInsight ed eseguire query interattive mediante Spark SQL](hdinsight-apache-spark-zeppelin-notebook-jupyter-spark-sql.md)
* [Usare Spark in HDInsight per la creazione di applicazioni di Machine Learning](hdinsight-apache-spark-ipython-notebook-machine-learning-v1.md)
* [Eseguire l’analisi interattiva dei dati con strumenti di Business Intelligence mediante Spark in HDInsight](hdinsight-apache-spark-use-bi-tools-v1.md)
* [Gestire le risorse del cluster Apache Spark in Azure HDInsight](hdinsight-apache-spark-resource-manager-v1.md)


[hdinsight-versions]: hdinsight-component-versioning.md
[hdinsight-upload-data]: hdinsight-upload-data.md
[hdinsight-storage]: hdinsight-hadoop-use-blob-storage.md

[azure-purchase-options]: http://azure.microsoft.com/pricing/purchase-options/
[azure-member-offers]: http://azure.microsoft.com/pricing/member-offers/
[azure-free-trial]: http://azure.microsoft.com/pricing/free-trial/
[azure-management-portal]: https://manage.windowsazure.com/
[azure-create-storageaccount]: storage-create-storage-account.md

<!---HONumber=AcomDC_0218_2016-->