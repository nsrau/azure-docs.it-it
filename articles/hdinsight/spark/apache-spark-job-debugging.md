---
title: Eseguire il debug di processi Apache Spark in esecuzione in Azure HDInsight
description: Usare l'interfaccia utente di YARN, l'interfaccia utente di Spark e il server della cronologia di Spark per tenere traccia ed eseguire il debug di processi in esecuzione in un cluster Spark in Azure HDInsight
author: hrasheed-msft
ms.author: hrasheed
ms.reviewer: jasonh
ms.service: hdinsight
ms.topic: conceptual
ms.custom: hdinsightactive
ms.date: 11/29/2019
ms.openlocfilehash: 110a8e86fc1916254ab914630ce10d2b7ae073b7
ms.sourcegitcommit: 6bb98654e97d213c549b23ebb161bda4468a1997
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 12/03/2019
ms.locfileid: "74775334"
---
# <a name="debug-apache-spark-jobs-running-on-azure-hdinsight"></a>Eseguire il debug di processi Apache Spark in esecuzione in Azure HDInsight

Questo articolo illustra come tenere traccia ed eseguire il debug di processi [Apache Spark](https://spark.apache.org/) in esecuzione nei cluster HDInsight tramite l'interfaccia utente di [Apache Hadoop YARN](https://hadoop.apache.org/docs/current/hadoop-yarn/hadoop-yarn-site/YARN.html), l'interfaccia utente di Spark e il server cronologia Spark. Si avvierà un processo Spark usando un notebook disponibile nel cluster Spark, **Machine Learning: analisi predittiva dei dati del controllo degli alimenti tramite MLlib**. È possibile usare la procedura seguente anche per tenere traccia di un'applicazione inviata usando qualsiasi altro approccio, ad esempio, **spark-submit**.

Se non si ha una sottoscrizione di Azure, creare un [account gratuito](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) prima di iniziare.

## <a name="prerequisites"></a>Prerequisiti

* Un cluster Apache Spark in HDInsight. Per istruzioni, vedere l'articolo dedicato alla [creazione di cluster Apache Spark in Azure HDInsight](apache-spark-jupyter-spark-sql.md).

* Si sarà già avviato il notebook **[Machine Learning: analisi predittiva dei dati del controllo degli alimenti tramite MLlib](apache-spark-machine-learning-mllib-ipython.md)** . Per istruzioni su come eseguire questo notebook, seguire il collegamento.  

## <a name="track-an-application-in-the-yarn-ui"></a>Tenere traccia di un'applicazione nell'interfaccia utente di YARN

1. Avviare l'interfaccia utente di YARN Selezionare **Yarn** in **Dashboard cluster**.

    ![portale di Azure avviare l'interfaccia utente di YARN](./media/apache-spark-job-debugging/launch-apache-yarn-ui.png)

   > [!TIP]  
   > In alternativa, è anche possibile avviare l'interfaccia utente di YARN dall'interfaccia utente di Ambari. Per avviare l'interfaccia utente di Ambariri, selezionare **Ambari Home** in **Dashboard cluster**. Dall'interfaccia utente di Ambariri passare a **YARN** > **collegamenti rapidi** > l' **interfaccia utente di gestione risorse**Active Gestione risorse >.

2. Poiché il processo Spark è stato avviato con Jupyter Notebook, il nome dell'applicazione è **remotesparkmagics**. Si tratta del nome per tutte le applicazioni avviate dai notebook. Per ottenere altre informazioni sul processo, selezionare l'ID applicazione con il nome dell'applicazione. Verrà avviata la visualizzazione dell'applicazione.

    ![Il server della cronologia Spark trova l'ID applicazione Spark](./media/apache-spark-job-debugging/find-application-id1.png)

    Per le applicazioni avviate da Jupyter Notebook lo stato è sempre **IN ESECUZIONE** fino a quando non si esce dal notebook.

3. Nella visualizzazione dell'applicazione è possibile eseguire il drill-down per trovare i contenitori associati all'applicazione e i log (stdout o stderr). È anche possibile avviare l'interfaccia utente di Spark facendo clic sul collegamento corrispondente all' **URL di verifica**, come illustrato di seguito.

    ![Log del contenitore di download del server cronologia Spark](./media/apache-spark-job-debugging/download-container-logs.png)

## <a name="track-an-application-in-the-spark-ui"></a>Tenere traccia di un'applicazione nell'interfaccia utente di Spark

Nell'interfaccia utente di Spark è possibile eseguire il drill-down dei processi Spark generati dall'applicazione avviata in precedenza.

1. Per avviare l'interfaccia utente di Spark, nella visualizzazione dell'applicazione selezionare il collegamento all' **URL di rilevamento**, come illustrato nella schermata precedente. È possibile visualizzare tutti i processi Spark avviati dall'applicazione in esecuzione in Jupyter Notebook.

    ![Scheda processi server cronologia Spark](./media/apache-spark-job-debugging/view-apache-spark-jobs.png)

2. Selezionare la scheda **esecutori** per visualizzare le informazioni di elaborazione e archiviazione per ogni Executor. È anche possibile recuperare lo stack di chiamate selezionando il collegamento del **dump del thread** .

    ![Scheda esecutori Server cronologia Spark](./media/apache-spark-job-debugging/view-spark-executors.png)

3. Selezionare la scheda **fasi** per visualizzare le fasi associate all'applicazione.

    ![Scheda fasi Server cronologia Spark](./media/apache-spark-job-debugging/view-apache-spark-stages.png "Visualizzare le fasi di Spark")

    Ogni fase può avere più attività per cui è possibile visualizzare le statistiche di esecuzione, come illustrato di seguito.

    ![Dettagli scheda fasi Server cronologia Spark](./media/apache-spark-job-debugging/view-spark-stages-details.png "Visualizza i dettagli delle fasi di Spark")

4. Nella pagina dei dettagli relativi alle fasi è possibile avviare la visualizzazione DAG. Espandere il collegamento **DAG Visualization** nella parte superiore della pagina, come illustrato di seguito.

    ![Mostrare la visualizzazione DAG delle fasi di Spark](./media/apache-spark-job-debugging/view-spark-stages-dag-visualization.png)

    DAG o Direct Aclyic Graph rappresenta le diverse fasi dell'applicazione. Ogni casella blu nel grafico rappresenta un'operazione Spark richiamata dall'applicazione.

5. Nella pagina dei dettagli relativi alle fasi è anche possibile avviare la visualizzazione della sequenza temporale dell'applicazione. Espandere il collegamento **Event Timeline** nella parte superiore della pagina, come illustrato di seguito.

    ![Visualizzare e la sequenza temporale di eventi delle fasi di Spark](./media/apache-spark-job-debugging/view-spark-stages-event-timeline.png)

    Verranno visualizzati gli eventi di Spark sotto forma di sequenza temporale. La visualizzazione della sequenza temporale è disponibile in tre livelli, tra processi, all'interno di un processo e all'interno di una fase. L'immagine precedente mostra la visualizzazione della sequenza temporale per una determinata fase.

   > [!TIP]  
   > Se si seleziona la casella di controllo **Enable zooming** , è possibile scorrere a sinistra e destra nella visualizzazione della sequenza temporale.

6. Altre schede nell'interfaccia utente di Spark forniscono anche informazioni utili sull'istanza di Spark.

   * Scheda archiviazione: se l'applicazione crea un RDD, è possibile trovare informazioni su tali dati nella scheda archiviazione.
   * Scheda Environment (ambiente): questa scheda fornisce informazioni utili sull'istanza di Spark, ad esempio:
     * Versione di Scala
     * Directory dei log eventi associata al cluster
     * Numero di core executor per l'applicazione
     * e così via.

## <a name="find-information-about-completed-jobs-using-the-spark-history-server"></a>Trovare informazioni sui processi completati tramite Server cronologia Spark

Una volta completato un processo, le informazioni corrispondenti vengono salvate in modo permanente nel Server cronologia Spark.

1. Per avviare il server cronologia Spark, nella pagina **Panoramica** selezionare **Server cronologia Spark** in **Dashboard cluster**.

    ![portale di Azure avviare il server cronologia Spark](./media/apache-spark-job-debugging/launch-spark-history-server.png "Avviare la cronologia Spark Server1")

   > [!TIP]  
   > In alternativa, è anche possibile avviare l'interfaccia utente del Server cronologia Spark dall'interfaccia utente di Ambari. Per avviare l'interfaccia utente di Ambari, nel pannello Panoramica selezionare **Ambari Home** in **Dashboard cluster**. Dall'interfaccia utente di Ambari passare a **Spark2** > **collegamenti rapidi** > **interfaccia utente di Spark2 Cronologia server**.

2. Vengono elencate tutte le applicazioni completate. Per ulteriori informazioni, selezionare un ID applicazione per eseguire il drill-down in un'applicazione.

    ![Applicazioni complete del server cronologia Spark](./media/apache-spark-job-debugging/view-completed-applications.png "Avviare la cronologia Spark Server2")

## <a name="see-also"></a>Vedi anche

* [Gestire le risorse del cluster Apache Spark in Azure HDInsight](apache-spark-resource-manager.md)
* [Eseguire il debug dei processi Apache Spark tramite il Server cronologia Spark esteso](apache-azure-spark-history-server.md)

### <a name="for-data-analysts"></a>Per gli analisti dei dati

* [Apache Spark with Machine Learning: Use Spark in HDInsight for analyzing building temperature using HVAC data](apache-spark-ipython-notebook-machine-learning.md) (Apache Spark con Machine Learning: usare Spark in HDInsight per analizzare la temperatura di un edificio usando dati HVAC)
* [Apache Spark con Machine Learning: utilizzare Spark in HDInsight per stimare i risultati dell'ispezione cibo](apache-spark-machine-learning-mllib-ipython.md)
* [Analisi dei log del sito Web con Apache Spark in HDInsight](apache-spark-custom-library-website-log-analysis.md)
* [Analisi dei dati di telemetria di Application Insights con Apache Spark in HDInsight](apache-spark-analyze-application-insight-logs.md)
* [Usare Caffe in Azure HDInsight Spark per l'apprendimento avanzato distribuito](apache-spark-deep-learning-caffe.md)

### <a name="for-spark-developers"></a>Per gli sviluppatori di Spark

* [Creare un'applicazione autonoma con Scala](apache-spark-create-standalone-application.md)
* [Eseguire processi in modalità remota in un cluster Apache Spark usando Apache Livy](apache-spark-livy-rest-interface.md)
* [Usare il plug-in degli strumenti HDInsight per IntelliJ IDEA per creare e inviare applicazioni Spark Scala](apache-spark-intellij-tool-plugin.md)
* [Usare il plug-in Strumenti HDInsight per IntelliJ IDEA per eseguire il debug di applicazioni Apache Spark in remoto](apache-spark-intellij-tool-plugin-debug-jobs-remotely.md)
* [Usare i notebook di Apache Zeppelin con un cluster Apache Spark in HDInsight](apache-spark-zeppelin-notebook.md)
* [Kernel disponibili per notebook di Jupyter nel cluster Apache Spark per HDInsight](apache-spark-jupyter-notebook-kernels.md)
* [Usare pacchetti esterni con i notebook Jupyter](apache-spark-jupyter-notebook-use-external-packages.md)
* [Installare Jupyter nel computer e connetterlo a un cluster HDInsight Spark](apache-spark-jupyter-notebook-install-locally.md)
