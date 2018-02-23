---
title: Gestire le risorse del cluster Apache Spark in Azure HDInsight | Documentazione Microsoft
description: Informazioni su come gestire le risorse per i cluster Spark in Azure HDInsight per ottenere prestazioni migliori.
services: hdinsight
documentationcenter: 
author: mumian
manager: cgronlun
editor: cgronlun
tags: azure-portal
ms.assetid: 9da7d4e3-458e-4296-a628-77b14643f7e4
ms.service: hdinsight
ms.custom: hdinsightactive
ms.workload: big-data
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 01/23/2018
ms.author: jgao
ms.openlocfilehash: 914811812b7e01f7b58f92c85cb5a16580c45796
ms.sourcegitcommit: d87b039e13a5f8df1ee9d82a727e6bc04715c341
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 02/21/2018
---
# <a name="manage-resources-for-apache-spark-cluster-on-azure-hdinsight"></a>Gestire le risorse del cluster Apache Spark in Azure HDInsight 

Informazioni su come accedere a interfacce associate al cluster Spark, come l'interfaccia utente di Ambari, l'interfaccia utente di YARN e Server cronologia Spark, e come modificare la configurazione del cluster per ottenere prestazioni ottimali.

**Prerequisiti:**

* Un cluster Apache Spark in HDInsight. Per istruzioni, vedere l'articolo dedicato alla [creazione di cluster Apache Spark in Azure HDInsight](apache-spark-jupyter-spark-sql.md).

## <a name="open-the-ambari-web-ui"></a>Aprire l'interfaccia utente Web Ambari

