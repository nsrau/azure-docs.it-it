<properties 
	pageTitle="Utilizzare Gestione risorse per allocare le risorse nel cluster Apache Spark in HDInsight | Microsoft Azure" 
	description="Imparare a utilizzare il gestore di risorse per i cluster di Spark su HDInsight per ottenere prestazioni migliori." 
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
	ms.date="12/22/2015" 
	ms.author="nitinme"/>


# Gestire le risorse del cluster Apache Spark in Azure HDInsight (Linux)

Spark in Azure HDInsight (Linux) fornisce l'interfaccia utente Web di Ambari per gestire le risorse del cluster e monitorare l'integrità del cluster. È inoltre possibile usare il Server cronologia Spark per tenere traccia delle applicazioni che sono state eseguite nel cluster e l'interfaccia utente di YARN per il monitoraggio delle applicazioni attualmente in esecuzione nel cluster. Questo articolo fornisce istruzioni su come accedere a tali interfacce utente e su come eseguire alcune attività di gestione delle risorse di base mediante queste interfacce.

**Prerequisiti:**

È necessario disporre di quanto segue:

- Una sottoscrizione di Azure. Vedere [Ottenere una versione di valutazione gratuita di Azure](https://azure.microsoft.com/documentation/videos/get-azure-free-trial-for-testing-hadoop-in-hdinsight/).
- Un cluster Apache Spark in HDInsight Linux. Per istruzioni, vedere l'articolo relativo alla [creazione di cluster Apache Spark in Azure HDInsight](hdinsight-apache-spark-jupyter-spark-sql.md).

## Come è possibile avviare l'interfaccia utente Web di Ambari?

1. Dalla Schermata iniziale del [portale di anteprima di Azure](https://ms.portal.azure.com/) fare clic sul riquadro del cluster Spark (se è stato aggiunto sulla Schermata iniziale). È anche possibile passare al cluster in **Sfoglia tutto** > **Cluster HDInsight**. 
 
2. Dal pannello del cluster Spark fare clic su **Dashboard**. Quando richiesto, immettere le credenziali di amministratore per il cluster di Spark.

	![Avviare Ambari](./media/hdinsight-apache-spark-resource-manager/hdispark.cluster.launch.dashboard.png "Avviare gestione risorse")

3. Questa operazione consente di avviare l'interfaccia utente Web di Ambari, come illustrato di seguito.

	![Interfaccia utente Web Ambari](./media/hdinsight-apache-spark-resource-manager/ambari-web-ui.png "Interfaccia utente Web Ambari")

## Come è possibile avviare il Server cronologia Spark?

1. Dalla Schermata iniziale del [portale di anteprima di Azure](https://ms.portal.azure.com/) fare clic sul riquadro del cluster Spark (se è stato aggiunto sulla Schermata iniziale).

2. Dal pannello del cluster in **Collegamenti rapidi** fare clic su **Dashboard cluster**. Nel pannello **Dashboard cluster** fare clic su **Server cronologia Spark**.

	![Server cronologia Spark](./media/hdinsight-apache-spark-resource-manager/launch-history-server.png "Server cronologia Spark")

	Quando richiesto, immettere le credenziali di amministratore per il cluster di Spark.


## Come è possibile avviare l'interfaccia utente di Yarn?

È possibile usare l'interfaccia utente di YARN per il monitoraggio delle applicazioni attualmente in esecuzione nel cluster Spark. Prima di accedere all'interfaccia utente di YARN, è necessario aver abilitato tunneling SSH nel cluster. Per istruzioni, vedere [Usare il tunneling SSH per accedere all'interfaccia Web di Ambari](hdinsight-linux-ambari-ssh-tunnel.md).

1. Avviare l'interfaccia utente Web di Ambari, come illustrato nella sezione precedente.

2. Dall'interfaccia utente Web di Ambari, selezionare YARN dall'elenco a sinistra della pagina.

3. 3\.Quando le informazioni del servizio YARN vengono visualizzate, selezionare **Collegamenti rapidi**. Verrà visualizzato un elenco di nodi head del cluster. Selezionare uno dei nodi head e quindi selezionare l'**interfaccia utente di ResourceManager**.

	![Avviare l'interfaccia utente di YARN](./media/hdinsight-apache-spark-resource-manager/launch-yarn-ui.png "Avviare l'interfaccia utente di YARN")

4. Questa operazione consente di avviare l'interfaccia utente di YARN e di visualizzare una pagina simile alla seguente:

	![Interfaccia utente di Yarn](./media/hdinsight-apache-spark-resource-manager/yarn-ui.png "Interfaccia utente di Yarn")

##<a name="scenariosrm"></a>Come è possibile gestire le risorse tramite l'interfaccia utente Web di Ambari?

Ecco alcuni scenari comuni che si verificano con il cluster Spark e le istruzioni su come affrontare quelli che usano l'interfaccia utente Web di Ambari.

### La funzionalità di Business Intelligence non è in uso con il cluster Spark. Come è possibile ripristinare le risorse?

1. Avviare l'interfaccia utente Web di Ambari, come illustrato in precedenza. Dal riquadro di spostamento sinistro fare clic su **Spark** e quindi su **Configs**.

2. Nell'elenco delle configurazioni disponibili, cercare **Custom spark-thrift-sparkconf** e modificare i valori per **spark.executor.memory** e **spark.drivers.core** impostandoli su **0**.

	![Risorse per la Business Intelligence](./media/hdinsight-apache-spark-resource-manager/spark-bi-resources.png "Risorse per la Business Intelligence")

3. Fare clic su **Save**. Immettere una descrizione delle modifiche apportate e quindi fare nuovamente clic su **Save**.

4. Nella parte superiore della pagina, verrà visualizzato un prompt per riavviare il servizio Spark. Fare clic su **Restart** per rendere effettive le modifiche.


### I notebook Jupyter non vengono eseguiti come previsto. Come è possibile riavviare il servizio?

1. Avviare l'interfaccia utente Web di Ambari, come illustrato in precedenza. Dal riquadro di spostamento sinistro fare clic su **Jupyter**, **Service Actions** e quindi su **Restart All**. Verrà avviato il servizio Jupyter su tutti i nodi head.

	![Riavviare Jupyter](./media/hdinsight-apache-spark-resource-manager/restart-jupyter.png "Riavviare Jupyter")

	


## <a name="seealso"></a>Vedere anche


* [Panoramica: Apache Spark su Azure HDInsight](hdinsight-apache-spark-overview.md)

### Scenari

* [Spark con Business Intelligence: eseguire l'analisi interattiva dei dati con strumenti di Business Intelligence mediante Spark in HDInsight](hdinsight-apache-spark-use-bi-tools.md)

* [Spark con Machine Learning: utilizzare Spark in HDInsight per l'analisi della temperatura di compilazione utilizzando dati HVAC](hdinsight-apache-spark-ipython-notebook-machine-learning.md)

* [Spark con Machine Learning: usare Spark in HDInsight per prevedere i risultati del controllo degli alimenti](hdinsight-apache-spark-machine-learning-mllib-ipython.md)

* [Streaming Spark: usare Spark in HDInsight per la creazione di applicazioni di streaming in tempo reale](hdinsight-apache-spark-eventhub-streaming.md)

* [Analisi dei log del sito Web mediante Spark in HDInsight](hdinsight-apache-spark-custom-library-website-log-analysis.md)

### Creare ed eseguire applicazioni

* [Creare un'applicazione autonoma con Scala](hdinsight-apache-spark-create-standalone-application.md)

* [Eseguire processi in modalità remota in un cluster Spark usando Livy](hdinsight-apache-spark-livy-rest-interface.md)

### Estensioni

* [Usare i notebook di Zeppelin con un cluster Spark in HDInsight](hdinsight-apache-spark-use-zeppelin-notebook.md)

* [Kernel disponibili per notebook di Jupyter nel cluster Spark per HDInsight](hdinsight-apache-spark-jupyter-notebook-kernels.md)



[hdinsight-versions]: ../hdinsight-component-versioning/
[hdinsight-upload-data]: ../hdinsight-upload-data/
[hdinsight-storage]: ../hdinsight-use-blob-storage/


[azure-purchase-options]: http://azure.microsoft.com/pricing/purchase-options/
[azure-member-offers]: http://azure.microsoft.com/pricing/member-offers/
[azure-free-trial]: http://azure.microsoft.com/pricing/free-trial/
[azure-management-portal]: https://manage.windowsazure.com/
[azure-create-storageaccount]: ../storage-create-storage-account/

<!---HONumber=AcomDC_0128_2016-->