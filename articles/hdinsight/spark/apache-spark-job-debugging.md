---
title: Eseguire il debug di processi Apache Spark in esecuzione in Azure HDInsight | Microsoft Docs
description: Usare l'interfaccia utente di YARN, l'interfaccia utente di Spark e il server della cronologia di Spark per tenere traccia ed eseguire il debug di processi in esecuzione in un cluster Spark in Azure HDInsight
services: hdinsight
documentationcenter: 
author: mumian
manager: cgronlun
editor: cgronlun
tags: azure-portal
ms.assetid: 59af05a7-2bd9-44b0-b55f-2438d294198b
ms.service: hdinsight
ms.custom: hdinsightactive
ms.workload: big-data
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 12/20/2017
ms.author: jgao
ms.openlocfilehash: fb2487ec854260bacf98789bd1be482172ead6a7
ms.sourcegitcommit: 901a3ad293669093e3964ed3e717227946f0af96
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 12/21/2017
---
# <a name="debug-apache-spark-jobs-running-on-azure-hdinsight"></a>Eseguire il debug di processi Apache Spark in esecuzione in Azure HDInsight

Questo articolo illustra come tenere traccia ed eseguire il debug di processi Spark in esecuzione nei cluster HDInsight tramite l'interfaccia utente di YARN, l'interfaccia utente di Spark e il server cronologia Spark. Per questo articolo, si avvierà un processo Spark usando un notebook disponibile nel cluster Spark, **Machine Learning: analisi predittiva dei dati del controllo degli alimenti tramite MLlib**. È possibile usare la procedura seguente per tenere traccia di un'applicazione inviata usando anche qualsiasi altro approccio, ad esempio, **spark-submit**.

## <a name="prerequisites"></a>prerequisiti
È necessario disporre di quanto segue:

