---
title: Apache Hadoop & Visual Studio Data Lake Tools - Azure HDInsight
description: Informazioni su come installare e usare Data Lake Tools per Visual Studio. Usare lo strumento per connettersi ai cluster Apache Hadoop in Azure HDInsight e quindi eseguire query Hive.Use tool to connect to Apache Hadoop clusters in Azure HDInsight, and then run Hive queries.
keywords: strumenti Hadoop, query Hive, Visual Studio, Hadoop in Visual Studio
author: hrasheed-msft
ms.author: hrasheed
ms.reviewer: jasonh
ms.service: hdinsight
ms.custom: hdinsightactive,hdiseo17may2017,seodec18
ms.topic: conceptual
ms.date: 04/14/2020
ms.openlocfilehash: 7504826f267d717f30c5e88621578412c744e5f9
ms.sourcegitcommit: ea006cd8e62888271b2601d5ed4ec78fb40e8427
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 04/14/2020
ms.locfileid: "81383515"
---
# <a name="use-data-lake-tools-for-visual-studio-to-connect-to-azure-hdinsight-and-run-apache-hive-queries"></a>Usare Strumenti Data Lake per Visual Studio per connettersi ad Azure HDInsight ed eseguire query Apache Hive

Informazioni su come usare Microsoft Azure Data Lake e Strumenti di analisi di flusso per Visual Studio (Strumenti lago). Usare lo strumento per connettersi [ai cluster Apache Hadoop in Azure HDInsight](apache-hadoop-introduction.md) e inviare query Hive.Use the tool to connect to Apache Hadoop clusters in Azure HDInsight and submit Hive queries.  

Per altre informazioni sull'uso di HDInsight, vedere Introduzione a HDInsight.For more information about using [HDInsight,](apache-hadoop-linux-tutorial-get-started.md)see Get started with HDInsight .  

Per ulteriori informazioni sulla connessione a Apache Storm, vedere [Sviluppare topologie C, per Apache Storm utilizzando gli strumenti Data Lake](../storm/apache-storm-develop-csharp-visual-studio-topology.md).

È possibile usare Strumenti Data Lake per Visual Studio per accedere ad Azure Data Lake Analytics e a HDInsight. Per informazioni su Strumenti Data Lake, vedere [Sviluppare script U-SQL tramite Strumenti Data Lake per Visual Studio](../../data-lake-analytics/data-lake-analytics-data-lake-tools-get-started.md).

## <a name="prerequisites"></a>Prerequisiti

Per completare questo articolo e usare Data Lake Tools per Visual Studio, sono necessari gli elementi seguenti:To complete this article and use Data Lake Tools for Visual Studio, you need the following items:

* Disporre di un cluster HDInsight di Azure. Per creare un cluster Azure HDInsight, vedere [Iniziare a usare Apache Hadoop in Azure HDInsight](apache-hadoop-linux-tutorial-get-started.md). Per eseguire query Apache Hive interattive, è necessario un cluster [Interactive Query in HDInsight](../interactive-query/apache-interactive-query-get-started.md).  

