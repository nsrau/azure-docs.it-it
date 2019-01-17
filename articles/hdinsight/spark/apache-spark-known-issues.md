---
title: Risolvere i problemi del cluster Apache Spark in Azure HDInsight
description: Informazioni sui problemi relativi ai cluster Apache Spark in HDInsight di Azure e come risolverli.
services: hdinsight
author: hrasheed-msft
ms.reviewer: jasonh
ms.service: hdinsight
ms.custom: hdinsightactive
ms.topic: conceptual
ms.date: 02/21/2018
ms.author: hrasheed
ms.openlocfilehash: 69a07499b5d29fb3b8d8473c85ca345aa7ce60ac
ms.sourcegitcommit: dede0c5cbb2bd975349b6286c48456cfd270d6e9
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 01/16/2019
ms.locfileid: "54330365"
---
# <a name="known-issues-for-apache-spark-cluster-on-hdinsight"></a>Problemi noti del cluster Apache Spark in HDInsight

Questo documento elenca tutti i problemi noti relativi all'anteprima pubblica di HDInsight Spark.  

## <a name="apache-livy-leaks-interactive-session"></a>Apache Livy perde la sessione interattiva
Quando [Apache Livy](https://livy.incubator.apache.org/) viene riavviato (da [Apache Ambari](https://ambari.apache.org/) oppure a causa del riavvio della macchina virtuale con nodo head 0) con una sessione interattiva ancora attiva, una sessione di processo interattiva va persa. Di conseguenza, i nuovi processi possono rimanere bloccati in stato Accettato.

**Soluzione:**

Seguire questa procedura per risolvere il problema:

1. Eseguire SSH nel nodo head. Per altre informazioni, vedere [Usare SSH con HDInsight](../hdinsight-hadoop-linux-use-ssh-unix.md).

2. Eseguire il comando seguente per trovare gli ID applicazione dei processi interattivi avviati tramite Livy. 
   
        yarn application –list
   
    I nomi di processo predefiniti sono Livy se i processi sono stati avviati con una sessione interattiva di Livy senza nomi espliciti specificati. Per la sessione di Livy avviata tramite [Jupyter Notebook](https://jupyter.org/), il nome del processo inizia con remotesparkmagics_*. 
3. Eseguire il comando seguente per terminare questi processi. 
   
        yarn application –kill <Application ID>

Vengono avviati nuovi processi. 

## <a name="spark-history-server-not-started"></a>Il server cronologia Spark non viene avviato
Il server cronologia Spark non viene avviato automaticamente dopo la creazione di un cluster.  

**Soluzione:** 

Avviare manualmente il server cronologia da Ambari.

## <a name="permission-issue-in-spark-log-directory"></a>Problema di autorizzazioni nella directory log Spark
hdiuser ottiene l'errore seguente durante l'invio di un processo con spark-submit:

```
java.io.FileNotFoundException: /var/log/spark/sparkdriver_hdiuser.log (Permission denied)
```
Inoltre, non viene scritto alcun log del driver. 

**Soluzione:**

1. Aggiungere hdiuser al gruppo Hadoop. 
2. Indicare 777 autorizzazioni in /var/log/spark dopo la creazione del cluster. 
3. Aggiornare il percorso del log Spark tramite Ambari in modo che corrisponda a una directory con 777 autorizzazioni.  
4. Eseguire spark-submit come sudo.  

## <a name="spark-phoenix-connector-is-not-supported"></a>Il connettore Spark-Phoenix non è supportato

I cluster HDInsight Spark non supportano il connettore Spark-Phoenix.

**Soluzione:**

Usare il connettore Spark-HBase. Per istruzioni, vedere [How to use Spark-HBase connector](https://blogs.msdn.microsoft.com/azuredatalake/2016/07/25/hdinsight-how-to-use-spark-hbase-connector/) (Come usare un connettore Spark-HBase).

## <a name="issues-related-to-jupyter-notebooks"></a>Problemi relativi ai notebook Jupyter
Seguito alcuni problemi noti relativi ai notebook Jupyter.

### <a name="notebooks-with-non-ascii-characters-in-filenames"></a>Notebook con nomi di file contenenti caratteri non ASCII
Non usare caratteri non ASCII nei nomi di file dei notebook Jupyter. Se tramite l'interfaccia utente di Jupyter si prova a caricare un file con un nome non ASCII, l'operazione si interrompe senza alcun messaggio di errore. Jupyter non consente di caricare il file, ma non genera nemmeno un errore visibile.

### <a name="error-while-loading-notebooks-of-larger-sizes"></a>Errore durante il caricamento di notebook di maggiori dimensioni
Quando si caricano notebook di maggiori dimensioni, potrebbe comparire l'errore **`Error loading notebook`** .  

**Soluzione:**

Se viene visualizzato questo errore, non significa che i dati sono danneggiati o persi.  I notebook sono ancora disponibili su disco in `/var/lib/jupyter`ed è possibile usare SSH nel cluster per accedervi. Per altre informazioni, vedere [Usare SSH con HDInsight](../hdinsight-hadoop-linux-use-ssh-unix.md).

Dopo aver effettuato la connessione al cluster usando SSH, è possibile copiare i notebook dal cluster nel computer locale (tramite SCP o WinSCP) come backup per evitare la perdita di dati importanti del notebook. È quindi possibile usare SSH per il tunneling al nodo head sulla porta 8001 e accedere a Jupyter senza passare attraverso il gateway.  Da qui è possibile cancellare l'output del notebook e salvarlo di nuovo per ridurne le dimensioni.

Per evitare questo errore in futuro, è necessario seguire alcune procedure consigliate:

* È importante mantenere ridotte le dimensioni del notebook. L'output dei processi Spark inviato a Jupyter viene salvato in modo permanente nel notebook.  Con Jupyter è in genere consigliabile evitare di eseguire `.collect()` su RDD o frame di dati di grandi dimensioni. Se si vuole visualizzare il contenuto di un RDD, considerare in alternativa la possibilità di eseguire `.take()` o `.sample()` per evitare che l'output assuma dimensioni troppo grandi.
* Quando si salva un notebook, cancellare anche tutte le celle di output per ridurre le dimensioni.

### <a name="notebook-initial-startup-takes-longer-than-expected"></a>L'avvio iniziale del notebook richiede più tempo del previsto
La prima istruzione del codice nel notebook di Jupyter tramite il magic Spark potrebbe richiedere più di un minuto.  

**Spiegazione:**

Ciò accade quando viene eseguita la prima cella di codice. In background viene avviata la configurazione della sessione e vengono impostati i contesti Spark, SQL e Hive. La prima istruzione viene eseguita dopo l'impostazione di questi contesti, dando l'impressione che l'esecuzione dell'istruzione impieghi molto tempo.

### <a name="jupyter-notebook-timeout-in-creating-the-session"></a>Timeout del notebook di Jupyter durante la creazione della sessione
Quando il cluster Spark esaurisce le risorse, si verifica il timeout dei kernel Spark e PySpark nel notebook Jupyter durante il tentativo di creare la sessione. 

**Soluzioni:** 

1. Liberare alcune risorse nel cluster Spark nei modi seguenti:
   
   * Arrestando altri notebook Spark selezionando il menu Close and Halt o facendo clic su Shutdown nel notebook.
   * Arrestando altre applicazioni Spark da YARN.
2. Riavviare il notebook che si stava cercando di avviare. Ora dovrebbero essere disponibili risorse sufficienti per creare una sessione.

## <a name="see-also"></a>Vedere anche 
* [Panoramica: Apache Spark in Azure HDInsight](apache-spark-overview.md)

### <a name="scenarios"></a>Scenari
* [Apache Spark con BI: Eseguire l'analisi interattiva dei dati con strumenti di Business Intelligence mediante Spark in HDInsight](apache-spark-use-bi-tools.md)
* [Apache Spark con Machine Learning: usare Spark in HDInsight per analizzare la temperatura di un edificio con dati HVAC](apache-spark-ipython-notebook-machine-learning.md)
* [Apache Spark con apprendimento automatico: usare Spark in HDInsight per stimare i risultati di controllo degli alimenti](apache-spark-machine-learning-mllib-ipython.md)
* [Analisi dei log del sito Web con Apache Spark in HDInsight](apache-spark-custom-library-website-log-analysis.md)

### <a name="create-and-run-applications"></a>Creare ed eseguire applicazioni
* [Creare un'applicazione autonoma con Scala](apache-spark-create-standalone-application.md)
* [Eseguire processi in modalità remota in un cluster Apache Spark usando Apache Livy](apache-spark-livy-rest-interface.md)

### <a name="tools-and-extensions"></a>Strumenti ed estensioni
* [Usare il plug-in degli strumenti HDInsight per IntelliJ IDEA per creare e inviare applicazioni Spark in Scala](apache-spark-intellij-tool-plugin.md)
* [Usare il plug-in Strumenti HDInsight per IntelliJ IDEA per eseguire il debug di applicazioni Apache Spark in remoto](apache-spark-intellij-tool-plugin-debug-jobs-remotely.md)
* [Usare i notebook di Apache Zeppelin con un cluster Apache Spark in HDInsight](apache-spark-zeppelin-notebook.md)
* [Kernel disponibili per notebook di Jupyter nel cluster Apache Spark per HDInsight](apache-spark-jupyter-notebook-kernels.md)
* [Usare pacchetti esterni con i notebook Jupyter](apache-spark-jupyter-notebook-use-external-packages.md)
* [Installare Jupyter Notebook nel computer e connetterlo a un cluster HDInsight Spark](apache-spark-jupyter-notebook-install-locally.md)

### <a name="manage-resources"></a>Gestire risorse
* [Gestire le risorse del cluster Apache Spark in Azure HDInsight](apache-spark-resource-manager.md)
* [Tenere traccia ed eseguire il debug di processi in esecuzione nel cluster Apache Spark in Azure HDInsight](apache-spark-job-debugging.md)

