---
title: Gestire le risorse del cluster Apache Spark in Azure HDInsight
description: Informazioni su come gestire le risorse per i cluster Spark in Azure HDInsight per migliorare le prestazioni.
author: hrasheed-msft
ms.author: hrasheed
ms.reviewer: jasonh
ms.service: hdinsight
ms.topic: conceptual
ms.custom: hdinsightactive
ms.date: 12/06/2019
ms.openlocfilehash: 3aab89f86dcd48328771cd0fda03d1c9de4bc2c2
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/27/2020
ms.locfileid: "75932097"
---
# <a name="manage-resources-for-apache-spark-cluster-on-azure-hdinsight"></a>Gestire le risorse del cluster Apache Spark in Azure HDInsight

Informazioni su come accedere alle interfacce come [Apache Ambari](https://ambari.apache.org/) UI, [Apache Hadoop YARN](https://hadoop.apache.org/docs/current/hadoop-yarn/hadoop-yarn-site/YARN.html) UI e il [server di cronologia Spark](./apache-azure-spark-history-server.md) associato al cluster [Apache Spark](https://spark.apache.org/) e come ottimizzare la configurazione del cluster per ottenere prestazioni ottimali.

## <a name="open-the-spark-history-server"></a>Aprire il Server cronologia Spark

Server cronologia Spark è l'interfaccia utente Web per le applicazioni Spark completate e in esecuzione. È un'estensione dell'interfaccia utente Web di Spark. Per informazioni complete, vedere [Server della cronologia Spark](./apache-azure-spark-history-server.md).

## <a name="open-the-yarn-ui"></a>Aprire l'interfaccia utente di YARN

È possibile usare l'interfaccia utente di YARN per il monitoraggio delle applicazioni attualmente in esecuzione nel cluster Spark.

1. Nel [portale di Azure](https://portal.azure.com/) aprire il cluster Spark. Per altre informazioni, vedere [Elencare e visualizzare i cluster](../hdinsight-administer-use-portal-linux.md#showClusters).

2. In **Dashboard cluster**selezionare **Filato**. Quando richiesto, immettere le credenziali di amministratore per il cluster di Spark.

    ![Avviare l'interfaccia utente di YARN](./media/apache-spark-resource-manager/azure-portal-dashboard-yarn.png)

   > [!TIP]  
   > In alternativa, è anche possibile avviare l'interfaccia utente di YARN dall'interfaccia utente di Ambari. Dall'interfaccia utente di Ambari, accedere a **YARN** > **Quick Links** > **Active** > **Resource Manager UI**.

## <a name="optimize-clusters-for-spark-applications"></a>Ottimizzare i cluster per le applicazioni Spark

I tre parametri principali che possono essere usati per la configurazione di Spark, a seconda dei requisiti dell'applicazione, sono `spark.executor.instances`, `spark.executor.cores` e `spark.executor.memory`. Un Executor è un processo avviato per un'applicazione Spark. Viene eseguito sul nodo di lavoro e svolge le attività per l'applicazione. Il numero predefinito di executor e le relative dimensioni per ogni cluster vengono calcolati in base al numero di nodi di lavoro e alle relative dimensioni. Questi dati vengono archiviati in `spark-defaults.conf` nei nodi head del cluster.

I tre parametri di configurazione possono essere configurati a livello di cluster, per tutte le applicazioni in esecuzione nel cluster, o possono anche essere specificati per ogni singola applicazione.

### <a name="change-the-parameters-using-ambari-ui"></a>Modificare i parametri con l'interfaccia utente di Ambari

1. Dall'interfaccia utente di Ambari passare a **Spark2** > **Configs** > **Custom spark2-defaults**.

    ![Impostare i parametri utilizzando Ambari custom](./media/apache-spark-resource-manager/ambari-ui-spark2-configs.png "Impostare i parametri utilizzando Ambari custom")

1. I valori predefiniti sono appropriati per quattro applicazioni Spark in esecuzione contemporaneamente nel cluster. È possibile modificare questi valori nell'interfaccia utente, come illustrato nello screenshot seguente:

    ![Impostare parametri con Ambari](./media/apache-spark-resource-manager/ambari-ui-spark2-defaults.png "Impostare parametri con Ambari")

1. Selezionare **Salva** per salvare le modifiche alla configurazione. Nella parte superiore della pagina viene richiesto di riavviare tutti i servizi interessati. Selezionare **Restart** (Riavvia).

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

Il server Spark Thrift fornisce l'accesso JDBC/ODBC a un cluster Spark e viene usato per rispondere alle query di Spark SQL. Strumenti come Power BI, Tableau e così via usano il protocollo ODBC per comunicare con Spark Thrift Server per eseguire query Spark SQL come applicazione Spark. Quando si crea un cluster Spark, vengono avviate due istanze del server Spark Thrift, una in ogni nodo head. Ogni Thrift Spark Server è visibile come un'applicazione Spark nell'interfaccia utente di YARN.

Spark Thrift Server utilizza l'allocazione `spark.executor.instances` dell'esecutore dinamico Spark e pertanto non viene utilizzato. Il server Spark Thrift usa invece `spark.dynamicAllocation.maxExecutors` e `spark.dynamicAllocation.minExecutors` per specificare il numero di executor. I parametri `spark.executor.cores`di `spark.executor.memory` configurazione e vengono utilizzati per modificare le dimensioni dell'esecutore. È possibile modificare questi parametri come illustrato nella procedura seguente:

* Espandere la categoria **Advanced spark2-thrift-sparkconf** per aggiornare i parametri `spark.dynamicAllocation.maxExecutors`e `spark.dynamicAllocation.minExecutors`.

    ![Configurare il server Spark Thrift](./media/apache-spark-resource-manager/ambari-ui-advanced-thrift-sparkconf.png "Configurare il server Spark Thrift")

* Espandere la categoria **Custom spark2-thrift-sparkconf** per aggiornare i parametri `spark.executor.cores`e `spark.executor.memory`.

    ![Configurare il parametro server Spark thrift](./media/apache-spark-resource-manager/ambari-ui-custom-thrift-sparkconf.png "Configurare il parametro server Spark thrift")

### <a name="change-the-driver-memory-of-the-spark-thrift-server"></a>Modificare la memoria del driver del server Spark Thrift

La memoria del driver del server Spark Thrift è configurata al 25% delle dimensioni della RAM nodo head, a condizione che le dimensioni totali della RAM del nodo head siano maggiori di 14 GB. Per modificare la configurazione della memoria del driver, è possibile usare l'interfaccia utente di Ambari, come illustrato nello screenshot seguente:

Dall'interfaccia utente di Ambari, accedere a **Spark2** > **Configs** > **Advanced spark2-env**. Specificare quindi il valore per **spark_thrift_cmd_opts**.

## <a name="reclaim-spark-cluster-resources"></a>Recuperare le risorse cluster di Spark

Grazie all'allocazione dinamica di Spark le uniche risorse usate dal server Thrift sono quelle per i due master applicazioni. Per recuperare tali risorse, è necessario arrestare i servizi del server Thrift in esecuzione nel cluster.

1. Nel riquadro sinistro dell'interfaccia utente di Ambari selezionare **Spark2**.

2. Nella pagina successiva selezionare **Spark2 Thrift Servers**.

    ![Riavviare thrift server1](./media/apache-spark-resource-manager/ambari-ui-spark2-thrift-servers.png "Riavviare thrift server1")

3. Verranno visualizzati i due nodi di testa su cui è in esecuzione Spark2 Thrift Server. Selezionare uno dei nodi head.

    ![Riavviare thrift server2](./media/apache-spark-resource-manager/restart-thrift-server-2.png "Riavviare thrift server2")

4. Nella pagina successiva sono elencati tutti i servizi in esecuzione in quel nodo head. Nell'elenco selezionare il pulsante a discesa accanto a Spark2 Thrift Server, quindi selezionare **Interrompi**.

    ![Riavviare thrift server3](./media/apache-spark-resource-manager/ambari-ui-spark2-thriftserver-restart.png "Riavviare thrift server3")
5. Ripetere questi passaggi anche per l'altro nodo head.

## <a name="restart-the-jupyter-service"></a>Riavviare il servizio Jupyter

Avviare l'interfaccia utente Web di Ambari, come illustrato all'inizio dell'articolo. Nel riquadro di spostamento sinistro selezionare **Jupyter**, **Azioni servizio**e quindi **Riavvia tutto**. Verrà avviato il servizio Jupyter su tutti i nodi head.

![Riavviare Jupyter](./media/apache-spark-resource-manager/apache-ambari-restart-jupyter.png "Riavviare Jupyter")

## <a name="monitor-resources"></a>Monitorare le risorse

Avviare l'interfaccia utente di Yarn, come illustrato all'inizio dell'articolo. Nella tabella Cluster Metrics (Metriche cluster) nella parte superiore della schermata, verificare i valori delle colonne **Memory Used** (Memoria in uso) e **Memory Total** (Memoria totale). Se i due valori sono simili, potrebbero non esserci risorse sufficienti per avviare l'applicazione successiva. Lo stesso vale per le colonne **VCores Used** (VCore in uso) e **VCores Total** (VCore totali). Inoltre, nella visualizzazione principale, se c'è un'applicazione rimasta nello stato **ACCEPTED** e non esegue la transizione nello stato **RUNNING** nor **FAILED,** questo potrebbe anche essere un'indicazione che non sta ottenendo abbastanza risorse per avviare.

![Limite risorse](./media/apache-spark-resource-manager/apache-ambari-resource-limit.png "Limite risorse")

## <a name="kill-running-applications"></a>Terminare le applicazioni in esecuzione

1. Nell'interfaccia utente Filati, dal pannello a sinistra, selezionare **In esecuzione**. Dall'elenco delle applicazioni in esecuzione, determinare l'applicazione da uccidere e selezionare **l'ID**.

    ![Uccidi App1](./media/apache-spark-resource-manager/apache-ambari-kill-app1.png "Uccidi App1")

2. Selezionare **Uccidi applicazione** nell'angolo in alto a destra, quindi scegliere **OK**.

    ![Kill App2](./media/apache-spark-resource-manager/apache-ambari-kill-app2.png "Kill App2")

## <a name="see-also"></a>Vedere anche

* [Tenere traccia ed eseguire il debug di processi in esecuzione nel cluster Apache Spark in Azure HDInsight](apache-spark-job-debugging.md)

### <a name="for-data-analysts"></a>Per gli analisti dei dati

* [Apache Spark con Machine Learning: utilizzare Spark in HDInsight per l'analisi della temperatura di compilazione utilizzando dati HVAC](apache-spark-ipython-notebook-machine-learning.md)
* [Apache Spark con Machine Learning: utilizzare Spark in HDInsight per prevedere i risultati di un controllo alimentare](apache-spark-machine-learning-mllib-ipython.md)
* [Analisi dei log del sito Web con Apache Spark in HDInsight](apache-spark-custom-library-website-log-analysis.md)
* [Analisi dei dati di telemetria di Application Insights con Apache Spark in HDInsight](apache-spark-analyze-application-insight-logs.md)

### <a name="for-apache-spark-developers"></a>Per gli sviluppatori di Apache Spark

* [Creare un'applicazione autonoma con Scala](apache-spark-create-standalone-application.md)
* [Eseguire processi in modalità remota in un cluster Apache Spark usando Apache Livy](apache-spark-livy-rest-interface.md)
* [Usare il plug-in degli strumenti HDInsight per IntelliJ IDEA per creare e inviare applicazioni Spark in Scala](apache-spark-intellij-tool-plugin.md)
* [Usare il plug-in Strumenti HDInsight per IntelliJ IDEA per eseguire il debug di applicazioni Apache Spark in remoto](apache-spark-intellij-tool-plugin-debug-jobs-remotely.md)
* [Usare i notebook di Apache Zeppelin con un cluster Apache Spark in HDInsight](apache-spark-zeppelin-notebook.md)
* [Kernel disponibili per notebook di Jupyter nel cluster Apache Spark per HDInsight](apache-spark-jupyter-notebook-kernels.md)
* [Usare pacchetti esterni con i notebook Jupyter](apache-spark-jupyter-notebook-use-external-packages.md)
* [Installare Jupyter Notebook nel computer e connetterlo a un cluster HDInsight Spark](apache-spark-jupyter-notebook-install-locally.md)
