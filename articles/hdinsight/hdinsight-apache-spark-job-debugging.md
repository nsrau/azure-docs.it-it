<properties 
	pageTitle="Tenere traccia ed eseguire il debug di processi in esecuzione nel cluster Apache Spark in Azure HDInsight | Microsoft Azure" 
	description="Usare l'interfaccia utente di YARN, l'interfaccia utente di Spark e il server della cronologia di Spark per tenere traccia ed eseguire il debug di processi in esecuzione in un cluster Spark in Azure HDInsight" 
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
	ms.date="05/31/2016" 
	ms.author="nitinme"/>

# Tenere traccia ed eseguire il debug di processi in esecuzione nel cluster Apache Spark in Azure HDInsight (anteprima)

Questo articolo illustra come tenere traccia ed eseguire il debug di processi Spark tramite l'interfaccia utente di YARN, l'interfaccia utente di Spark e il server di cronologia Spark. Per questo articolo,si avvierà un processo Spark usando un notebook disponibile nel cluster Spark, **Machine Learning: analisi predittiva dei dati del controllo degli alimenti tramite MLlib**. È possibile usare la procedura seguente per tenere traccia di un'applicazione inviata usando anche qualsiasi altro approccio, ad esempio, **spark-submit**.

##Prerequisiti

È necessario disporre di quanto segue:

