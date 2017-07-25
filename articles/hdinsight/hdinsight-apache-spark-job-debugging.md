---
title: Eseguire il debug di processi Apache Spark in esecuzione in Azure HDInsight | Microsoft Docs
description: Usare l'interfaccia utente di YARN, l'interfaccia utente di Spark e il server della cronologia di Spark per tenere traccia ed eseguire il debug di processi in esecuzione in un cluster Spark in Azure HDInsight
services: hdinsight
documentationcenter: 
author: nitinme
manager: jhubbard
editor: cgronlun
tags: azure-portal
ms.assetid: 59af05a7-2bd9-44b0-b55f-2438d294198b
ms.service: hdinsight
ms.custom: hdinsightactive
ms.workload: big-data
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 07/20/2017
ms.author: nitinme
ms.translationtype: HT
ms.sourcegitcommit: 2812039649f7d2fb0705220854e4d8d0a031d31e
ms.openlocfilehash: 069f63dd43bd81c209826382d04266c2640f017e
ms.contentlocale: it-it
ms.lasthandoff: 07/22/2017

---
# <a name="debug-apache-spark-jobs-running-on-azure-hdinsight"></a>Eseguire il debug di processi Apache Spark in esecuzione in Azure HDInsight

Questo articolo illustra come tenere traccia ed eseguire il debug di processi Spark in esecuzione nei cluster HDInsight tramite l'interfaccia utente di YARN, l'interfaccia utente di Spark e il server di cronologia Spark. Per questo articolo,si avvierà un processo Spark usando un notebook disponibile nel cluster Spark, **Machine Learning: analisi predittiva dei dati del controllo degli alimenti tramite MLlib**. È possibile usare la procedura seguente per tenere traccia di un'applicazione inviata usando anche qualsiasi altro approccio, ad esempio, **spark-submit**.

## <a name="prerequisites"></a>Prerequisiti
È necessario disporre di quanto segue:

