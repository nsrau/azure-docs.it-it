<properties 
	pageTitle="Una panoramica di Spark Apache in HDInsight | Microsoft Azure" 
	description="Introduzione a Spark Apache in HDInsight e scenari in cui usare Spark in HDInsight nelle applicazioni." 
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

# Panoramica: Apache Spark su Azure HDInsight (Windows)
 
> [AZURE.NOTE]HDInsight offre ora cluster Spark su Linux. Per informazioni sulle funzionalità offerte con HDInsight Spark in Linux, vedere [Panoramica: Apache Spark in Azure HDInsight (Linux)](hdinsight-apache-spark-overview.md).

<a href="http://spark.apache.org/" target="_blank">Apache Spark</a> è un framework open source di elaborazione parallela che supporta l'elaborazione in memoria per migliorare le prestazioni di applicazioni analitiche di Big Data. Il motore di elaborazione Spark è costruito per la velocità, la semplicità di utilizzo e le analisi sofisticate. Le funzionalità di elaborazione in memoria rendono Spark un valido strumento per l'esecuzione di algoritmi iterativi in calcoli grafici e di Machine Learning. Spark è inoltre compatibile con archiviazione Blob di Azure (WASB) in modo che i dati esistenti archiviati in Azure possono essere elaborati facilmente tramite Spark.

Quando si crea un cluster di Spark in HDInsight, si creano risorse di calcolo di Azure con Spark installato e configurato. Bastano circa dieci minuti per creare un cluster di Spark in HDInsight. I dati per la varianza sono archiviati nell'archivio BLOB di Azure. Vedere [Utilizzo dell’archiviazione BLOB di Azure con HDInsight][hdinsight-storage].

![Apache Spark in Azure HDInsight](./media/hdinsight-apache-spark-overview-v1/hdispark.architecture.png "Apache Spark in Azure HDInsight")


**Si vuole iniziare a utilizzare Apache Spark in Azure HDInsight?** Vedere [Guida introduttiva: creare un cluster di Spark in HDInsight ed eseguire applicazioni di esempio usando Jupyter e Zeppelin](hdinsight-apache-spark-zeppelin-notebook-jupyter-spark-sql-v1.md).



Guarda un video di panoramica che descrive Spark Apache in Azure HDInsight.

> [AZURE.VIDEO announcing-apache-spark-on-azure-hdinsight]

## Perché usare Spark in Azure HDInsight? 

Azure HDInsight offre un servizio Spark completamente gestito. I vantaggi dell'utilizzo di Spark in HDInsight sono:

