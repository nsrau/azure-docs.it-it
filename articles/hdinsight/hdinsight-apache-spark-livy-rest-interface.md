---
title: Usare Livy Spark per inviare processi al cluster Spark in Azure HDInsight | Microsoft Docs
description: "Informazioni su come usare l&quot;API REST di Apache Spark per inviare i processi in modalità remota a un cluster Azure HDInsight."
keywords: apache spark rest api,livy spark
services: hdinsight
documentationcenter: 
author: nitinme
manager: jhubbard
editor: cgronlun
tags: azure-portal
ms.assetid: 2817b779-1594-486b-8759-489379ca907d
ms.service: hdinsight
ms.custom: hdinsightactive,hdiseo17may2017
ms.workload: big-data
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 05/15/2017
ms.author: nitinme
ms.translationtype: Human Translation
ms.sourcegitcommit: e7da3c6d4cfad588e8cc6850143112989ff3e481
ms.openlocfilehash: 541aeb4eba6d00f13021af5789cf1dde961301fd
ms.contentlocale: it-it
ms.lasthandoff: 05/16/2017


---
# <a name="use-apache-spark-rest-api-to-submit-remote-jobs-to-an-hdinsight-spark-cluster"></a>Usare l'API REST di Apache Spark per inviare i processi remoti a un cluster HDInsight Spark

Informazioni su come usare Livy, l'API REST di Apache Spark per inviare processi remoti a un cluster Azure HDInsight Spark. Per informazioni dettagliate, vedere [Livy](https://github.com/cloudera/hue/tree/master/apps/spark/java#welcome-to-livy-the-rest-spark-server).

È possibile usare Livy per l'esecuzione interattiva di shell Spark o per inviare processi batch da eseguire su Spark. Questo articolo parla dell'uso di Livy per inviare processi batch. La sintassi seguente usa Curl per eseguire chiamate API REST all'endpoint Livy Spark.

**Prerequisiti:**

È necessario disporre di quanto segue:

* Un cluster Apache Spark in HDInsight. Per istruzioni, vedere l'articolo relativo alla [creazione di cluster Apache Spark in Azure HDInsight](hdinsight-apache-spark-jupyter-spark-sql.md).

## <a name="submit-a-livy-spark-batch-job"></a>Inviare un processo batch Livy Spark
Prima di inviare un processo batch, è necessario caricare il file con estensione jar dell'applicazione nell'archivio del cluster associato al cluster. A tale scopo è possibile usare [**AzCopy**](../storage/storage-use-azcopy.md), un'utilità della riga di comando. Sono disponibili molti altri client da usare per caricare i dati. Altre informazioni in merito sono disponibili in [Caricare dati per processi Hadoop in HDInsight](hdinsight-upload-data.md).

    curl -k --user "<hdinsight user>:<user password>" -v -H <content-type> -X POST -d '{ "file":"<path to application jar>", "className":"<classname in jar>" }' 'https://<spark_cluster_name>.azurehdinsight.net/livy/batches'

**Esempi**:

* Se il file con estensione jar si trova nell'archivio del cluster (WASB)
  
        curl -k --user "admin:mypassword1!" -v -H 'Content-Type: application/json' -X POST -d '{ "file":"wasbs://mycontainer@mystorageaccount.blob.core.windows.net/data/SparkSimpleTest.jar", "className":"com.microsoft.spark.test.SimpleFile" }' "https://mysparkcluster.azurehdinsight.net/livy/batches"
* Se si vuole trasferire il nome del file con estensione jar e il nome della classe come parte di un file di input (in questo esempio, input.txt)
  
        curl -k  --user "admin:mypassword1!" -v -H "Content-Type: application/json" -X POST --data @C:\Temp\input.txt "https://mysparkcluster.azurehdinsight.net/livy/batches"

## <a name="get-information-on-livy-spark-batches-running-on-the-cluster"></a>Ottenere informazioni sui batch Livy Spark in esecuzione nel cluster
    curl -k --user "<hdinsight user>:<user password>" -v -X GET "https://<spark_cluster_name>.azurehdinsight.net/livy/batches"