* [Visual Studio](https://visualstudio.microsoft.com/downloads/). [L'edizione Community di Visual Studio](https://visualstudio.microsoft.com/vs/community/) è gratuita. Le istruzioni illustrate di seguito sono per [Visual Studio 2019](https://visualstudio.microsoft.com/downloads/).

## <a name="install-data-lake-tools-for-visual-studio"></a>Installare Data Lake Tools per Visual Studio  

Seguire le istruzioni appropriate per installare Data Lake Tools per la versione di Visual Studio in uso:

* Per Visual Studio 2017 o Visual Studio 2019:

    Durante l'installazione di Visual Studio, assicurarsi di includere il carico di lavoro di sviluppo di **Azure** o il carico di lavoro di **archiviazione e elaborazione dei dati.**  

    Per le installazioni di Visual Studio esistenti, passare alla barra dei menu dell'IDE e selezionare **Strumenti** > Get Tools and Features per aprire il programma di installazione di Visual Studio.For existing Visual Studio installations, go to the IDE menu bar, and select Tools**Get Tools and Features** to open Visual Studio Installer. Nella scheda **Carichi di lavoro** selezionare almeno il carico di lavoro di sviluppo di **Azure** (in Web **& Cloud**). In alternativa, selezionare il carico di lavoro **Archiviazione ed elaborazione dati** (in Altri set di **strumenti**).

  ![Selezione del carico di lavoro, programma di installazione di Visual StudioWorkload selection, Visual Studio Installer](./media/apache-hadoop-visual-studio-tools-get-started/vs-installation.png)

* Per Visual Studio 2015:

    [Scarica Data Lake Tools](https://www.microsoft.com/download/details.aspx?id=49504). Scegliere la versione di Strumenti Data Lake che corrisponde alla versione di Visual Studio in uso.

## <a name="update-data-lake-tools-for-visual-studio"></a>Aggiornare Data Lake Tools per Visual StudioUpdate Data Lake Tools for Visual Studio  

Assicurarsi quindi di aggiornare Data Lake Tools alla versione più recente.

1. Aprire Visual Studio.

2. Nella finestra **Start** selezionare **Continua senza codice**.

3. Nella barra dei menu dell'IDE di Visual Studio scegliere **Estensioni** > **per la gestione delle estensioni**.

4. Nella finestra di dialogo **Gestisci estensioni** espandere il nodo **Aggiornamenti.**

5. Se l'elenco degli aggiornamenti disponibili include **Azure Data Lake e Strumenti analitici flusso**, selezionarlo. Quindi selezionare il pulsante **Aggiorna.** Dopo aver visualizzato e scompare la finestra di dialogo **Scarica e installa,** Visual Studio aggiunge l'estensione **Azure Data Lake e Stream Analytic Tools** alla pianificazione dell'aggiornamento.

6. Chiudere tutte le finestre di Visual Studio. Viene visualizzata la finestra di dialogo **Programma di installazione VSIX.**

7. Selezionare **Licenza** per leggere le condizioni di licenza, quindi selezionare **Chiudi** per tornare alla finestra di dialogo Programma di installazione **VSIX.**

8. Selezionare **Modifica**. Viene avviata l'installazione dell'aggiornamento dell'estensione. Dopo un po ', la finestra di dialogo cambia per mostrare che è stata completata apportando modifiche. Selezionare **Chiudi**, quindi riavviare Visual Studio per completare l'installazione.

> [!NOTE]  
> È possibile usare solo Strumenti Data Lake versione 2.3.0.0 o successiva per connettersi ai cluster Interactive Query ed eseguire query Hive interattive.

## <a name="connect-to-azure-subscriptions"></a>Connettersi alle sottoscrizioni di Azure

È possibile usare Data Lake Tools per Visual Studio per connettersi ai cluster HDInsight, eseguire alcune operazioni di gestione di base ed eseguire query Hive.You can use Data Lake Tools for Visual Studio to connect to your HDInsight clusters, do some basic management operations, and run Hive queries.

> [!NOTE]  
> Per informazioni sulla connessione a un cluster Hadoop generico, vedere [Come scrivere e inviare query Hive utilizzando Visual Studio](https://blogs.msdn.microsoft.com/xiaoyong/2015/05/04/how-to-write-and-submit-hive-queries-using-visual-studio/).

### <a name="connect-to-an-azure-subscription"></a>Connettersi a una sottoscrizione di Azure

Per connettersi alla sottoscrizione di Azure:

1. Aprire Visual Studio.

2. Nella finestra **Start** selezionare **Continua senza codice**.

3. Nella barra dei menu dell'IDE scegliere **Visualizza** > **Esplora server**.

4. In **Esplora server**fare clic con il pulsante destro del mouse su **Azure**, scegliere Connetti alla sottoscrizione di **Microsoft Azure**e completare il processo di autenticazione. In **Esplora server**espandere **Azure** > **HDInsight** per visualizzare un elenco di cluster HDInsight esistenti.

5. Se non si dispone di cluster, crearne uno usando il portale di Azure, Azure PowerShell o HDInsight SDK. Per ulteriori informazioni, vedere [Configurare i cluster in HDInsight.](../hdinsight-hadoop-provision-linux-clusters.md)

   ![Elenco cluster HDInsight, Esplora server, Visual Studio](./media/apache-hadoop-visual-studio-tools-get-started/hdinsight-visual-studio-tools-server-explorer.png)

6. Espandere un cluster HDInsight. Il cluster contiene nodi per **Hive Databases**. Inoltre, un account di archiviazione predefinito, eventuali account di archiviazione collegati aggiuntivi e **Registro del servizio Hadoop**. È possibile espandere ulteriormente le entità.

Dopo la connessione alla sottoscrizione di Azure, è possibile eseguire le attività seguenti.

### <a name="connect-to-azure-from-visual-studio"></a>Connettersi ad Azure da Visual StudioConnect to Azure from Visual Studio

Per connettersi al portale di Azure da Visual Studio:

1. In **Esplora server**espandere **Azure** > **HDInsight** e selezionare il cluster.

2. Fare clic con il pulsante destro del mouse su un cluster HDInsight e selezionare Gestisci cluster nel portale di Azure.Right-click an HDInsight cluster, and select **Manage Cluster in Azure portal**.

### <a name="offer-questions-and-feedback-from-visual-studio"></a>Offrire domande e commenti e suggerimenti da Visual StudioOffer questions and feedback from Visual Studio

Per porre domande e fornire commenti e suggerimenti da Visual Studio:To porre domande e, o fornire feedback da Visual Studio:

1. In Esplora server scegliere **Azure** > **HDInsight**.

2. Fare clic con il pulsante destro del mouse su **HDInsight** e selezionare **Forum MSDN** per porre domande o **inviare commenti e suggerimenti** per inviare commenti e suggerimenti.

## <a name="link-to-or-edit-a-cluster"></a>Creare un collegamento o modificare un cluster

> [!NOTE]
> Attualmente l'unico tipo di cluster HDInsight a cui è possibile eseguire il collegamento è un tipo Hive.Currently the only type of HDInsight cluster you can link to is a Hive type.

Per collegare un cluster HDInsight:To link an HDInsight cluster:

1. Fare clic con il pulsante destro del mouse su **HDInsight**, quindi selezionare **Collega un cluster HDInsight** per visualizzare la finestra di dialogo **Collega un cluster HDInsight** .

2. Immettere un URL `https://CLUSTERNAME.azurehdinsight.net`di **connessione** nel modulo . Il **nome del cluster** viene compilato automaticamente con la parte del nome del cluster dell'URL quando si passa a un altro campo. Immettere quindi un **Nome utente** e una **Password**e selezionare **Avanti**.

    ![Collegare un cluster, HDInsight, Visual StudioLink a cluster, HDInsight, Visual Studio](./media/apache-hadoop-visual-studio-tools-get-started/hdinsight-visual-studio-tools-link-cluster-dialog.png)

3. Fare clic su **Fine**. Se il collegamento del cluster ha esito positivo, il cluster viene quindi elencato nel nodo **HDInsight.If** the cluster linking is successful, the cluster is then listed under the HDInsight node.

Per aggiornare un cluster collegato, fare clic con il pulsante destro del mouse sul cluster e scegliere **Modifica**. È quindi possibile aggiornare le informazioni sul cluster.

![Modificare un cluster collegato, HDInsight, Visual Studio](./media/apache-hadoop-visual-studio-tools-get-started/hdinsight-visual-studio-tools-link-cluster-update.png)

## <a name="explore-linked-resources"></a>Esplorare risorse collegate

Da Esplora server è possibile visualizzare l'account di archiviazione predefinito e qualsiasi account di archiviazione collegato. Se si espande l'account di archiviazione predefinito, è possibile visualizzare i contenitori presenti. L'account di archiviazione predefinito e il contenitore predefinito sono contrassegnati.

![Risorse collegate di Data Lake Tools per Visual Studio in Esplora server](./media/apache-hadoop-visual-studio-tools-get-started/hdinsight-visual-studio-tools-linked-resources.png)

Fare clic con il pulsante destro del mouse su un contenitore e selezionare **Visualizza contenitore** per visualizzare il contenuto del contenitore. Dopo aver aperto un contenitore, è possibile utilizzare i pulsanti della barra degli strumenti per **aggiornare** l'elenco del contenuto, **Carica BLOB**, Eliminare i **BLOB selezionati,** **aprire IL BLOB**e scaricare i BLOB (**Salva con**nome ) selezionati.

![Operazioni di elenco e BLOB dei contenitori, cluster HDInsight, Visual StudioContainer list and blob operations, HDInsight cluster, Visual Studio](./media/apache-hadoop-visual-studio-tools-get-started/hdinsight-visual-studio-tools-blob-operations.png)

## <a name="run-interactive-apache-hive-queries"></a>Eseguire query Interactive Apache Hive

[Apache Hive](https://hive.apache.org) è un'infrastruttura di data warehouse basata su Hadoop. Hive viene usato per riepilogo, le query e l'analisi dei dati. È possibile usare Strumenti Data Lake per Visual Studio per eseguire query Hive da Visual Studio. Per altre informazioni su Hive, vedere [Che cos'è Apache Hive e HiveQL in Azure HDInsight?](hdinsight-use-hive.md).

[Query interattiva in Azure HDInsight](../interactive-query/apache-interactive-query-get-started.md) usa Hive su LLAP in Apache Hive 2.1.Interactive Query in Azure HDInsight uses [Hive on LLAP](https://cwiki.apache.org/confluence/display/Hive/LLAP) in Apache Hive 2.1. Query interattiva porta l'interattività in query complesse in stile data warehouse su set di dati archiviati di grandi dimensioni. L'esecuzione di query Hive su Interactive Query è molto più veloce rispetto ai processi batch Hive tradizionali. 

> [!NOTE]  
> È possibile eseguire query Hive interattive solo quando ci si connette a un cluster [Interactive Query in HDInsight](../interactive-query/apache-interactive-query-get-started.md).

È anche possibile usare Data Lake Tools per Visual Studio per vedere cosa c'è all'interno di un processo Hive.You can also use Data Lake Tools for Visual Studio to see what's inside a Hive job. Strumenti Data Lake per Visual Studio raccoglie ed espone i log Yarn di determinati processi Hive.

In **Esplora server**scegliere **Azure** > **HDInsight** e selezionare il cluster.  Questo nodo è il punto di partenza in **Esplora server** per le sezioni da seguire.

### <a name="view-hivesampletable"></a>Visualizzare hivesampletable

Tutti i cluster HDInsight dispongono di `hivesampletable`una tabella Hive di esempio predefinita denominata .  

Dal cluster scegliere **Hive Databases** > **default** > **hivesampletable**.

* Per visualizzare `hivesampletable` lo schema:

    Espandere **hivesampletable**. Vengono visualizzati i `hivesampletable` nomi e i tipi di dati delle colonne.

* Per visualizzare `hivesampletable` i dati:

    Fare clic con il pulsante destro del mouse su **hivesampletable**e selezionare **Visualizza prime 100 righe**. L'elenco di 100 risultati viene visualizzato nella finestra **Tabella Hive: hivesampletable.** Questa azione equivale all'esecuzione della query Hive seguente utilizzando il driver ODBC Hive:

    `SELECT * FROM hivesampletable LIMIT 100`

    È possibile personalizzare il conteggio delle righe modificando **Numero di righe**; è possibile scegliere 50, 100, 200 o 1000 righe dall'elenco a discesa.

### <a name="create-hive-tables"></a>Creare tabelle Hive

Per creare una tabella Hive, è possibile usare l'interfaccia utente grafica o query Hive. Per informazioni sull'uso delle query Hive, vedere [Creare ed eseguire query Hive.](#create-and-run-hive-queries)

1. Dal cluster scegliere **Hive Databases** > **default**.

2. Fare clic con il pulsante destro del mouse su **Predefinito**e **scegliere Crea tabella**.

3. Configurare la tabella.

4. Selezionare il pulsante Crea tabella per inviare il processo, che crea la nuova tabella Hive.Select the **Create Table** button to submit the job, which creates the new Hive table.

    ![Finestra Crea tabella, Hive, cluster HDInsight, Visual StudioCreate Table window, Hive, HDInsight cluster, Visual Studio](./media/apache-hadoop-visual-studio-tools-get-started/hdinsight-visual-studio-tools-create-hive-table.png)

### <a name="create-and-run-hive-queries"></a>Creare ed eseguire query Hive

Per la creazione e l'esecuzione di query Hive sono disponibili due opzioni:

* Creare query ad hoc
* Creare un'applicazione Hive

#### <a name="create-an-ad-hoc-query"></a>Creare una query ad hoc

Per creare ed eseguire una query ad hoc:

1. Fare clic con il pulsante destro del mouse sul cluster in cui si desidera eseguire la query e scegliere **Scrivi una query Hive**.  

2. Immettere una query Hive.Enter a Hive query.

    L'editor Hive supporta IntelliSense. Strumenti Data Lake per Visual Studio supporta il caricamento di metadati remoti quando si modifica lo script Hive. Se ad esempio `SELECT * FROM`si digita , IntelliSense elenca tutti i nomi di tabella suggeriti. Quando si specifica un nome di tabella, IntelliSense elenca i nomi delle colonne. Gli strumenti supportano la maggior parte delle funzioni definite dall'utente predefinite, delle sottoquery e delle istruzioni DML Hive.

    ![Esempio di IntelliSense 1, query ad hoc Hive, cluster HDInsight, Visual Studio](./media/apache-hadoop-visual-studio-tools-get-started/hdinsight-visual-studio-tools-intellisense-table-names.png)

    ![Esempio di IntelliSense 2, query ad hoc Hive, cluster HDInsight, Visual Studio](./media/apache-hadoop-visual-studio-tools-get-started/hdinsight-visual-studio-tools-intellisense-column-names.png)

    > [!NOTE]  
    > IntelliSense suggerisce solo i metadati del cluster selezionato nella barra degli strumenti HDInsight.

    Di seguito è riportata una query di esempio che è possibile usare:Here is a sample query that you can use:

    ```sql
    SELECT devicemodel, COUNT(devicemodel) AS deviceCount
    FROM hivesampletable
    GROUP BY devicemodel
    ORDER BY devicemodel
    ```

3. Scegliere la modalità di esecuzione:

    * **Interattiva**  

        Nel primo elenco a discesa scegliere **Interattivo**, quindi **selezionare Esegui**.

        ![Modalità interattiva, query ad hoc Hive, cluster HDInsight, Visual Studio](./media/apache-hadoop-visual-studio-tools-get-started/hdinsight-query-execute.png)  

    * **Batch**  

        Nel primo elenco a discesa scegliere **Batch**e quindi **Selezionare Invia**. In alternativa, selezionare l'icona a discesa accanto a **Invia** e scegliere **Avanzate**.

        ![Modalità batch, query ad hoc Hive, cluster HDInsight, Visual Studio](./media/apache-hadoop-visual-studio-tools-get-started/hdinsight-query-batch.png)

        Se si seleziona l'opzione di invio avanzato, viene visualizzata la finestra di dialogo **Invia script.** Configurare **Nome processo**, **Argomenti**, **Configurazioni aggiuntive**e Directory di **stato** per lo script.

        ![Finestra di dialogo Invia script, Query ad hoc Hive, cluster HDInsight, Visual Studio](./media/apache-hadoop-visual-studio-tools-get-started/hdinsight-visual-studio-tools-submit-jobs-advanced.png)

      > [!NOTE]  
      > Non è possibile inviare batch ai cluster di query interattive.  È necessario utilizzare la modalità interattiva.

#### <a name="create-a-hive-application"></a>Creare un'applicazione Hive

Per creare ed eseguire una soluzione Hive:

1. Dalla barra dei menu, scegliere **File** > **Nuovo** > **progetto**.

2. Nella finestra **Crea un nuovo progetto** selezionare la casella di ricerca e **digitare Hive**. Quindi scegliere **Hive Application** e selezionare **Next**.

3. Nella finestra **Configura nuovo progetto** immettere un nome di **progetto**, selezionare o creare il **percorso**del progetto , quindi scegliere **Crea**.

    ![Nuova applicazione Hive, configurare la finestra del nuovo progetto, HDInsight Visual Studio](./media/apache-hadoop-visual-studio-tools-get-started/hdinsight-visual-studio-tools-new-hive-project.png)

4. In **Esplora soluzioni** fare doppio clic su **Script.hql** per aprire lo script.

### <a name="view-job-summary-and-output"></a>Visualizzare il riepilogo e l'output dei processi

Il riepilogo del lavoro varia leggermente tra la modalità **Batch** e la modalità **interattiva.**

![Hive job summary windows, batch e modalità interattiva, Visual Studio](./media/apache-hadoop-visual-studio-tools-get-started/hdinsight-job-summary.png)

Utilizzare l'icona **Aggiorna** per aggiornare lo stato fino a quando lo stato del processo non diventa **Finito**.  

* Per i dettagli del processo dalla modalità **Batch,** selezionare i collegamenti nella parte inferiore per visualizzare **Query processo**, **Output processo**o **Registro processi**oppure per visualizzare i registri **filati**.

* Per i dettagli del processo dalla modalità **interattiva,** vedere i riquadri Output e **HiveServer2 Output.For** the job details from Interactive mode, see the **Output** and HiveServer2 Output panes.

    ![Hive interactive job output, HDInsight cluster, Visual Studio](./media/apache-hadoop-visual-studio-tools-get-started/hdinsight-job-details.png)

### <a name="view-job-graph"></a>Visualizza grafico processi

Attualmente, i grafici dei processi vengono visualizzati solo per i processi Hive che utilizzano Tez come motore di esecuzione.  Per informazioni sull'abilitazione di Tez, vedere [Che cos'è Apache Hive e HiveQL in Azure HDInsight?](hdinsight-use-hive.md).  Vedere anche [Utilizzare Apache Tez anziché Sovrapposizione mappa](../hdinsight-hadoop-optimize-hive-query.md#use-apache-tez-instead-of-map-reduce).  

Per visualizzare tutti gli operatori all'interno del vertice, fare doppio clic sui vertici del grafico del lavoro. È anche possibile puntare a un operatore specifico per visualizzare altri dettagli sull'operatore.

Anche se Tez è specificato come motore di esecuzione, il grafico dei processi potrebbe non essere visualizzato se non viene avviata alcuna applicazione Tez.  Questa situazione può verificarsi perché il processo non contiene istruzioni DML. Oppure perché le istruzioni DML possono restituire senza avviare un'applicazione Tez. Ad esempio, `SELECT * FROM table1` non avviare l'applicazione Tez.

![Apache Hive job graph, Visual Studio](./media/apache-hadoop-visual-studio-tools-get-started/hdinsight-fast-path-hive-execution.png)

### <a name="view-task-execution-detail"></a>Visualizzare i dettagli di esecuzione delle attività

Dal grafico dei processi, è possibile selezionare Dettagli esecuzione attività per ottenere informazioni strutturate e visualizzate per i processi Hive.From the job graph, you can select **Task Execution Detail** to get structured and visualized information for Hive jobs. Si possono anche ottenere maggiori dettagli sul lavoro. Se si verificano problemi di prestazioni, è possibile usare la visualizzazione per ottenere altri dettagli sul problema. Ad esempio, è possibile recuperare informazioni sul funzionamento di ogni attività e informazioni dettagliate su ogni attività (lettura/scrittura dei dati, pianificazione/inizio/ora di fine e altro ancora). Usare le informazioni per ottimizzare le configurazioni dei processi o l'architettura di sistema in base alle informazioni visualizzate.

![Finestra Visualizzazione esecuzione attività, Strumenti di Visual Studio di Data Lake](./media/apache-hadoop-visual-studio-tools-get-started/hdinsight-visual-studio-tools-task-execution-view.png)

### <a name="view-hive-jobs"></a>Visualizzare processi Hive

È possibile visualizzare query di processo, output di processo, log di processo e log Yarn per i processi Hive.

Nella versione più recente degli strumenti, puoi vedere cosa c'è dentro i tuoi lavori Hive raccogliendo e rimuovendo i log di Yarn. Un log Yarn consente di analizzare eventuali problemi di prestazioni. Per ulteriori informazioni su come HDInsight raccoglie i registri di Yarn, vedere Accedere ai registri delle [applicazioni Apache Hadoop YARN](../hdinsight-hadoop-access-yarn-app-logs-linux.md).

Per visualizzare processi Hive:

1. Fare clic con il pulsante destro del mouse su un cluster HDInsight e scegliere **Visualizza processi**.

    ![Visualizzare processi, Apache Hive, cluster HDInsight, Visual StudioView Jobs, Apache Hive, HDInsight cluster, Visual Studio](./media/apache-hadoop-visual-studio-tools-get-started/hdinsight-visual-studio-tools-view-hive-jobs.png)

    Viene visualizzato l'elenco di processi Hive eseguiti nel cluster.  

2. Selezionare un processo. Nella finestra **Riepilogo lavoro Hive,** selezionare uno dei seguenti collegamenti:
    - **Query processo**
    - **Uscita lavoro**
    - **Registro lavori**  
    - **Registro filati**

## <a name="run-apache-pig-scripts"></a>Eseguire script Apache Pig

1. Dalla barra dei menu, scegliere **File** > **Nuovo** > **progetto**.

2. Nella finestra **Start,** selezionare la casella di ricerca e immettere **Pig**. Quindi selezionare **Pig Application** e **next (Avanti).**

3. Nella finestra **Configura il nuovo progetto** immettere un Nome **progetto**e selezionare o creare un **percorso** per il progetto. Selezionare quindi **Crea**.

4. Nel riquadro **Esplora soluzioni** IDE fare doppio clic su **Script.pig** per aprire lo script.

## <a name="feedback-and-known-issues"></a>Commenti, suggerimenti e problemi noti

* È stato risolto un problema a causa del quale i risultati che iniziano con valori Null non vengono visualizzati. Se questo problema impedisce di lavorare, contattare il team di supporto.

* Lo script HQL creato da Visual Studio è codificato, a seconda dell'impostazione dell'area locale dell'utente. Lo script non viene eseguito correttamente se lo si carica in un cluster come file binario.

## <a name="next-steps"></a>Passaggi successivi

In questo articolo è stato illustrato come usare il pacchetto Strumenti Data Lake per Visual Studio per connettersi ai cluster HDInsight da Visual Studio. È stato anche illustrato come eseguire una query Hive. 

* [Eseguire query Apache Hive usando gli strumenti Data Lake per Visual Studio](apache-hadoop-use-hive-visual-studio.md)
* [Cosa sono Apache Hive e HiveQL in Azure HDInsight](hdinsight-use-hive.md)
* [Creare un cluster Apache Hadoop - Modello](apache-hadoop-linux-tutorial-get-started.md)
* [Inviare processi Apache Hadoop in HDInsightSubmit Apache Hadoop jobs in HDInsight](submit-apache-hadoop-jobs-programmatically.md)
* [Analizzare i dati di Twitter mediante Apache Hive e Apache Hadoop in HDInsight](../hdinsight-analyze-twitter-data-linux.md)
