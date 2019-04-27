---
title: Connettersi ad Apache Hadoop tramite Data Lake Tools per Visual Studio - Azure HDInsight
description: Informazioni su come installare e usare Strumenti Data Lake per Visual Studio per connettersi a cluster Apache Hadoop in Azure HDInsight e quindi eseguire query Hive.
keywords: strumenti Hadoop, query Hive, Visual Studio, Hadoop in Visual Studio
author: hrasheed-msft
ms.author: hrasheed
ms.reviewer: jasonh
ms.service: hdinsight
ms.custom: hdinsightactive,hdiseo17may2017,seodec18
ms.topic: conceptual
ms.date: 02/21/2019
ms.openlocfilehash: 12d4a690ada0954015e515d616e3eb95ce1bfc3a
ms.sourcegitcommit: 61c8de2e95011c094af18fdf679d5efe5069197b
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 04/23/2019
ms.locfileid: "62124950"
---
# <a name="use-data-lake-tools-for-visual-studio-to-connect-to-azure-hdinsight-and-run-apache-hive-queries"></a>Usare Strumenti Data Lake per Visual Studio per connettersi ad Azure HDInsight ed eseguire query Apache Hive

Informazioni su come usare [Stream Analitica Tools per Visual Studio e Microsoft Azure Data Lake](https://www.microsoft.com/download/details.aspx?id=49504) (definita anche come strumenti Data Lake) per connettersi a Apache Hadoop cluster in [Azure HDInsight](../hdinsight-hadoop-introduction.md) e inviare query Hive.  

Per altre informazioni sull'uso di HDInsight, vedere [Introduzione a HDInsight](../hdinsight-hadoop-introduction.md) e [Introduzione all'uso di HDInsight](apache-hadoop-linux-tutorial-get-started.md).  

