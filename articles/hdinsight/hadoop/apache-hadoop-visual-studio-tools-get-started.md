---
title: Apache Hadoop & Visual Studio Data Lake Tools-Azure HDInsight
description: Informazioni su come installare e usare Strumenti Data Lake per Visual Studio per connettersi a cluster Apache Hadoop in Azure HDInsight e quindi eseguire query Hive.
keywords: strumenti Hadoop, query Hive, Visual Studio, Hadoop in Visual Studio
author: hrasheed-msft
ms.author: hrasheed
ms.reviewer: jasonh
ms.service: hdinsight
ms.custom: hdinsightactive,hdiseo17may2017,seodec18
ms.topic: conceptual
ms.date: 10/29/2019
ms.openlocfilehash: 4ad58bc2d61f063dce2c23f60a65dcbec48a2303
ms.sourcegitcommit: ac56ef07d86328c40fed5b5792a6a02698926c2d
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 11/08/2019
ms.locfileid: "73825048"
---
# <a name="use-data-lake-tools-for-visual-studio-to-connect-to-azure-hdinsight-and-run-apache-hive-queries"></a>Usare Strumenti Data Lake per Visual Studio per connettersi ad Azure HDInsight ed eseguire query Apache Hive

Informazioni su come usare Microsoft Azure Data Lake e gli strumenti di analisi di flusso per Visual Studio (detti anche strumenti di Data Lake) per connettersi ai [cluster Apache Hadoop in Azure HDInsight](apache-hadoop-introduction.md) e inviare query hive.  

Per altre informazioni sull'uso di HDInsight, vedere [Introduzione a HDInsight](apache-hadoop-linux-tutorial-get-started.md).  