| Funzionalità | Descrizione |
|-------------------------------------|-------------------|
| Facilità di creazione | È possibile creare un nuovo cluster Spark in HDInsight in pochi minuti mediante il portale di gestione di Azure, Azure PowerShell o l’SDK HDInsight .NET. Vedere [Creare un cluster Spark in HDInsight](hdinsight-apache-spark-provision-clusters.md) |
| Semplicità d'uso | Spark nei cluster HDInsight include notebook Zeppelin e Jupyter preconfigurati. È possibile usarli per la visualizzazione e l'elaborazione interattiva di dati. Gli URL per questi notebook sono https://CLUSTERNAME.azurehdinsight.net/zeppelin e https://CLUSTERNAME.azurehdinsight.net/jupyter. Sostituire __CLUSTERNAME__ con il nome del cluster HDInsight.|
| API REST | Spark in HDInsight include processo server Spark, ovvero un server API REST che consente agli utenti di inviare e monitorare i processi in esecuzione in modalità remota. |
| Query simultanee | Spark in HDInsight supporta query simultanee. In questo modo più query da un utente o più query da vari utenti e applicazioni possono condividere le stesse risorse di cluster. |
| La memorizzazione nella cache nelle unità SSD | È possibile scegliere di memorizzare i dati in memoria o nelle SSD associate ai nodi del cluster. La memorizzazione nella cache in memoria offre le migliori prestazioni di query ma può essere costosa; la memorizzazione nella cache in SSDs fornisce un'ottima opzione per migliorare le prestazioni delle query senza la necessità di creare un cluster di dimensioni necessario ad adattare l'intero set di dati in memoria.|
| Integrazione con servizi di Azure | Spark in HDInsight viene fornito con un connettore per Hub eventi di Azure. Gli utenti possono creare applicazioni di streaming mediante Hub eventi, oltre a [Kafka](http://kafka.apache.org/) che è già disponibile come parte di Spark. |
| Integrazione con strumenti di Business Intelligence | Spark per HDInsight fornisce connettori per gli strumenti di Business Intelligence più diffusi, come [Power BI](http://www.powerbi.com/) e [Tableau](http://www.tableau.com/products/desktop) per l'analisi dei dati.|
| Librerie Anaconda precaricate | I cluster Spark in HDInsight sono dotati di librerie Anaconda preinstallate [Anaconda](http://docs.continuum.io/anaconda/) fornisce quasi 200 librerie per Machine Learning, l’analisi dei dati, la visualizzazione e così via.|
| Scalabilità | Anche se è possibile specificare il numero di nodi del cluster durante la fase di creazione, in seguito può essere necessario aumentare o ridurre il cluster sulla base del carico di lavoro. Tutti i cluster HDInsight consentono di modificare il numero di nodi del cluster. Inoltre, è possibile eliminare i cluster Spark senza alcuna perdita di dati poiché tutti i dati vengono archiviati nell'archiviazione Blob di Azure. |
| Supporto 24/7 | Spark in HDInsight include supporto 24/7 a livello aziendale e un contratto di servizio disponibile 99,9% del tempo.|



## Quali sono i casi di utilizzo per Spark in HDInsight?

Apache Spark in HDInsight consente gli scenari principali seguenti.

### Analisi dei dati interattivi e Business Intelligence

[Esaminare un'esercitazione](hdinsight-apache-spark-use-bi-tools-v1.md)

Apache Spark in HDInsight archivia i dati nei blob di Azure. Gli esperti aziendali e i responsabili decisionali possono analizzare e generare rapporti su dati e usare Microsoft Power BI per creare rapporti dai dati analizzati. Gli analisti possono avviare dai dati non strutturati/semi strutturato in archiviazione di Azure, definire uno schema per i dati usando notebook e quindi compilare modelli di dati tramite Microsoft Power BI. Spark in HDInsight supporta inoltre una serie di strumenti di BI di terze parti quali Tableau, Qlikview e Lumira SAP ed è una piattaforma ideale per analisti dei dati, esperti aziendali e responsabili decisionali.

### Machine learning iterativo

[Esaminare un'esercitazione](hdinsight-apache-spark-ipython-notebook-machine-learning-v1.md)

Apache Spark viene fornito con [MLlib](http://spark.apache.org/mllib/), una libreria di machine learning basata su Spark. Inoltre, Spark in HDInsight include inoltre Anaconda, una distribuzione di Python con un'ampia gamma di pacchetti per l'apprendimento automatico. Aggiungendo il supporto incorporato per notebook Jupyter si otterrà un ambiente di qualità elevata per la creazione di applicazioni di Machine Learning.

### Analisi dei dati in tempo reale e streaming

[Esaminare un'esercitazione](hdinsight-apache-spark-csharp-apache-zeppelin-eventhub-streaming.md)

L'analisi dei dati in tempo reale viene usata per gli scenari che vanno dalla riduzione dei tempi di analisi dei dati attraverso l'elaborazione di dati quando arrivano alla creazione di una vera soluzione di streaming. Spark in HDInsight offre un supporto completo per la creazione di soluzioni di analisi in tempo reale. Mentre Spark ha già connettori per acquisire i dati da molte origini, quali socket Kafka, Flume, Twitter, ZeroMQ o TCP, Spark in HDInsight aggiunge un eccellente supporto per l'inserimento di dati da Hub eventi di Azure. Hub eventi è il servizio di Accodamento messaggi maggiormente usato in Azure. Disporre di un supporto per Hub eventi rende Spark in HDInsight la piattaforma ideale per la compilazione della pipeline di analisi in tempo reale.

##<a name="next-steps"></a>Quali componenti sono inclusi come parte di un cluster di Spark?

Spark in HDInsight include i componenti seguenti che sono disponibili nei cluster per impostazione predefinita.

- [Spark 1.3.1](https://spark.apache.org/docs/1.3.1/). Viene fornito con Spark Core, Spark SQL, streaming API, GraphX e MLlib Spark.
- [Anaconda](http://docs.continuum.io/anaconda/)
- [Server dei processi Spark](https://github.com/spark-jobserver/spark-jobserver)
- [Notebook Jupyter](https://jupyter.org)

Spark in HDInsight fornisce inoltre un [driver ODBC](http://go.microsoft.com/fwlink/?LinkId=616229) per la connettività ai cluster Spark in HDInsight da strumenti di Business Intelligence, quali Microsoft Power BI e Tableau.

##<a name="see-also"></a>Vedere anche

* [Guida introduttiva: uso di Spark in HDInsight con Zeppelin Notebook per eseguire l'analisi interattiva dei dati](hdinsight-apache-spark-zeppelin-notebook-jupyter-spark-sql.md)
* [Creare un cluster Apache Spark in HDInsight utilizzando le opzioni personalizzate](hdinsight-apache-spark-provision-clusters.md)
* [Gestire le risorse del cluster Apache Spark in Azure HDInsight](hdinsight-apache-spark-resource-manager-v1.md)
* [Invio di processi in modalità remota a un cluster Apache Spark in Azure HDInsight](hdinsight-apache-spark-job-server.md)


[hdinsight-storage]: ../hdinsight-use-blob-storage/

<!---HONumber=AcomDC_1223_2015-->