È possibile usare Apache Ambari per monitorare il cluster e apportare modifiche alla configurazione. Per altre informazioni, vedere [Gestire cluster Hadoop in HDInsight tramite il portale di Azure](../hdinsight-administer-use-portal-linux.md#open-the-ambari-web-ui).

## <a name="open-the-spark-history-server"></a>Aprire il Server cronologia Spark

Server cronologia Spark è l'interfaccia utente Web per le applicazioni Spark completate e in esecuzione. Si tratta di un'estensione dell'interfaccia utente Web di Spark.

**Per aprire l'interfaccia utente Web Server cronologia Spark**

1. Nel [portale di Azure](https://portal.azure.com/) aprire il cluster Spark. Per altre informazioni, vedere [Elencare e visualizzare i cluster](../hdinsight-administer-use-portal-linux.md#list-and-show-clusters).
2. In **Collegamenti rapidi** fare clic su **Dashboard cluster** e quindi su **Server cronologia Spark**.

    ![Server cronologia Spark](./media/apache-spark-resource-manager/launch-history-server.png "Server cronologia Spark")

    Quando richiesto, immettere le credenziali di amministratore per il cluster di Spark. È inoltre possibile aprire il Server cronologia Spark passando all'URL seguente:

    ```
    https://<ClusterName>.azurehdinsight.net/sparkhistory
    ```

    Sostituire <ClusterName> con il nome del cluster Spark.

L'interfaccia utente Web Server cronologia Spark è simile all'immagine seguente:

![Server cronologia Spark in HDInsight](./media/apache-spark-resource-manager/hdinsight-spark-history-server.png)

## <a name="open-the-yarn-ui"></a>Aprire l'interfaccia utente di YARN
È possibile usare l'interfaccia utente di YARN per il monitoraggio delle applicazioni attualmente in esecuzione nel cluster Spark.

1. Nel [portale di Azure](https://portal.azure.com/) aprire il cluster Spark. Per altre informazioni, vedere [Elencare e visualizzare i cluster](../hdinsight-administer-use-portal-linux.md#list-and-show-clusters).
2. In **Collegamenti rapidi** fare clic su **Dashboard cluster** e quindi su **YARN**.

    ![Avviare l'interfaccia utente di YARN](./media/apache-spark-resource-manager/launch-yarn-ui.png)

   > [!TIP]
   > In alternativa, è anche possibile avviare l'interfaccia utente di YARN dall'interfaccia utente di Ambari. Per avviare l'interfaccia utente di Ambari, fare clic su **Dashboard cluster** e quindi su **Dashboard cluster HDInsight**. Nell'interfaccia utente di Ambari fare clic su **YARN**, su **Quick Links** (Collegamenti rapidi), sulla funzionalità di gestione risorse attiva e quindi fare clic su **Resource Manager UI** (Interfaccia utente di Gestione risorse).
   >
   >

## <a name="optimize-clusters-for-spark-applications"></a>Ottimizzare i cluster per le applicazioni Spark
I tre parametri principali che possono essere usati per la configurazione di Spark, a seconda dei requisiti dell'applicazione, sono `spark.executor.instances`, `spark.executor.cores` e `spark.executor.memory`. Un Executor è un processo avviato per un'applicazione Spark. Viene eseguito sul nodo di lavoro e svolge le attività per l'applicazione. Il numero predefinito di executor e le relative dimensioni per ogni cluster vengono calcolati in base al numero di nodi di lavoro e alle relative dimensioni. Questi dati vengono archiviati in `spark-defaults.conf` nei nodi head del cluster.

I tre parametri di configurazione possono essere configurati a livello di cluster, per tutte le applicazioni in esecuzione nel cluster, o possono anche essere specificati per ogni singola applicazione.

### <a name="change-the-parameters-using-ambari-ui"></a>Modificare i parametri con l'interfaccia utente di Ambari
1. Nell'interfaccia utente di Ambari fare clic su **Spark**, **Configs** (Configurazioni) e quindi espandere **Custom spark-defaults**.

    ![Impostare parametri con Ambari](./media/apache-spark-resource-manager/set-parameters-using-ambari.png)
2. I valori predefiniti sono appropriati per quattro applicazioni Spark in esecuzione contemporaneamente nel cluster. È possibile modificare questi valori nell'interfaccia utente, come illustrato nello screenshot seguente:

    ![Impostare parametri con Ambari](./media/apache-spark-resource-manager/set-executor-parameters.png)
3. Per salvare la configurazione, fare clic su **Save** . Nella parte superiore della pagina verrà richiesto di riavviare tutti i servizi interessati. Fare clic su **Restart**.

    ![Riavviare i servizi](./media/apache-spark-resource-manager/restart-services.png)

### <a name="change-the-parameters-for-an-application-running-in-jupyter-notebook"></a>Modificare i parametri per un'applicazione in esecuzione in Jupyter Notebook
Per le applicazioni in esecuzione in Jupyter Notebook è possibile usare `%%configure` magic per apportare le modifiche di configurazione. Idealmente, è necessario apportare le modifiche all'inizio dell'applicazione, prima di eseguire la prima cella di codice. Ciò garantisce che la configurazione venga applicata alla sessione Livy quando viene creata. Se si vuole modificare la configurazione in una fase successiva nell'applicazione, è necessario usare il parametro `-f` . Tuttavia, in questo modo tutte le operazioni eseguite nell'applicazione andranno perse.

Il frammento di codice seguente mostra come modificare la configurazione per un'applicazione in esecuzione in Jupyter.

    %%configure
    {"executorMemory": "3072M", "executorCores": 4, "numExecutors":10}

I parametri di configurazione devono essere passati come una stringa JSON e devono trovarsi nella riga successiva a magic, come illustrato nella colonna di esempio.

### <a name="change-the-parameters-for-an-application-submitted-using-spark-submit"></a>Modificare i parametri per un'applicazione inviata tramite spark-submit
Il comando seguente è un esempio di come modificare i parametri di configurazione per un'applicazione batch inviata tramite `spark-submit`.

    spark-submit --class <the application class to execute> --executor-memory 3072M --executor-cores 4 –-num-executors 10 <location of application jar file> <application parameters>

### <a name="change-the-parameters-for-an-application-submitted-using-curl"></a>Modificare i parametri per un'applicazione inviata tramite cURL
Il comando seguente è un esempio di come modificare i parametri di configurazione per un'applicazione batch inviata tramite cURL.

    curl -k -v -H 'Content-Type: application/json' -X POST -d '{"file":"<location of application jar file>", "className":"<the application class to execute>", "args":[<application parameters>], "numExecutors":10, "executorMemory":"2G", "executorCores":5' localhost:8998/batches

### <a name="change-these-parameters-on-a-spark-thrift-server"></a>Modificare questi parametri nel server Spark Thrift
Il server Spark Thrift fornisce l'accesso JDBC/ODBC a un cluster Spark e viene usato per rispondere alle query di Spark SQL. Strumenti come Power BI, Tableau e così via usano il protocollo ODBC per comunicare con il server Spark Thrift per eseguire query di Spark SQL come un'applicazione Spark. Quando si crea un cluster Spark, vengono avviate due istanze del server Spark Thrift, una in ogni nodo head. Ogni Thrift Spark Server è visibile come un'applicazione Spark nell'interfaccia utente di YARN.

Il server Spark Thrift usa l'allocazione di executor dinamica di Spark e quindi non viene usato `spark.executor.instances` . Il server Spark Thrift usa invece `spark.dynamicAllocation.minExecutors` e `spark.dynamicAllocation.maxExecutors` per specificare il numero di executor. Per modificare le dimensioni degli executor, si usano i parametri di configurazione `spark.executor.cores` e `spark.executor.memory`. È possibile modificare questi parametri come illustrato nella procedura seguente:

* Espandere la categoria **Advanced spark-thrift-sparkconf** per aggiornare i parametri `spark.dynamicAllocation.minExecutors`, `spark.dynamicAllocation.maxExecutors` e `spark.executor.memory`.

    ![Configurare il server Spark Thrift](./media/apache-spark-resource-manager/spark-thrift-server-1.png)    
* Espandere la categoria **Custom spark-thrift-sparkconf** per aggiornare il parametro `spark.executor.cores`.

    ![Configurare il server Spark Thrift](./media/apache-spark-resource-manager/spark-thrift-server-2.png)

### <a name="change-the-driver-memory-of-the-spark-thrift-server"></a>Modificare la memoria del driver del server Spark Thrift
La memoria del driver del server Spark Thrift è configurata al 25% delle dimensioni della RAM nodo head, a condizione che le dimensioni totali della RAM del nodo head siano maggiori di 14 GB. Per modificare la configurazione della memoria del driver, è possibile usare l'interfaccia utente di Ambari, come illustrato nello screenshot seguente:

* Nell'interfaccia utente di Ambari fare clic su **Spark**, **Configs** (Configurazioni), espandere **Advanced spark-env**, quindi specificare il valore per **spark_thrift_cmd_opts**.

    ![Configurare la RAM del server Spark Thrift](./media/apache-spark-resource-manager/spark-thrift-server-ram.png)

## <a name="reclaim-spark-cluster-resources"></a>Recuperare le risorse cluster di Spark
Grazie all'allocazione dinamica di Spark le uniche risorse usate dal server Thrift sono quelle per i due master applicazioni. Per recuperare tali risorse, è necessario arrestare i servizi del server Thrift in esecuzione nel cluster.

1. Nel riquadro sinistro dell'interfaccia utente di Ambari fare clic su **Spark**.
2. Nella pagina successiva fare clic su **Server Spark Thrift**.

    ![Riavviare il server Thrift](./media/apache-spark-resource-manager/restart-thrift-server-1.png)
3. Verranno visualizzati due nodi head in cui è in esecuzione il server Spark Thrift. Fare clic su uno dei nodi head.

    ![Riavviare il server Thrift](./media/apache-spark-resource-manager/restart-thrift-server-2.png)
4. Nella pagina successiva sono elencati tutti i servizi in esecuzione in quel nodo head. Nell'elenco fare clic sul pulsante dell'elenco a discesa accanto al server Spark Thrift e quindi fare clic su **Stop**.

    ![Riavviare il server Thrift](./media/apache-spark-resource-manager/restart-thrift-server-3.png)
5. Ripetere questi passaggi anche per l'altro nodo head.

## <a name="restart-the-jupyter-service"></a>Riavviare il servizio Jupyter
Avviare l'interfaccia utente Web di Ambari, come illustrato all'inizio dell'articolo. Dal riquadro di spostamento sinistro fare clic su **Jupyter**, **Service Actions** (Azioni servizio) e quindi su **Restart All** (Riavvia tutto). Verrà avviato il servizio Jupyter su tutti i nodi head.

![Riavviare Jupyter](./media/apache-spark-resource-manager/restart-jupyter.png "Riavviare Jupyter")

## <a name="monitor-resources"></a>Monitorare le risorse
Avviare l'interfaccia utente di Yarn, come illustrato all'inizio dell'articolo. Nella tabella Cluster Metrics (Metriche cluster) nella parte superiore della schermata, verificare i valori delle colonne **Memory Used** (Memoria in uso) e **Memory Total** (Memoria totale). Se i due valori sono simili, potrebbero non esserci risorse sufficienti per avviare l'applicazione successiva. Lo stesso vale per le colonne **VCores Used** (VCore in uso) e **VCores Total** (VCore totali). Se nella visualizzazione principale è presente un'applicazione con stato **ACCEPTED** (ACCETTATO) che non passa allo stato **RUNNING** (IN ESECUZIONE) o **FAILED** (NON RIUSCITO), ciò può anche indicare che l'applicazione non ha risorse sufficienti per l'avvio.

![Limite di risorse](./media/apache-spark-resource-manager/resource-limit.png "Limite di risorse")

## <a name="kill-running-applications"></a>Terminare le applicazioni in esecuzione
1. Nell'interfaccia utente di Yarn, nel pannello a sinistra, fare clic su **In esecuzione**. Dall'elenco delle applicazioni in esecuzione, determinare l'applicazione da terminare e fare clic sull'**ID**.

    ![Terminare App1](./media/apache-spark-resource-manager/kill-app1.png "Terminare App1")

2. Fare clic su **Kill Application** (Termina applicazione) nella parte superiore destra, quindi fare clic su **OK**.

    ![Terminare App2](./media/apache-spark-resource-manager/kill-app2.png "Terminare App2")

## <a name="see-also"></a>Vedere anche 
* [Tenere traccia ed eseguire il debug di processi in esecuzione nel cluster Apache Spark in Azure HDInsight](apache-spark-job-debugging.md)

### <a name="for-data-analysts"></a>Per gli analisti dei dati

* [Spark con Machine Learning: utilizzare Spark in HDInsight per l'analisi della temperatura di compilazione utilizzando dati HVAC](apache-spark-ipython-notebook-machine-learning.md)
* [Spark con Machine Learning: utilizzare Spark in HDInsight per stimare i risultati dell'ispezione cibo](apache-spark-machine-learning-mllib-ipython.md)
* [Analisi dei log del sito Web mediante Spark in HDInsight](apache-spark-custom-library-website-log-analysis.md)
* [Application Insight telemetry data analysis using Spark in HDInsight (Analisi dei dati di telemetria di Application Insights con Spark in HDInsight)](apache-spark-analyze-application-insight-logs.md)
* [Usare Caffe in Azure HDInsight Spark per l'apprendimento avanzato distribuito](apache-spark-deep-learning-caffe.md)

### <a name="for-spark-developers"></a>Per gli sviluppatori di Spark

* [Creare un'applicazione autonoma con Scala](apache-spark-create-standalone-application.md)
* [Eseguire processi in modalità remota in un cluster Spark usando Livy](apache-spark-livy-rest-interface.md)
* [Usare il plug-in degli strumenti HDInsight per IntelliJ IDEA per creare e inviare applicazioni Spark in Scala](apache-spark-intellij-tool-plugin.md)
* [Use HDInsight Tools Plugin for IntelliJ IDEA to debug Spark applications remotely (Usare il plug-in Strumenti HDInsight per IntelliJ IDEA per eseguire il debug di applicazioni Spark in remoto)](apache-spark-intellij-tool-plugin-debug-jobs-remotely.md)
* [Usare i notebook di Zeppelin con un cluster Spark in HDInsight](apache-spark-zeppelin-notebook.md)
* [Kernel disponibili per notebook di Jupyter nel cluster Spark per HDInsight](apache-spark-jupyter-notebook-kernels.md)
* [Usare pacchetti esterni con i notebook Jupyter](apache-spark-jupyter-notebook-use-external-packages.md)
* [Installare Jupyter Notebook nel computer e connetterlo a un cluster HDInsight Spark](apache-spark-jupyter-notebook-install-locally.md)