**Esempi**:

* Per recuperare tutti i batch Livy Spark in esecuzione nel cluster:
  
        curl -k --user "admin:mypassword1!" -v -X GET "https://mysparkcluster.azurehdinsight.net/livy/batches"
* Se si vuole recuperare un batch specifico con un determinato ID batch
  
        curl -k --user "admin:mypassword1!" -v -X GET "https://mysparkcluster.azurehdinsight.net/livy/batches/{batchId}"

## <a name="delete-a-livy-spark-batch-job"></a>Eliminare un processo batch Livy Spark
    curl -k --user "<hdinsight user>:<user password>" -v -X DELETE "https://<spark_cluster_name>.azurehdinsight.net/livy/batches/{batchId}"

**Esempio**:

    curl -k --user "admin:mypassword1!" -v -X DELETE "https://mysparkcluster.azurehdinsight.net/livy/batches/{batchId}"

## <a name="livy-spark-and-high-availability"></a>Livy Spark e disponibilità elevata
Livy fornisce disponibilità elevata per i processi Spark in esecuzione nel cluster. Ecco alcuni esempi:

* Se il servizio Livy diventa inattivo dopo l'invio di un processo in modalità remota a un cluster Spark, il processo rimane in esecuzione in background. Quando Livy ritorna attivo, ripristina lo stato del processo e crea un report.
* I notebook di Jupyter per HDInsight sono basati su Livy in back-end. Se un notebook è in esecuzione in un processo Spark e il servizio Livy viene riavviato, il notebook continuerà a eseguire le celle del codice. 