Per ulteriori informazioni sulla connessione a un cluster di Apache Storm, [vedere C# sviluppare topologie per Apache Storm utilizzando gli strumenti di data Lake per Visual Studio](../storm/apache-storm-develop-csharp-visual-studio-topology.md).

È possibile usare Strumenti Data Lake per Visual Studio per accedere ad Azure Data Lake Analytics e a HDInsight. Per informazioni su Strumenti Data Lake, vedere [Sviluppare script U-SQL tramite Strumenti Data Lake per Visual Studio](../../data-lake-analytics/data-lake-analytics-data-lake-tools-get-started.md).

## <a name="prerequisites"></a>Prerequisiti

Per completare questo articolo e usare Data Lake Tools per Visual Studio, sono necessari gli elementi seguenti:

* Disporre di un cluster HDInsight di Azure. Per creare un cluster Azure HDInsight, vedere [Iniziare a usare Apache Hadoop in Azure HDInsight](apache-hadoop-linux-tutorial-get-started.md). Per eseguire query Apache Hive interattive, è necessario un cluster [Interactive Query in HDInsight](../interactive-query/apache-interactive-query-get-started.md).  

* [Visual Studio](https://visualstudio.microsoft.com/downloads/) [Visual Studio Community Edition](https://visualstudio.microsoft.com/vs/community/) è gratuito. Le istruzioni visualizzate sono disponibili per [Visual Studio 2019](https://visualstudio.microsoft.com/downloads/).

## <a name="install-data-lake-tools-for-visual-studio"></a>Installare Data Lake Tools per Visual Studio  

Seguire le istruzioni appropriate per installare Data Lake Tools per la versione di Visual Studio:

- Per Visual Studio 2017 o Visual Studio 2019:

    Durante l'installazione di Visual Studio, assicurarsi di includere il carico di lavoro **sviluppo di Azure** o il carico **di lavoro elaborazione ed archiviazione dati** .  

    Per le installazioni esistenti di Visual Studio, passare alla barra dei menu dell'IDE e selezionare **strumenti** > **ottenere strumenti e funzionalità** per aprire programma di installazione di Visual Studio. Nella scheda **carichi di lavoro** selezionare almeno il carico di lavoro di **sviluppo di Azure** (in **Web & cloud**) o il carico di lavoro **elaborazione e archiviazione dati** (in **altri set di strumenti**).

  ![Selezione del carico di lavoro, Programma di installazione di Visual Studio](./media/apache-hadoop-visual-studio-tools-get-started/vs-installation.png)

- Per Visual Studio 2015:

    [Scaricare Data Lake Tools](https://www.microsoft.com/download/details.aspx?id=49504). Scegliere la versione di Strumenti Data Lake che corrisponde alla versione di Visual Studio in uso.

## <a name="update-data-lake-tools-for-visual-studio"></a>Aggiornare Data Lake Tools per Visual Studio  

Assicurarsi quindi di aggiornare Data Lake Tools alla versione più recente.

1. Aprire Visual Studio.

2. Nella finestra **Start** selezionare **continua senza codice**.

3. Nella barra dei menu dell'IDE di Visual Studio scegliere **estensioni** > **Gestisci estensioni**.

4. Nella finestra di dialogo **Gestisci estensioni** espandere il nodo **aggiornamenti** .

5. Se l'elenco degli aggiornamenti disponibili include **Azure Data Lake e strumenti di analisi di flusso**, selezionarlo. Quindi selezionare il pulsante **Aggiorna** . Dopo che la finestra di dialogo **download e installazione** viene visualizzata e scompare, Visual Studio aggiunge l'estensione degli **strumenti di analisi di flusso e Azure Data Lake** alla pianificazione dell'aggiornamento.

6. Chiudere tutte le finestre di Visual Studio. Verrà visualizzata la finestra di dialogo **programma di installazione VSIX** .

7. Selezionare **licenza** per leggere le condizioni di licenza, quindi fare clic su **Chiudi** per tornare alla finestra di dialogo del **programma di installazione VSIX** .

8. Selezionare **Modifica**. Viene avviata l'installazione dell'aggiornamento dell'estensione. Dopo un po' di tempo, la finestra di dialogo Cambia per indicare che è stata apportata una modifica. Selezionare **Chiudi**, quindi riavviare Visual Studio per completare l'installazione.

> [!NOTE]  
> È possibile usare solo Strumenti Data Lake versione 2.3.0.0 o successiva per connettersi ai cluster Interactive Query ed eseguire query Hive interattive.

## <a name="connect-to-azure-subscriptions"></a>Connettersi alle sottoscrizioni di Azure

È possibile usare Data Lake Tools per Visual Studio per connettersi ai cluster HDInsight, eseguire alcune operazioni di gestione di base ed eseguire query hive.

> [!NOTE]  
> Per informazioni sulla connessione a un cluster Hadoop generico, vedere [come scrivere e inviare query hive usando Visual Studio](https://blogs.msdn.microsoft.com/xiaoyong/2015/05/04/how-to-write-and-submit-hive-queries-using-visual-studio/).

### <a name="connect-to-an-azure-subscription"></a>Connettersi a una sottoscrizione di Azure.

Per connettersi alla sottoscrizione di Azure:

1. Aprire Visual Studio.

2. Nella finestra **Start** selezionare **continua senza codice**.

3. Nella barra dei menu dell'IDE scegliere **visualizza** > **Esplora server**.

4. In **Esplora server**fare clic con il pulsante destro del mouse su **Azure**, scegliere **Connetti a Microsoft Azure sottoscrizione**e completare il processo di autenticazione. Da **Esplora server**espandere **Azure** > **HDInsight** per visualizzare un elenco di cluster HDInsight esistenti.

5. Se non si dispone di cluster, crearne uno usando il portale di Azure, Azure PowerShell o HDInsight SDK. Per altre informazioni, vedere [configurare i cluster in HDInsight](../hdinsight-hadoop-provision-linux-clusters.md).

   ![Elenco di cluster HDInsight, Esplora server, Visual Studio](./media/apache-hadoop-visual-studio-tools-get-started/hdinsight-visual-studio-tools-server-explorer.png)

6. Espandere un cluster HDInsight. Il cluster contiene nodi per i **database hive**, un account di archiviazione predefinito, tutti gli account di archiviazione collegati aggiuntivi e il **log del servizio Hadoop**. È possibile espandere ulteriormente le entità.

Dopo la connessione alla sottoscrizione di Azure, è possibile eseguire le attività seguenti.

### <a name="connect-to-azure-from-visual-studio"></a>Connettersi ad Azure da Visual Studio

Per connettersi al portale di Azure da Visual Studio:

1. In **Esplora server**espandere **Azure** > **HDInsight** e selezionare il cluster.

2. Fare clic con il pulsante destro del mouse su un cluster HDInsight e scegliere **Gestisci cluster in portale di Azure**.

### <a name="offer-questions-and-feedback-from-visual-studio"></a>Offrire domande e commenti e suggerimenti da Visual Studio

Per porre domande e/o fornire commenti e suggerimenti da Visual Studio:

1. Da Esplora server scegliere **Azure** > **HDInsight**.

2. Fare clic con il pulsante destro del mouse su **HDInsight** e selezionare **Forum MSDN** per porre domande o **inviare commenti e suggerimenti** per inviare commenti e suggerimenti.

## <a name="link-to-or-edit-a-cluster"></a>Collegamento o modifica di un cluster

> [!NOTE]
> Attualmente, l'unico tipo di cluster HDInsight a cui è possibile collegarsi è un tipo hive.

Per collegare un cluster HDInsight:

1. Fare clic con il pulsante destro del mouse su **HDInsight**e quindi scegliere **collega un cluster HDInsight** per visualizzare la finestra di dialogo **collega un cluster HDInsight** .

2. Immettere un **URL di connessione** nel formato *https\://\<nome&nbsp;cluster >. azurehdinsight. NET*. Il **nome del cluster** viene compilato automaticamente con la parte relativa al nome del cluster dell'URL quando si passa a un altro campo. Immettere quindi un **nome utente** e una **password**e fare clic su **Avanti**.

    ![Collegare un cluster, HDInsight, Visual Studio](./media/apache-hadoop-visual-studio-tools-get-started/hdinsight-visual-studio-tools-link-cluster-dialog.png)

3. Selezionare **Fine**. Se il collegamento del cluster ha esito positivo, il cluster viene elencato sotto il nodo **HDInsight** .

Per aggiornare un cluster collegato, fare clic con il pulsante destro del mouse sul cluster e scegliere **modifica**. È quindi possibile aggiornare le informazioni del cluster.

![Modificare un cluster collegato, HDInsight, Visual Studio](./media/apache-hadoop-visual-studio-tools-get-started/hdinsight-visual-studio-tools-link-cluster-update.png)

## <a name="explore-linked-resources"></a>Esplorare risorse collegate
Da Esplora server è possibile visualizzare l'account di archiviazione predefinito e qualsiasi account di archiviazione collegato. Se si espande l'account di archiviazione predefinito, è possibile visualizzare i contenitori presenti. L'account di archiviazione predefinito e il contenitore predefinito sono contrassegnati.

![Strumenti di Data Lake per le risorse collegate di Visual Studio in Esplora server](./media/apache-hadoop-visual-studio-tools-get-started/hdinsight-visual-studio-tools-linked-resources.png)

Fare clic con il pulsante destro del mouse su un contenitore e selezionare **Visualizza contenitore** per visualizzare il contenuto del contenitore. Dopo l'apertura di un contenitore, è possibile usare i pulsanti della barra degli strumenti per **aggiornare** l'elenco di contenuto, **caricare BLOB**, eliminare i BLOB **selezionati**, **aprire BLOB**e scaricare i BLOB selezionati (**Salva con nome**).

![Elenco di contenitori e operazioni BLOB, cluster HDInsight, Visual Studio](./media/apache-hadoop-visual-studio-tools-get-started/hdinsight-visual-studio-tools-blob-operations.png)

## <a name="run-interactive-apache-hive-queries"></a>Eseguire query Interactive Apache Hive
[Apache Hive](https://hive.apache.org) è un'infrastruttura di data warehouse basata su Hadoop. Hive viene usato per riepilogo, le query e l'analisi dei dati. È possibile usare Strumenti Data Lake per Visual Studio per eseguire query Hive da Visual Studio. Per altre informazioni su hive, vedere [che cos'è Apache hive e HiveQL in Azure HDInsight?](hdinsight-use-hive.md).

Per le [query interattive in Azure HDInsight](../interactive-query/apache-interactive-query-get-started.md) viene usato [hive in LLAP](https://cwiki.apache.org/confluence/display/Hive/LLAP) in Apache hive 2,1. Interactive query offre interattività a query complesse di tipo data warehouse su set di impostazioni di grandi dimensioni archiviati. L'esecuzione di query hive in Interactive query è molto più veloce rispetto ai tradizionali processi batch hive. 

> [!NOTE]  
> È possibile eseguire query Hive interattive solo quando ci si connette a un cluster [Interactive Query in HDInsight](../interactive-query/apache-interactive-query-get-started.md).

È anche possibile usare Strumenti Data Lake per Visual Studio per visualizzare il contenuto di un processo Hive. Strumenti Data Lake per Visual Studio raccoglie ed espone i log Yarn di determinati processi Hive.

Da **Esplora server**scegliere **Azure** > **HDInsight** e selezionare il cluster.  Questo nodo è il punto iniziale in **Esplora server** per le sezioni da seguire.

### <a name="view-hivesampletable"></a>Visualizza hivesampletable

Tutti i cluster HDInsight hanno una tabella hive di esempio predefinita denominata `hivesampletable`.  

Dal cluster scegliere **database Hive** > **predefinito** > **hivesampletable**.

- Per visualizzare lo schema di `hivesampletable`:

    Espandere **hivesampletable**. Vengono visualizzati i nomi e i tipi di dati delle colonne `hivesampletable`.

- Per visualizzare i dati `hivesampletable`:

    Fare clic con il pulsante destro del mouse su **hivesampletable**e selezionare **Visualizza prime 100 righe**. L'elenco dei risultati di 100 viene visualizzato nella finestra **hive Table: hivesampletable** . Questa azione equivale a eseguire la query hive seguente usando il driver ODBC di hive:

    `SELECT * FROM hivesampletable LIMIT 100`

    È possibile personalizzare il conteggio delle righe modificando il **numero di righe**; è possibile scegliere 50, 100, 200 o 1000 righe dall'elenco a discesa.

### <a name="create-hive-tables"></a>Creare tabelle Hive
Per creare una tabella Hive, è possibile usare l'interfaccia utente grafica o query Hive. Per informazioni sull'uso di query hive, vedere [creare ed eseguire query hive](#create-and-run-hive-queries).

1. Nel cluster scegliere **database Hive** > **valore predefinito**.

2. Fare clic con il pulsante destro del mouse su **predefinito**e scegliere **Crea tabella**.

3. Configurare la tabella.

4. Selezionare il pulsante **Crea tabella** per inviare il processo, che crea la nuova tabella hive.

    ![Creazione della finestra tabella, hive, cluster HDInsight, Visual Studio](./media/apache-hadoop-visual-studio-tools-get-started/hdinsight-visual-studio-tools-create-hive-table.png)

### <a name="create-and-run-hive-queries"></a>Creare ed eseguire query hive
Per la creazione e l'esecuzione di query Hive sono disponibili due opzioni:

* Creare query ad hoc
* Creare un'applicazione Hive

#### <a name="create-an-ad-hoc-query"></a>Creare una query ad hoc

Per creare ed eseguire una query ad hoc:

1. Fare clic con il pulsante destro del mouse sul cluster in cui si vuole eseguire la query e selezionare **Scrivi una query hive**.  

2. Immettere una query hive.

    L'editor Hive supporta IntelliSense. Strumenti Data Lake per Visual Studio supporta il caricamento di metadati remoti quando si modifica lo script Hive. Se ad esempio si digita `SELECT * FROM`, IntelliSense elenca tutti i nomi di tabella suggeriti. Quando si specifica un nome di tabella, IntelliSense elenca i nomi delle colonne. Gli strumenti supportano la maggior parte delle funzioni definite dall'utente predefinite, delle sottoquery e delle istruzioni DML Hive.

    ![Esempio di IntelliSense 1, query ad hoc hive, cluster HDInsight, Visual Studio](./media/apache-hadoop-visual-studio-tools-get-started/hdinsight-visual-studio-tools-intellisense-table-names.png)

    ![Esempio di IntelliSense 2, query ad hoc hive, cluster HDInsight, Visual Studio](./media/apache-hadoop-visual-studio-tools-get-started/hdinsight-visual-studio-tools-intellisense-column-names.png)

    > [!NOTE]  
    > IntelliSense suggerisce solo i metadati del cluster selezionato nella barra degli strumenti HDInsight.

    Ecco una query di esempio che è possibile usare:

    ```sql
    SELECT devicemodel, COUNT(devicemodel) AS deviceCount
    FROM hivesampletable
    GROUP BY devicemodel
    ORDER BY devicemodel
    ```

3. Scegliere la modalità di esecuzione:

    * **Interattivo**  

        Nel primo elenco a discesa, scegliere **interattivo**, quindi selezionare **Esegui**.

        ![Modalità interattiva, query hive ad hoc, cluster HDInsight, Visual Studio](./media/apache-hadoop-visual-studio-tools-get-started/hdinsight-query-execute.png)  

    * **Batch**  

        Nel primo elenco a discesa scegliere **batch**, quindi selezionare **Invia** (oppure selezionare l'icona a discesa accanto a **Invia** e scegliere **Avanzate**).

        ![Modalità batch, query ad hoc hive, cluster HDInsight, Visual Studio](./media/apache-hadoop-visual-studio-tools-get-started/hdinsight-query-batch.png)

        Se si seleziona l'opzione di invio avanzato, viene visualizzata la finestra di dialogo **Invia script** . Configurare **nome processo**, **argomenti**, **configurazioni aggiuntive**e directory di **stato** per lo script.

        ![Finestra di dialogo Invia script, query ad hoc hive, cluster HDInsight, Visual Studio](./media/apache-hadoop-visual-studio-tools-get-started/hdinsight-visual-studio-tools-submit-jobs-advanced.png)

      > [!NOTE]  
      > Non è possibile inviare batch ai cluster Interactive query.  È necessario utilizzare la modalità interattiva.

#### <a name="create-a-hive-application"></a>Creare un'applicazione Hive

Per creare ed eseguire una soluzione Hive:

1. Dalla barra dei menu scegliere **File** > **nuovo** > **progetto**.

2. Nella finestra **Crea un nuovo progetto** selezionare la casella di ricerca e digitare **hive**. Quindi scegliere **applicazione hive** e fare clic su **Avanti**.

3. Nella finestra **Configura nuovo progetto** immettere un **nome di progetto**, selezionare o creare il **percorso**del progetto, quindi selezionare **Crea**.

    ![Nuova applicazione hive, configurare la finestra nuovo progetto, HDInsight Visual Studio](./media/apache-hadoop-visual-studio-tools-get-started/hdinsight-visual-studio-tools-new-hive-project.png)

4. In **Esplora soluzioni** fare doppio clic su **Script.hql** per aprire lo script.

### <a name="view-job-summary-and-output"></a>Visualizzare il riepilogo e l'output del processo

Il riepilogo del processo varia leggermente tra il **batch** e la modalità **interattiva** .

![Finestre di riepilogo del processo hive, modalità batch e interattiva, Visual Studio](./media/apache-hadoop-visual-studio-tools-get-started/hdinsight-job-summary.png)

Usare l'icona di **aggiornamento** per aggiornare lo stato finché lo stato del processo non diventa **completato**.  

* Per i dettagli del processo in modalità **batch** , selezionare i collegamenti nella parte inferiore per visualizzare **la query del processo**, l'output del **processo**o il **log**del processo o per **visualizzare i log Yarn**.

* Per informazioni **dettagliate sui processi** in modalità **interattiva** , vedere i riquadri output e **output HiveServer2** .

    ![Output del processo interattivo hive, cluster HDInsight, Visual Studio](./media/apache-hadoop-visual-studio-tools-get-started/hdinsight-job-details.png)

### <a name="view-job-graph"></a>Visualizzare il grafico del processo

Attualmente, i grafici dei processi vengono visualizzati solo per i processi hive che usano Tez come motore di esecuzione.  Per informazioni sull'abilitazione di TeZ, vedere [che cos'è Apache hive e HiveQL in Azure HDInsight?](hdinsight-use-hive.md).  Vedere anche [usare Apache Tez invece della riduzione della mappa](../hdinsight-hadoop-optimize-hive-query.md#use-apache-tez-instead-of-map-reduce).  

Per visualizzare tutti gli operatori all'interno del vertice, fare doppio clic sui vertici del grafico del processo. È anche possibile puntare a un operatore specifico per visualizzare altri dettagli sull'operatore.

Anche se TeZ è specificato come motore di esecuzione, il grafo del processo potrebbe non essere visualizzato se non viene avviata alcuna applicazione Tez.  Questa situazione può verificarsi perché il processo non contiene istruzioni DML o perché le istruzioni DML possono restituire senza avviare un'applicazione Tez. Ad esempio, `SELECT * FROM table1` non avvierà l'applicazione Tez.

![Grafico del processo di Apache Hive, Visual Studio](./media/apache-hadoop-visual-studio-tools-get-started/hdinsight-fast-path-hive-execution.png)

### <a name="view-task-execution-detail"></a>Visualizzare i dettagli dell'esecuzione dell'attività

Dal grafico del processo è possibile selezionare i **Dettagli di esecuzione delle attività** per ottenere informazioni strutturate e visualizzate per i processi hive. È anche possibile ottenere altri dettagli sul processo. Se si verificano problemi di prestazioni, è possibile usare la visualizzazione per ottenere altri dettagli sul problema. Ad esempio, è possibile recuperare informazioni sul funzionamento di ogni attività e informazioni dettagliate su ogni attività (lettura/scrittura dati, ora di inizio/fine e altro). Usare le informazioni per ottimizzare le configurazioni dei processi o l'architettura di sistema in base alle informazioni visualizzate.

![Finestra visualizzazione esecuzione attività, Data Lake Strumenti di Visual Studio](./media/apache-hadoop-visual-studio-tools-get-started/hdinsight-visual-studio-tools-task-execution-view.png)

### <a name="view-hive-jobs"></a>Visualizzare processi Hive

È possibile visualizzare query di processo, output di processo, log di processo e log Yarn per i processi Hive.

Nella versione più recente degli strumenti è possibile visualizzare i contenuti dei processi Hive raccogliendo ed esponendo i log Yarn. Un log Yarn consente di analizzare eventuali problemi di prestazioni. Per altre informazioni sul modo in cui HDInsight raccoglie i log Yarn, vedere [accedere ai log delle applicazioni yarn Apache Hadoop](../hdinsight-hadoop-access-yarn-app-logs-linux.md).

Per visualizzare processi Hive:

1. Fare clic con il pulsante destro del mouse su un cluster HDInsight e selezionare **Visualizza processi**.

    ![Visualizzazione di processi, Apache Hive, cluster HDInsight, Visual Studio](./media/apache-hadoop-visual-studio-tools-get-started/hdinsight-visual-studio-tools-view-hive-jobs.png)

    Viene visualizzato l'elenco di processi Hive eseguiti nel cluster.  

2. Selezionare un processo. Nella finestra **Riepilogo processo hive** selezionare uno dei collegamenti seguenti:
    - **Query processo**
    - **Output processo**
    - **Log processo**  
    - **Log Yarn**

## <a name="run-apache-pig-scripts"></a>Eseguire script Apache Pig

1. Dalla barra dei menu scegliere **File** > **nuovo** > **progetto**.

2. Nella finestra di **avvio** selezionare la casella di ricerca e immettere **Pig**. Selezionare quindi **applicazione Pig** e selezionare **Avanti**.

3. Nella finestra **Configura nuovo progetto** immettere un **nome di progetto**e selezionare o creare un **percorso** per il progetto. Selezionare quindi **Crea**.

4. Nel riquadro **Esplora soluzioni** IDE, fare doppio clic su **script. Pig** per aprire lo script.

## <a name="feedback-and-known-issues"></a>Commenti, suggerimenti e problemi noti

* È stato risolto un problema a causa del quale i risultati che iniziano con valori Null non vengono visualizzati. Se questo problema impedisce di lavorare, contattare il team di supporto.

* Lo script HQL creato da Visual Studio viene codificato, a seconda dell'impostazione dell'area locale dell'utente. Lo script non viene eseguito correttamente se lo si carica in un cluster come file binario.

## <a name="next-steps"></a>Passaggi successivi

In questo articolo è stato illustrato come usare il pacchetto Strumenti Data Lake per Visual Studio per connettersi ai cluster HDInsight da Visual Studio. È stato anche illustrato come eseguire una query Hive. Per altre informazioni, vedere questi articoli:

* [Eseguire query Apache Hive usando gli strumenti Data Lake per Visual Studio](apache-hadoop-use-hive-visual-studio.md)
* [Che cos'è Apache Hive e HiveQL in Azure HDInsight?](hdinsight-use-hive.md)
* [Creare Apache Hadoop cluster-modello](apache-hadoop-linux-tutorial-get-started.md)
* [Inviare processi Apache Hadoop in HDInsight](submit-apache-hadoop-jobs-programmatically.md)
* [Analizzare i dati di Twitter usando Apache Hive e Apache Hadoop in HDInsight](../hdinsight-analyze-twitter-data-linux.md)
