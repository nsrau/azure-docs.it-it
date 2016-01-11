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


# Gestire le risorse del cluster Apache Spark in Azure HDInsight (Windows)

> [AZURE.NOTE]HDInsight offre ora cluster Spark su Linux. Per informazioni su come gestire le risorse per un cluster Spark in HDInsight Linux, vedere [Gestire le risorse del cluster Apache Spark in HDInsight di Azure (Linux)](hdinsight-apache-spark-resource-manager.md).

Gestione risorse è un componente del dashboard cluster Spark che consente di gestire risorse, ad esempio core e RAM utilizzate da ogni applicazione in esecuzione nel cluster.

## <a name="launchrm"></a>Come è possibile avviare il gestore di risorse?

1. Dalla Schermata iniziale del [portale di anteprima di Azure](https://ms.portal.azure.com/) fare clic sul riquadro per il cluster Spark (se è stato bloccato sulla Schermata iniziale). È anche possibile passare al cluster in **Sfoglia tutto** > **Cluster HDInsight**. 
 
2. Dal pannello del cluster Spark fare clic su **Dashboard**. Quando richiesto, immettere le credenziali di amministratore per il cluster di Spark.

	![Aprire gestione risorse](./media/hdinsight-apache-spark-resource-manager-v1/hdispark.cluster.launch.dashboard.png "Avviare gestione risorse")

##<a name="scenariosrm"></a>Come risolvere il problema utilizzando il gestore di risorse?

Ecco alcuni scenari comuni che si verificano con il cluster Spark e le istruzioni su come affrontare quelli che utilizzano il gestore di risorse.

### Il cluster Spark in HDInsight è lento

Cluster Apache Spark in HDInsight è progettato per multi-tenancy, pertanto le risorse vengono suddivise tra più componenti (notebook, server dei processi e così via). In questo modo è possibile utilizzare contemporaneamente tutti i componenti di Spark senza preoccuparsi di qualsiasi componente non in grado di ottenere le risorse per l'esecuzione, ma ogni componente sarà più lento poiché le risorse vengono frammentate. Questo può essere regolato in base alle esigenze.


### Si usa il notebook Jupyter solo con il cluster Spark. Come è possibile allocare tutte le risorse?

1. Dal **Dashboard Spark** fare clic sulla scheda **Interfaccia utente Spark** per individuare il numero massimo di core e la RAM massima che è possibile allocare per le applicazioni.

	![Allocazione delle risorse](./media/hdinsight-apache-spark-resource-manager-v1/hdispark.ui.resource.png "Trovare le risorse allocate a un cluster di Spark")

	In base alla schermata precedente, il numero massimo di core allocabili è 7 (totale di 8 core di cui 1 in uso) e la quantità massima di RAM allocabile è 9 GB (totale di 12 GB di RAM, di cui 2 GB devono essere riservati per l'utilizzo da parte del sistema e 1 GB è in uso da parte di altre applicazioni).

	Inoltre, è consigliabile tenere tutte le applicazioni in esecuzione. È possibile esaminare le applicazioni in esecuzione dalla scheda **Interfaccia utente Spark**.

	![Applicazioni in esecuzione](./media/hdinsight-apache-spark-resource-manager-v1/hdispark.ui.running.apps.png "Applicazioni in esecuzione nel cluster")

	
2. Sul dashboard di Spark HDInsight, fare clic sulla scheda **Gestione risorse** e specificare i valori per **Numero di core predefinito per l’applicazione** e **Memoria predefinita dell’esecutore per ogni nodo del ruolo di lavoro**. Impostare altre proprietà su 0.

	![Allocazione delle risorse](./media/hdinsight-apache-spark-resource-manager-v1/hdispark.ui.allocate.resources.png "Allocare le risorse alle applicazioni")

### Non si utilizzano strumenti di Business Intelligence con il cluster di Spark. Come riprendere le risorse? 

Specificare il conteggio del core del server Thrift e la memoria esecutore del server Thrift come 0. Senza memoria centrale o memoria generica allocata, il server Thrift entra in uno stato di **ATTESA**.

![Allocazione delle risorse](./media/hdinsight-apache-spark-resource-manager-v1/hdispark.ui.no.thrift.png "Nessuna risorsa al server thrift")

##<a name="seealso"></a>Vedere anche

* [Panoramica: Apache Spark su Azure HDInsight](hdinsight-apache-spark-overview-v1.md)
* [Creare un cluster Spark in HDInsight](hdinsight-apache-spark-provision-clusters.md)
* [Eseguire l'analisi interattiva dei dati con strumenti di Business Intelligence mediante Spark in HDInsight](hdinsight-apache-spark-use-bi-tools-v1.md)
* [Usare Spark in HDInsight per la creazione di applicazioni di Machine Learning](hdinsight-apache-spark-ipython-notebook-machine-learning-v1.md)
* [Usare Spark in HDInsight per la creazione di applicazioni di streaming in tempo reale](hdinsight-apache-spark-csharp-apache-zeppelin-eventhub-streaming.md)


[hdinsight-versions]: ../hdinsight-component-versioning/
[hdinsight-upload-data]: ../hdinsight-upload-data/
[hdinsight-storage]: ../hdinsight-use-blob-storage/


[azure-purchase-options]: http://azure.microsoft.com/pricing/purchase-options/
[azure-member-offers]: http://azure.microsoft.com/pricing/member-offers/
[azure-free-trial]: http://azure.microsoft.com/pricing/free-trial/
[azure-management-portal]: https://manage.windowsazure.com/
[azure-create-storageaccount]: ../storage-create-storage-account/

<!---HONumber=AcomDC_1223_2015-->