* Una sottoscrizione di Azure. Vedere [Ottenere una versione di valutazione gratuita di Azure](https://azure.microsoft.com/documentation/videos/get-azure-free-trial-for-testing-hadoop-in-hdinsight/).
* Un cluster Apache Spark in HDInsight. Per istruzioni, vedere l'articolo relativo alla [creazione di cluster Apache Spark in Azure HDInsight](hdinsight-apache-spark-jupyter-spark-sql.md).
* Si sarà già avviato il notebook **[Machine Learning: analisi predittiva dei dati del controllo degli alimenti tramite MLlib](hdinsight-apache-spark-machine-learning-mllib-ipython.md)**. Per istruzioni su come eseguire questo notebook, seguire il collegamento.  

## <a name="track-an-application-in-the-yarn-ui"></a>Tenere traccia di un'applicazione nell'interfaccia utente di YARN
1. Avviare l'interfaccia utente di YARN Nel pannello del cluster fare clic su **Dashboard cluster** e quindi su **YARN**.
   
    ![Avviare l'interfaccia utente di YARN](./media/hdinsight-apache-spark-job-debugging/launch-yarn-ui.png)
   
   > [!TIP]
   > In alternativa, è anche possibile avviare l'interfaccia utente di YARN dall'interfaccia utente di Ambari. Per avviare l'interfaccia utente di Ambari, nel pannello del cluster fare clic su **Dashboard cluster** e quindi su **Dashboard cluster HDInsight**. Nell'interfaccia utente di Ambari fare clic su **YARN**, su **Collegamenti rapidi**, sulla funzionalità di gestione risorse attiva e quindi fare clic su **ResourceManager UI**.    
   > 
   > 
2. Poiché il processo Spark è stato avviato con Jupyter Notebook, il nome dell'applicazione è **remotesparkmagics**. Si tratta del nome per tutte le applicazioni avviate dai notebook. Per ottenere altre informazioni sul processo, fare clic sull'ID dell'applicazione relativo al nome dell'applicazione. Verrà avviata la visualizzazione dell'applicazione.
   
    ![Trovare un ID applicazione di Spark](./media/hdinsight-apache-spark-job-debugging/find-application-id.png)
   
    Per le applicazioni avviate da Jupyter Notebook lo stato è sempre **IN ESECUZIONE** fino a quando non si esce dal notebook.
3. Nella visualizzazione dell'applicazione è possibile eseguire il drill-down per trovare i contenitori associati all'applicazione e i log (stdout o stderr). È anche possibile avviare l'interfaccia utente di Spark facendo clic sul collegamento corrispondente all' **URL di verifica**, come illustrato di seguito. 
   
    ![Scaricare i log del contenitore](./media/hdinsight-apache-spark-job-debugging/download-container-logs.png)

## <a name="track-an-application-in-the-spark-ui"></a>Tenere traccia di un'applicazione nell'interfaccia utente di Spark
Nell'interfaccia utente di Spark è possibile eseguire il drill-down dei processi Spark generati dall'applicazione avviata in precedenza.

1. Per avviare l'interfaccia utente di Spark, nella visualizzazione dell'applicazione, fare clic sul collegamento relativo a **URL di verifica**, come illustrato nella schermata precedente. È possibile visualizzare tutti i processi Spark avviati dall'applicazione in esecuzione in Jupyter Notebook.
   
    ![Visualizzare processi Spark](./media/hdinsight-apache-spark-job-debugging/view-spark-jobs.png)
2. Fare clic sulla scheda **Executors** per visualizzare le informazioni di elaborazione e archiviazione per ogni executor. È anche possibile recuperare lo stack di chiamate facendo clic sul collegamento **Thread Dump** .
   
    ![Visualizzare gli executor Spark](./media/hdinsight-apache-spark-job-debugging/view-spark-executors.png)
3. Fare clic sulla scheda **Stages** per visualizzare le fasi associate all'applicazione.
   
    ![Visualizzare le fasi di Spark](./media/hdinsight-apache-spark-job-debugging/view-spark-stages.png)
   
    Ogni fase può avere più attività per cui è possibile visualizzare le statistiche di esecuzione, come illustrato di seguito.
   
    ![Visualizzare le fasi di Spark](./media/hdinsight-apache-spark-job-debugging/view-spark-stages-details.png) 
4. Nella pagina dei dettagli relativi alle fasi è possibile avviare la visualizzazione DAG. Espandere il collegamento **DAG Visualization** nella parte superiore della pagina, come illustrato di seguito.
   
    ![Mostrare la visualizzazione DAG delle fasi di Spark](./media/hdinsight-apache-spark-job-debugging/view-spark-stages-dag-visualization.png)
   
    DAG o Direct Aclyic Graph rappresenta le diverse fasi dell'applicazione. Ogni casella blu nel grafico rappresenta un'operazione Spark richiamata dall'applicazione.
5. Nella pagina dei dettagli relativi alle fasi è anche possibile avviare la visualizzazione della sequenza temporale dell'applicazione. Espandere il collegamento **Event Timeline** nella parte superiore della pagina, come illustrato di seguito.
   
    ![Visualizzare e la sequenza temporale di eventi delle fasi di Spark](./media/hdinsight-apache-spark-job-debugging/view-spark-stages-event-timeline.png)
   
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
   
    ![Avviare Server cronologia Spark](./media/hdinsight-apache-spark-job-debugging/launch-spark-history-server.png)
   
   > [!TIP]
   > In alternativa, è anche possibile avviare l'interfaccia utente del Server cronologia Spark dall'interfaccia utente di Ambari. Per avviare l'interfaccia utente di Ambari, nel pannello del cluster fare clic su **Dashboard cluster** e quindi su **Dashboard cluster HDInsight**. Nell'interfaccia utente di Ambari fare clic su **Spark**, fare clic su **Collegamenti rapidi** e quindi sull'interfaccia utente di **Server cronologia Spark**.
   > 
   > 
2. Saranno elencate tutte le applicazioni completate. Fare clic su un ID applicazione per eseguire il drill-down di un'applicazione per altre informazioni.
   
    ![Avviare Server cronologia Spark](./media/hdinsight-apache-spark-job-debugging/view-completed-applications.png)

## <a name="see-also"></a>Vedere anche
* [Panoramica: Apache Spark su Azure HDInsight](hdinsight-apache-spark-overview.md)