## <a name="show-me-an-example"></a>Mostra un esempio
Questa sezione esamina alcuni esempi di come usare Livy Spark per inviare un processo batch, monitorare il progresso del processo e quindi eliminarlo. L'applicazione usata in questo esempio è quella sviluppata nell'articolo [Creare un'applicazione Scala autonoma da eseguire nel cluster HDInsight Spark](hdinsight-apache-spark-create-standalone-application.md). I passaggi seguenti presuppongono quanto segue:

* Il file con estensione jar dell'applicazione è già stato copiato nell'account di archiviazione associato al cluster.
* CuRL è installato nel computer in cui si sta provando a eseguire questi passaggi.

Eseguire i passaggi seguenti.

1. Verificare prima che Livy Spark sia in esecuzione nel cluster. È possibile eseguire questa operazione ottenendo un elenco di batch in esecuzione. Se è la prima volta che si esegue un processo usando Livy, il risultato restituito dovrebbe essere zero.
   
        curl -k --user "admin:mypassword1!" -v -X GET "https://mysparkcluster.azurehdinsight.net/livy/batches"
   
    L'output dovrebbe essere simile al seguente:
   
        < HTTP/1.1 200 OK
        < Content-Type: application/json; charset=UTF-8
        < Server: Microsoft-IIS/8.5
        < X-Powered-By: ARR/2.5
        < X-Powered-By: ASP.NET
        < Date: Fri, 20 Nov 2015 23:47:53 GMT
        < Content-Length: 34
        <
        {"from":0,"total":0,"sessions":[]}* Connection #0 to host mysparkcluster.azurehdinsight.net left intact
   
    Si noti che l'ultima riga nell'output corrisponde a **total:0**, che indica che non sono presenti batch in esecuzione.

2. Inviare ora un processo batch. Il frammento di codice seguente usa un file di input (input.txt) per trasferire il nome del file con estensione jar e il nome della classe come parametri. Questo è l'approccio consigliato se si eseguono questi passaggi da un computer Windows.
   
        curl -k --user "admin:mypassword1!" -v -H "Content-Type: application/json" -X POST --data @C:\Temp\input.txt "https://mysparkcluster.azurehdinsight.net/livy/batches"
   
    I parametri nel file **input.txt** sono definiti come segue:
   
        { "file":"wasbs:///example/jars/SparkSimpleApp.jar", "className":"com.microsoft.spark.example.WasbIOTest" }
   
    L'output dovrebbe essere simile al seguente:
   
        < HTTP/1.1 201 Created
        < Content-Type: application/json; charset=UTF-8
        < Location: /0
        < Server: Microsoft-IIS/8.5
        < X-Powered-By: ARR/2.5
        < X-Powered-By: ASP.NET
        < Date: Fri, 20 Nov 2015 23:51:30 GMT
        < Content-Length: 36
        <
        {"id":0,"state":"starting","log":[]}* Connection #0 to host mysparkcluster.azurehdinsight.net left intact
   
    Si noti che l'ultima riga dell'output indica **state:starting**. Indica anche **id:0**. Questo è l'ID del batch.

3. È ora possibile recuperare lo stato di questo batch specifico usando l'ID del batch.
   
        curl -k --user "admin:mypassword1!" -v -X GET "https://mysparkcluster.azurehdinsight.net/livy/batches/0"
   
    L'output dovrebbe essere simile al seguente:
   
        < HTTP/1.1 200 OK
        < Content-Type: application/json; charset=UTF-8
        < Server: Microsoft-IIS/8.5
        < X-Powered-By: ARR/2.5
        < X-Powered-By: ASP.NET
        < Date: Fri, 20 Nov 2015 23:54:42 GMT
        < Content-Length: 509
        <
        {"id":0,"state":"success","log":["\t diagnostics: N/A","\t ApplicationMaster host: 10.0.0.4","\t ApplicationMaster RPC port: 0","\t queue: default","\t start time: 1448063505350","\t final status: SUCCEEDED","\t tracking URL: http://hn0-myspar.lpel1gnnvxne3gwzqkfq5u5uzh.jx.internal.cloudapp.net:8088/proxy/application_1447984474852_0002/","\t user: root","15/11/20 23:52:47 INFO Utils: Shutdown hook called","15/11/20 23:52:47 INFO Utils: Deleting directory /tmp/spark-b72cd2bf-280b-4c57-8ceb-9e3e69ac7d0c"]}* Connection #0 to host mysparkcluster.azurehdinsight.net left intact
   
    L'output ora indica **state:success**, il che suggerisce che il processo è stato completato.

4. Se si vuole, è ora possibile eliminare il batch.
   
        curl -k --user "admin:mypassword1!" -v -X DELETE "https://mysparkcluster.azurehdinsight.net/livy/batches/0"
   
    L'output dovrebbe essere simile al seguente:
   
        < HTTP/1.1 200 OK
        < Content-Type: application/json; charset=UTF-8
        < Server: Microsoft-IIS/8.5
        < X-Powered-By: ARR/2.5
        < X-Powered-By: ASP.NET
        < Date: Sat, 21 Nov 2015 18:51:54 GMT
        < Content-Length: 17
        <
        {"msg":"deleted"}* Connection #0 to host mysparkcluster.azurehdinsight.net left intact
   
    L'ultima riga dell'output indica che il batch è stato correttamente eliminato. Se si elimina un processo in fase di esecuzione, il processo verrà definitivamente eliminato. Se si elimina un processo completato correttamente o non correttamente, le informazioni sul processo vengono eliminate completamente.

## <a name="using-livy-spark-on-hdinsight-35-clusters"></a>Uso di Livy Spark nei cluster HDInsight 3.5

Un cluster HDInsight 3.5, per impostazione predefinita, disabilita l'uso di percorsi di file locali per accedere ai file di dati di esempio o a file JAR. Si consiglia di usare invece il percorso `wasb://` per accedere a file JAR o a file di dati di esempio dal cluster. Se si vuole usare un percorso locale, è necessario aggiornare di conseguenza la configurazione di Ambari. A tale scopo, procedere come segue:

1. Passare al portale di Ambari per il cluster. L'interfaccia utente Web di Ambari è disponibile nel cluster HDInsight all'indirizzo https://**NOMECLUSTER**.azurehdidnsight.net, dove NOMECLUSTER è il nome del cluster.

2. Nel riquadro di spostamento sinistro fare clic su **Livy** e quindi su **Configs** (Configurazioni).

3. In **livy-default** aggiungere il nome della proprietà `livy.file.local-dir-whitelist` e impostarne il valore su **"/"**, per consentire l'accesso completo al file system. Se si vuole consentire l'accesso solo a una directory specifica, specificare il percorso di tale directory come valore.

## <a name="troubleshooting"></a>Risoluzione dei problemi

Di seguito sono illustrati alcuni problemi riscontrabili durante l'uso di Livy per l'invio di processi remoti ai cluster Spark.

### <a name="using-an-external-jar-from-the-additional-storage-is-not-supported"></a>L'uso di un file jar esterno dalla risorsa di archiviazione aggiuntiva non è supportato.

**Problema:** se si esegue un processo Livy Spark usando il riferimento a un file con estensione jar esterno dalla risorsa di archiviazione associata al cluster, il processo avrà esito negativo.

**Soluzione:** verificare che il file jar che si vuole usare sia disponibile nella risorsa di archiviazione associata al cluster HDInsight predefinito.


## <a name="seealso"></a>Vedere anche
* [Panoramica: Apache Spark su Azure HDInsight](hdinsight-apache-spark-overview.md)

### <a name="scenarios"></a>Scenari
* [Spark con Business Intelligence: eseguire l'analisi interattiva dei dati con strumenti di Business Intelligence mediante Spark in HDInsight](hdinsight-apache-spark-use-bi-tools.md)
* [Spark con Machine Learning: utilizzare Spark in HDInsight per l'analisi della temperatura di compilazione utilizzando dati HVAC](hdinsight-apache-spark-ipython-notebook-machine-learning.md)
* [Spark con Machine Learning: usare Spark in HDInsight per prevedere i risultati del controllo degli alimenti](hdinsight-apache-spark-machine-learning-mllib-ipython.md)
* [Streaming Spark: usare Spark in HDInsight per la creazione di applicazioni di streaming in tempo reale](hdinsight-apache-spark-eventhub-streaming.md)
* [Analisi dei log del sito Web mediante Spark in HDInsight](hdinsight-apache-spark-custom-library-website-log-analysis.md)

### <a name="create-and-run-applications"></a>Creare ed eseguire applicazioni
* [Creare un'applicazione autonoma con Scala](hdinsight-apache-spark-create-standalone-application.md)

### <a name="tools-and-extensions"></a>Strumenti ed estensioni
* [Usare il plug-in degli strumenti HDInsight per IntelliJ IDEA per creare e inviare applicazioni Spark in Scala](hdinsight-apache-spark-intellij-tool-plugin.md)
* [Use HDInsight Tools Plugin for IntelliJ IDEA to debug Spark applications remotely (Usare il plug-in Strumenti HDInsight per IntelliJ IDEA per eseguire il debug di applicazioni Spark in remoto)](hdinsight-apache-spark-intellij-tool-plugin-debug-jobs-remotely.md)
* [Usare i notebook di Zeppelin con un cluster Spark in HDInsight](hdinsight-apache-spark-use-zeppelin-notebook.md)
* [Kernel disponibili per notebook di Jupyter nel cluster Spark per HDInsight](hdinsight-apache-spark-jupyter-notebook-kernels.md)
* [Usare pacchetti esterni con i notebook Jupyter](hdinsight-apache-spark-jupyter-notebook-use-external-packages.md)
* [Installare Jupyter Notebook nel computer e connetterlo a un cluster HDInsight Spark](hdinsight-apache-spark-jupyter-notebook-install-locally.md)

### <a name="manage-resources"></a>Gestire risorse
* [Gestire le risorse del cluster Apache Spark in Azure HDInsight](hdinsight-apache-spark-resource-manager.md)
* [Tenere traccia ed eseguire il debug di processi in esecuzione nel cluster Apache Spark in Azure HDInsight](hdinsight-apache-spark-job-debugging.md)


