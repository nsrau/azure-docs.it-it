<properties
	pageTitle="Note sulla versione di componenti Hadoop in Azure HDInsight | Microsoft Azure"
	description="Note sulla versione più recente e le versioni di componenti Hadoop per Azure HDInsight. È possibile ottenere suggerimenti per lo sviluppo e i dettagli per Hadoop, Apache Storm e HBase."
	services="hdinsight"
	documentationCenter=""
	editor="cgronlun"
	manager="paulettm"
	authors="nitinme"
	tags="azure-portal"/>

<tags
	ms.service="hdinsight"
	ms.workload="big-data"
	ms.tgt_pltfrm="na"
	ms.devlang="na"
	ms.topic="article"
	ms.date="08/17/2016"
	ms.author="nitinme"/>


# Note sulla versione di componenti Hadoop in Azure HDInsight

## Note sulla versione di R Server su HDInsight rilasciata il 17/08/2016

• R Server 8.0.5 - principalmente una versione di correzione di bug. Vedere la sezione [Note sulla versione di R Server](https://msdn.microsoft.com/microsoft-r/notes/r-server-notes) per maggiori informazioni. • Il pacchetto AzureML nel nodo perimetrale, [questo pacchetto R](https://cran.r-project.org/web/packages/AzureML/vignettes/getting_started.html), consente la pubblicazione e l'uso dei modelli R come servizio Web di Azure ML. Vedere la sezione ["Rendere operativo un modello"](hdinsight-hadoop-r-server-overview.md#operationalize-a-model) dell'articolo ["Panoramica di R Server su HDInsight"](hdinsight-hadoop-r-server-overview.md) per maggiori informazioni. • Le dipendenze Linux dei [100 pacchetti R più diffusi](https://github.com/metacran/cranlogs) sono ora preinstallate. • Opzione per l'uso del repository CRAN quando si aggiungono pacchetti R ai nodi di dati. Vedere la sezione ["Installare pacchetti R"](hdinsight-hadoop-r-server-get-started.md#install-r-packages) dell'articolo ["Introduzione all'uso di R Server su HDInsight "](hdinsight-hadoop-r-server-get-started.md) per maggiori informazioni. • Miglioramento dell'affidabilità del provisioning di R Server dopo la creazione dei cluster.


## Note sulla versione di HDInsight rilasciata in data 01/08/2016

Numeri di versione completi per i cluster HDInsight basati su Linux distribuiti con questa versione:

|HDI |Cluster versione HDI |HDP |Compilazione HDP |Compilazione Ambari |
|----|----------------------|----|------------|-------------|
|3\.2 |3\.2.1000.0.8028416 |2\.2 |2\.2.9.1-19 |2\.2.1.12-4 |
|3\.3 |3\.3.1000.0.8028416 |2\.3 |2\.3.3.1-25 |2\.2.1.12-4 |
|3\.4 |3\.4.1000.0.8053402 |2\.4 |2\.4.2.4-5 |2\.2.1.12-4 |

Numeri di versione completi per i cluster HDInsight basati su Windows distribuiti con questa versione:

|HDI |Cluster versione HDI |HDP |Compilazione HDP |
|----|----------------------|----|--------------|
|2\.1 |2\.1.10.1005.2488842 |1\.3 |1\.3.12.0-01795|
|3\.0 |3\.0.6.1005.2488842 |2\.0 |2\.0.13.0-2117 |
|3\.1 |3\.1.4.1005.2488842 |2\.1 |2\.1.16.0-2374 |
|3\.2 |3\.2.7.1005.2488842 |2\.2 |2\.2.9.1-11 |
|3\.3 |3\.3.0.1005.2488842 |2\.3 |2\.3.3.1-25 |

Questa versione contiene gli aggiornamenti seguenti.

| Titolo | Descrizione | Area interessata (ad esempio servizio, componente o SDK) | Tipo di cluster (ad esempio Spark, Hadoop, HBase o Storm) | JIRA (se applicabile) |
|-------------------------------------------------|------------------------------------------------------|---------------------------------------------------------|-----------------------------------------------------|----------------------|
| Modifiche apportate ai cluster HDInsight 3.4 | Il valore predefinito per le configurazioni Hive seguenti è stato modificato per ottenere prestazioni migliori <ul><li>`hive.vectorized.execution.reduce.enabled=true`</li><li>`hive.tez.min.partition.factor=1f`</li><li>`hive.tez.max.partition.factor=3f`</li><li>`tez.shuffle-vertex-manager.min-src-fraction=0.9`</li><li>`tez.shuffle-vertex-manager.max-src-fraction=0.95`</li><li>`tez.runtime.shuffle.connect.timeout= 30000`</li></ul>| Service | Tutti| N/D|
| In questa versione sono incluse le correzioni seguenti | HIVE-13632, HIVE-12897, HIVE-12907, HIVE-12908, HIVE-12988, HIVE-13510, HIVE-13572, HIVE-13716, HIVE-13726, HIVE-12505, HIVE-13632, HIVE-13661, HIVE-13705, HIVE-13743, HIVE-13810, HIVE-13857, HIVE-13902, HIVE-13911, HIVE-13933| Service | Tutti| N/D

## Note sulla versione di HDInsight rilasciata il 14/07/2016

Numeri di versione completi per i cluster HDInsight basati su Linux distribuiti con questa versione:

|HDI |Cluster versione HDI |HDP |Compilazione HDP |Compilazione Ambari |
|----|----------------------|----|------------|-------------|
|3\.2 |3\.2.1000.0.7932505 |2\.2 |2\.2.9.1-11 |2\.2.1.12-2 |
|3\.3 |3\.3.1000.0.7932505 |2\.3 |2\.3.3.1-18 |2\.2.1.12-2 |
|3\.4 |3\.4.1000.0.7933003 |2\.4 |2\.4.2.0 |2\.2.1.12-2 |

Numeri di versione completi per i cluster HDInsight basati su Windows distribuiti con questa versione:

|HDI |Cluster versione HDI |HDP |Compilazione HDP |
|----|----------------------|----|--------------|
|2\.1 |2\.1.10.989.2441725 |1\.3 |1\.3.12.0-01795|
|3\.0 |3\.0.6.989.2441725 |2\.0 |2\.0.13.0-2117 |
|3\.1 |3\.1.4.989.2441725 |2\.1 |2\.1.16.0-2374 |
|3\.2 |3\.2.7.989.2441725 |2\.2 |2\.2.9.1-11 |
|3\.3 |3\.3.0.989.2441725 |2\.3 |2\.3.3.1-21 |

## Note sulla versione di HDInsight rilasciata il 07/07/2016

Numeri di versione completi per i cluster HDInsight basati su Linux distribuiti con questa versione:

|HDI |Cluster versione HDI |HDP |Compilazione HDP |
|----|----------------------|----|------------|
|3\.2 |3\.2.1000.0.7864996 |2\.2 |2\.2.9.1-11 |
|3\.3 |3\.3.1000.0.7864996 |2\.3 |2\.3.3.1-18 |
|3\.4 |3\.4.1000.0.7861906 |2\.4 |2\.4.2.0 |

Numeri di versione completi per i cluster HDInsight basati su Windows distribuiti con questa versione:

|HDI |Cluster versione HDI |HDP |Compilazione HDP |
|----|----------------------|----|--------------|
|2\.1 |2\.1.10.977.2413853 |1\.3 |1\.3.12.0-01795|
|3\.0 |3\.0.6.977.2413853 |2\.0 |2\.0.13.0-2117 |
|3\.1 |3\.1.4.977.2413853 |2\.1 |2\.1.16.0-2374 |
|3\.2 |3\.2.7.977.2413853 |2\.2 |2\.2.9.1-11 |
|3\.3 |3\.3.0.977.2413853 |2\.3 |2\.3.3.1-21 |

Questa versione contiene gli aggiornamenti seguenti.

| Titolo | Descrizione | Area interessata (ad esempio servizio, componente o SDK) | Tipo di cluster (ad esempio Spark, Hadoop, HBase o Storm) | JIRA (se applicabile) |
|-------------------------------------------------|------------------------------------------------------|---------------------------------------------------------|-----------------------------------------------------|----------------------|
| [Strumenti di HDInsight per IntelliJ](hdinsight-apache-spark-intellij-tool-plugin.md) | Il plug-in IntelliJ IDEA per i cluster HDInsight Spark è ora integrato nel toolkit di Azure per IntelliJ. Supporta l’SDK v2.9.1 di Azure, i più recenti SDK di Java e include tutte le funzionalità del plug-in HDInsight per IntelliJ indipendente.| Strumenti | Spark| N/D|
| [Strumenti di HDInsight per Eclipse](hdinsight-apache-spark-eclipse-tool-plugin.md) | Il toolkit di Azure per Eclipse supporta ora i cluster HDInsight Spark e offre le funzionalità seguenti: <ul><li>Creare e scrivere facilmente un'applicazione Spark in Scala e Java con il supporto alla creazione avanzata per IntelliSense, la formattazione automatica, il controllo degli errori e così via.</li><li>Testare l'applicazione Spark localmente.</li><li>Inviare processi al cluster HDInsight Spark e recuperare i risultati.</li><li>Connettersi ad Azure e accedere a tutti i cluster Spark associati con le sottoscrizioni di Azure.</li><li>Navigare in tutte le risorse di archiviazione associate del cluster HDInsight Spark.</li></ul>| Strumenti | Spark| N/D

A partire da questa versione, sono stati modificati i criteri di applicazione delle patch del sistema operativo guest per i cluster HDInsight basati su Linux. L'obiettivo del nuovo criterio è di ridurre in modo consistente il numero di riavvii causati dall'applicazione delle patch. Il nuovo criterio continuerà ad applicare le patch alle macchine virtuali sui cluster Linux ogni lunedì o giovedì a partire dalle ore 00:00 UTC con un flusso di intervalli sui nodi in ogni cluster. Tuttavia, le macchine virtuali verranno riavviate solo una volta ogni 30 giorni a causa dell'applicazione delle patch al sistema operativo guest. In aggiunta, il primo riavvio di un cluster appena creato non verrà eseguito prima di 30 giorni dalla data di creazione del cluster.

>[AZURE.NOTE] Queste modifiche verranno applicate solo ai cluster appena creati pari o successivi a questa versione.

## Note sulla versione di HDInsight rilasciata il 06/06/2016

Numeri di versione completi per i cluster HDInsight distribuiti con questa versione:

|HDP |Versione HDI |Versione Spark |Numero di compilazione Ambari |Numero di compilazione HDP|
|-------|---------------|---------------|-----------------------|----------------|
|2\.3 |3\.3.1000.0.7702215|	1\.5.2|	2\.2.1.8-2|	2\.3.3.1-18|
|2\.4 |3\.4.1000.0.7702224|	1\.6.1|	2\.2.1.8-2|	2\.4.2.0|


Questa versione contiene gli aggiornamenti seguenti.

| Titolo | Descrizione | Area interessata (ad esempio servizio, componente o SDK) | Tipo di cluster (ad esempio Spark, Hadoop, HBase o Storm) | JIRA (se applicabile) |
|-------------------------------------------------|------------------------------------------------------|---------------------------------------------------------|-----------------------------------------------------|----------------------|
| Spark in HDInsight è disponibile a livello generale | Questa versione offre miglioramenti di disponibilità, scalabilità e produttività per Apache Spark open source in HDInsight. <ul><li>Contratto di servizio leader di settore per una disponibilità del 99,9% che lo rende adatto per i carichi di lavoro aziendali impegnativi.</li><li>Livello di archiviazione scalabile con Azure Data Lake Store.</li><li>Strumenti di produttività per ogni fase dell'esplorazione e dello sviluppo dei dati. I Jupyter Notebook con kernel Spark personalizzato consentono l'esplorazione interattiva dei dati, l'integrazione con dashboard BI come Power BI, Tableau e Qlik è perfetta per la condivisione rapida dei dati e il reporting continuo, il plug-in IntelliJ è un'opzione affidabile per lo sviluppo e il debug a lungo termine di elementi di codice.</li></ul>| Service | Spark| N/D|
| Strumenti di HDInsight per IntelliJ | Questo è un plug-in IntelliJ IDEA per cluster HDInsight Spark. Consente le funzionalità seguenti.<ul><li>Creare e scrivere facilmente un'applicazione Spark in Scala e Java con il supporto alla creazione avanzata per IntelliSense, la formattazione automatica, il controllo degli errori e così via.</li><li>Testare l'applicazione Spark localmente.</li><li>Inviare processi al cluster HDInsight Spark e recuperare i risultati.</li><li>Connettersi ad Azure e accedere a tutti i cluster Spark associati con le sottoscrizioni di Azure.</li><li>Navigare in tutte le risorse di archiviazione associate del cluster HDInsight Spark.</li><li>Navigare in tutta la cronologia dei processi e le informazioni dei processi per il cluster HDInsight Spark.</li><li>Eseguire il debug remoto dei processi Spark dal computer desktop.</li></ul>| Strumenti | Spark| N/D

## Note sulla versione di HDInsight rilasciata il 13/05/2016

Numeri di versione completi per i cluster HDInsight distribuiti con questa versione:

* HDInsight (Windows) 2.1.10.875.2159884 (HDP 1.3.12.0-01795 - non modificato)
* HDInsight (Windows) 3.0.6.875.2159884 (HDP 2.0.13.0-2117 - non modificato)
* HDInsight (Windows) 3.1.4.922.2266903 (HDP 2.1.15.0-2374 - non modificato)
* HDInsight (Windows) 3.2.7.922.2266903 (HDP 2.2.9.1-11)
* HDInsight (Windows) 3.3.0.922.2266903 (HDP 2.3.3.1-18)
* HDInsight (Linux) 3.2.1000.0.7565644 (HDP 2.2.9.1-11)
* HDInsight (Linux) 3.3.1000.0.7565644 (HDP 2.3.3.1-18)
* HDInsight (Linux) 3.4.1000.0.7548380 (HDP 2.4.2.0)

Questa versione contiene gli aggiornamenti seguenti.

| Titolo | Descrizione | Area interessata (ad esempio servizio, componente o SDK) | Tipo di cluster (ad esempio Spark, Hadoop, HBase o Storm) | JIRA (se applicabile) |
|-------------------------------------------------|------------------------------------------------------|---------------------------------------------------------|-----------------------------------------------------|----------------------|
| Aggiornamento della versione di Spark e altre correzioni di bug | Questa versione aggiorna la versione Spark nel cluster HDInsight a 1.6.1 e corregge altri bug| Service | Spark| N/D

## Note sulla versione di HDInsight dell'11/04/2016

Numeri di versione completi per i cluster HDInsight distribuiti con questa versione:

* HDInsight (Windows) 2.1.10.889.2191206 (HDP 1.3.12.0-01795 - non modificato)
* HDInsight (Windows) 3.0.6.889.2191206 (HDP 2.0.13.0-2117 - non modificato)
* HDInsight (Windows) 3.1.4.889.2191206 (HDP 2.1.15.0-2374 - non modificato)
* HDInsight (Windows) 3.2.7.889.2191206 (HDP 2.2.9.1-10)
* HDInsight (Windows) 3.3.0.889.2191206 (HDP 2.3.3.1-16 - non modificato)
* HDInsight (Linux) 3.2.1000.0.7339916 (HDP 2.2.9.1-10)
* HDInsight (Linux) 3.3.1000.0.7339916 (HDP 2.3.3.1-16)
* HDInsight (Linux) 3.4.1000.0.7338911 (HDP 2.4.1.1-3)
* SDK 1.5.8

Questa versione contiene gli aggiornamenti seguenti.

| Titolo | Descrizione | Area interessata (ad esempio servizio, componente o SDK) | Tipo di cluster (ad esempio Hadoop, HBase o Storm) | JIRA (se applicabile) |
|-------------------------------------------------|------------------------------------------------------|---------------------------------------------------------|-----------------------------------------------------|----------------------|
| Problemi di aggiornamento metastore personalizzato per HDI 3.4 | La creazione del cluster non viene eseguita correttamente se si usa un metastore personalizzato già usato in una versione precedente di un altro cluster HDInsight. Ciò è dovuto a un errore nello script di aggiornamento, ora risolto| Creazione del cluster | Tutti | N/D
| Ripristino a seguito dell'arresto anomalo del sistema Livy | Fornisce resilienza dello stato del processo per qualsiasi processo inviato tramite Livy | Affidabilità | Spark su Linux| N/D
| Contenuto Jupyter a disponibilità elevata | Consente di salvare e caricare il contenuto di notebook Jupyter da e verso l'account di archiviazione associato al cluster. Per altre informazioni, vedere [Kernel disponibili per i Jupyter Notebook](hdinsight-apache-spark-jupyter-notebook-kernels.md).| Notebook | Spark su Linux| N/D
| Rimozione di hiveContext nei notebook Jupyter | Usare l'oggetto `%%sql` invece di `%%hive`. SqlContext equivale a hiveContext. Per altre informazioni, vedere [Kernel disponibili per i Jupyter Notebook](hdinsight-apache-spark-jupyter-notebook-kernels.md).| Notebook | Cluster Spark su Linux| N/D
| Rimozione di versioni precedenti di Spark | La versione meno recente Spark 1.3.1 verrà rimossa dal servizio il 31/05 | Service | Cluster Spark in Windows | N/D

## Note sulla versione di HDInsight rilasciata il 29/03/2016

Numeri di versione completi per i cluster HDInsight distribuiti con questa versione:

* HDInsight (Windows) 2.1.10.875.2159884 (HDP 1.3.12.0-01795 - non modificato)
* HDInsight (Windows) 3.0.6.875.2159884 (HDP 2.0.13.0-2117 - non modificato)
* HDInsight (Windows) 3.1.4.875.2159884 (HDP 2.1.15.0-2374 - non modificato)
* HDInsight (Windows) 3.2.7.875.2159884 (HDP 2.2.9.1-7 - non modificato)
* HDInsight (Windows) 3.3.0.875.2159884 (HDP 2.3.3.1-16)
* HDInsight (Linux) 3.2.1000.0.7193255 (HDP 2.2.9.1-8 - non modificato)
* HDInsight (Linux) 3.3.1000.0.7193255 (HDP 2.3.3.1-7 - non modificato)
* HDInsight (Linux) 3.4.1000.0.7195842 (HDP 2.4.1.0-327)
* SDK 1.5.8

Questa versione contiene gli aggiornamenti seguenti.

| Titolo | Descrizione | Area interessata (ad esempio servizio, componente o SDK) | Tipo di cluster (ad esempio Hadoop, HBase o Storm) | JIRA (se applicabile) |
|-------------------------------------------------|------------------------------------------------------|---------------------------------------------------------|-----------------------------------------------------|----------------------|
| È stata aggiunta la versione 3.4 di HDInsight e sono state aggiornate le versioni HDP di tutti i cluster di HDInsight | Con questa versione, è stata aggiunta la versione 3.4 di HDInsight (basata su HDP 2.4) e sono state aggiornate anche le altre versioni di HDP. Le note sulla versione di HDP 2.4 sono disponibili [qui](http://docs.hortonworks.com/HDPDocuments/HDP2/HDP-2.4.0/bk_HDP_RelNotes/content/ch_relnotes_v240.html) e altre informazioni sulle versioni di HDInsight sono disponibili [qui](hdinsight-component-versioning.md).| Service | Tutti i cluster Linux| N/D
| HDInsight Premium | HDInsight è ora disponibile in due categorie: Standard e Premium. Attualmente HDInsight Premium è disponibile in anteprima e solo per i cluster Hadoop e Spark su Linux. Per altre informazioni, vedere [qui](hdinsight-component-versioning.md#hdinsight-standard-and-hdinsight-premium).| Service | Hadoop e Spark su Linux| N/D
| Microsoft R Server | HDInsight Premium mette a disposizione Microsoft R Server, che può essere incluso con i cluster Spark e Hadoop su Linux. Per altre informazioni, vedere [Panoramica di R Server su HDInsight (anteprima)](hdinsight-hadoop-r-server-overview.md).| Service | Hadoop e Spark su Linux| N/D
| Spark 1.6.0 | I cluster HDInsight 3.4 ora includono Spark 1.6.0| Service | Cluster Spark su Linux| N/D
| Miglioramenti del notebook Jupyter | I notebook Jupyter disponibili con cluster Spark ora offrono kernel Spark supplementari. Includono inoltre miglioramenti quali utilizzo di %%magic, la visualizzazione automatica e l'integrazione con le librerie di visualizzazione Python (come matplotlib). Per altre informazioni, vedere [Kernel disponibili per i Jupyter Notebook](hdinsight-apache-spark-jupyter-notebook-kernels.md). | Service | Cluster Spark su Linux | N/D

## Note sulla versione di HDInsight rilasciata il 22/03/2016

Numeri di versione completi per i cluster HDInsight distribuiti con questa versione:

* HDInsight (Windows) 2.1.10.875.2159884 (HDP 1.3.12.0-01795 - non modificato)
* HDInsight (Windows) 3.0.6.875.2159884 (HDP 2.0.13.0-2117 - non modificato)
* HDInsight (Windows) 3.1.4.875.2159884 (HDP 2.1.15.0-2374 - non modificato)
* HDInsight (Windows) 3.2.7.875.2159884 (HDP 2.2.9.1-7 - non modificato)
* HDInsight (Windows) 3.3.0.875.2159884 (HDP 2.3.3.1-16)
* HDInsight (Linux) 3.2.1000.0.7193255 (HDP 2.2.9.1-8 - non modificato)
* HDInsight (Linux) 3.3.1000.0.7193255 (HDP 2.3.3.1-7 - non modificato)
* SDK 1.5.8

Questa versione contiene gli aggiornamenti seguenti.

| Titolo | Descrizione | Area interessata (ad esempio servizio, componente o SDK) | Tipo di cluster (ad esempio Hadoop, HBase o Storm) | JIRA (se applicabile) |
|-------------------------------------------------|------------------------------------------------------|---------------------------------------------------------|-----------------------------------------------------|----------------------|
| Versioni aggiornate di HDInsight per tutti i cluster HDInsight | Con questo rilascio sono state aggiornate le versioni di HDInsight per tutti i cluster HDInsight| Service | Tutti| N/D


## Note sulla versione di HDInsight rilasciata il 10/03/2016

Numeri di versione completi per i cluster HDInsight distribuiti con questa versione:

* HDInsight (Windows) 2.1.10.859.2123216 (HDP 1.3.12.0-01795 - non modificato)
* HDInsight (Windows) 3.0.6.859.2123216 (HDP 2.0.13.0-2117 - non modificato)
* HDInsight (Windows) 3.1.4.859.2123216 (HDP 2.1.15.0-2374 - non modificato)
* HDInsight (Windows) 3.2.7.859.2123216 (HDP 2.2.9.1-7)
* HDInsight (Windows) 3.3.0.859.2123216 (HDP 2.3.3.1-5 - non modificato)
* HDInsight (Linux) 3.2.1000.7076817 (HDP 2.2.9.1-8)
* HDInsight (Linux) 3.3.1000.7076817 (HDP 2.3.3.1-7)
* SDK 1.5.8

Questa versione contiene gli aggiornamenti seguenti.

| Titolo | Descrizione | Area interessata (ad esempio servizio, componente o SDK) | Tipo di cluster (ad esempio Hadoop, HBase o Storm) | JIRA (se applicabile) |
|-------------------------------------------------|------------------------------------------------------|---------------------------------------------------------|-----------------------------------------------------|----------------------|
| Versioni aggiornate di HDInsight per tutti i cluster HDInsight | Con questo rilascio sono state aggiornate le versioni di HDInsight per tutti i cluster HDInsight| Service | Tutti| N/D

## Note per la versione di HDInsight rilasciata il 27/01/2016

Numeri di versione completi per i cluster HDInsight distribuiti con questa versione:

* HDInsight (Windows) 2.1.10.817.2028315 (HDP 1.3.12.0-01795 - non modificato)
* HDInsight (Windows) 3.0.6.817.2028315 (HDP 2.0.13.0-2117 - non modificato)
* HDInsight (Windows) 3.1.4.817.2028315 (HDP 2.1.15.0-2374 - non modificato)
* HDInsight (Windows) 3.2.7.817.2028315 (HDP 2.2.9.1-1)
* HDInsight (Windows) 3.3.0.817.2028315 (HDP 2.3.3.1-5 - non modificato)
* HDInsight (Linux) 3.2.1000.4072335 (HDP 2.2.9.1-1)
* HDInsight (Linux) 3.3.1000.4072335 (HDP 2.3.3.1-1)
* SDK 1.5.8

Questa versione contiene gli aggiornamenti seguenti.

| Titolo | Descrizione | Area interessata (ad esempio servizio, componente o SDK) | Tipo di cluster (ad esempio Hadoop, HBase o Storm) | JIRA (se applicabile) |
|-------------------------------------------------|------------------------------------------------------|---------------------------------------------------------|-----------------------------------------------------|----------------------|
| Versioni aggiornate di HDInsight per tutti i cluster HDInsight | Con questo rilascio sono state aggiornate le versioni di HDInsight per tutti i cluster HDInsight| Service | Tutti| N/D

## Note sulla versione di HDInsight rilasciata il 02/12/2015

Numeri di versione completi per i cluster HDInsight distribuiti con questa versione:

* HDInsight (Windows) 2.1.10.763.1931434 (HDP 1.3.12.0-01795 - non modificato)
* HDInsight (Windows) 3.0.6.763.1931434 (HDP 2.0.13.0-2117 - non modificato)
* HDInsight (Windows) 3.1.4.763.1931434 (HDP 2.1.15.0-2374 - non modificato)
* HDInsight (Windows) 3.2.7.763.1931434 (HDP 2.2.7.1-34 - non modificato)
* HDInsight (Windows) 3.3.1000.0 (HDP 2.3.3.1-5)
* HDInsight (Linux) 3.2.1000.0.6392801 (HDP 2.2.7.1-34 - non modificato)
* HDInsight (Linux) 3.3.1000.0 (HDP 2.3.3.0-3039)
* SDK 1.5.8

Questa versione contiene gli aggiornamenti seguenti.

| Titolo | Descrizione | Area interessata (ad esempio servizio, componente o SDK) | Tipo di cluster (ad esempio Hadoop, HBase o Storm) | JIRA (se applicabile) |
|-------------------------------------------------|------------------------------------------------------|---------------------------------------------------------|-----------------------------------------------------|----------------------|
| È stata aggiunta la versione 3.3 di HDInsight e sono state aggiornate le versioni HDP di tutti i cluster di HDInsight | Con questa versione, è stata aggiunta la versione 3.3 di HDInsight (basata su HDP 2.3) e sono state aggiornate anche le altre versioni di HDP. Le note sulla versione di HDP 2.3 sono disponibili [qui](http://docs.hortonworks.com/HDPDocuments/HDP2/HDP-2.3.0/bk_HDP_RelNotes/content/ch_relnotes_v230.html) e altre informazioni sulle versioni di HDInsight sono disponibili [qui](hdinsight-component-versioning.md).| Service | Tutti| N/D

## Note per la versione di HDInsight rilasciata il 30/11/2015

Numeri di versione completi per i cluster HDInsight distribuiti con questa versione:

* HDInsight (Windows) 2.1.10.757.1923908 (HDP 1.3.12.0-01795 - non modificato)
* HDInsight (Windows) 3.0.6.757.1923908 (HDP 2.0.13.0-2117 - non modificato)
* HDInsight (Windows) 3.1.4.757.1923908 (HDP 2.1.15.0-2374 - non modificato)
* HDInsight (Windows) 3.2.7.757.1923908 (HDP 2.2.7.1-34)
* HDInsight (Linux) 3.2.1000.0.6392801 (HDP 2.2.7.1-34)
* SDK 1.5.8

Questa versione contiene gli aggiornamenti seguenti.

| Titolo | Descrizione | Area interessata (ad esempio servizio, componente o SDK) | Tipo di cluster (ad esempio Hadoop, HBase o Storm) | JIRA (se applicabile) |
|-------------------------------------------------|------------------------------------------------------|---------------------------------------------------------|-----------------------------------------------------|----------------------|
| Versioni aggiornate di HDInsight per tutti i cluster HDInsight e le versioni HDP per i cluster HDInsight 3.2 (Windows e Linux) | Con questa versione, le versioni di HDInsight e HDP sono state aggiornate | Service | Tutti| N/D


## Note sulla versione di HDInsight rilasciata il 27/10/2015

Numeri di versione completi per i cluster HDInsight distribuiti con questa versione:

* HDInsight (Windows) 2.1.10.726.1866228 (HDP 1.3.12.0-01795 - non modificato)
* HDInsight (Windows) 3.0.6.726.1866228 (HDP 2.0.13.0-2117 - non modificato)
* HDInsight (Windows) 3.1.4.726.1866228 (HDP 2.1.15.0-2374 - non modificato)
* HDInsight (Windows) 3.2.7.726.1866228 (HDP 2.2.7.1-33)
* HDInsight (Linux) 3.2.1000.0.6035701 (HDP 2.2.7.1-33)
* SDK 1.5.8

Questa versione contiene gli aggiornamenti seguenti.

| Titolo | Descrizione | Area interessata (ad esempio servizio, componente o SDK) | Tipo di cluster (ad esempio Hadoop, HBase o Storm) | JIRA (se applicabile) |
|-------------------------------------------------|------------------------------------------------------|---------------------------------------------------------|-----------------------------------------------------|----------------------|
| Versioni di HDInsight aggiornate per tutti i cluster di HDInsight (Windows e Linux) | Con questa versione, le versioni di HDInsight e HDP sono state aggiornate | Service | Tutti| N/D
| Jupyter corretto per i cluster di Windows Spark con cluster in lettere maiuscole | I cluster con nomi DNS specificati in lettere maiuscole presentavano problemi con i notebook Jupyter a causa di una verifica della richiesta di origine. La correzione ha riguardato la modifica del nome DNS per la configurazione di Jupyter in lettere minuscole. | Service | HDInsight Spark (Windows)| N/D


## Note per la versione di HDInsight rilasciata il 20/10/2015

Numeri di versione completi per i cluster HDInsight distribuiti con questa versione:

* HDInsight 2.1.10.716.1846990 (Windows) (HDP 1.3.12.0-01795 - invariato)
* HDInsight 3.0.6.716.1846990 (Windows) (HDP 2.0.13.0-2117 - invariato)
* HDInsight 3.1.4.716.1846990 (Windows) (HDP 2.1.16.0-2374)
* HDInsight 3.2.7.716.1846990 (Windows) (HDP 2.2.7.1-0004)
* HDInsight 3.2.1000.0.5930166 (Linux) (HDP 2.2.7.1-0004)
* SDK 1.5.8

Questa versione contiene gli aggiornamenti seguenti.

| Titolo | Descrizione | Area interessata (ad esempio servizio, componente o SDK) | Tipo di cluster (ad esempio Hadoop, HBase o Storm) | JIRA (se applicabile) |
|-------------------------------------------------|------------------------------------------------------|---------------------------------------------------------|-----------------------------------------------------|----------------------|
| Versione HDP predefinita modificata in HDP 2.2 | La versione predefinita per i cluster HDInsight basati su Windows è stata modificata in HDP 2.2. HDInsight versione 3.2 (HDP 2.2) è disponibile a livello generale dal mese di febbraio 2015. Questa modifica riguarda solo la versione predefinita del cluster, installata quando non viene effettuata alcuna selezione esplicita durante il provisioning del cluster tramite il portale di Azure, cmdlet di PowerShell o SDK. | Service | Tutti| N/D |
|Modifiche al formato dei nomi di macchina virtuale per la distribuzione di più cluster HDInsight basati su Linux in un'unica rete virtuale | In questa versione è stato aggiunto il supporto per la distribuzione di più cluster HDInsight basati su Linux in un'unica rete virtuale. Nell'ambito di questo aggiornamento, il formato dei nomi di macchina virtuale nel cluster è stato modificato, rispettivamente, da headnode*, workernode* e zookeepernode* a hn*, wn* e zk*. Non è consigliabile impostare una dipendenza diretta sul formato dei nomi di macchina virtuale, poiché sono soggetti a modifica. Usare "hostname -f" nel computer locale o nelle API di Ambari per determinare l'elenco di host e il mapping dei componenti agli host. Altre informazioni sono disponibili agli indirizzi [https://github.com/apache/ambari/blob/trunk/ambari-server/docs/api/v1/hosts.md](https://github.com/apache/ambari/blob/trunk/ambari-server/docs/api/v1/hosts.md) e [https://github.com/apache/ambari/blob/trunk/ambari-server/docs/api/v1/host-components.md](https://github.com/apache/ambari/blob/trunk/ambari-server/docs/api/v1/host-components.md). | Service | Cluster HDInsight basati su Linux | N/D |
| Modifiche di configurazione | Per i cluster HDInsight 3.1, sono state abilitate le configurazioni seguenti: <ul><li>tez.yarn.ats.enabled e yarn.log.server.url. In questo modo, Application Timeline Server e il server di log possono supportare anche file di registro.</li></ul>Per i cluster HDInsight 3.2, sono state modificate le configurazioni seguenti: <ul><li>mapreduce.fileoutputcommitter.algorithm.version è stato impostato su 2. In questo modo, è possibile usare la versione 2 di FileOutputCommitter.</li></ul> | Service | Tutti | N/D |


## Note per la versione di HDInsight rilasciata il 09/09/2015

Numeri di versione completi per i cluster HDInsight distribuiti con questa versione:

* HDInsight 2.1.10.675.1768697 (HDP 1.3.12.0-01795, senza modifiche)
* HDInsight 3.0.6.675.1768697 (HDP 2.0.13.0-2117, senza modifiche)
* HDInsight 3.1.4.675.1768697 (HDP 2.1.15.0-2334, senza modifiche)
* HDInsight 3.2.6.675.1768697 (HDP 2.2.6.1-0012, senza modifiche)
* SDK 1.5.8

Questa versione contiene gli aggiornamenti seguenti.

| Titolo | Descrizione | Area interessata (ad esempio servizio, componente o SDK) | Tipo di cluster (ad esempio Hadoop, HBase o Storm) | JIRA (se applicabile) |
|-------------------------------------------------|------------------------------------------------------|---------------------------------------------------------|-----------------------------------------------------|----------------------|
| Versioni aggiornate di HDInsight per tutti i cluster HDInsight | Con questa versione le versioni di HDInsight sono state aggiornate | Service | Tutti| N/D |

## Note per la versione di HDInsight rilasciata il 31/07/2015

Numeri di versione completi per i cluster HDInsight distribuiti con questa versione:

* HDInsight 2.1.10.640.1695824 (HDP 1.3.12.0-01795, senza modifiche)
* HDInsight 3.0.6.640.1695824 (HDP 2.0.13.0-2117, senza modifiche)
* HDInsight 3.1.4.640.1695824 (HDP 2.1.15.0-2334, senza modifiche)
* HDInsight 3.2.6.640.1695824 (HDP 2.2.6.1-0012, senza modifiche)
* SDK 1.5.8

Questa versione contiene gli aggiornamenti seguenti.

| Titolo | Descrizione | Area interessata (ad esempio servizio, componente o SDK) | Tipo di cluster (ad esempio Hadoop, HBase o Storm) | JIRA (se applicabile) |
|-------------------------------------------------|------------------------------------------------------|---------------------------------------------------------|-----------------------------------------------------|----------------------|
| Correggere il flusso di lavoro della nuova immagine del nodo del cluster Spark | Corretto un bug che impediva il ripristino dei nodi del cluster Spark dopo la nuova immagine | Service | Spark| N/D |


## Note per la versione di HDInsight rilasciata il 31/07/2015

Numeri di versione completi per i cluster HDInsight distribuiti con questa versione:

* HDInsight 2.1.10.635.1684502 (HDP 1.3.12.0-01795, senza modifiche)
* HDInsight 3.0.6.635.1684502 (HDP 2.0.13.0-2117, senza modifiche)
* HDInsight 3.1.4.635.1684502 (HDP 2.1.15.0-2334, senza modifiche)
* HDInsight 3.2.6.635.1684502 (HDP 2.2.6.1-0012, senza modifiche)
* SDK 1.5.8

Questa versione contiene gli aggiornamenti seguenti.

| Titolo | Descrizione | Area interessata (ad esempio servizio, componente o SDK) | Tipo di cluster (ad esempio Hadoop, HBase o Storm) | JIRA (se applicabile) |
|-------------------------------------------------|------------------------------------------------------|---------------------------------------------------------|-----------------------------------------------------|----------------------|
| Versioni aggiornate di HDInsight per tutti i cluster HDInsight | Con questa versione le versioni di HDInsight sono state aggiornate | Service | Tutti| N/D |


## Note per la versione di HDInsight rilasciata il 07/07/2015

Numeri di versione completi per i cluster HDInsight distribuiti con questa versione:

* HDInsight 2.1.10.610.1630216 (HDP 1.3.12.0-01795, senza modifiche)
* HDInsight 3.0.6.610.1630216 (HDP 2.0.13.0-2117, senza modifiche)
* HDInsight 3.1.4.610.1630216 (HDP 2.1.15.0-2334, senza modifiche)
* HDInsight 3.2.4.610.1630216 (HDP 2.2.6.1-0012)
* SDK 1.5.8


Questa versione contiene gli aggiornamenti seguenti.

| Titolo | Descrizione | Area interessata (ad esempio servizio, componente o SDK) | Tipo di cluster (ad esempio Hadoop, HBase o Storm) | JIRA (se applicabile) |
|-------------------------------------------------|------------------------------------------------------|---------------------------------------------------------|-----------------------------------------------------|----------------------|
| Versioni aggiornate di HDP per i cluster HDInsight 3.2 | Con questa versione HDInsight 3.2 distribuisce HDP 2.2.6.1-0012 | Servizio | Tutti | N/D |


## Note per la versione di HDInsight rilasciata il 26/06/2015

Numeri di versione completi per i cluster HDInsight distribuiti con questa versione:

* HDInsight 2.1.10.601.1610731 (HDP 1.3.12.0-01795, senza modifiche)
* HDInsight 3.0.6.601.1610731 (HDP 2.0.13.0-2117, senza modifiche)
* HDInsight 3.1.4.601.1610731 (HDP 2.1.15.0-2334, senza modifiche)
* HDInsight 3.2.4.601.1610731 (HDP 2.2.6.1-0011)
* SDK 1.5.8


Questa versione contiene gli aggiornamenti seguenti.

<table border="1">
<tr>
<th>Titolo</th>
<th>Descrizione</th>
<th>Area interessata (ad esempio servizio, componente o SDK)</p></th>
<th>Tipo di cluster (ad esempio Hadoop, HBase o Storm)</th>
<th>JIRA (se applicabile)</th>
</tr>


<tr>
<td>Versioni aggiornate di HDP per i cluster HDInsight 3.2</td>
<td>Con questa versione HDInsight 3.2 distribuisce HDP 2.2.6.1</td>
<td>Service</td>
<td>Tutti</td>
<td>N/D</td>
</tr>

</table>

## Note sulla versione di HDInsight rilasciata il 18/06/2015

Numeri di versione completi per i cluster HDInsight distribuiti con questa versione:

* HDInsight 2.1.10.596.1601657 (HDP 1.3.12.0-01795 - senza modifiche)
* HDInsight 3.0.6.596.1601657 (HDP 2.0.13.0-2117 - senza modifiche)
* HDInsight 3.1.4.596.1601657 (HDP 2.1.15.0-2334)
* HDInsight 3.2.4.596.1601657 (HDP 2.2.6.1-0002)
* SDK 1.5.8


Questa versione contiene gli aggiornamenti seguenti.

<table border="1">
<tr>
<th>Titolo</th>
<th>Descrizione</th>
<th>Area interessata (ad esempio servizio, componente o SDK)</p></th>
<th>Tipo di cluster (ad esempio Hadoop, HBase o Storm)</th>
<th>JIRA (se applicabile)</th>
</tr>


<tr>
<td>Altre porte HTTPS aperte</td>
<td>Il servizio cloud ora apre le 5 porte comprese tra 8001 e 8005 5 nel cluster, ad esempio all’indirizzo https://<clustername>.azurehdinsight.net:8001/. Le richieste URL vengono autenticate utilizzando lo stesso meccanismo di password di autenticazione di base della porta 443. Tali porte vengono associate alla stessa porta sul nodo head attivo. È possibile utilizzare le azioni di script per porre i servizi clienti in ascolto su tali porte nel nodo head e instradarli all'esterno del cluster.</td>
<td>Servizio cloud</td>
<td>Tutti</td>
<td>N/D</td>
</tr>

<tr>
<td>Problema di riproduzione casuale MapReduce intermittente per HDInsight 3.2</td>
<td>Correzione di una rara race condition intermittente nella condizione casuale di MapReduce nei cluster di grandi dimensioni che provoca occasionali errori delle attività. Per ulteriori informazioni, vedere <a href="https://issues.apache.org/jira/browse/MAPREDUCE-6361" target="_blank">MAPREDUCE 6361</a>.</td>
<td>Funzionalità principale di Hadoop</td>
<td>Tutti</td>
<td><a href="https://issues.apache.org/jira/browse/MAPREDUCE-6361" target="_blank">MAPREDUCE-6361</a></td>
</tr>

<tr>
<td>Spostare nella versione più recente di Azure SDK per Java versione 2.2 per HDInsight 3.2</td>
<td>Passaggio alla versione più recente di Azure SDK per Java utilizzato dal driver WASB. L'SDK più recente include alcune correzioni e le relative note sulla versione sono disponibili all'indirizzo https://github.com/Azure/azure-storage-java/blob/master/ChangeLog.txt.</td>
<td>Funzionalità principale di Hadoop</td>
<td>Tutti</td>
<td><a href="https://issues.apache.org/jira/browse/HADOOP-11959" target="_blank">HADOOP 11959</a></td>
</tr>

<tr>
<td>Passare a HDP 2.1.15 per i cluster HDInsight 3.1</td>
<td>Le note sulla versione di Hortonworks sono disponibili <a href="http://docs.hortonworks.com/HDPDocuments/HDP2/HDP-2.1.15-Win/bk_releasenotes_HDP-Win/content/ch_relnotes-HDP-2.1.15.html" target="_blank">qui</a>.</td>
<td>HDP</td>
<td>Tutti</td>
<td>N/D</td>
</tr>

</table>

## Note per la versione di HDInsight rilasciata il 04/06/2015

Numeri di versione completi per i cluster HDInsight distribuiti con questa versione:

* HDInsight 2.1.10.583.1575584 (HDP 1.3.12.0-01795 - senza modifiche)
* HDInsight 3.0.6.583.1575584 (HDP 2.0.13.0-2117 - senza modifiche)
* HDInsight 3.1.3.583.1575584 (HDP 2.1.12.1-0003 - senza modifiche)
* HDInsight 3.2.4.583.1575584 (HDP 2.2.6.1-1)
* SDK 1.5.8


Questa versione contiene gli aggiornamenti seguenti.

<table border="1">
<tr>
<th>Titolo</th>
<th>Descrizione</th>
<th>Area interessata (ad esempio servizio, componente o SDK)</p></th>
<th>Tipo di cluster (ad esempio Hadoop, HBase o Storm)</th>
<th>JIRA (se applicabile)</th>
</tr>


<tr>
<td>Correzione per errore gateway non valido 502 per i cluster Storm</td>
<td>Questa versione corregge un bug che interessa l'API per l'invio di processi che ha comportato l'inattività del sito Web dopo un riavvio.</td>
<td>Service</td>
<td>Storm</td>
<td>N/D</td>
</tr>

</table>

## Note per la versione di HDInsight rilasciata il 01/06/2015

Numeri di versione completi per i cluster HDInsight distribuiti con questa versione:

* HDInsight 2.1.10.577.1563827 (HDP 1.3.12.0-01795 - senza modifiche)
* HDInsight 3.0.6.577.1563827 (HDP 2.0.13.0-2117 - senza modifiche)
* HDInsight 3.1.3.577.1563827 (HDP 2.1.12.1-0003 - senza modifiche)
* HDInsight 3.2.4.577.1563827 (HDP 2.2.6.0-2800 - senza modifiche)
* SDK 1.5.8


Questa versione contiene gli aggiornamenti seguenti.

<table border="1">
<tr>
<th>Titolo</th>
<th>Descrizione</th>
<th>Area interessata (ad esempio servizio, componente o SDK)</p></th>
<th>Tipo di cluster (ad esempio Hadoop, HBase o Storm)</th>
<th>JIRA (se applicabile)</th>
</tr>


<tr>
<td>Varie correzioni di bug</td>
<td>Questa versione corregge bug correlati al provisioning di cluster.</td>
<td>Service</td>
<td>Tutti i tipi di cluster</td>
<td>N/D</td>
</tr>

</table>

## Note per la versione di HDInsight rilasciata il 27/05/2015

Numeri di versione completi per i cluster HDInsight distribuiti con questa versione:

* HDInsight 3.2.4.570.1554102 (HDP 2.2.6.0-2800)
* Altre versioni di cluster e SDK non vengono distribuiti all'interno di questa versione.


Questa versione contiene gli aggiornamenti seguenti.

<table border="1">
<tr>
<th>Titolo</th>
<th>Descrizione</th>
<th>Area interessata (ad esempio servizio, componente o SDK)</p></th>
<th>Tipo di cluster (ad esempio Hadoop, HBase o Storm)</th>
<th>JIRA (se applicabile)</th>
</tr>


<tr>
<td>Aggiornamento HDP 2.2</td>
<td>Questa versione di HDInsight 3.2 contiene HDP 2.2.6 e introduce diverse importanti correzioni di bug per HDInsight. Le note sulla versione complete sono disponibili sul sito Web relativo alle <a href="http://dev.hortonworks.com.s3.amazonaws.com/HDPDocuments/HDP2/HDP-2.2.6/HDP_RelNotes_v226/index.html">note sulla versione di HDP 2.2.6</a>.</td>
<td>HDP</td>
<td>Tutti i tipi di cluster</td>
<td>N/D</td>
</tr>

<tr>
<td>Passare alla configurazione di memoria predefinita del contenitore Yarn</td>
<td>In questo aggiornamento la memoria predefinita disponibile per i contenitori YARN (yarn.nodemanager.resource.memory-mb e yarn.scheduler.maximum-allocation-mb), avviata dal Node Manager, è stata aumentata a 5632 MB. In precedenza era stato ridotto a 4608 MB, ma in base a varie esecuzioni del processo, il nuovo valore deve offrire affidabilità e prestazioni migliori per la maggior parte dei processi, pertanto è stato scelto questo valore predefinito superiore. Come consueto, in caso di dipendenza critica nella configurazione della memoria, impostare il valore in modo esplicito durante la creazione del cluster.</td>
<td>HDP</td>
<td>Tutti i tipi di cluster</td>
<td>N/D</td>
</tr>

<tr>
<td>Parità di configurazione predefinita per i cluster HBase e Storm</td>
<td>Questo aggiornamento consente di ripristinare l'uso degli stessi valori di configurazione YARN dei cluster Hadoop per i cluster Hbase e Storm. Questa operazione viene eseguita per la parità in tutti i tipi di cluster.</td>
<td>HDP</td>
<td>HBase, Storm</td>
<td>N/D</td>
</tr>

</table>

## Note per la versione di HDInsight rilasciata il 20/05/2015

Numeri di versione completi per i cluster HDInsight distribuiti con questa versione:

* HDInsight 2.1.10.564.1542093 (HDP 1.3.12.0-01795 - senza modifiche)
* HDInsight 3.0.6.564.1542093 (HDP 2.0.13.0-2117 - senza modifiche)
* HDInsight 3.1.3.564.1542093 (HDP 2.1.12.1-0003)
* HDInsight 3.2.4.564.1542093 (HDP 2.2.4.6-2)
* SDK 1.5.8

Questa versione contiene gli aggiornamenti seguenti.

<table border="1">
<tr>
<th>Titolo</th>
<th>Descrizione</th>
<th>Area interessata (ad esempio servizio, componente o SDK)</p></th>
<th>Tipo di cluster (ad esempio Hadoop, HBase o Storm)</th>
<th>JIRA (se applicabile)</th>
</tr>


<tr>
<td>Supporto dell'EventHub SCP.NET</td>
<td>I pacchetti cluster aggiornati per HDInsight Storm introducono nuove funzionalità in SCP.NET. Sarà ora possibile accedere alle nuove API nel generatore di topologie che rendono più semplice utilizzare EventHubSpout o gli Spout Java. È necessario aggiornare l'SDK per il client SCP.NET in modo che sia compatibile con i nuovi cluster quando i contratti sono stati aggiornati. Per informazioni dettagliate sulle nuove API, note sulla versione e sull'utilizzo (correzioni di bug incluse), fare riferimento al file Leggimi incluso nel pacchetto NuGet di SCP.NET.</td>
<td>Strumenti VS</td>
<td>Cluster Storm HDInsight 3.2</td>
<td>N/D</td>
</tr>

<tr>
<td>Aggiornamento del driver JDBC</td>
<td>Il driver è stato aggiornato alla versione supportata di SQL Server in sqljdbc_4.1.5605.100.</td>
<td>Metastore</td>
<td>Tutti</td>
<td>N/D</td>
</tr>
</table>

## Note per la versione di HDInsight rilasciata il 27/04/2015

Numeri di versione completi per i cluster HDInsight distribuiti con questa versione:

* HDInsight 2.1.10.537.1486660 (HDP 1.3.12.0-01795, senza modifiche)
* HDInsight 3.0.6.537.1486660 (HDP 2.0.13.0-2117, senza modifiche)
* HDInsight 3.1.3.537.1486660 (HDP 2.1.12.0-2329, senza modifiche)
* HDInsight 3.2.3.537.1486660 (HDP 2.2.2.2-4)
* SDK 1.5.8

Questa versione contiene gli aggiornamenti seguenti.

<table border="1">
<tr>
<th>Titolo</th>
<th>Descrizione</th>
<th>Area interessata (ad esempio servizio, componente o SDK)</p></th>
<th>Tipo di cluster (ad esempio Hadoop, HBase o Storm)</th>
<th>JIRA (se applicabile)</th>
</tr>


<tr>
<td>Correggere le dipendenze DLL</td>
<td>Rimuove la dipendenza di HDInsight dal framework per unit test.</td>
<td>SDK</td>
<td>Hadoop</td>
<td>N/D</td>
</tr>

<tr>
<td>Correzione dei bug per race condition</td>
<td>Prima di eseguire il polling sullo stato, una richiesta creata dal cluster deve ora attendere che la richiesta PUT venga accettata</td>
<td>SDK</td>
<td>Hadoop</td>
<td>N/D</td>
</tr>
</table>

## Note per la versione di HDInsight rilasciata il 14/04/2015

Numeri di versione completi per i cluster HDInsight distribuiti con questa versione:

* HDInsight 2.1.10.521.1453250 (HDP 1.3.12.0-01795, senza modifiche)
* HDInsight 3.0.6.521.1453250 (HDP 2.0.13.0-2117, senza modifiche)
* HDInsight 3.1.3.521.1453250 (HDP 2.1.12.0-2329, senza modifiche)
* HDInsight 3.2.3.525.1459730 (HDP 2.2.2.2-2)
* SDK 1.5.6

Questa versione contiene gli aggiornamenti seguenti.

<table border="1">
<tr>
<th>Titolo</th>
<th>Descrizione</th>
<th>Area interessata (ad esempio servizio, componente o SDK)</p></th>
<th>Tipo di cluster (ad esempio Hadoop, HBase o Storm)</th>
<th>JIRA (se applicabile)</th>
</tr>


<tr>
<td>Correzioni di bug</td>
<td>In questa versione di HDI 3.2 sono incluse correzioni per Apache TEZ 2214 e TEZ 1923. Queste correzioni sono specificamente necessarie per particolari query Hive con Tez che richiedono la riproduzione con sequenza casuale di una notevole quantità di dati.
</td>
<td>HDP</td>
<td>Hadoop</td>
<td><a href="https://issues.apache.org/jira/browse/TEZ-2214">TEZ 2214</a></br><a href="https://issues.apache.org/jira/browse/TEZ-1923">TEZ 1923</a></td>
</tr>
</table>

## Note per la versione di HDInsight rilasciata il 06/04/2015

Numeri di versione completi per i cluster HDInsight distribuiti con questa versione:

* HDInsight 2.1.10.521.1453250 (HDP 1.3.12.0-01795, senza modifiche)
* HDInsight 3.0.6.521.1453250 (HDP 2.0.13.0-2117, senza modifiche)
* HDInsight 3.1.3.521.1453250 (HDP 2.1.12.0-2329, senza modifiche)
* HDInsight 3.2.3.521.1453250 (HDP 2.2.2.2-1)
* SDK 1.5.6

Questa versione contiene gli aggiornamenti seguenti.

<table border="1">
<tr>
<th>Titolo</th>
<th>Descrizione</th>
<th>Area interessata (ad esempio servizio, componente o SDK)</p></th>
<th>Tipo di cluster (ad esempio Hadoop, HBase o Storm)</th>
<th>JIRA (se applicabile)</th>
</tr>


<tr>
<td>HDInsight .NET SDK 1.5.6</td>
<td>Aggiornamenti per la rimozione di alcune classi interne per HDInsight su Linux.</td>
<td>SDK</td>
<td>Hadoop</td>
<td>N/D</td>
</tr>

<tr>
<td>Libreria Avro 1.5.6</td>
<td>Aggiunto <b>KnownTypeAttribute</b> per il metodo <b>GetAllKnownTypes</b>. Corretto NullReferenceException quando un tipo è null per il metodo GetAllKnownTypes.</td>
<td>SDK</td>
<td>Hadoop</td>
<td>N/D</td>
</tr>

<tr>
<td>Correzioni di bug</td>
<td>Diverse correzioni di bug per il servizio</td>
<td>Service</td>
<td>Tutti</td>
<td>N/D</td>
</tr>

</table>
<br>

## Note per la versione di HDInsight rilasciata il 01/04/2015

Numeri di versione completi per i cluster HDInsight distribuiti con questa versione:

* HDInsight 2.1.10.513.1431705 (HDP 1.3.12.0-01795)
* HDInsight 3.0.6.513.1431705 (HDP 2.0.13.0-2117)
* HDInsight 3.1.3.513.1431705 (HDP 2.1.12.0-2329)
* HDInsight 3.2.3.513.1431705 (HDP 2.2.2.1-2600)
* SDK 1.5.5

Questa versione contiene gli aggiornamenti seguenti.

<table border="1">
<tr>
<th>Titolo</th>
<th>Descrizione</th>
<th>Area interessata (ad esempio servizio, componente o SDK)</p></th>
<th>Tipo di cluster (ad esempio Hadoop, HBase o Storm)</th>
<th>JIRA (se applicabile)</th>
</tr>


<tr>
<td>Possibilità di abilitare/disabilitare credenziali di desktop remoto su cluster Windows tramite .NET SDK</td>
<td>Supporto a livello di codice per l'abilitazione o la disabilitazione di credenziali RDP su cluster Windows.</td>
<td>SDK</td>
<td>Tutti</td>
<td>N/D</td>
</tr>

<tr>
<td>Possibilità di abilitare credenziali di desktop remoto su cluster di cui è in corso il provisioning</td>
<td>Supporto a livello di codice per l'abilitazione di credenziali di desktop remoto durante la creazione di cluster. In questo modo viene eliminato il processo in due passaggi che prevede prima il provisioning del cluster e quindi l'abilitazione del desktop remoto.</td>
<td>SDK</td>
<td>Tutti</td>
<td>N/D</td>
</tr>

<tr>
<td>Aggiornamento di Python alla versione 2.7.8</td>
<td>Aggiornamento di Python su cluster HDInsight alla versione 2.7.8, contenente alcuni importanti aggiornamenti per la sicurezza per le versioni di HDInsight 2.1, 3.0, 3.1 e 3.2</td>
<td>Service</td>
<td>Tutti</td>
<td>N/D</td>
</tr>

<tr>
<td>Modifica della configurazione di YARN</td>
<td>Il valore del parametro di configurazione di YARN yarn.resourcemanager.max-completed-applications è stato portato a 1000 per tutti i tipi di cluster per le versioni di HDInsight 3.1 e 3.2. Questo valore controlla solo l'elenco delle applicazioni completate nell'interfaccia utente di YARN. Per ottenere informazioni sulle applicazioni inviate prima all'elenco di applicazioni visualizzato nell'interfaccia utente, passare direttamente al server della cronologia.</td>
<td>YARN</td>
<td>Tutti</td>
<td>N/D</td>
</tr>

<tr>
<td>Ridimensionamento dei nodi in un cluster HBase</td>
<td>È ora possibile ridimensionare i nodi (verso l'alto e verso il basso) nei cluster HBase per HDInsight versioni 3.1 e 3.2</td>
<td>Service</td>
<td>HBase</td>
<td>N/D</td>
</tr>

<tr>
<td>Aggiornamento JDBC</td>
<td>Il driver JDBC SQL è aggiornato alla versione sqljdbc_4.0.2206.100 per HDInsight 3.2. Questa versione contiene importanti miglioramenti per la sicurezza.</td>
<td>HDP</td>
<td>Tutti</td>
<td>N/D</td>
</tr>

<tr>
<td>Aggiornamento della configurazione di JVM</td>
<td>Il valore del parametro di configurazione di JVM networkaddress.cache.ttl è stato portato a 300 secondi dal valore predefinito -1 per le versioni di HDInsight 3.1 e 3.2. Questo valore di configurazione controlla i criteri di memorizzazione nella cache relativi a ricerche di nomi completate con esito positivo dal servizio dei nomi. In questo modo viene corretto un bug correlato alla crescita e alla riduzione dei cluster HBase.</td>
<td>Service</td>
<td>HBase</td>
<td>N/D</td>
</tr>

<tr>
<td>Aggiornamento a Azure Storage SDK per Java 2.0</td>
<td>HDInsight 3.2 è stato aggiornato per l'uso della versione più recente di Azure Storage SDK per Java. Sono presenti diverse correzioni di bug per la versione corrente 0.6.0.</td>
<td>HDP</td>
<td>Tutti</td>
<td>N/D</td>
</tr>

<tr>
<td>Aggiornamento al codice sorgente WASB Apache</td>
<td>HDInsight 3.2 usa ora la versione più recente del codice per il driver del file system WASB di Apache Hadoop. Con questa modifica, il driver WASB viene ora fornito come file separato con estensione jar. Si tratta soltanto di un cambiamento di "imballaggio" che non determina alcuna modifica del comportamento del driver WASB. Il nome del file con estensione jar è hadoop-azure-2.6.0.jar.</td>
<td>HDP</td>
<td>Tutti</td>
<td>N/D</td>
</tr>

<tr>
<td>Aggiornamenti del nome file jar in HDInsight 3.2</td>
<td>L'aggiornamento a HDInsight versione 3.2 contiene diverse correzioni di bug. Sono inoltre stati aggiornati alcuni file interni con estensione jar forniti come parte di HDP. Si noti che i file con estensione jar sono interni al pacchetto HDP e non sono pensati per l'uso diretto da parte di applicazioni dei clienti. Le applicazioni devono integrare la propria versione dei file con estensione jar, in modo che un aggiornamento ai file con estensione jar HDP interni non interrompa le applicazioni dei clienti.</td>
<td>HDP</td>
<td>Tutti</td>
<td>N/D</td>
</tr>

</table>
<br>

## Note per la versione di HDInsight rilasciata il 03/03/2015

Numeri di versione completi per i cluster HDInsight distribuiti con questa versione:

* HDInsight 2.1.10.488.1375841 (HDP 1.3.9.0-01351, senza modifiche)
* HDInsight 3.0.6.488.1375841 (HDP 2.0.9.0-2097, senza modifiche)
* HDInsight 3.1.3.488.1375841 (HDP 2.1.10.0-2290, senza modifiche)
* HDInsight 3.2.3.488.1375841 (HDP-2.2.10.0-2340, senza modifiche)
* SDK 1.5.0 (senza modifiche)

Questa versione contiene l'aggiornamento seguente.

<table border="1">
<tr>
<th>Titolo</th>
<th>Descrizione</th>
<th>Area interessata (ad esempio servizio, componente o SDK)</p></th>
<th>Tipo di cluster (ad esempio Hadoop, HBase o Storm)</th>
<th>JIRA</th>
</tr>


<tr>
<td>Miglioramenti dell'affidabilità</td>
<td>Sono state apportate correzioni che assicurano una migliore scalabilità del servizio per gestire un aumento del carico rispetto alla creazione del cluster.</td>
<td>Service</td>
<td>Tutti</td>
<td>N/D</td>
</tr>



</table>
<br>

## Note per la versione di HDInsight rilasciata il 18/02/2015

Numeri di versione completi per i cluster HDInsight distribuiti con questa versione:

* HDInsight 2.1.10.471.1342507 (HDP 1.3.9.0-01351, senza modifiche)
* HDInsight 3.0.6.471.1342507 (HDP 2.0.9.0-2097, senza modifiche)
* HDInsight 3.1.3.471.1342507 (HDP 2.1.10.0-2290, senza modifiche)
* HDInsight 3.2.3.471.1342507 (HDP-2.2.10.0-2340)
* SDK 1.5.0

Questa versione contiene gli aggiornamenti seguenti.

<table border="1">
<tr>
<th>Titolo</th>
<th>Descrizione</th>
<th>Area interessata (ad esempio servizio, componente o SDK)</p></th>
<th>Tipo di cluster (ad esempio Hadoop, HBase o Storm)</th>
<th>JIRA (se applicabile)</th>
</tr>


<tr>
<td>Cluster HDInsight 3.2</td>
<td>Hadoop 2.6/HDP2.2 è disponibile con cluster HDInsight 3.2. e contiene importanti aggiornamenti per tutti i componenti open source. Per altri dettagli, vedere Novità delle versioni cluster di Hadoop incluse in HDInsight e le <a href ="http://docs.hortonworks.com/HDPDocuments/HDP2/HDP-2.2.0/HDP_2.2.0_Release_Notes_20141202_version/index.html" target="_blank">note per la versione 2.2.0.0 di HDP</a>.</td>
<td>Software open source</td>
<td>Tutti</td>
<td>N/D</td>
</tr>

<tr>
<td>HDinsight in Linux (anteprima)</td>
<td>È possibile distribuire cluster per l'esecuzione in Ubuntu Linux. Per altri dettagli, vedere Introduzione a HDInsight su Linux.</td>
<td>Service</td>
<td>Hadoop</td>
<td>N/D</td>
</tr>

<tr>
<td>Disponibilità generale di Storm</td>
<td>I cluster Apache Storm sono in genere disponibili. Per altri dettagli, vedere Introduzione all'uso di Storm in HDInsight.</td>
<td>Service</td>
<td>Storm</td>
<td>N/D</td>
</tr>

<tr>
<td>Dimensioni delle macchine virtuali</td>
<td>Azure HDInsight è disponibile per più tipi e dimensioni di macchine virtuali. HDInsight può usare macchine virtuali di dimensioni da A2 ad A7 per scopi generali, nodi della serie D che integrano unità SSD (Solid-State Drive) e processori più veloci del 60 percento. Può usare inoltre macchine virtuali di dimensioni A8 e A9 con supporto per InfiniBand per connessioni di rete più veloci.</td>
<td>Service</td>
<td>Tutti</td>
<td>N/D</td>
</tr>

<tr>
<td>Scalabilità del cluster</td>
<td>È possibile modificare il numero di nodi dati per un cluster HDInsight in esecuzione senza che sia necessario eliminarlo o ricrearlo. Attualmente questa possibilità è offerta solo dai tipi di cluster Hadoop Query e Apache Storm. Il supporto per il tipo di cluster Apache HBase sarà disponibile a breve. Per altre informazioni, vedere Gestire i cluster di HDInsight.</td>
<td>Service</td>
<td>Hadoop, Storm</td>
<td>N/D</td>
</tr>

<tr>
<td>Strumenti di Visual Studio</td>
<td>Oltre a fornire gli strumenti completi per Apache Storm, Apache Hive in Visual Studio è stato aggiornato per includere funzionalità di completamento istruzioni, convalida locale e supporto per il debug migliorato. Per altre informazioni, vedere Introduzione all'uso di HDInsight Tools per Visual Studio.</td>
<td>Strumenti</td>
<td>Hadoop</td>
<td>N/D</td>
</tr>

<tr>
<td>Connettore Hadoop per DocumentDB</td>
<td>Il connettore Hadoop per DocumentDB consente di eseguire operazioni complesse di aggregazione, analisi e manipolazione sui documenti JSON senza schema archiviati in raccolte DocumentDB o in account di database. Per altre informazioni e per un'esercitazione, vedere Eseguire un processo Hadoop usando DocumentDB e HDInsight.</td>
<td>Service</td>
<td>Hadoop</td>
<td>N/D</td>
</tr>

<tr>
<td>Correzioni di bug</td>
<td>Sono state apportate diverse correzioni di bug di minore entità per i servizi di HDInsight. Non sono previste modifiche del comportamento rilevabili dai clienti.</td>
<td>Service</td>
<td>Tutti</td>
<td>N/D</td>
</tr>

</table>
<br>

## Note per la versione di HDInsight rilasciata il 06/02/2015

Numeri di versione completi per i cluster HDInsight distribuiti con questa versione:

* HDInsight 2.1.10.463.1325367 (HDP 1.3.9.0-01351, senza modifiche)
* HDInsight 3.0.6.463.1325367 (HDP 2.0.9.0-2097, senza modifiche)
* HDInsight 3.1.2.463.1325367 (HDP 2.1.10.0-2290)
* SDK N/D

Questa versione contiene gli aggiornamenti seguenti.

<table border="1">
<tr>
<th>Titolo</th>
<th>Descrizione</th>
<th>Area interessata (ad esempio servizio, componente o SDK)</p></th>
<th>Tipo di cluster (ad esempio Hadoop, HBase o Storm)</th>
<th>JIRA (se applicabile)</th>
</tr>


<tr>
<td>Correzioni di bug</td>
<td>Sono state apportate diverse correzioni di bug di minore entità per i servizi di HDInsight. Non sono previste modifiche del comportamento rilevabili dai clienti.</td>
<td>Service</td>
<td>Tutti</td>
<td>N/D</td>
</tr>

<tr>
<td>Aggiornamento di manutenzione HDP 2.1</td>
<td>HDInsight 3.1 è aggiornato per la distribuzione di HDP 2.1.10.0. Per altre informazioni, vedere le <a href ="http://docs.hortonworks.com/HDPDocuments/HDP2/HDP-2.1.10/bk_releasenotes_hdp_2.1/content/ch_relnotes-HDP-2.1.10.html" target="_blank">note per la versione HDP-2.1.10</a>. </td>
<td>Software open source</td>
<td>Tutti</td>
<td>N/D</td>
</tr>

<tr>
<td>Aggiornamenti binari HDP</td>
<td>In HBase sono presenti alcuni file con estensione jar di cui sono stati aggiornati i nomi. Questi file con estensione jar sono usati internamente da HBase, motivo per cui non sono previste dipendenze dai nomi di tali file. Sono inclusi:
<ul>
<li>./lib/jetty-6.1.26.hwx.jar</li>
<li>./lib/jetty-sslengine-6.1.26.hwx.jar</li>
<li>./lib/jetty-util-6.1.26.hwx.jar</li>
</ul>
</td>
<td>Software open source</td>
<td>HBase</td>
<td>N/D</td>
</tr>

</table>
<br>

## Note per la versione di HDInsight rilasciata il 29/01/2015

Numeri di versione completi per i cluster HDInsight distribuiti con questa versione:

* HDInsight 2.1.10.455.1309616 (HDP 1.3.9.0-01351, senza modifiche)
* HDInsight 3.0.6.455.1309616 (HDP 2.0.9.0-2097, senza modifiche)
* HDInsight 3.1.2.455.1309616 (HDP 2.1.9.0-2196, senza modifiche)
* SDK N/D

Questa versione contiene l'aggiornamento seguente.

<table border="1">
<tr>
<th>Titolo</th>
<th>Descrizione</th>
<th>Area interessata (ad esempio servizio, componente o SDK)</p></th>
<th>Tipo di cluster (ad esempio Hadoop, HBase o Storm)</th>
<th>JIRA (se applicabile)</th>
</tr>


<tr>

<td>Correzioni di bug</td>
<td>Sono state apportate alcune importanti correzioni di bug che migliorano l'affidabilità dei cluster HDInsight durante gli aggiornamenti di Azure.</td>
<td>Service</td>
<td>Tutti</td>
<td>N/D</td>
</tr>



</table>
<br>

## Note per la versione di HDInsight rilasciata il 5/1/2015

Numeri di versione completi per i cluster HDInsight distribuiti con questa versione:

* HDInsight 2.1.10.420.1246118 (HDP 1.3.9.0-01351, senza modifiche)
* HDInsight 3.0.6.420.1246118 (HDP 2.0.9.0-2097, senza modifiche)
* HDInsight 3.1.2.420.1246118 (HDP 2.1.9.0-2196, senza modifiche)


Questa versione contiene gli aggiornamenti seguenti.

<table border="1">
<tr>
<th>Titolo</th>
<th>Descrizione</th>
<th>Componente</th>
<th>Tipo di cluster</th>
<th>JIRA (se applicabile)</th>
</tr>


<tr>
<td>Esempi per l'analisi delle tendenze di Twitter e i consigli cinematografici basati su Mahout</td>
<td><p>In questa versione la console query di HDInsight include due esempi aggiuntivi:</p>

<p><b>Analisi delle tendenze Twitter</b><br>
Le API pubbliche offerte da siti quali Twitter costituiscono un'utile origine di dati per l'analisi e la comprensione delle tendenze più popolari. In questa esercitazione viene descritto come usare Hive per ottenere un elenco di utenti di Twitter che hanno inviato il maggior numero di tweet contenenti una determinata parola. </p>

<p><b>Consigli cinematografici di Mahout</b><br>
Apache Mahout è una libreria Machine Learning di Apache Hadoop. Mahout contiene gli algoritmi per l'elaborazione dei dati, ad esempio applicazione di filtri, classificazione e clustering. In questa esercitazione viene usato un motore di raccomandazione per generare consigli cinematografici in base ai film visti dagli amici.</p></td>
<td>Console query</td>
<td>Hadoop</td>
<td>N/D</td>
</tr>

<tr>
<td>Ripristino del valore predefinito per la configurazione di Hive: hive.auto.convert.join.noconditionaltask.size</td>
<td><p>Questa configurazione delle dimensioni si applica a join mappa convertiti automaticamente. Il valore rappresenta la somma delle dimensioni delle tabelle che possono essere convertite in mappe di hash adatte alle dimensioni della memoria. In una versione precedente questo valore è stato aumentato da 10 MB (valore predefinito) a 128 MB. Il nuovo valore di 128 MB, tuttavia, provoca errori di esecuzione dei processi per problemi di memoria insufficiente. In questa versione è stato ripristinato il valore predefinito di&#160;10 MB. I clienti possono comunque scegliere di sostituire questo valore durante la creazione dei cluster in base alle query e alle dimensioni di tabella. Per altre informazioni su questa impostazione e su come sostituirla, vedere la sezione della documentazione di Hortonworks relativa alla <a href="http://docs.hortonworks.com/HDPDocuments/HDP2/HDP-2.0.0.2/ds_Hive/optimize-joins.html#JoinOptimization-OptimizeAutoJoinConversion" target="_blank">ottimizzazione della conversione automatica dei join</a>. </p></td>
<td>Hive</td>
<td>Hadoop, Hbase</td>
<td>N/D</td>
</tr>

</table>
<br>

## Note per la versione di HDInsight rilasciata il 23/12/2014

Numeri di versione completi per i cluster HDInsight distribuiti con questa versione:

* HDInsight 2.1.10.420.1246783 (versione HDP senza modifiche)
* HDInsight 3.0.6.420.1246783 (versione HDP senza modifiche)
* HDInsight 3.1.1.420.1246783 (versione HDP senza modifiche)

Questa versione contiene l'aggiornamento seguente.

<table border="1">
<tr>
<th>Titolo</th>
<th>Descrizione</th>
<th>Componente</th>
<th>Tipo di cluster</th>
<th>JIRA (se applicabile)</th>
</tr>


<tr>
<td>Errori intermittenti durante la creazione dei cluster a causa di un carico eccessivo</td>
<td><p>L'algoritmo migliorato per il download dei pacchetti HDP durante la creazione dei cluster permette una gestione più efficace degli errori dovuti a un carico eccessivo.</p></td>
<td>Service</td>
<td>Hadoop, Hbase, Storm</td>
<td>N/D</td>
</tr>



</table>
<br>

## Note per la versione di HDInsight rilasciata il 18/12/2014

Questa versione include l'aggiornamento dei componenti indicato di seguito.

<table border="1">
<tr>
<th>Titolo</th>
<th>Descrizione</th>
<th>Componente</th>
<th>Tipo di cluster</th>
<th>JIRA (se applicabile)</th>
</tr>

<tr>
<td><a href = "hdinsight-hadoop-customize-cluster.md" target="_blank">Personalizzazione dei cluster disponibile a livello generale</a></td>
<td><p>La personalizzazione offre la possibilità di personalizzare i cluster Azure HDInsight con i progetti disponibili nell'ecosistema Apache Hadoop. Con questa nuova funzionalità, è possibile provare e distribuire progetti Hadoop in Azure HDInsight. Questo scenario viene reso possibile attraverso la funzionalità **Script Action**, che consente di modificare cluster Hadoop in modo arbitrario usando script personalizzati. Questa personalizzazione è disponibile su tutti i tipi di cluster HDInsight, inclusi Hadoop, HBase e Storm. Per dimostrare l'efficacia di questa funzionalità, Microsoft ha documentato il processo di installazione dei moduli <a href = "hdinsight-hadoop-spark-install.md" target="_blank">Spark</a>, <a href = "hdinsight-hadoop-r-scripts.md" target="_blank">R</a>, <a href = "hdinsight-hadoop-solr-install.md" target="_blank">Solr</a> e <a href = "hdinsight-hadoop-giraph-install.md" target="_blank">Giraph</a>. Questa versione consente ora ai clienti di specificare anche azioni di script personalizzate tramite il portale di Azure e include informazioni aggiuntive e procedure consigliate su come creare azioni di script personalizzate usando metodi helper e indicazioni su come testare l'azione di script. </p></td>
<td>Disponibilità generale delle funzionalità</td>
<td>Tutti</td>
<td>N/D</td>
</tr>


</table>
<br>

## Note per la versione di HDinsight rilasciata il 05/12/2014

Numeri di versione completi per i cluster HDInsight distribuiti con questa versione:

* HDInsight 2.1.9.406.1221105 (HDP 1.3.9.0-01351)
* HDInsight 3.0.5.406.1221105 (HDP 2.0.9.0-2097)
* HDInsight 3.1.1.406.1221105 (HDP 2.1.9.0-2196)
* SDK HDInsight N/D

Questa versione include gli aggiornamenti dei componenti indicati di seguito.

<table border="1">
<tr>
<th>Titolo</th>
<th>Descrizione</th>
<th>Componente</th>
<th>Tipo di cluster</th>
<th>JIRA (se applicabile)</th>
</tr>

<tr>
<td>Correzione di bug: errore intermittente durante l'aggiunta di un numero elevato di partizioni a una tabella in un file DLL di Hive. </td>
<td><p>Se si verifica un errore di connessione intermittente con il database metastore di Hive durante l'aggiunta di molte partizioni a una tabella di Hive, è possibile che si verifichi un errore del file DLL di Hive. Se si verifica questo errore, nel log degli errori di Hive verrà visualizzata l'istruzione seguente: </p><p>"ERROR [main]: ql.Driver (SessionState.java:printError(547)) - FAILED: Execution Error, return code 1 from org.apache.hadoop.hive.ql.exec.DDLTask. MetaException(message:java.lang.RuntimeException: commitTransaction was called but openTransactionCalls = 0. This probably indicates that there are unbalanced calls to openTransaction/commitTransaction)"</p></td>
<td>Hive</td>
<td>Hadoop, Hbase</td>
<td>HIVE-482 (Si tratta di JIRA interno, non può essere indicato esternamente. Specificato qui come riferimento).</td>
</tr>

<tr>
<td>Correzione di bug: interruzione occasionale nella console di query di HDInsight</td>
<td>Quando si verifica questo problema, nel log WebHCat verrà visualizzata l'istruzione seguente per il processo di avvio di WebHCat: <p>"org.apache.hive.hcatalog.templeton.CatchallExceptionMapper | org.apache.hadoop.ipc.RemoteException(org.apache.hadoop.yarn.exceptions.YarnRuntimeException): Could not load history file {wasb url to the history file}"</p></td>
<td>WebHCat</td>
<td>Hadoop</td>
<td>HIVE-482 (Si tratta di JIRA interno, non può essere indicato esternamente. Specificato qui come riferimento).</td>
</tr>

<tr>
<td>Correzione di bug: picco occasionale della latenza delle query Hbase</td>
<td>Se si verifica questo problema, gli utenti noteranno un picco occasionale di 3 secondi nella latenza delle query Hbase. </td>
<td>Gateway del cluster HDInsight</td>
<td>HBase</td>
<td>N/D</td>
</tr>

<tr>
<td>Modifiche al nome file jar HDP</td>
<td>Per i cluster HDI versione 3.0 sono state apportate alcune modifiche ai file jar interni installati da HDP. jetty-6.1.26.jar è stato sostituito con jetty-6.1.26.hwx.jar. jetty-util-6.1.26.jar è stato sostituito con jetty-util-6.1.26.hwx.jar. Queste modifiche sono applicabili ai progetti Hadoop, Mahout, WebHCat e Oozie.</td>
<td>Hadoop, Mahout, WebHCat, Oozie</td>
<td>Hadoop, HBase</td>
<td>N/D</td>
</tr>

</table>
<br>


## Note per la versione di HDInsight rilasciata il 21/11/2014

Numeri di versione completi per i cluster HDInsight distribuiti con questa versione:

* HDInsight 2.1.9.382.1169709 (nessuna modifica rispetto al 14/11/2014)
* HDInsight 3.0.5.382.1169709 (nessuna modifica rispetto al 14/11/2014)
* HDInsight 3.1.1.382.1169709 (nessuna modifica rispetto al 14/11/2014)
* HDInsight SDK 1.4.0

Questa versione include gli aggiornamenti dei componenti indicati di seguito.

<table border="1">
<tr>
<th>Titolo</th>
<th>Descrizione</th>
<th>Componente</th>
<th>Tipo di cluster</th>
<th>JIRA (se applicabile)</th>
</tr>

<tr>
<td>Accesso ai registri applicazioni</td>
<td>Possibilità di enumerare a livello di codice le applicazioni eseguite nei cluster e di scaricare i registri rilevanti relativi ad applicazioni o ai contenitori per semplificare il debug di applicazioni che presentano problemi.</td>
<td>SDK</td>
<td>Hadoop</td>
<td>N/D</td>
</tr>

<tr>
<td>Possibilità di specificare un nome di area geografica in IHdInsightClient.DeleteCluster </td>
<td>Azure HDInsight SDK permette di specificare un nome di area quando si usa **DeleteCluster**. Questo consente di sbloccare i clienti che hanno due risorse con lo stesso nome in aree geografiche diverse e non riescono a eliminarle.</td>
<td>SDK</td>
<td>Tutti</td>
<td>N/D</td>
</tr>

<tr>
<td>ClusterDetails.DeploymentId</td>
<td>L'oggetto **ClusterDetails** restituisce un campo **DeploymentID** che rappresenta un identificatore univoco del cluster. L'univocità è garantita in tutti i tentativi di creazione di cluster con gli stessi nomi.</td>
<td>SDK</td>
<td>Tutti</td>
<td>N/D</td>
</tr>
</table>
<br>

## Note per la versione di HDInsight rilasciata il 14/11/2014

Numeri di versione completi per i cluster HDInsight distribuiti con questa versione:

* HDInsight 2.1.9.382.1169709
* HDInsight 3.0.5.382.1169709
* HDInsight 3.1.1.382.1169709

Questa versione include le nuove funzionalità, gli aggiornamenti di componenti e le correzioni di bug seguenti.

<table border="1">
<tr>
<th>Titolo</th>
<th>Descrizione</th>
<th>Componente</th>
<th>Tipo di cluster</th>
<th>JIRA (se applicabile)</th>
</tr>

<tr>
<td>Azione di script (anteprima)</td>
<td>Anteprima della funzionalità di personalizzazione dei cluster, che permette la modifica di cluster Hadoop in modo arbitrario tramite script personalizzati. Grazie a questa funzionalità, gli utenti possono provare a usare e distribuire nei cluster Azure HDInsight i progetti disponibili nell'ecosistema Apache Hadoop. Questa funzionalità di personalizzazione è disponibile in tutti i tipi di cluster HDInsight, inclusi Hadoop, HBase e Storm.</td>
<td>Nuova funzionalità</td>
<td>Tutti</td>
<td>N/D</td>
</tr>

<tr>
<td>Processi predefiniti per l'analisi dei log relativi a Siti web e Archiviazione di Azure</td>
<td>La console di query di HDInsight include una raccolta introduttiva che supporta soluzioni da usare sui dati dell'utente o su dati di esempio.
<p>**Soluzioni utilizzabili sui dati dell'utente**:<br>
Sono stati creati processi per alcuni degli scenari più comuni di analisi dei dati, per offrire un punto di partenza per la creazione di soluzioni personalizzate. È possibile usare i propri dati insieme al processo per verificarne il funzionamento. Quando si è pronti, sarà quindi possibile usare le competenze acquisite per creare una soluzione modellata sul processo predefinito.</p>
<p>**Soluzioni utilizzabili sui dati di esempio**:<br>
Per imparare a usare HDInsight, esaminare nel dettaglio alcuni scenari di base, ad esempio l'analisi di log Web e dati di sensori. Si imparerà a usare HDInsight per analizzare questi dati e a connettere altre applicazioni e servizi a questi dati. La visualizzazione dei dati tramite la connessione a Microsoft Excel offre un esempio delle potenzialità di questo approccio.</p></td>
<td>Console query</td>
<td>Hadoop</td>
<td>N/D</td>
</tr>

<tr>
<td>Correzione per la perdita di memoria in Templeton</td>
<td>È stata risolta la perdita di memoria in Templeton che ha interessato i clienti con cluster con esecuzioni di lunga durata o che inviavano centinaia di richieste di processi al secondo. Questo problema si presentava sotto forma di errori di tipo 5xx in Templeton e come soluzione alternativa veniva suggerito di riavviare il servizio. Questa soluzione alternativa non è più necessaria.</td>
<td>Templeton</td>
<td>Tutti</td>
<td>https://issues.apache.org/jira/browse/HADOOP-11248</td>
</tr>
</table>
<br>


**Nota**: per illustrare le nuove funzionalità rese disponibili dalla personalizzazione dei cluster, sono state documentate le procedure che usano azioni di script per installare moduli Spark e R in un cluster. Per altre informazioni, vedere:

* [Installare e usare Spark 1.0 nei cluster HDInsight](hdinsight-hadoop-spark-install.md)
* [Installare e usare R nei cluster Hadoop HDInsight](hdinsight-hadoop-r-scripts.md)




## Note per la versione di HDInsight rilasciata il 07/11/2014

Numeri di versione completi per i cluster HDInsight distribuiti con questa versione:

* HDInsight 2.1 2.1.9.374.1153876
* HDInsight 3.0 3.0.5.374.1153876
* HDInsight 3.1 3.1.1.374.1153876

Questa versione include gli aggiornamenti dei componenti indicati di seguito.

<table border="1">
<tr>
<th>Titolo</th>
<th>Descrizione</th>
<th>Componente</th>
<th>Tipo di cluster</th>
<th>JIRA (se applicabile)</th>
</tr>

<tr>
<td>HDP 2.1.7</td>
<td>Questa versione è basata su Hortonworks Data Platform (HDP) 2.1.7. Per altre informazioni, vedere le <a href="http://docs.hortonworks.com/HDPDocuments/HDP2/HDP-2.1.7-Win/bk_releasenotes_HDP-Win/content/ch_relnotes-HDP-2.1.7.html" target="_blank">note sulla versione per HDP 2.1.7</a>.</td>
<td>HDP</td>
<td>Tutti</td>
<td>N/D</td>
</tr>

<tr>
<td>Server di sequenza temporale YARN</td>
<td>YARN Timeline Server (noto anche come Generic Application History Server) è stato abilitato per impostazione predefinita. Timeline Server fornisce informazioni generiche sulle applicazioni complete, ad esempio l'ID, il nome, lo stato, l'ora di invio e l'ora di completamento dell'applicazione. Queste informazioni sull'applicazione possono essere recuperate dal nodo head tramite l'accesso all'URI http://headnodehost:8188 oppure tramite l'esecuzione del comando YARN seguente: yarn application –list –appStates ALL. È anche possibile recuperare in modo remoto queste applicazioni tramite l'API REST all'indirizzo https://{ClusterDnsName}. azurehdinsight.net/ws/v1/applicationhistory/. Per altre informazioni dettagliate, vedere <a href="http://hadoop.apache.org/docs/r2.4.0/hadoop-yarn/hadoop-yarn-site/TimelineServer.html" target="_blank">Server di sequenza temporale YARN</a>.</td>
<td>Servizio, YARN</td>
<td>Hadoop, HBase</td>
<td>N/D</td>
</tr>

<tr>
<td>ID di distribuzione cluster</td>
<td>A partire dalla versione più recente dell'SDK, 1.3.3.1.5426.29232, gli utenti possono accedere agli ID univoci rilasciati da HDInsight per ogni cluster. Questo consente ai clienti di individuare istanze univoche dei cluster quando si riutilizza un nome DNS in scenari di creazione o eliminazione.</td>
<td>SDK</td>
<td>Tutti</td>
<td>N/D</td>
</tr>
</table>
<br>

**Nota**: il bug che impediva la visualizzazione del numero di versione completo nel portale o la restituzione di tale numero da parte dell'SDK o di PowerShell è stato risolto in questa versione.

## Note per la versione rilasciata il 15/10/2014

Questa versione dell'hotfix ha risolto una perdita di memoria in Templeton che influiva sugli utenti che ne fanno ampio uso. In alcuni casi, tali utenti visualizzavano errori che si manifestavano come codici di errore 500 perché le richieste non avevano memoria sufficiente per l'esecuzione. La soluzione alternativa per questo problema consisteva nel riavvio del servizio Templeton. Il problema è stato risolto.


## Note per la versione rilasciata il 07/10/2014

* Quando si usa l'endpoint Ambari, "https://{clusterDns}.azurehdinsight.net/ambari/api/v1/clusters/{clusterDns}.azurehdinsight.net/services/{servicename}/components/{componentname}", il campo *host\_name* restituisce il nome di dominio completo (FQDN) del nodo anziché il solo nome host. Ad esempio, invece di restituire "**headnode0**", si otterrà il nome FQDN "**headnode0.{ClusterDNS}.azurehdinsight.net**". Questa modifica si è resa necessaria per facilitare scenari in cui sia possibile implementare più tipi di cluster, come HBase e Hadoop, in un'unica rete virtuale. Ciò accade, ad esempio, quando si usa HBase come piattaforma back-end per Hadoop.

* Sono state fornite nuove impostazioni di memoria per l'implementazione predefinita del cluster HDInsight. Le precedenti impostazioni di memoria predefinite non tenevano adeguatamente conto delle indicazioni relative al numero di core CPU implementati. Queste nuove impostazioni di memoria dovrebbero offrire valori predefiniti migliori, sulla base delle raccomandazioni di Hortonworks. Per modificare queste impostazioni, consultare la documentazione di riferimento dell'SDK sulla modifica della configurazione del cluster. Le nuove impostazioni di memoria usate dal cluster HDInsight predefinito con 4 core CPU (8 contenitori) sono elencate nella tabella seguente. Vengono forniti tra parentesi anche i valori usati prima di questa versione.

<table border="1">
<tr><th>Componente</th><th>Allocazione della memoria</th></tr>
<tr><td> yarn.scheduler.minimum-allocation</td><td>768 MB (in precedenza 512 MB)</td></tr>
<tr><td> yarn.scheduler.maximum-allocation</td><td>6.144 MB (invariato)</td></tr>
<tr><td>yarn.nodemanager.resource.memory</td><td>6.144 MB (invariato)</td></tr>
<tr><td>mapreduce.map.memory</td><td>768 MB (in precedenza 512 MB)</td></tr>
<tr><td>mapreduce.map.java.opts</td><td>opts=-Xmx512m (in precedenza -Xmx410m)</td></tr>
<tr><td>mapreduce.reduce.memory</td><td>1.536 MB (in precedenza 1.024 MB)</td></tr>
<tr><td>mapreduce.reduce.java.opts</td><td>opts=-Xmx1024m (in precedenza -Xmx819m)</td></tr>
<tr><td>yarn.app.mapreduce.am.resource</td><td>768 MB (in precedenza 1.024 MB)</td></tr>
<tr><td>yarn.app.mapreduce.am.command</td><td>opts=-Xmx512m (in precedenza -Xmx819m)</td></tr>
<tr><td>mapreduce.task.io.sort</td><td>256 MB (in precedenza 200 MB)</td></tr>
<tr><td>tez.am.resource.memory</td><td>1.536 MB (invariato)</td></tr>

</table><br>

Per altre informazioni sulle impostazioni di configurazione di memoria usate da YARN e da MapReduce su Hortonworks Data Platform usata da HDInsight, vedere l'articolo su come [determinare le impostazioni di configurazione di memoria HDP](http://docs.hortonworks.com/HDPDocuments/HDP2/HDP-2.1-latest/bk_installing_manually_book/content/rpm-chap1-11.html). Hortonworks ha fornito anche uno strumento per calcolare correttamente le impostazioni di memoria.

Per quanto riguarda Azure PowerShell e il messaggio di errore HDInsight SDK: "*Cluster is not configured for HTTP services access*":

* Questo errore è un [problema di compatibilità](https://social.msdn.microsoft.com/Forums/azure/a7de016d-8de1-4385-b89e-d2e7a1a9d927/hdinsight-powershellsdk-error-cluster-is-not-configured-for-http-services-access?forum=hdinsight) noto che può verificarsi a causa di una differenza tra la versione di SDK HDInsight o Azure PowerShell e la versione del cluster. Per i cluster creati sulla versione 8/15 o successiva esiste il supporto della nuova funzionalità di provisioning nelle reti virtuali, che però non viene interpretata correttamente dalle versioni precedenti di SDK HDInsight o Azure PowerShell. Il risultato è un errore in alcune operazioni di invio dei processi. Se si usano le API di HDInsight SDK o i cmdlet di Azure PowerShell (**Use-AzureRmHDInsightCluster** o **Invoke-AzureRmHDInsightHiveJob**) per inviare i processi, è possibile che tali operazioni abbiano esito negativo e che venga generato il messaggio di errore "*Cluster <clustername> is not configured for HTTP services access* (Il cluster <nomecluster> non è configurato per l'accesso ai servizi HTTP)". In alternativa, a seconda dell'operazione, è possibile che venga generato un altro messaggio che informa che *non è possibile eseguire la connessione al cluster*.

* Questi problemi di compatibilità sono stati risolti nelle versioni più recenti di SDK HDInsight e di Azure PowerShell. È consigliabile aggiornare HDInsight SDK alla versione 1.3.1.6 o successiva e gli strumenti di Azure PowerShell alla versione 0.8.8 o successiva. È possibile accedere alla versione più recente di HDInsight SDK da [NuGet](http://nuget.codeplex.com/wikipage?title=Getting%20Started) e agli strumenti di Azure PowerShell in [Come installare e configurare Azure PowerShell](../powershell-install-configure.md).



## Note per la versione di HDInsight 3.1 rilasciata il 12/09/2014

* Questa versione è basata su Hortonworks Data Platform (HDP) 2.1.5. Per un elenco dei bug corretti in questa versione, vedere la pagina relativa alle [correzioni disponibili in questa versione](http://docs.hortonworks.com/HDPDocuments/HDP2/HDP-2.1.5/bk_releasenotes_hdp_2.1/content/ch_relnotes-hdp-2.1.5-fixed.html) nel sito Hortonworks.
* Nella cartella delle librerie Pig, il file "avro-mapred-1.7.4.jar" è stato modificato in avro-mapred-1.7.4-hadoop2.jar. Questi file contengono una correzione di bug non essenziale che non causa interruzioni. È consigliabile che i clienti non creino una dipendenza diretta sul nome dello stesso file JAR in modo da evitare interruzioni quando i file vengono rinominati.


## Note per la versione rilasciata il 21/08/2014

* È stata aggiunta la configurazione WebHCat (HIVE-7155) seguente, che imposta il limita di memoria predefinito per un processo controller Templeton a 1 GB (il valore predefinito precedente era 512 MB):

	 templeton.mapper.memory.mb (=1024)

	* Questa modifica risolve l'errore seguente che si verificava in alcune query Hive a causa dei limiti di memoria più bassi: “Container is running beyond physical memory limits.”
	* Per tornare alle impostazioni predefinite precedenti, è possibile impostare questo valore di configurazione su 512 mediante Azure PowerShell al momento della creazione del cluster usando il comando seguente:

		Add-AzureRmHDInsightConfigValues -Core @{"templeton.mapper.memory.mb"="512";}


* Il nome host del ruolo è stato modificato in *zookeeper*. Questo influisce sulla risoluzione dei nomi nel cluster, ma non sulle API REST esterne. Se sono presenti componenti che usano il nome host *zookeepernode* sarà necessario aggiornarli in modo che usino il nuovo nome. I nuovi nomi per i tre nodi zookeeper sono:
	* zookeeper0
	* zookeeper1
	* zookeeper2
* La matrice di supporto delle versioni di HBase è stata aggiornata. Per i carichi di lavoro HBase di produzione è supportato solo HDInsight 3.1 (HBase versione 0.98). In futuro la versione 3.0 disponibile in anteprima non sarà supportata.

## Note sui cluster creati prima del 15/8/2014

È possibile che, a causa di una differenza tra le versioni di Azure PowerShell o HDInsight SDK e quelle di un cluster, venga generato un messaggio di errore di Azure PowerShell o HDInsight SDK, ad esempio "Cluster <clustername> is not configured for HTTP services access" o, a seconda dell'operazione, un altro messaggio di errore, ad esempio "Cannot connect to cluster". Per i cluster creati sulla versione 8/15 o successiva esiste il supporto della nuova funzionalità di provisioning nelle reti virtuali, che però non viene interpretata correttamente dalle versioni precedenti di Azure PowerShell o HDInsight SDK, dando origine a errori nelle operazioni di invio dei processi. Se si usano le API HDInsight SDK o i cmdlet di Azure PowerShell (come ad esempio Use-AzureRmHDInsightCluster o Invoke-AzureRmHDInsightHiveJob) per inviare i processi, è possibile che le operazioni abbiano esito negativo e che venga generato uno dei messaggi di errore descritti.

Questi problemi di compatibilità sono stati risolti nelle versioni più recenti di SDK HDInsight e di Azure PowerShell. È consigliabile aggiornare HDInsight SDK alla versione 1.3.1.6 o successiva e gli strumenti di Azure PowerShell alla versione 0.8.8 o successiva. È possibile ottenere l'accesso alla versione di HDInsight SDK più recente da [NuGet][nuget-link]. È possibile accedere agli strumenti di Azure PowerShell usando l'[installazione guidata piattaforma Web Microsoft][webpi-link].


## Note per la versione rilasciata il 28/07/2014

* **Disponibilità di HDInsight in nuove aree geografiche**: con questa versione la presenza geografica di HDInsight è stata estesa a tre nuove aree. I clienti di HDInsight possono creare cluster nelle aree seguenti:
	* Asia orientale
	* Stati Uniti centro-settentrionali
	* Stati Uniti centro-meridionali
* HDInsight versione 1.6 (HDP 1.1 e Hadoop 1.0.3) e HDInsight versione 2.1 (HDP1.3 e Hadoop 1.2) verranno rimossi dal portale di gestione di Azure. È possibile continuare a creare cluster Hadoop per queste versioni usando il cmdlet di Azure PowerShell [New-AzureRmHDInsightCluster](http://msdn.microsoft.com/library/dn593744.aspx) oppure usando [HDInsight SDK](http://msdn.microsoft.com/library/azure/dn469975.aspx). Per altre informazioni, fare riferimento alla pagina relativa al [controllo delle versioni del componente HDInsight](hdinsight-component-versioning.md).
* Modifiche a Hortonworks Data Platform (HDP) in questa versione:

<table border="1">
<tr><th>HDP</th><th>Modifiche</th></tr>
<tr><td>HDP 1.3/HDI 2.1</td><td>Nessuna modifica</td></tr>
<tr><td>HDP 2.0/HDI 3.0</td><td>Nessuna modifica</td></tr>
<tr><td>HDP 2.1/HDI 3.1</td><td>zookeeper: ['3.4.5.2.1.3.0-1948'] -> ['3.4.5.2.1.3.2-0002']</td></tr>


</table><br>

## Note per la versione rilasciata il 24/6/2014

In questa versione sono disponibili miglioramenti al servizio HDInsight:

* **Disponibilità di HDP 2.1**: HDInsight 3.1, che contiene HDP 2.1, è disponibile a livello generale e rappresenta la versione predefinita per i nuovi cluster.
* **HBase - Miglioramenti nel portale di gestione di Azure**: i cluster HBase sono disponibili in anteprima. È possibile creare cluster HBase dal portale con pochi clic.

HBase consente di creare numerosi carichi di lavoro in tempo reale su HDInsight, da siti Web interattivi da usare con grandi set di dati a servizi che archiviano dati di sensori e telemetria da milioni di endpoint. Il passaggio successivo consiste nell'analizzare i dati di questi carichi di lavoro tramite processi Hadoop e questo è immediatamente possibile in HDInsight mediante Azure PowerShell e il dashboard del cluster Hive.

### Apache Mahout è preinstallato in HDInsight 3.1

 [Mahout](http://hortonworks.com/hadoop/mahout/) è preinstallato nei cluster Hadoop HDInsight 3.1. In questo modo, è possibile eseguire processi Mahout senza dover configurare ulteriormente i cluster. Ad esempio, è possibile accedere in remoto a un cluster Hadoop usando il protocollo RDP (Remote Desktop Protocol) ed eseguire senza ulteriori passaggi il comando di Mahout Hello world:

		mahout org.apache.mahout.classifier.df.tools.Describe -p /user/hdp/glass.data -f /user/hdp/glass.info -d I 9 N L  

		mahout org.apache.mahout.classifier.df.BreimanExample -d /user/hdp/glass.data -ds /user/hdp/glass.info -i 10 -t 100

Per una spiegazione completa della procedura, vedere la documentazione relativa all'[esempio Breiman](https://mahout.apache.org/users/classification/breiman-example.html) nel sito Web di Apache Mahout.


### Le query Hive possono usare Tez in HDInsight 3.1

Hive 0.13 è disponibile in HDInsight 3.1 e può eseguire query mediante Tez, consentendo notevoli miglioramenti delle prestazioni. Tez non è abilitato per impostazione predefinita per le query Hive. Per usarlo, è necessario acconsentire esplicitamente. È possibile abilitare Tez eseguendo il frammento di codice seguente:

		set hive.execution.engine=tez;
		select sc_status, count(*), histogram_numeric(sc_bytes,5) from website_logs_orc_local group by sc_status;

Hortonworks ha pubblicato un elenco dettagliato dei miglioramenti delle prestazioni delle query Hive con Tez risultanti dall'esecuzione di benchmark standard. Per informazioni dettagliate, vedere l'articolo relativo al [benchmarking di Apache Hive 13 per la soluzione Hadoop di livello enterprise](http://hortonworks.com/blog/benchmarking-apache-hive-13-enterprise-hadoop/).

Per altri dettagli sull'uso di Hive con Tez, vedere l'articolo relativo a [Hive su Tez](https://cwiki.apache.org/confluence/display/Hive/Hive+on+Tez).

###Disponibilità globale
Con il rilascio di HDInsight in Hadoop 2.2, Microsoft ha reso HDInsight disponibile in tutte le principali aree geografiche di Azure. In particolare, sono stati collegati i data center dell'Europa occidentale e dell'Asia sudorientale. Questo consente ai clienti di individuare i cluster in un data center chiuso e potenzialmente situato in una zona con analoghi requisiti di conformità.


###Operazioni consigliate e sconsigliate tra le versioni dei cluster

**I metastore Oozie usati con un cluster HDInsight 3.1 non sono compatibili con le versioni precedenti dei cluster HDInsight 2.1 e non possono essere usati con questa versione precedente**.

Un database metastore Oozie personalizzato, implementato con un cluster HDInsight 3.1, non può essere riutilizzato con un cluster HDInsight 2.1, anche se tale metastore è stato originato con un cluster HDInsight 2.1. Questo scenario non è supportato perché lo schema del metastore viene aggiornato quando usato con un cluster HDInsight 3.1 e dunque non è più compatibile con il metastore richiesto dai cluster HDInsight 2.1. Qualsiasi tentativo di riutilizzare un metastore Oozie che è stato usato con un cluster HDInsight 3.1 renderà inutilizzabile il cluster HDInsight 2.1.

**I metastore Oozie non possono essere condivisi tra cluster.**

I metastore Oozie vengono collegati a cluster specifici e non possono essere condivisi tra cluster.

###Modifiche di rilievo

**Sintassi del prefisso**: nei cluster HDInsight 3.1 e 3.0 è supportata solo la sintassi "wasbs://". La sintassi "asv://" precedente è supportata nei cluster HDInsight 2.1 e 1.6, ma non è supportata nei cluster HDInsight 3.1 o 3.0 e non sarà supportata nelle versioni successive. Questo significa che i processi inviati a un cluster HDInsight 3.1 o 3.0 che usano esplicitamente la sintassi "asv://" avranno esito negativo. Sarà pertanto necessario usare la sintassi "wasbs://". Avrà inoltre esito negativo qualsiasi processo inviato a un cluster HDInsight 3.1 o 3.0 e creato con un metastore esistente che contiene riferimenti espliciti a risorse che usano la sintassi "asv://". Sarà necessario ricreare i metastore in modo da fare riferimento alle risorse usando la sintassi "wasbs://".


**Porte**: le porte usate dal servizio HDInsight sono state cambiate. I numeri di porta usati in precedenza erano compresi nell'intervallo di porte temporanee del sistema operativo Windows. Per le comunicazioni di breve durata basate su IP, le porte vengono allocate automaticamente da un intervallo di porte temporanee predefinito. Il nuovo set di numeri di porta per il servizio Hortonworks Data Platform (HDP) sono esterne a questo intervallo, per evitare conflitti con le porte usate dai servizi in esecuzione sul nodo head. I nuovi numeri di porta non dovrebbero provocare modifiche di rilievo. I nuovi numeri in uso sono i seguenti:

 **HDInsight 1.6 (HDP 1.1)** <table border="1">
<tr><th>Nome</th><th>Valore</th></tr>
<tr><td>dfs.http.address</td><td>namenodehost:30070</td></tr>
<tr><td>dfs.datanode.address</td><td>0.0.0.0:30010</td></tr>
<tr><td>dfs.datanode.http.address</td><td>0.0.0.0:30075</td></tr>
<tr><td>dfs.datanode.ipc.address</td><td>0.0.0.0:30020</td></tr>
<tr><td>dfs.secondary.http.address</td><td>0.0.0.0:30090</td></tr>
<tr><td>mapred.job.tracker.http.address</td><td>jobtrackerhost:30030</td></tr>
<tr><td>mapred.task.tracker.http.address</td><td>0.0.0.0:30060</td></tr>
<tr><td>mapreduce.history.server.http.address</td><td>0.0.0.0:31111</td></tr>
<tr><td>templeton.port</td><td>30111</td></tr>
</table><br>

 **HDInsight 3.1 e 3.0 (HDP 2.1 e 2.0)** <table border="1">
<tr><th>Nome</th><th>Valore</th></tr>
<tr><td>dfs.namenode.http-address</td><td>namenodehost:30070</td></tr>
<tr><td>dfs.namenode.https-address</td><td>headnodehost:30470</td></tr>
<tr><td>dfs.datanode.address</td><td>0.0.0.0:30010</td></tr>
<tr><td>dfs.datanode.http.address</td><td>0.0.0.0:30075</td></tr>
<tr><td>dfs.datanode.ipc.address</td><td>0.0.0.0:30020</td></tr>
<tr><td>dfs.namenode.secondary.http-address</td><td>0.0.0.0:30090</td></tr>
<tr><td>yarn.nodemanager.webapp.address</td><td>0.0.0.0:30060</td></tr>
<tr><td>templeton.port</td><td>30111</td></tr>
</table><br>

###Dipendenze

In HDInsight 3.x (HDP2.x) sono state aggiunte le dipendenze seguenti:

* guice-servlet
* optiq-core
* javax.inject
* activation
* jsr305
* geronimo-jaspic\_1.0\_spec
* jul-to-slf4j
* java-xmlbuilder
* ant
* commons-compiler
* jdo-api
* commons-math3
* paranamer
* jaxb-impl
* stringtemplate
* eigenbase-xom
* jersey-servlet
* commons-exec
* jaxb-api
* jetty-all-server
* janino
* xercesImpl
* optiq-avatica
* jta
* eigenbase-properties
* groovy-all
* hamcrest-core
* mail
* linq4j
* jpam
* jersey-client
* aopalliance
* geronimo-annotation\_1.0\_spec
* ant-launcher
* jersey-guice
* xml-apis
* stax-api
* asm-commons
* asm-tree
* wadl
* geronimo-jta\_1.1\_spec
* guice
* leveldbjni-all
* velocity
* jettison
* snappy-java
* jetty-all
* commons-dbcp

Le dipendenze seguenti non esistono più in HDInsight 3.x (HDP2.x):

* jdeb
* kfs
* sqlline
* ivy
* aspectjrt
* json
* core
* jdo2-api
* avro-mapred
* datanucleus-enhancer
* jsp
* commons-logging-api
* commons-math
* JavaEWAH
* aspectjtools
* javolution
* hdfsproxy
* hbase
* snappy

###Modifiche della versione

Tra HDInsight 2.x (HDP1.x) e HDInsight 3.x (HDP2.x) sono state apportate le seguenti modifiche della versione:

* metrics-core: ['2.1.2'] -> ['3.0.0']
* derbynet: ['10.4.2.0'] -> ['10.10.1.1']
* datanucleus: ['rdbms-3.0.8'] -> ['rdbms-3.2.9']
* jasper-compiler: ['5.5.12'] -> ['5.5.23']
* log4j: ['1.2.15', '1.2.16'] -> ['1.2.16', '1.2.17']
* derbyclient: ['10.4.2.0'] -> ['10.10.1.1']
* httpcore: ['4.2.4'] -> ['4.2.5']
* hsqldb: ['1.8.0.10'] -> ['2.0.0']
* jets3t: ['0.6.1'] -> ['0.9.0']
* protobuf-java: ['2.4.1'] -> ['2.5.0']
* derby: ['10.4.2.0'] -> ['10.10.1.1']
* jasper: ['runtime-5.5.12'] -> ['runtime-5.5.23']
* commons-daemon: ['1.0.1'] -> ['1.0.13']
* datanucleus-core: ['3.0.9'] -> ['3.2.10']
* datanucleus-api-jdo: ['3.0.7'] -> ['3.2.6']
* zookeeper: ['3.4.5.1.3.9.0-01320'] -> ['3.4.5.2.1.3.0-1948']
* bonecp: ['0.7.1.RELEASE'] -> ['
* 0\.8.0.RELEASE']


### Driver
Il driver JDBC Java Database Connnectivity) per SQL Server viene usato internamente da HDInsight e non viene usato per operazioni esterne. Se si desidera connettersi a HDInsight mediante ODBC (Open Database Connectivity), usare Microsoft Hive ODBC Driver. Per altre informazioni, vedere [Connettere Excel a HDInsight mediante Microsoft Hive ODBC Driver](hdinsight-connect-excel-hive-odbc-driver.md).


### Correzioni di bug

In questa versione le versioni seguenti di HDInsight sono state aggiornate con numerose correzioni di bug:

* HDInsight 2,1 (HDP 1,3)
* HDInsight 3,0 (HDP 2,0)
* HDInsight 3.1 (HDP 2.1)


## Note sulla versione di Hortonworks

Le note sulla versione relative alle piattaforme HDP (Hortonworks Data Platform) usate dalle versioni del cluster HDInsight sono disponibili nelle pagine seguenti:

* HDInsight versione 3.1 usa una distribuzione Hadoop basata su [Hortonworks Data Platform 2.1.7][hdp-2-1-7]. Si tratta del cluster Hadoop predefinito creato per l'uso del portale di Azure dopo il 7/11/2014. I cluster HDInsight 3.1 creati prima del 7/11/2014 sono basati su [Hortonworks Data Platform 2.1.1][hdp-2-1-1].

* HDInsight versione 3.0 usa una distribuzione Hadoop basata su [Hortonworks Data Platform 2.0][hdp-2-0-8].

* HDInsight versione 2.1 usa una distribuzione Hadoop basata su [Hortonworks Data Platform 1.3][hdp-1-3-0].

* HDInsight versione 1.6 usa una distribuzione Hadoop basata su [Hortonworks Data Platform 1.1][hdp-1-1-0].

[hdp-2-1-7]: http://docs.hortonworks.com/HDPDocuments/HDP2/HDP-2.1.7-Win/bk_releasenotes_HDP-Win/content/ch_relnotes-HDP-2.1.7.html

[hdp-2-1-1]: http://docs.hortonworks.com/HDPDocuments/HDP2/HDP-2.1.1/bk_releasenotes_hdp_2.1/content/ch_relnotes-hdp-2.1.1.html

[hdp-2-0-8]: http://docs.hortonworks.com/HDPDocuments/HDP2/HDP-2.0.8.0/bk_releasenotes_hdp_2.0/content/ch_relnotes-hdp2.0.8.0.html

[hdp-1-3-0]: http://docs.hortonworks.com/HDPDocuments/HDP1/HDP-1.3.0/bk_releasenotes_hdp_1.x/content/ch_relnotes-hdp1.3.0_1.html

[hdp-1-1-0]: http://docs.hortonworks.com/HDPDocuments/HDP1/HDP-Win-1.1/bk_releasenotes_HDP-Win/content/ch_relnotes-hdp-win-1.1.0_1.html

[nuget-link]: https://www.nuget.org/packages/Microsoft.WindowsAzure.Management.HDInsight/

[webpi-link]: http://go.microsoft.com/?linkid=9811175&clcid=0x409

[hdinsight-install-spark]: ../hdinsight-hadoop-spark-install/
[hdinsight-r-scripts]: ../hdinsight-hadoop-r-scripts/
 

<!---HONumber=AcomDC_0817_2016-->