Per altre informazioni sulla connessione a un cluster Apache Storm, vedere [Sviluppare topologie C# per Apache Storm in HDInsight usando Visual Studio](../storm/apache-storm-develop-csharp-visual-studio-topology.md).

È possibile usare Strumenti Data Lake per Visual Studio per accedere ad Azure Data Lake Analytics e a HDInsight. Per informazioni su Strumenti Data Lake, vedere [Sviluppare script U-SQL tramite Strumenti Data Lake per Visual Studio](../../data-lake-analytics/data-lake-analytics-data-lake-tools-get-started.md).

## <a name="prerequisites"></a>Prerequisiti

Per completare questa esercitazione e usare Strumenti Data Lake per Visual Studio, sono necessari gli elementi seguenti:

* Disporre di un cluster HDInsight di Azure. Per creare un cluster Azure HDInsight, vedere [Iniziare a usare Apache Hadoop in Azure HDInsight](apache-hadoop-linux-tutorial-get-started.md). Per eseguire query Apache Hive interattive, è necessario un cluster [Interactive Query in HDInsight](../interactive-query/apache-interactive-query-get-started.md).  

* [Visual Studio](https://visualstudio.microsoft.com/downloads/) (2013 o versione successiva).  Il [Visual Studio Community edition](https://visualstudio.microsoft.com/vs/community/) è gratuito.  Vedere anche [installazione di Visual Studio 2017](https://docs.microsoft.com/visualstudio/install/install-visual-studio).

  > [!IMPORTANT]  
  > Strumenti data Lake non è più supportato per Visual Studio 2013. 

## <a name="install-data-lake-tools-for-visual-studio"></a>Installare Data Lake Tools per Visual Studio  
<a name="install-or-update-data-lake-tools-for-visual-studio"></a>

* Visual Studio 2017  
  Durante l'installazione, assicurarsi includere almeno i carichi di lavoro **sviluppo di Azure** oppure **elaborazione ed archiviazione dati**.  

  Per le installazioni esistenti, dalla barra dei menu, passare a **degli strumenti** > **Ottieni strumenti e funzionalità...**  per aprire l'installazione di Visual Studio.  Selezionare almeno i carichi di lavoro **sviluppo di Azure** oppure **elaborazione ed archiviazione dati**.

  ![Screenshot del programma di installazione di Visual Studio](./media/apache-hadoop-visual-studio-tools-get-started/vs2017_installation.png)

* Visual Studio 2013 and 2015  
  [Scaricare gli strumenti Data Lake](https://www.microsoft.com/download/details.aspx?id=49504). Scegliere la versione di Strumenti Data Lake che corrisponde alla versione di Visual Studio in uso.  

> [!NOTE]  
> Attualmente, è disponibile solo la versione in lingua inglese di Strumenti Data Lake per Visual Studio.

## <a name="update-data-lake-tools-for-visual-studio"></a>Aggiornare gli strumenti Data Lake per Visual Studio  

1. Aprire Visual Studio.

2. Dalla barra dei menu, passare a **degli strumenti** > **estensioni e aggiornamenti...** .

3. Dal **estensioni e aggiornamenti** finestra, espandere **aggiornamenti** a sinistra.

4. Se è disponibile, un aggiornamento **Azure Data Lake e strumenti di analisi Stream** verranno visualizzati nella finestra principale.  Selezionare **Aggiorna**.

> [!NOTE]  
> È possibile usare solo Strumenti Data Lake versione 2.3.0.0 o successiva per connettersi ai cluster Interactive Query ed eseguire query Hive interattive.

## <a name="connect-to-azure-subscriptions"></a>Connettersi alle sottoscrizioni di Azure
È possibile usare Strumenti Data Lake per Visual Studio per connettersi a cluster HDInsight ed eseguire alcune operazioni di gestione di base e query Hive.

> [!NOTE]  
> Per informazioni sulla connessione a un cluster Hadoop generico, vedere [Write and submit Hive queries by using Visual Studio](https://blogs.msdn.com/b/xiaoyong/archive/2015/05/04/how-to-write-and-submit-hive-queries-using-visual-studio.aspx) (Scrivere e inviare query Hive con Visual Studio).

Per connettersi alla sottoscrizione di Azure:

1. Aprire Visual Studio.

2. Dalla barra dei menu, passare a **View** > **Esplora Server**.

3. Da Esplora Server, fare doppio clic su **Azure**, selezionare **Connetti a sottoscrizione di Microsoft Azure...** e completare il processo di accesso.

4. Da Esplora Server, viene visualizzato un elenco di cluster HDInsight esistenti. Se non sono disponibili cluster, è possibile crearne uno usando il portale di Azure, Azure PowerShell o HDInsight SDK. Per altre informazioni, vedere [Creare cluster Hadoop in HDInsight](../hdinsight-hadoop-provision-linux-clusters.md).

   ![Screenshot dell'elenco di cluster di Strumenti Data Lake per Visual Studio in Esplora server](./media/apache-hadoop-visual-studio-tools-get-started/hdinsight.visual.studio.tools.server.explorer.png "Elenco di cluster di Strumenti Data Lake per Visual Studio in Esplora server")

5. Espandere un cluster HDInsight. Vengono visualizzati i **database Hive**, un account di archiviazione predefinito, gli account di archiviazione collegati e il **log del servizio Hadoop**. È possibile espandere ulteriormente le entità.

Dopo la connessione alla sottoscrizione di Azure, è possibile eseguire le attività seguenti.

Per connettersi al portale di Azure da Visual Studio:

1. Da Esplora Server, passare a **Azure** > **HDInsight** e selezionare il cluster.

2. Fare doppio clic su un cluster HDInsight e selezionare **Gestisci Cluster nel portale di Azure**.

Per porre domande e/o fornire commenti e suggerimenti da Visual Studio:

1. Da Esplora Server, passare a **Azure** > **HDInsight**.

2. Fare doppio clic su **HDInsight** e selezionare **MSDN Forum** per porre domande, o **inviare un Feedback** per inviare commenti e suggerimenti.

## <a name="explore-linked-resources"></a>Esplorare risorse collegate
Da Esplora server è possibile visualizzare l'account di archiviazione predefinito e qualsiasi account di archiviazione collegato. Se si espande l'account di archiviazione predefinito, è possibile visualizzare i contenitori presenti. L'account di archiviazione predefinito e il contenitore predefinito sono contrassegnati. Per visualizzare i contenuti dei contenitori, fare clic su uno dei contenitori con il pulsante destro del mouse.

![Screenshot dell'elenco di risorse collegate di Strumenti Data Lake per Visual Studio in Esplora server](./media/apache-hadoop-visual-studio-tools-get-started/hdinsight.visual.studio.tools.linked.resources.png "Elenco di risorse collegate")

Dopo aver aperto un contenitore, è possibile usare i pulsanti seguenti per caricare, eliminare e scaricare i BLOB:

![Screenshot delle operazioni sui BLOB per Strumenti Data Lake per Visual Studio in Esplora server](./media/apache-hadoop-visual-studio-tools-get-started/hdinsight.visual.studio.tools.blob.operations.png "Caricare, eliminare e scaricare BLOB in Esplora server")

## <a name="run-interactive-apache-hive-queries"></a>Eseguire query Interactive Apache Hive
[Apache Hive](https://hive.apache.org) è un'infrastruttura di data warehouse basata su Hadoop. Hive viene usato per riepilogo, le query e l'analisi dei dati. È possibile usare Strumenti Data Lake per Visual Studio per eseguire query Hive da Visual Studio. Per altre informazioni su Hive, vedere [Usare Apache Hive con HDInsight](hdinsight-use-hive.md).

[Interactive Query](../interactive-query/apache-interactive-query-get-started.md) usa [Hive in LLAP](https://cwiki.apache.org/confluence/display/Hive/LLAP) in Apache Hive 2.1. Interactive Query garantisce l'interattività alle query di tipo data warehouse complesse sui set di dati di grandi dimensioni archiviati. L'esecuzione di query Hive in Interactive Query è molto più veloce rispetto ai tradizionali processi batch Hive. Per altre informazioni, vedere eseguire Apache processi batch Hive.

> [!NOTE]  
> È possibile eseguire query Hive interattive solo quando ci si connette a un cluster [Interactive Query in HDInsight](../interactive-query/apache-interactive-query-get-started.md).

È anche possibile usare Strumenti Data Lake per Visual Studio per visualizzare il contenuto di un processo Hive. Strumenti Data Lake per Visual Studio raccoglie ed espone i log Yarn di determinati processi Hive.

Da Esplora Server, passare a **Azure** > **HDInsight** e selezionare il cluster.  Questo sarà il punto di partenza in Esplora Server per le sezioni a seguire.

### <a name="view-hivesampletable"></a>Visualizzazione hivesampletable
Tutti i cluster HDInsight includono una tabella Hive di esempio predefinita denominata `hivesampletable`.  

Dal cluster, passare a **database Hive** > **predefinita** > **hivesampletable**.

* Per visualizzare `hivesampletable` dello schema:  
Espandere **hivesampletable**.

* Per visualizzare `hivesampletable` dati:  
Fare doppio clic su **hivesampletable**e selezionare **Visualizza prime 100 righe**.  Questa operazione equivale a eseguire la query Hive seguente usando il driver ODBC di Hive:

   `SELECT * FROM hivesampletable LIMIT 100`

  È possibile personalizzare il numero delle righe.

  ![Screenshot di una query sullo schema Hive HDInsight in Visual Studio](./media/apache-hadoop-visual-studio-tools-get-started/hdinsight.visual.studio.tools.hive.schema.png "Risultati della query Hive")

### <a name="create-hive-tables"></a>Creare tabelle Hive
Per creare una tabella Hive, è possibile usare l'interfaccia utente grafica o query Hive. Per informazioni sull'uso di query Hive, vedere [Eseguire query Apache Hive](#run.queries).

1. Dal cluster, passare a **database Hive** > **predefinita**.

2. Fare doppio clic su **predefinite**e selezionare **Create Table**.

3. Configurare la tabella in base alle esigenze.  

4. Selezionare **Crea tabella** per inviare il processo per creare la nuova tabella Hive.

    ![Screenshot della finestra Crea tabella di Strumenti di Visual Studio in HDInsight](./media/apache-hadoop-visual-studio-tools-get-started/hdinsight.visual.studio.tools.create.hive.table.png "Creare una tabella Hive")

### <a name="run.queries"></a>Creare ed eseguire query Hive
Per la creazione e l'esecuzione di query Hive sono disponibili due opzioni:

* Creare query ad hoc
* Creare un'applicazione Hive

Per creare ed eseguire query ad hoc:

1. Fare doppio clic su cluster in cui si desidera eseguire la query e selezionare **Scrivi una Query Hive**.  

2. Immettere le query Hive.  

    L'editor Hive supporta IntelliSense. Strumenti Data Lake per Visual Studio supporta il caricamento di metadati remoti quando si modifica lo script Hive. Ad esempio, se si digita `SELECT * FROM`, IntelliSense Elenca tutti i nomi di tabella suggeriti. Quando si specifica un nome di tabella, IntelliSense elenca i nomi delle colonne. Gli strumenti supportano la maggior parte delle funzioni definite dall'utente predefinite, delle sottoquery e delle istruzioni DML Hive.

    ![Screenshot 1 di un esempio di IntelliSense in Strumenti di Visual Studio in HDInsight](./media/apache-hadoop-visual-studio-tools-get-started/hdinsight.visual.studio.tools.intellisense.table.names.png "IntelliSense in U-SQL")

    ![Screenshot 2 di un esempio di IntelliSense in Strumenti di Visual Studio in HDInsight](./media/apache-hadoop-visual-studio-tools-get-started/hdinsight.visual.studio.tools.intellisense.column.names.png "IntelliSense in U-SQL")

   > [!NOTE]  
   > IntelliSense suggerisce solo i metadati del cluster selezionato nella barra degli strumenti HDInsight.

3. Scegliere la modalità di esecuzione:

    * **Interattivo**  

      Assicurarsi **Interactive** sia selezionata e quindi selezionare **Execute**.

      ![Screenshot della query ed eseguire](./media/apache-hadoop-visual-studio-tools-get-started/execute.png)  

    * **Batch**  

      Assicurarsi **Batch** sia selezionata e quindi selezionare **Submit**.  Se si seleziona l'opzione di invio avanzato, configurare **il nome del processo**, **argomenti**, **configurazioni aggiuntive**, e **stato Directory**per lo script.

      ![Screenshot di Progettazione query e batch](./media/apache-hadoop-visual-studio-tools-get-started/batch.png)  

      ![Screenshot di una query Hive Hadoop HDInsight](./media/apache-hadoop-visual-studio-tools-get-started/hdinsight.visual.studio.tools.submit.jobs.advanced.png "Inviare query")

      > [!NOTE]  
      > Non è possibile inviare batch ai cluster Interactive Query.  È necessario usare la modalità interattiva.

Per creare ed eseguire una soluzione Hive:

1. Dalla barra dei menu, passare a **File** > **New** > **progetto...** .

2. Nel riquadro a sinistra, passare a **Installed** > **Azure Data Lake** > **HIVE (HDInsight)**.  

3. Nel riquadro centrale selezionare **Hive Application** (Applicazione Hive). Immettere le proprietà e quindi selezionare **OK**.

    ![Screenshot di un nuovo progetto Hive in Strumenti di Visual Studio per HDInsight](./media/apache-hadoop-visual-studio-tools-get-started/hdinsight.visual.studio.tools.new.hive.project.png "Creare applicazioni Hive da Visual Studio")

4. In **Esplora soluzioni** fare doppio clic su **Script.hql** per aprire lo script.

### <a name="view-job-summary-and-output"></a>Visualizza il riepilogo processo e output

Il riepilogo del processo varia leggermente tra **Batch** e **Interactive** modalità.

![Riepilogo processi](./media/apache-hadoop-visual-studio-tools-get-started/jobSummary.png "riepilogo processo Hive")

Usare la **Refresh** pulsante per aggiornare lo stato fino a quando assume lo stato del processo **Finished**.  

* Per i dettagli del processo da **Batch** modalità, selezionare i collegamenti nella parte inferiore per visualizzare **Query del processo**, **Output del processo**, **log processo**, o **Log di yarn**.

* Per i dettagli del processo da **Interactive** modalità, vedere schede **Output** e **Output di HiveServer2**.

  ![Dettagli processo](./media/apache-hadoop-visual-studio-tools-get-started/tabs.png "i dettagli dei processi Hive")

### <a name="view-job-graph"></a>Grafico del processo Visualizza

Attualmente, i grafici del processo vengono visualizzati solo per i processi Hive che usano Tez come motore di esecuzione.  Per informazioni su come abilitare Tez, vedere [Usare Apache Hive in HDInsight](hdinsight-use-hive.md).  Vedere anche [usare Apache Tez invece di MapReduce](../hdinsight-hadoop-optimize-hive-query.md#use-apache-tez-instead-of-map-reduce).  

Per visualizzare tutti gli operatori all'interno del vertice, fare doppio clic sui vertici del grafico del processo. È anche possibile puntare a un operatore specifico per visualizzare altri dettagli sull'operatore.

Grafico del processo potrebbe non comparire anche se viene specificato Tez come motore di esecuzione se non viene avviata alcuna applicazione Tez.  Ciò può verificarsi perché il processo non contiene istruzioni DML o le istruzioni DML possono restituire senza avviare un'applicazione Tez. Ad esempio, `SELECT * FROM table1` non avvierà l'applicazione Tez.

![Grafico del processo](./media/apache-hadoop-visual-studio-tools-get-started/hdinsight.fast.path.hive.execution.png "riepilogo processo Hive")


### <a name="task-execution-detail"></a>Dettagli esecuzione attività

Grafico del processo, è possibile selezionare **Dettagli esecuzione attività** per ottenere informazioni per i processi Hive strutturate e visive. Sono disponibili anche altri dettagli sui processi. Se si verificano problemi di prestazioni, è possibile usare la visualizzazione per ottenere altri dettagli sul problema. È ad esempio possibile ottenere informazioni sul funzionamento di ogni attività e informazioni dettagliate su ogni attività (lettura/scrittura dati, ora di pianificazione/inizio/fine e così via). Usare le informazioni per ottimizzare le configurazioni dei processi o l'architettura di sistema in base alle informazioni visualizzate.

![Screenshot della finestra di visualizzazione dell'esecuzione dell'attività in Strumenti di Visual Studio di Data Lake](./media/apache-hadoop-visual-studio-tools-get-started/hdinsight.visual.studio.tools.task.execution.view.png "Visualizzazione dell'esecuzione dell'attività")


### <a name="view-hive-jobs"></a>Visualizzare processi Hive
È possibile visualizzare query di processo, output di processo, log di processo e log Yarn per i processi Hive.

Nella versione più recente degli strumenti è possibile visualizzare i contenuti dei processi Hive raccogliendo ed esponendo i log Yarn. Un log Yarn consente di analizzare eventuali problemi di prestazioni. Per altre informazioni su come HDInsight raccoglie i log Yarn, vedere [Accedere ai log dell'applicazione HDInsight a livello di codice](../hdinsight-hadoop-access-yarn-app-logs-linux.md).

Per visualizzare processi Hive:

1. Fare doppio clic su un cluster HDInsight e selezionare **Visualizza processi**. Viene visualizzato l'elenco di processi Hive eseguiti nel cluster.  

2. Selezionare un processo. Nella finestra **Hive Job Summary** (Riepilogo processo Hive) selezionare uno dei collegamenti seguenti:
   - **Query processo**
   - **Output processo**
   - **Log processo**  
   - **Log Yarn**

     ![Screenshot della finestra di visualizzazione dei processi Hive in Strumenti di Visual Studio di HDInsight](./media/apache-hadoop-visual-studio-tools-get-started/hdinsight.visual.studio.tools.view.hive.jobs.png "Visualizzare processi Hive")


## <a name="run-apache-pig-scripts"></a>Eseguire script Apache Pig

1. Dalla barra dei menu, passare a **File** > **New** > **progetto...** .

2. Nel riquadro a sinistra, passare a **Installed** > **Azure Data Lake** > **Pig (HDInsight)**.  

3. Nel riquadro centrale, selezionare **Pig Application**. Immettere le proprietà e quindi selezionare **OK**.

4. Nelle **Esplora soluzioni**, fare doppio clic su **Pig** per aprire lo script.

## <a name="feedback-and-known-issues"></a>Commenti, suggerimenti e problemi noti
* È stato risolto un problema a causa del quale i risultati che iniziano con valori Null non vengono visualizzati. Se questo problema impedisce di lavorare, contattare il team di supporto.
* Lo script HQL creato da Visual Studio viene codificato, a seconda dell'impostazione dell'area locale dell'utente. Lo script non viene eseguito correttamente se lo si carica in un cluster come file binario.

## <a name="next-steps"></a>Passaggi successivi
In questo articolo è stato illustrato come usare il pacchetto Strumenti Data Lake per Visual Studio per connettersi ai cluster HDInsight da Visual Studio. È stato anche illustrato come eseguire una query Hive. Per altre informazioni, vedere questi articoli:

* [Eseguire query Apache Hive usando gli strumenti Data Lake per Visual Studio](apache-hadoop-use-hive-visual-studio.md)
* [Usare Hive di Hadoop in HDInsight](hdinsight-use-hive.md)
* [Introduzione all'uso di Apache Hadoop in HDInsight](apache-hadoop-linux-tutorial-get-started.md)
* [Inviare processi Apache Hadoop in HDInsight](submit-apache-hadoop-jobs-programmatically.md)
* [Analizzare i dati di Twitter con Apache Hadoop in HDInsight](../hdinsight-analyze-twitter-data-linux.md)

