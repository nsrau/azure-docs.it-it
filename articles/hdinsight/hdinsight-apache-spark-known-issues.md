---
title: Problemi noti del cluster Apache Spark in Azure HDInsight | Documentazione Microsoft
description: Problemi noti dei cluster Apache Spark in Azure HDInsight.
services: hdinsight
documentationcenter: 
author: mumian
manager: jhubbard
editor: cgronlun
tags: azure-portal
ms.assetid: 610c4103-ffc8-4ec0-ad06-fdaf3c4d7c10
ms.service: hdinsight
ms.custom: hdinsightactive
ms.workload: big-data
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 03/24/2017
ms.author: nitinme
translationtype: Human Translation
ms.sourcegitcommit: 4f2230ea0cc5b3e258a1a26a39e99433b04ffe18
ms.openlocfilehash: 2ba5f280b38622b6a0c966d76617cd5698420b92
ms.lasthandoff: 03/25/2017


---
# <a name="known-issues-for-apache-spark-cluster-on-hdinsight"></a>Problemi noti del cluster Apache Spark in HDInsight

Questo documento elenca tutti i problemi noti relativi all'anteprima pubblica di HDInsight Spark.  

## <a name="livy-leaks-interactive-session"></a>Livy perde la sessione interattiva
Quando Livy viene riavviato (da Ambari oppure a causa del riavvio della macchina virtuale con nodo head 0) con una sessione interattiva ancora attiva, una sessione di processo interattiva andrà persa. Per questo motivo, i nuovi processi possono rimanere bloccati in stato Accettato e non possono essere avviati.

**Soluzione:**

Seguire questa procedura per risolvere il problema:

1. Eseguire SSH nel nodo head. Per altre informazioni, vedere [Usare SSH con HDInsight](hdinsight-hadoop-linux-use-ssh-unix.md).

2. Eseguire il comando seguente per trovare gli ID applicazione dei processi interattivi avviati tramite Livy. 
   
        yarn application –list
   
    I nomi di processo predefiniti corrispondono a Livy se i processi sono stati avviati con una sessione interattiva Livy senza specificare nomi espliciti. Per la sessione Livy avviata dal notebook di Jupyter, il nome del processo verrà avviato con remotesparkmagics_*. 
3. Eseguire il comando seguente per terminare questi processi. 
   
        yarn application –kill <Application ID>

Verranno avviati nuovi processi. 

## <a name="spark-history-server-not-started"></a>Il server cronologia Spark non viene avviato
Il server cronologia Spark non viene avviato automaticamente dopo la creazione di un cluster.  

**Soluzione:** 

Avviare manualmente il server cronologia da Ambari.

## <a name="permission-issue-in-spark-log-directory"></a>Problema di autorizzazioni nella directory log Spark
Quando hdiuser invia un processo con spark-submit, si verifica un errore java.io.FileNotFoundException: /var/log/spark/sparkdriver_hdiuser.log (autorizzazione negata) e il log del driver non viene scritto. 

**Soluzione:**

1. Aggiungere hdiuser al gruppo Hadoop. 
2. Indicare 777 autorizzazioni in /var/log/spark dopo la creazione del cluster. 
3. Aggiornare il percorso del log Spark tramite Ambari in modo che corrisponda a una directory con 777 autorizzazioni.  
4. Eseguire spark-submit come sudo.  

## <a name="spark-phoenix-connector-is-not-supported"></a>Il connettore Spark-Phoenix non è supportato

Attualmente, il connettore Spark-Phoenix non è supportato in un cluster HDInsight Spark.

**Soluzione:**

