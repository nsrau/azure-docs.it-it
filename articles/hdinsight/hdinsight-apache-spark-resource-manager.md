<properties 
	pageTitle="Utilizzare Gestione risorse per allocare le risorse nel cluster Apache Spark in HDInsight| Azure" 
	description="Imparare a utilizzare il gestore di risorse per i cluster di Spark su HDInsight per ottenere prestazioni migliori." 
	services="hdinsight" 
	documentationCenter="" 
	authors="nitinme" 
	manager="paulettm" 
	editor="cgronlun"/>

<tags 
	ms.service="hdinsight" 
	ms.workload="big-data" 
	ms.tgt_pltfrm="na" 
	ms.devlang="na" 
	ms.topic="article" 
	ms.date="07/19/2015" 
	ms.author="nitinme"/>


# Gestire le risorse del cluster Apache Spark in Azure HDInsight

Gestione risorse è un componente del dashboard cluster Spark che consente di gestire risorse, ad esempio core e RAM utilizzate da ogni applicazione in esecuzione nel cluster.

## <a name="launchrm"></a>Come è possibile avviare il gestore di risorse?

1. Selezionare il cluster Spark nel portale di Azure e, nella parte inferiore della barra delle applicazioni del portale, fare clic su **Dashboard Spark**.

2. Nel riquadro superiore nel dashboard, scegliere la scheda **Gestione risorse**.

##<a name="scenariosrm"></a>Come risolvere il problema utilizzando il gestore di risorse?

Ecco alcuni scenari comuni che si verificano con il cluster Spark e le istruzioni su come affrontare quelli che utilizzano il gestore di risorse.

### Il cluster Spark in HDInsight è lento

Cluster Apache Spark in HDInsight è progettato per multi-tenancy, pertanto le risorse vengono suddivise tra più componenti (notebook, server dei processi e così via). In questo modo è possibile utilizzare contemporaneamente tutti i componenti di Spark senza preoccuparsi di qualsiasi componente non in grado di ottenere le risorse per l'esecuzione, ma ogni componente sarà più lento poiché le risorse vengono frammentate. Questo può essere regolato in base alle esigenze.


### Si usa il notebook Jupyter solo con il cluster Spark. Come è possibile allocare tutte le risorse?

1. Dal **Dashboard Spark** fare clic sulla scheda **Spark UI** per individuare il numero massimo di core e la RAM massima che è possibile allocare per le applicazioni.

	![Allocazione delle risorse](./media/hdinsight-apache-spark-resource-manager/HDI.Spark.UI.Resource.png "Trovare le risorse allocate a un cluster di Spark")

	In base alla schermata precedente, il numero massimo di core allocabili è 7 (totale di 8 core di cui 1 in uso) e la quantità massima di RAM allocabile è 9 GB (totale di 12 GB di RAM, di cui 2 GB devono essere riservati per l'utilizzo da parte del sistema e 1 GB è in uso da parte di altre applicazioni).

	Inoltre, è consigliabile tenere tutte le applicazioni in esecuzione. È possibile esaminare le applicazioni in esecuzione dalla scheda **interfaccia utente Spark**.

	![Applicazioni in esecuzione](./media/hdinsight-apache-spark-resource-manager/HDI.Spark.UI.Running.Apps.png "Applicazioni in esecuzione nel cluster")

	
2. Sul dashboard di Spark HDInsight fare clic sulla scheda **Gestione risorse** scheda e specificare i valori per **applicazione core numero predefinito** e **Esecutore memoria predefinito per ogni nodo di lavoro**. Impostare altre proprietà su 0.

	![Allocazione delle risorse](./media/hdinsight-apache-spark-resource-manager/HDI.Spark.UI.Allocate.Resources.png "Allocare le risorse alle applicazioni")

### Non si utilizzano strumenti di Business Intelligence con il cluster di Spark. Come riprendere le risorse? 

Specificare il conteggio del core del server Thrift e la memoria esecutore del server Thrift come 0. Senza componenti di base o memoria allocata, il server thrift entra in uno stato di **attesa**.

![Allocazione delle risorse](./media/hdinsight-apache-spark-resource-manager/HDI.Spark.UI.No.Thrift.png "Nessuna risorsa al server thrift")

##<a name="seealso"></a>Vedere anche

* [Panoramica: Apache Spark su Azure HDInsight](hdinsight-apache-spark-overview.md)
* [Eseguire il provisioning di uno Spark nel cluster HDInsight](hdinsight-apache-spark-provision-clusters.md)
* [Eseguire l’analisi interattiva dei dati con strumenti di Business Intelligence mediante Spark in HDInsight](hdinsight-apache-spark-use-bi-tools.md)
* [Utilizzare Spark in HDInsight per la creazione di applicazioni di Machine Learning](hdinsight-apache-spark-ipython-notebook-machine-learning.md)
* [Utilizzare Spark in HDInsight per la creazione di applicazioni di streaming in tempo reale](hdinsight-apache-spark-csharp-apache-zeppelin-eventhub-streaming.md)


[hdinsight-versions]: ../hdinsight-component-versioning/
[hdinsight-upload-data]: ../hdinsight-upload-data/
[hdinsight-storage]: ../hdinsight-use-blob-storage/


[azure-purchase-options]: http://azure.microsoft.com/pricing/purchase-options/
[azure-member-offers]: http://azure.microsoft.com/pricing/member-offers/
[azure-free-trial]: http://azure.microsoft.com/pricing/free-trial/
[azure-management-portal]: https://manage.windowsazure.com/
[azure-create-storageaccount]: ../storage-create-storage-account/

<!---HONumber=July15_HO4-->