* Una sottoscrizione di Azure. Vedere [Ottenere una versione di prova gratuita di Azure](https://azure.microsoft.com/documentation/videos/get-azure-free-trial-for-testing-hadoop-in-hdinsight/).
* Un cluster Apache Spark in HDInsight. Per istruzioni, vedere l'articolo dedicato alla [creazione di cluster Apache Spark in Azure HDInsight](apache-spark-jupyter-spark-sql.md).
* Si sarà già avviato il notebook **[Machine Learning: analisi predittiva dei dati del controllo degli alimenti tramite MLlib](apache-spark-machine-learning-mllib-ipython.md)**. Per istruzioni su come eseguire questo notebook, seguire il collegamento.  

## <a name="track-an-application-in-the-yarn-ui"></a>Tenere traccia di un'applicazione nell'interfaccia utente di YARN
1. Avviare l'interfaccia utente di YARN Nel pannello del cluster fare clic su **Dashboard cluster** e quindi su **YARN**.
   
    ![Avviare l'interfaccia utente di YARN](./media/apache-spark-job-debugging/launch-yarn-ui.png)
   
   > [!TIP]
   > In alternativa, è anche possibile avviare l'interfaccia utente di YARN dall'interfaccia utente di Ambari. Per avviare l'interfaccia utente di Ambari, nel pannello del cluster fare clic su **Dashboard cluster** e quindi su **Dashboard cluster HDInsight**. Nell'interfaccia utente di Ambari fare clic su **YARN**, su **Collegamenti rapidi**, sulla funzionalità di gestione risorse attiva e quindi fare clic su **ResourceManager UI**.    
   > 
   > 
2. Poiché il processo Spark è stato avviato con Jupyter Notebook, il nome dell'applicazione è **remotesparkmagics**. Si tratta del nome per tutte le applicazioni avviate dai notebook. Per ottenere altre informazioni sul processo, fare clic sull'ID dell'applicazione relativo al nome dell'applicazione. Verrà avviata la visualizzazione dell'applicazione.
   
    ![Trovare un ID applicazione di Spark](./media/apache-spark-job-debugging/find-application-id.png)
   
    Per le applicazioni avviate da Jupyter Notebook lo stato è sempre **IN ESECUZIONE** fino a quando non si esce dal notebook.
3. Nella visualizzazione dell'applicazione è possibile eseguire il drill-down per trovare i contenitori associati all'applicazione e i log (stdout o stderr). È anche possibile avviare l'interfaccia utente di Spark facendo clic sul collegamento corrispondente all' **URL di verifica**, come illustrato di seguito. 
   
    ![Scaricare i log del contenitore](./media/apache-spark-job-debugging/download-container-logs.png)

## <a name="track-an-application-in-the-spark-ui"></a>Tenere traccia di un'applicazione nell'interfaccia utente di Spark
Nell'interfaccia utente di Spark è possibile eseguire il drill-down dei processi Spark generati dall'applicazione avviata in precedenza.

1. Per avviare l'interfaccia utente di Spark, nella visualizzazione dell'applicazione, fare clic sul collegamento relativo a **URL di verifica**, come illustrato nella schermata precedente. È possibile visualizzare tutti i processi Spark avviati dall'applicazione in esecuzione in Jupyter Notebook.
   
    ![Visualizzare processi Spark](./media/apache-spark-job-debugging/view-spark-jobs.png)
2. Fare clic sulla scheda **Executors** per visualizzare le informazioni di elaborazione e archiviazione per ogni executor. È anche possibile recuperare lo stack di chiamate facendo clic sul collegamento **Thread Dump** .
   
    ![Visualizzare gli executor Spark](./media/apache-spark-job-debugging/view-spark-executors.png)
3. Fare clic sulla scheda **Stages** per visualizzare le fasi associate all'applicazione.
   
    ![Visualizzare le fasi di Spark](./media/apache-spark-job-debugging/view-spark-stages.png)
   
    Ogni fase può avere più attività per cui è possibile visualizzare le statistiche di esecuzione, come illustrato di seguito.
   
    ![Visualizzare le fasi di Spark](./media/apache-spark-job-debugging/view-spark-stages-details.png) 
4. Nella pagina dei dettagli relativi alle fasi è possibile avviare la visualizzazione DAG. Espandere il collegamento **DAG Visualization** nella parte superiore della pagina, come illustrato di seguito.
   
    ![Mostrare la visualizzazione DAG delle fasi di Spark](./media/apache-spark-job-debugging/view-spark-stages-dag-visualization.png)
   
    DAG o Direct Aclyic Graph rappresenta le diverse fasi dell'applicazione. Ogni casella blu nel grafico rappresenta un'operazione Spark richiamata dall'applicazione.
5. Nella pagina dei dettagli relativi alle fasi è anche possibile avviare la visualizzazione della sequenza temporale dell'applicazione. Espandere il collegamento **Event Timeline** nella parte superiore della pagina, come illustrato di seguito.
   
    ![Visualizzare e la sequenza temporale di eventi delle fasi di Spark](./media/apache-spark-job-debugging/view-spark-stages-event-timeline.png)
   
    Verranno visualizzati gli eventi di Spark sotto forma di sequenza temporale. La visualizzazione della sequenza temporale è disponibile in tre livelli, tra processi, all'interno di un processo e all'interno di una fase. L'immagine precedente mostra la visualizzazione della sequenza temporale per una determinata fase.
   
   > [!TIP]
   > Se si seleziona la casella di controllo **Enable zooming** , è possibile scorrere a sinistra e destra nella visualizzazione della sequenza temporale.
   > 
   > 
6. Altre schede nell'interfaccia utente di Spark forniscono anche informazioni utili sull'istanza di Spark.
   
   * Scheda Storage: se l'applicazione crea oggetti RDD, è possibile trovare informazioni corrispondenti in questa scheda.
   * Scheda Environment: questa scheda fornisce molte informazioni utili sull'istanza di Spark, ad esempio 
     * Versione di Scala
     * Directory dei log eventi associata al cluster
     * Numero di core executor per l'applicazione
     * e così via.

## <a name="find-information-about-completed-jobs-using-the-spark-history-server"></a>Trovare informazioni sui processi completati tramite Server cronologia Spark
Una volta completato un processo, le informazioni corrispondenti vengono salvate in modo permanente nel Server cronologia Spark.

1. Per avviare il Server cronologia Spark, nel pannello del cluster fare clic su **Dashboard cluster** e quindi su **Server cronologia Spark**.
   
    ![Avviare Server cronologia Spark](./media/apache-spark-job-debugging/launch-spark-history-server.png)
   
   > [!TIP]
   > In alternativa, è anche possibile avviare l'interfaccia utente del Server cronologia Spark dall'interfaccia utente di Ambari. Per avviare l'interfaccia utente di Ambari, nel pannello del cluster fare clic su **Dashboard cluster** e quindi su **Dashboard cluster HDInsight**. Nell'interfaccia utente di Ambari fare clic su **Spark**, fare clic su **Collegamenti rapidi** e quindi sull'interfaccia utente di **Server cronologia Spark**.
   > 
   > 
2. Saranno elencate tutte le applicazioni completate. Fare clic su un ID applicazione per eseguire il drill-down di un'applicazione per altre informazioni.
   
    ![Avviare Server cronologia Spark](./media/apache-spark-job-debugging/view-completed-applications.png)

## <a name="see-also"></a>Vedere anche 
*  [Gestire le risorse del cluster Apache Spark in Azure HDInsight](apache-spark-resource-manager.md)

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
* [Streaming Spark: usare Spark in HDInsight per la creazione di applicazioni di streaming in tempo reale](apache-spark-eventhub-streaming.md)
* [Use HDInsight Tools Plugin for IntelliJ IDEA to debug Spark applications remotely (Usare il plug-in Strumenti HDInsight per IntelliJ IDEA per eseguire il debug di applicazioni Spark in remoto)](apache-spark-intellij-tool-plugin-debug-jobs-remotely.md)
* [Usare i notebook di Zeppelin con un cluster Spark in HDInsight](apache-spark-zeppelin-notebook.md)
* [Kernel disponibili per notebook di Jupyter nel cluster Spark per HDInsight](apache-spark-jupyter-notebook-kernels.md)
* [Usare pacchetti esterni con i notebook Jupyter](apache-spark-jupyter-notebook-use-external-packages.md)
* [Installare Jupyter Notebook nel computer e connetterlo a un cluster HDInsight Spark](apache-spark-jupyter-notebook-install-locally.md)


