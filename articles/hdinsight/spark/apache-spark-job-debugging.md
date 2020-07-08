---
title: Eseguire il debug di processi Apache Spark in esecuzione in Azure HDInsight
description: Usare l'interfaccia utente di YARN, l'interfaccia utente di Spark e il server della cronologia di Spark per tenere traccia ed eseguire il debug di processi in esecuzione in un cluster Spark in Azure HDInsight
author: hrasheed-msft
ms.author: hrasheed
ms.reviewer: jasonh
ms.service: hdinsight
ms.topic: how-to
ms.custom: hdinsightactive,seoapr2020
ms.date: 04/23/2020
ms.openlocfilehash: 817b8976f5d014d990945816c2df1c7e8ed729fb
ms.sourcegitcommit: 124f7f699b6a43314e63af0101cd788db995d1cb
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 07/08/2020
ms.locfileid: "86084886"
---
# <a name="debug-apache-spark-jobs-running-on-azure-hdinsight"></a>Eseguire il debug di processi Apache Spark in esecuzione in Azure HDInsight

Questo articolo illustra come tenere traccia ed eseguire il debug di Apache Spark processi in esecuzione nei cluster HDInsight. Eseguire il debug usando l'interfaccia utente di Apache Hadoop YARN, l'interfaccia utente di Spark e il server di cronologia Spark. Si avvierà un processo Spark usando un notebook disponibile nel cluster Spark, **Machine Learning: analisi predittiva dei dati del controllo degli alimenti tramite MLlib**. Usare la procedura seguente per tenere traccia di un'applicazione inviata usando anche altri approcci, ad esempio, **Spark-Submit**.

Se non si ha una sottoscrizione di Azure, creare un [account gratuito](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) prima di iniziare.

## <a name="prerequisites"></a>Prerequisiti

* Un cluster Apache Spark in HDInsight. Per istruzioni, vedere l'articolo dedicato alla [creazione di cluster Apache Spark in Azure HDInsight](apache-spark-jupyter-spark-sql.md).

* Si sarà già avviato il notebook **[Machine Learning: analisi predittiva dei dati del controllo degli alimenti tramite MLlib](apache-spark-machine-learning-mllib-ipython.md)**. Per istruzioni su come eseguire questo notebook, seguire il collegamento.  

## <a name="track-an-application-in-the-yarn-ui"></a>Tenere traccia di un'applicazione nell'interfaccia utente di YARN

1. Avviare l'interfaccia utente di YARN Selezionare **Yarn** in **Dashboard cluster**.

    ![portale di Azure avviare l'interfaccia utente di YARN](./media/apache-spark-job-debugging/launch-apache-yarn-ui.png)

   > [!TIP]  
   > In alternativa, è anche possibile avviare l'interfaccia utente di YARN dall'interfaccia utente di Ambari. Per avviare l'interfaccia utente di Ambariri, selezionare **Ambari Home** in **Dashboard cluster**. Dall'interfaccia utente di Ambari passare a **YARN**  >  **collegamenti rapidi** Yarn > **interfaccia utente**Active Gestione risorse > gestione risorse.

2. Poiché il processo Spark è stato avviato con i notebook di Jupyter, l'applicazione ha il nome **remotesparkmagics** (il nome per tutte le applicazioni avviate dai notebook). Per ottenere altre informazioni sul processo, selezionare l'ID applicazione con il nome dell'applicazione. Questa azione avvia la visualizzazione dell'applicazione.

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

    Questa immagine Mostra gli eventi Spark sotto forma di sequenza temporale. La visualizzazione della sequenza temporale è disponibile in tre livelli, tra processi, all'interno di un processo e all'interno di una fase. L'immagine precedente mostra la visualizzazione della sequenza temporale per una determinata fase.

   > [!TIP]  
   > Se si seleziona la casella di controllo **Enable zooming** , è possibile scorrere a sinistra e destra nella visualizzazione della sequenza temporale.

6. Altre schede nell'interfaccia utente di Spark forniscono anche informazioni utili sull'istanza di Spark.

   * Scheda archiviazione: se l'applicazione crea un RDD, è possibile trovare le informazioni nella scheda archiviazione.
   * Scheda Environment (ambiente): questa scheda fornisce informazioni utili sull'istanza di Spark, ad esempio:
     * Versione di Scala
     * Directory dei log eventi associata al cluster
     * Numero di core executor per l'applicazione

## <a name="find-information-about-completed-jobs-using-the-spark-history-server"></a>Trovare informazioni sui processi completati tramite Server cronologia Spark

Una volta completato un processo, le informazioni corrispondenti vengono salvate in modo permanente nel Server cronologia Spark.

1. Per avviare il server cronologia Spark, nella pagina **Panoramica** selezionare **Server cronologia Spark** in **Dashboard cluster**.

    ![portale di Azure avviare il server cronologia Spark](./media/apache-spark-job-debugging/launch-spark-history-server.png "Avviare la cronologia Spark Server1")

   > [!TIP]  
   > In alternativa, è anche possibile avviare l'interfaccia utente del Server cronologia Spark dall'interfaccia utente di Ambari. Per avviare l'interfaccia utente di Ambari, nel pannello Panoramica selezionare **Ambari Home** in **Dashboard cluster**. Dall'interfaccia utente di Ambari passare a **Spark2**  >  **Quick Links**  >  **Spark2 Cronologia server UI**.

2. Vengono elencate tutte le applicazioni completate. Per ulteriori informazioni, selezionare un ID applicazione per eseguire il drill-down in un'applicazione.

    ![Applicazioni complete del server cronologia Spark](./media/apache-spark-job-debugging/view-completed-applications.png "Avviare la cronologia Spark Server2")

## <a name="see-also"></a>Vedi anche

* [Gestire le risorse del cluster Apache Spark in Azure HDInsight](apache-spark-resource-manager.md)
* [Eseguire il debug dei processi Apache Spark tramite il Server cronologia Spark esteso](apache-azure-spark-history-server.md)
* [Eseguire il debug di applicazioni Apache Spark con Azure Toolkit for IntelliJ tramite SSH](apache-spark-intellij-tool-debug-remotely-through-ssh.md)