- Una sottoscrizione di Azure. Vedere [Ottenere una versione di valutazione gratuita di Azure](https://azure.microsoft.com/documentation/videos/get-azure-free-trial-for-testing-hadoop-in-hdinsight/).
- Un cluster Apache Spark in HDInsight Linux. Per istruzioni, vedere [Creare cluster Apache Spark in Azure HDInsight](hdinsight-apache-spark-jupyter-spark-sql.md).
- Si sarà già avviato il notebook **[Machine Learning: analisi predittiva dei dati del controllo degli alimenti tramite MLlib](hdinsight-apache-spark-machine-learning-mllib-ipython.md)**. Per istruzioni su come eseguire questo notebook, seguire il collegamento.  

## Tenere traccia di un'applicazione nell'interfaccia utente di YARN

1. Avviare l'interfaccia utente di YARN Nel pannello del cluster fare clic su **Dashboard cluster** e quindi su **YARN**.

	![Avviare l'interfaccia utente di YARN](./media/hdinsight-apache-spark-job-debugging/launch-yarn-ui.png)

	>[AZURE.TIP] In alternativa, è anche possibile avviare l'interfaccia utente di YARN dall'interfaccia utente di Ambari. Per avviare l'interfaccia utente di Ambari, nel pannello del cluster fare clic su **Dashboard cluster** e quindi su **Dashboard cluster HDInsight**. Nell'interfaccia utente di Ambari fare clic su **YARN**, fare clic su **Collegamenti rapidi**, sulla funzionalità di gestione risorse attiva e quindi fare clic su **ResourceManager UI**.

3. Poiché il processo Spark è stato avviato con Jupyter Notebook, il nome dell'applicazione è **remotesparkmagics**. Si tratta del nome per tutte le applicazioni avviate da notebook. Per ottenere altre informazioni sul processo, fare clic sull'ID dell'applicazione relativo al nome dell'applicazione. Verrà avviata la visualizzazione dell'applicazione.

	![Trovare un ID applicazione di Spark](./media/hdinsight-apache-spark-job-debugging/find-application-id.png)

	Per le applicazioni avviate da Jupyter Notebook lo stato è sempre **IN ESECUZIONE** fino a quando non si esce dal notebook.

4. Nella visualizzazione dell'applicazione è possibile eseguire il drill-down per trovare i contenitori associati all'applicazione e i log (stdout o stderr). È anche possibile avviare l'interfaccia utente di Spark facendo clic sul collegamento corrispondente all'**URL di verifica**, come illustrato di seguito.

	![Scaricare i log del contenitore](./media/hdinsight-apache-spark-job-debugging/download-container-logs.png)

## Tenere traccia di un'applicazione nell'interfaccia utente di Spark

Nell'interfaccia utente di Spark è possibile eseguire il drill-down dei processi Spark generati dall'applicazione avviata in precedenza.

1. Per avviare l'interfaccia utente di Spark, nella visualizzazione dell'applicazione, fare clic sul collegamento relativo a **URL di verifica**, come illustrato nella schermata precedente. È possibile visualizzare tutti i processi Spark avviati dall'applicazione in esecuzione in Jupyter Notebook.

	![Visualizzare processi Spark](./media/hdinsight-apache-spark-job-debugging/view-spark-jobs.png)

2. Fare clic sulla scheda **Executors** per visualizzare le informazioni di elaborazione e archiviazione per ogni executor. È anche possibile recuperare lo stack di chiamate facendo clic sul collegamento **Thread Dump**.

	![Visualizzare gli executor Spark](./media/hdinsight-apache-spark-job-debugging/view-spark-executors.png)
 
3. Fare clic sulla scheda **Stages** per visualizzare le fasi associate all'applicazione.

	![Visualizzare le fasi di Spark](./media/hdinsight-apache-spark-job-debugging/view-spark-stages.png)

	Ogni fase può avere più attività per cui è possibile visualizzare le statistiche di esecuzione, come illustrato di seguito.

	![Visualizzare le fasi di Spark](./media/hdinsight-apache-spark-job-debugging/view-spark-stages-details.png)

4. Nella pagina dei dettagli relativi alle fasi è possibile avviare la visualizzazione DAG. Espandere il collegamento **DAG Visualization** nella parte superiore della pagina, come illustrato di seguito.

	![Mostrare la visualizzazione DAG delle fasi di Spark](./media/hdinsight-apache-spark-job-debugging/view-spark-stages-dag-visualization.png)

	DAG o Direct Aclyic Graph rappresenta le diverse fasi dell'applicazione. Ogni casella blu nel grafico rappresenta un'operazione Spark richiamata dall'applicazione.

5. Nella pagina dei dettagli relativi alle fasi è anche possibile avviare la visualizzazione della sequenza temporale dell'applicazione. Espandere il collegamento **Event Timeline** nella parte superiore della pagina, come illustrato di seguito.

	![Visualizzare e la sequenza temporale di eventi delle fasi di Spark](./media/hdinsight-apache-spark-job-debugging/view-spark-stages-event-timeline.png)

	Verranno visualizzati gli eventi di Spark sotto forma di sequenza temporale. La visualizzazione della sequenza temporale è disponibile in tre livelli, tra processi, all'interno di un processo e all'interno di una fase. L'immagine precedente mostra la visualizzazione della sequenza temporale per una determinata fase.

	>[AZURE.TIP] Se si seleziona la casella di controllo **Enable zooming**, è possibile scorrere a sinistra e destra nella visualizzazione della sequenza temporale.

6. Altre schede nell'interfaccia utente di Spark forniscono anche informazioni utili sull'istanza di Spark.

	* Scheda Storage: se l'applicazione crea oggetti RDD, è possibile trovare informazioni corrispondenti in questa scheda.
	* Scheda Environment: questa scheda fornisce molte informazioni utili sull'istanza di Spark, ad esempio 
		* Versione di Scala
		* Directory dei log eventi associata al cluster
		* Numero di core executor per l'applicazione
		* e così via.

## Trovare informazioni sui processi completati tramite Server cronologia Spark

Una volta completato un processo, le informazioni corrispondenti vengono salvate in modo permanente nel Server cronologia Spark.

1. Per avviare il Server cronologia Spark, nel pannello del cluster,fare clic su **Dashboard cluster** e quindi su **Server cronologia Spark**.

	![Avviare Server cronologia Spark](./media/hdinsight-apache-spark-job-debugging/launch-spark-history-server.png)

	>[AZURE.TIP] In alternativa, è anche possibile avviare l'interfaccia utente del Server cronologia Spark dall'interfaccia utente di Ambari. Per avviare l'interfaccia utente di Ambari, nel pannello del cluster fare clic su **Dashboard cluster** e quindi su **Dashboard cluster HDInsight**. Nell'interfaccia utente di Ambari fare clic su **Spark**, fare clic su **Collegamenti rapidi** e quindi sull'interfaccia utente di **Server cronologia Spark**.

2. Saranno elencate tutte le applicazioni completate. Fare clic su un ID applicazione per eseguire il drill-down di un'applicazione per altre informazioni.

	![Avviare Server cronologia Spark](./media/hdinsight-apache-spark-job-debugging/view-completed-applications.png)
	

## <a name="seealso"></a>Vedere anche


* [Panoramica: Apache Spark su Azure HDInsight](hdinsight-apache-spark-overview.md)

### Scenari

* [Spark con Business Intelligence: eseguire l'analisi interattiva dei dati con strumenti di Business Intelligence mediante Spark in HDInsight](hdinsight-apache-spark-use-bi-tools.md)

* [Spark con Machine Learning: utilizzare Spark in HDInsight per l'analisi della temperatura di compilazione utilizzando dati HVAC](hdinsight-apache-spark-ipython-notebook-machine-learning.md)

* [Spark con Machine Learning: usare Spark in HDInsight per prevedere i risultati del controllo degli alimenti](hdinsight-apache-spark-machine-learning-mllib-ipython.md)

* [Streaming Spark: usare Spark in HDInsight per creare applicazioni di streaming in tempo reale](hdinsight-apache-spark-eventhub-streaming.md)

### Creare ed eseguire applicazioni

* [Creare un'applicazione autonoma con Scala](hdinsight-apache-spark-create-standalone-application.md)

* [Eseguire processi in modalità remota in un cluster Spark usando Livy](hdinsight-apache-spark-livy-rest-interface.md)

### Strumenti ed estensioni

* [Usare il plug-in degli strumenti HDInsight per IntelliJ IDEA per creare e inviare applicazioni Spark in Scala](hdinsight-apache-spark-intellij-tool-plugin.md)

* [Usare i notebook di Zeppelin con un cluster Spark in HDInsight](hdinsight-apache-spark-use-zeppelin-notebook.md)

* [Kernel disponibili per notebook di Jupyter nel cluster Spark per HDInsight](hdinsight-apache-spark-jupyter-notebook-kernels.md)

### Gestire le risorse

* [Gestire le risorse del cluster Apache Spark in Azure HDInsight](hdinsight-apache-spark-resource-manager.md)

<!---HONumber=AcomDC_0601_2016-->