Usare il connettore Spark-HBase. Per istruzioni, vedere [Come usare un connettore Spark-HBase](https://blogs.msdn.microsoft.com/azuredatalake/2016/07/25/hdinsight-how-to-use-spark-hbase-connector/).

## <a name="issues-related-to-jupyter-notebooks"></a>Problemi relativi ai notebook Jupyter
Seguito alcuni problemi noti relativi ai notebook Jupyter.

### <a name="notebooks-with-non-ascii-characters-in-filenames"></a>Notebook con nomi di file contenenti caratteri non ASCII
I notebook Jupyter utilizzabili nei cluster HDInsight Spark non devono contenere nei nomi di file caratteri non ASCII. Se si tenta di caricare tramite l'interfaccia utente Jupyter un file con un nome di file non ASCII, l'operazione si interromperà senza avvisi. Questo significa che Jupyter non consentirà di caricare il file, ma non genererà un errore visibile. 

### <a name="error-while-loading-notebooks-of-larger-sizes"></a>Errore durante il caricamento di notebook di maggiori dimensioni
Quando si caricano notebook di maggiori dimensioni, potrebbe comparire l'errore **`Error loading notebook`** .  

**Soluzione:**

Se viene visualizzato questo errore, non significa che i dati sono danneggiati o persi.  I notebook sono ancora disponibili su disco in `/var/lib/jupyter`ed è possibile usare SSH nel cluster per accedervi. Per altre informazioni, vedere [Usare SSH con HDInsight](hdinsight-hadoop-linux-use-ssh-unix.md).

Dopo aver effettuato la connessione al cluster usando SSH, è possibile copiare i notebook dal cluster nel computer locale (tramite SCP o WinSCP) come backup per evitare la perdita di dati importanti del notebook. È quindi possibile usare SSH per il tunneling al nodo head sulla porta 8001 e accedere a Jupyter senza passare attraverso il gateway.  Qui è possibile cancellare l'output del notebook e salvarlo di nuovo per ridurne le dimensioni.

Per evitare questo errore in futuro, è necessario seguire alcune procedure consigliate:

* È importante mantenere ridotte le dimensioni del notebook. L'output dei processi Spark inviato a Jupyter viene salvato in modo permanente nel notebook.  Con Jupyter in genere è consigliabile evitare di eseguire `.collect()` su RDD o frame di dati di grandi dimensioni. Se si vuole visualizzare il contenuto di un RDD, considerare invece la possibilità di eseguire `.take()` o `.sample()` per evitare la crescita eccessiva dell'output.
* Quando si salva un notebook, cancellare anche tutte le celle di output per ridurre le dimensioni.

### <a name="notebook-initial-startup-takes-longer-than-expected"></a>L'avvio iniziale del notebook richiede più tempo del previsto
La prima istruzione del codice nel notebook di Jupyter tramite il magic Spark potrebbe richiedere più di un minuto.  

**Spiegazione:**

Ciò accade quando viene eseguita la prima cella di codice. In background viene avviata la configurazione della sessione e vengono impostati i contesti Spark, SQL e Hive. La prima istruzione viene eseguita dopo l'impostazione di questi contesti, dando l'impressione che l'esecuzione dell'istruzione impieghi molto tempo.

### <a name="jupyter-notebook-timeout-in-creating-the-session"></a>Timeout del notebook di Jupyter durante la creazione della sessione
Quando il cluster Spark esaurisce le risorse, si verificherà il timeout dei kernel Spark e Pyspark nel notebook di Jupyter quando si cerca di creare la sessione. 

**Soluzioni:** 

1. Liberare alcune risorse nel cluster Spark nei modi seguenti:
   
   * Arrestando altri notebook Spark selezionando il menu Close and Halt o facendo clic su Shutdown nel notebook.
   * Arrestando altre applicazioni Spark da YARN.
2. Riavviare il notebook che si stava cercando di avviare. Ora dovrebbero essere disponibili risorse sufficienti per creare una sessione.

## <a name="see-also"></a>Vedere anche
* [Panoramica: Apache Spark su Azure HDInsight](hdinsight-apache-spark-overview.md)

### <a name="scenarios"></a>Scenari
* [Spark con Business Intelligence: eseguire l'analisi interattiva dei dati con strumenti di Business Intelligence mediante Spark in HDInsight](hdinsight-apache-spark-use-bi-tools.md)
* [Spark con Machine Learning: utilizzare Spark in HDInsight per l'analisi della temperatura di compilazione utilizzando dati HVAC](hdinsight-apache-spark-ipython-notebook-machine-learning.md)
* [Spark con Machine Learning: usare Spark in HDInsight per prevedere i risultati del controllo degli alimenti](hdinsight-apache-spark-machine-learning-mllib-ipython.md)
* [Streaming Spark: usare Spark in HDInsight per la creazione di applicazioni di streaming in tempo reale](hdinsight-apache-spark-eventhub-streaming.md)
* [Analisi dei log del sito Web mediante Spark in HDInsight](hdinsight-apache-spark-custom-library-website-log-analysis.md)

### <a name="create-and-run-applications"></a>Creare ed eseguire applicazioni
* [Creare un'applicazione autonoma con Scala](hdinsight-apache-spark-create-standalone-application.md)
* [Eseguire processi in modalità remota in un cluster Spark usando Livy](hdinsight-apache-spark-livy-rest-interface.md)

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


