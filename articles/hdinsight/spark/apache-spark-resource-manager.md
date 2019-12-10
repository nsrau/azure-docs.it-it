---
title: Gestire le risorse del cluster Apache Spark in Azure HDInsight
description: Informazioni su come gestire le risorse per i cluster Spark in Azure HDInsight per ottenere prestazioni migliori.
author: hrasheed-msft
ms.author: hrasheed
ms.reviewer: jasonh
ms.service: hdinsight
ms.topic: conceptual
ms.custom: hdinsightactive
ms.date: 12/06/2019
ms.openlocfilehash: 8c4306b9fb7a6f45ccad45083b6efc019e9a2920
ms.sourcegitcommit: 5b9287976617f51d7ff9f8693c30f468b47c2141
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 12/09/2019
ms.locfileid: "74951655"
---
# <a name="manage-resources-for-apache-spark-cluster-on-azure-hdinsight"></a>Gestire le risorse del cluster Apache Spark in Azure HDInsight

Informazioni su come accedere alle interfacce, ad esempio l'interfaccia utente di [Apache Ambari](https://ambari.apache.org/) , l'interfaccia utente di [Apache Hadoop Yarn](https://hadoop.apache.org/docs/current/hadoop-yarn/hadoop-yarn-site/YARN.html) e il [server di cronologia Spark](./apache-azure-spark-history-server.md) associato al cluster [Apache Spark](https://spark.apache.org/) e come ottimizzare la configurazione del cluster per ottenere prestazioni ottimali.

## <a name="open-the-spark-history-server"></a>Aprire il Server cronologia Spark

Server cronologia Spark è l'interfaccia utente Web per le applicazioni Spark completate e in esecuzione. Si tratta di un'estensione dell'interfaccia utente Web di Spark. Per informazioni complete, vedere [Server cronologia Spark](./apache-azure-spark-history-server.md).

## <a name="open-the-yarn-ui"></a>Aprire l'interfaccia utente di YARN

È possibile usare l'interfaccia utente di YARN per il monitoraggio delle applicazioni attualmente in esecuzione nel cluster Spark.

1. Nel [portale di Azure](https://portal.azure.com/) aprire il cluster Spark. Per altre informazioni, vedere [Elencare e visualizzare i cluster](../hdinsight-administer-use-portal-linux.md#showClusters).

2. Da **Dashboard cluster**selezionare **Yarn**. Quando richiesto, immettere le credenziali di amministratore per il cluster di Spark.

    ![Avviare l'interfaccia utente di YARN](./media/apache-spark-resource-manager/azure-portal-dashboard-yarn.png)

   > [!TIP]  
   > In alternativa, è anche possibile avviare l'interfaccia utente di YARN dall'interfaccia utente di Ambari. Dall'interfaccia utente di Ambariri passare a **YARN** > **collegamenti rapidi** > **interfaccia utente > gestione risorse** **attiva** .

## <a name="optimize-clusters-for-spark-applications"></a>Ottimizzare i cluster per le applicazioni Spark

I tre parametri principali che possono essere usati per la configurazione di Spark, a seconda dei requisiti dell'applicazione, sono `spark.executor.instances`, `spark.executor.cores` e `spark.executor.memory`. Un Executor è un processo avviato per un'applicazione Spark. Viene eseguito sul nodo di lavoro e svolge le attività per l'applicazione. Il numero predefinito di executor e le relative dimensioni per ogni cluster vengono calcolati in base al numero di nodi di lavoro e alle relative dimensioni. Questi dati vengono archiviati in `spark-defaults.conf` nei nodi head del cluster.

I tre parametri di configurazione possono essere configurati a livello di cluster, per tutte le applicazioni in esecuzione nel cluster, o possono anche essere specificati per ogni singola applicazione.

### <a name="change-the-parameters-using-ambari-ui"></a>Modificare i parametri con l'interfaccia utente di Ambari

1. Dall'interfaccia utente di Ambari passare a **Spark2** > **configs** > **Custom Spark2-defaults**.

    ![Impostare i parametri usando Ambari Custom](./media/apache-spark-resource-manager/ambari-ui-spark2-configs.png "Impostare i parametri usando Ambari Custom")

1. I valori predefiniti sono appropriati per quattro applicazioni Spark in esecuzione contemporaneamente nel cluster. È possibile modificare questi valori nell'interfaccia utente, come illustrato nello screenshot seguente:

    ![Impostare i parametri usando Ambari](./media/apache-spark-resource-manager/ambari-ui-spark2-defaults.png "Impostare parametri con Ambari")

1. Selezionare **Save (Salva** ) per salvare le modifiche alla configurazione. Nella parte superiore della pagina verrà richiesto di riavviare tutti i servizi interessati. Selezionare **Restart** (Riavvia).

    ![Riavviare i servizi](./media/apache-spark-resource-manager/apache-ambari-restart-services.png)

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

Il server Spark Thrift fornisce l'accesso JDBC/ODBC a un cluster Spark e viene usato per rispondere alle query di Spark SQL. Strumenti come Power BI, tableau e così via, usano il protocollo ODBC per comunicare con il server di risparmio Spark per eseguire query Spark SQL come applicazione Spark. Quando si crea un cluster Spark, vengono avviate due istanze del server Spark Thrift, una in ogni nodo head. Ogni Thrift Spark Server è visibile come un'applicazione Spark nell'interfaccia utente di YARN.

Il server di risparmio Spark usa l'allocazione di Executor dinamica di Spark e pertanto non viene usata la `spark.executor.instances`. Il server Spark Thrift usa invece `spark.dynamicAllocation.maxExecutors` e `spark.dynamicAllocation.minExecutors` per specificare il numero di executor. I parametri di configurazione `spark.executor.cores`e `spark.executor.memory` vengono utilizzati per modificare le dimensioni dell'executor. È possibile modificare questi parametri come illustrato nella procedura seguente:

* Espandere la categoria **Advanced spark2-Thrifty-sparkconf** per aggiornare i parametri `spark.dynamicAllocation.maxExecutors`e `spark.dynamicAllocation.minExecutors`.

    ![Configurare il server di risparmio Spark](./media/apache-spark-resource-manager/ambari-ui-advanced-thrift-sparkconf.png "Configurare il server Spark Thrift")

* Espandere la categoria **spark2-Thrifty-Sparkconf personalizzata** per aggiornare i parametri `spark.executor.cores`e `spark.executor.memory`.

    ![Configurare il parametro del server per la proprietà Spark](./media/apache-spark-resource-manager/ambari-ui-custom-thrift-sparkconf.png "Configurare il parametro del server per la proprietà Spark")

### <a name="change-the-driver-memory-of-the-spark-thrift-server"></a>Modificare la memoria del driver del server Spark Thrift

La memoria del driver del server Spark Thrift è configurata al 25% delle dimensioni della RAM nodo head, a condizione che le dimensioni totali della RAM del nodo head siano maggiori di 14 GB. Per modificare la configurazione della memoria del driver, è possibile usare l'interfaccia utente di Ambari, come illustrato nello screenshot seguente:

Dall'interfaccia utente di Ambari passare a **Spark2** > **configs** > **Advanced Spark2-ENV**. Specificare quindi il valore per **spark_thrift_cmd_opts**.

## <a name="reclaim-spark-cluster-resources"></a>Recuperare le risorse cluster di Spark

Grazie all'allocazione dinamica di Spark le uniche risorse usate dal server Thrift sono quelle per i due master applicazioni. Per recuperare tali risorse, è necessario arrestare i servizi del server Thrift in esecuzione nel cluster.

1. Dal riquadro sinistro dell'interfaccia utente di Ambari selezionare **Spark2**.

2. Nella pagina successiva selezionare **Spark2 Thrifty Servers**.

    ![Riavviare la parsimonia Server1](./media/apache-spark-resource-manager/ambari-ui-spark2-thrift-servers.png "Riavviare la parsimonia Server1")

3. Verranno visualizzati i due nodi head su cui è in esecuzione il server di Spark2. Selezionare uno dei nodi head.

    ![Riavviare l'Thrifty](./media/apache-spark-resource-manager/restart-thrift-server-2.png "Riavviare l'Thrifty")

4. Nella pagina successiva sono elencati tutti i servizi in esecuzione in quel nodo head. Nell'elenco selezionare il pulsante a discesa accanto a Spark2 parsimonia server e quindi selezionare **Arresta**.

    ![Riavvia Server3 di risparmio](./media/apache-spark-resource-manager/ambari-ui-spark2-thriftserver-restart.png "Riavvia Server3 di risparmio")
5. Ripetere questi passaggi anche per l'altro nodo head.

## <a name="restart-the-jupyter-service"></a>Riavviare il servizio Jupyter

Avviare l'interfaccia utente Web di Ambari, come illustrato all'inizio dell'articolo. Dal riquadro di spostamento a sinistra selezionare **Jupyter**, selezionare **azioni di servizio**e quindi selezionare **riavvia tutto**. Verrà avviato il servizio Jupyter su tutti i nodi head.

![Riavviare Jupyter](./media/apache-spark-resource-manager/apache-ambari-restart-jupyter.png "Riavviare Jupyter")

## <a name="monitor-resources"></a>Monitorare le risorse

Avviare l'interfaccia utente di Yarn, come illustrato all'inizio dell'articolo. Nella tabella Cluster Metrics (Metriche cluster) nella parte superiore della schermata, verificare i valori delle colonne **Memory Used** (Memoria in uso) e **Memory Total** (Memoria totale). Se i due valori sono simili, potrebbero non esserci risorse sufficienti per avviare l'applicazione successiva. Lo stesso vale per le colonne **VCores Used** (VCore in uso) e **VCores Total** (VCore totali). Inoltre, nella visualizzazione principale, se un'applicazione è rimasta in stato **accettato** e non passa allo stato di **esecuzione** o di **errore** , questo potrebbe anche indicare che non si sta ottenendo risorse sufficienti per iniziare.

![Limite di risorse](./media/apache-spark-resource-manager/apache-ambari-resource-limit.png "Limite di risorse")

## <a name="kill-running-applications"></a>Terminare le applicazioni in esecuzione

1. Nell'interfaccia utente di Yarn selezionare **Running (in esecuzione**) nel riquadro sinistro. Dall'elenco delle applicazioni in esecuzione, determinare l'applicazione da terminare e selezionare l' **ID**.

    ![Kill App1](./media/apache-spark-resource-manager/apache-ambari-kill-app1.png "Kill App1")

2. Selezionare **Kill Application** (Termina applicazione) nell'angolo superiore destro e quindi fare clic su **OK**.

    ![Kill App2](./media/apache-spark-resource-manager/apache-ambari-kill-app2.png "Kill App2")

## <a name="see-also"></a>Vedi anche

* [Tenere traccia ed eseguire il debug di processi in esecuzione nel cluster Apache Spark in Azure HDInsight](apache-spark-job-debugging.md)

### <a name="for-data-analysts"></a>Per gli analisti dei dati

* [Apache Spark with Machine Learning: Use Spark in HDInsight for analyzing building temperature using HVAC data](apache-spark-ipython-notebook-machine-learning.md) (Apache Spark con Machine Learning: usare Spark in HDInsight per analizzare la temperatura di un edificio usando dati HVAC)
* [Apache Spark con Machine Learning: utilizzare Spark in HDInsight per stimare i risultati dell'ispezione cibo](apache-spark-machine-learning-mllib-ipython.md)
* [Analisi dei log del sito Web con Apache Spark in HDInsight](apache-spark-custom-library-website-log-analysis.md)
* [Analisi dei dati di telemetria di Application Insights con Apache Spark in HDInsight](apache-spark-analyze-application-insight-logs.md)
* [Usare Caffe in Azure HDInsight Spark per l'apprendimento avanzato distribuito](apache-spark-deep-learning-caffe.md)

### <a name="for-apache-spark-developers"></a>Per gli sviluppatori di Apache Spark

* [Creare un'applicazione autonoma con Scala](apache-spark-create-standalone-application.md)
* [Eseguire processi in modalità remota in un cluster Apache Spark usando Apache Livy](apache-spark-livy-rest-interface.md)
* [Usare il plug-in degli strumenti HDInsight per IntelliJ IDEA per creare e inviare applicazioni Spark Scala](apache-spark-intellij-tool-plugin.md)
* [Usare il plug-in Strumenti HDInsight per IntelliJ IDEA per eseguire il debug di applicazioni Apache Spark in remoto](apache-spark-intellij-tool-plugin-debug-jobs-remotely.md)
* [Usare i notebook di Apache Zeppelin con un cluster Apache Spark in HDInsight](apache-spark-zeppelin-notebook.md)
* [Kernel disponibili per notebook di Jupyter nel cluster Apache Spark per HDInsight](apache-spark-jupyter-notebook-kernels.md)
* [Usare pacchetti esterni con i notebook Jupyter](apache-spark-jupyter-notebook-use-external-packages.md)
* [Installare Jupyter nel computer e connetterlo a un cluster HDInsight Spark](apache-spark-jupyter-notebook-install-locally.md)
