---
title: Strumenti di Apache Hadoop e Data Lake per Visual Studio-Azure HDInsight
description: Informazioni su come installare e usare Strumenti Data Lake per Visual Studio per connettersi a cluster Apache Hadoop in Azure HDInsight e quindi eseguire query Hive.
keywords: strumenti Hadoop, query Hive, Visual Studio, Hadoop in Visual Studio
services: hdinsight
author: hrasheed-msft
ms.author: hrasheed
ms.reviewer: jasonh
ms.service: hdinsight
ms.custom: hdinsightactive,hdiseo17may2017,seodec18
ms.topic: conceptual
ms.date: 06/03/2019
ms.openlocfilehash: 706bcbed39ca2a3fe0a38eba864edb863c31b4ec
ms.sourcegitcommit: 7c5a2a3068e5330b77f3c6738d6de1e03d3c3b7d
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 09/11/2019
ms.locfileid: "70884100"
---
# <a name="use-data-lake-tools-for-visual-studio-to-connect-to-azure-hdinsight-and-run-apache-hive-queries"></a>Usare Strumenti Data Lake per Visual Studio per connettersi ad Azure HDInsight ed eseguire query Apache Hive

Informazioni su come usare [Microsoft Azure Data Lake e gli strumenti di analisi di flusso per Visual Studio](https://www.microsoft.com/download/details.aspx?id=49504) (detti anche strumenti di data Lake) per connettersi ai cluster Apache Hadoop in [Azure HDInsight](../hdinsight-hadoop-introduction.md) e inviare query hive.  

Per altre informazioni sull'uso di HDInsight, vedere [Introduzione a HDInsight](../hdinsight-hadoop-introduction.md) e [Introduzione all'uso di HDInsight](apache-hadoop-linux-tutorial-get-started.md).  

Per altre informazioni sulla connessione a un cluster Apache Storm, vedere [Sviluppare topologie C# per Apache Storm in HDInsight usando Visual Studio](../storm/apache-storm-develop-csharp-visual-studio-topology.md).

È possibile usare Strumenti Data Lake per Visual Studio per accedere ad Azure Data Lake Analytics e a HDInsight. Per informazioni su Strumenti Data Lake, vedere [Sviluppare script U-SQL tramite Strumenti Data Lake per Visual Studio](../../data-lake-analytics/data-lake-analytics-data-lake-tools-get-started.md).

## <a name="prerequisites"></a>Prerequisiti

Per completare questo articolo e usare Data Lake Tools per Visual Studio, sono necessari gli elementi seguenti:

* Disporre di un cluster HDInsight di Azure. Per creare un cluster Azure HDInsight, vedere [Iniziare a usare Apache Hadoop in Azure HDInsight](apache-hadoop-linux-tutorial-get-started.md). Per eseguire query Apache Hive interattive, è necessario un cluster [Interactive Query in HDInsight](../interactive-query/apache-interactive-query-get-started.md).  

* [Visual Studio](https://visualstudio.microsoft.com/downloads/) (2013 o versione successiva).  [Visual Studio Community Edition](https://visualstudio.microsoft.com/vs/community/) è gratuito.  Vedere anche [installare Visual studio 2017](https://docs.microsoft.com/visualstudio/install/install-visual-studio) e [Visual Studio 2019](https://visualstudio.microsoft.com/downloads/). Sono disponibili piccole variazioni di interfaccia con Visual Studio 2019.

  > [!IMPORTANT]  
  > Data Lake strumenti non è più supportato per Visual Studio 2013.

## <a name="install-data-lake-tools-for-visual-studio"></a>Installare Data Lake Tools per Visual Studio  
<a name="install-or-update-data-lake-tools-for-visual-studio"></a>

* Visual Studio 2017 o Visual Studio 2019  
  Durante l'installazione, assicurarsi di includere almeno i carichi di lavoro **sviluppo di Azure** o l' **archiviazione e l'elaborazione dei dati**.  

  Per le installazioni esistenti, dalla barra dei menu passare a **strumenti** > **Ottieni strumenti e funzionalità...** per aprire programma di installazione di Visual Studio.  Selezionare quindi almeno i carichi di lavoro **sviluppo di Azure** o **archiviazione ed elaborazione dei dati**.

  ![Screenshot di Programma di installazione di Visual Studio](./media/apache-hadoop-visual-studio-tools-get-started/vs-2017-installation.png)

* Visual Studio 2013 e 2015  
  [Scaricare Data Lake Tools](https://www.microsoft.com/download/details.aspx?id=49504). Scegliere la versione di Strumenti Data Lake che corrisponde alla versione di Visual Studio in uso.  

> [!NOTE]  
> Attualmente, è disponibile solo la versione in lingua inglese di Strumenti Data Lake per Visual Studio.

## <a name="update-data-lake-tools-for-visual-studio"></a>Aggiornare Data Lake Tools per Visual Studio  

1. Aprire Visual Studio.

2. Dalla barra dei menu passare a **strumenti** > **estensioni e aggiornamenti.**

3. Nella finestra **estensioni e aggiornamenti** espandere **aggiornamenti** a sinistra.

4. Se è disponibile un aggiornamento, gli **strumenti di analisi di flusso e Azure Data Lake** verranno visualizzati nella finestra principale.  Selezionare **Aggiorna**.

> [!NOTE]  
> È possibile usare solo Strumenti Data Lake versione 2.3.0.0 o successiva per connettersi ai cluster Interactive Query ed eseguire query Hive interattive.

## <a name="connect-to-azure-subscriptions"></a>Connettersi alle sottoscrizioni di Azure
È possibile usare Strumenti Data Lake per Visual Studio per connettersi a cluster HDInsight ed eseguire alcune operazioni di gestione di base e query Hive.

> [!NOTE]  
> Per informazioni sulla connessione a un cluster Hadoop generico, vedere [Write and submit Hive queries by using Visual Studio](https://blogs.msdn.com/b/xiaoyong/archive/2015/05/04/how-to-write-and-submit-hive-queries-using-visual-studio.aspx) (Scrivere e inviare query Hive con Visual Studio).

Per connettersi alla sottoscrizione di Azure:

1. Aprire Visual Studio.

2. Dalla barra dei menu passare a **Visualizza** > **Esplora server**.

3. In Esplora server fare clic con il pulsante destro del mouse su **Azure**, scegliere **Connetti a Microsoft Azure sottoscrizione...** e completare il processo di accesso.

4. Da Esplora server viene visualizzato un elenco di cluster HDInsight esistenti. Se non sono disponibili cluster, è possibile crearne uno usando il portale di Azure, Azure PowerShell o HDInsight SDK. Per altre informazioni, vedere [Creare cluster Hadoop in HDInsight](../hdinsight-hadoop-provision-linux-clusters.md).

   ![Screenshot dell'elenco di cluster di Strumenti Data Lake per Visual Studio in Esplora server](./media/apache-hadoop-visual-studio-tools-get-started/hdinsight-visual-studio-tools-server-explorer.png "Elenco di cluster di Strumenti Data Lake per Visual Studio in Esplora server")

5. Espandere un cluster HDInsight. Vengono visualizzati i **database hive**, un account di archiviazione predefinito, gli account di archiviazione collegati e il **log del servizio Hadoop** . È possibile espandere ulteriormente le entità.

Dopo la connessione alla sottoscrizione di Azure, è possibile eseguire le operazioni seguenti.

Per connettersi al portale di Azure da Visual Studio:

1. Da Esplora server passare ad **Azure** > **HDInsight** e selezionare il cluster.

2. Fare clic con il pulsante destro del mouse su un cluster HDInsight e scegliere **Gestisci cluster in portale di Azure [sic]** .

Per porre domande e/o fornire commenti e suggerimenti da Visual Studio:

1. Da Esplora server passare ad **Azure** > **HDInsight**.

2. Fare clic con il pulsante destro del mouse su **HDInsight** e selezionare **Forum MSDN** per porre domande o **inviare commenti e suggerimenti** per inviare commenti e suggerimenti.

## <a name="link-a-cluster"></a>Collegare un cluster
È possibile collegare un cluster facendo clic con il pulsante destro del mouse su **HDInsight** e quindi selezionare **collega un cluster HDInsight**. Immettere **l'URL della connessione**, il **nome utente** e la **password**, fare clic su **Avanti** e quindi su **fine**, il cluster dovrebbe essere elencato nel nodo HDInsight riuscito.

![Screenshot della finestra di dialogo del cluster di collegamenti di Data Lake Tools per Visual Studio](./media/apache-hadoop-visual-studio-tools-get-started/hdinsight-visual-studio-tools-link-cluster-dialog.png)

Fare clic con il pulsante destro del mouse sul cluster collegato, scegliere **modifica**. l'utente potrebbe aggiornare le informazioni sul cluster. L'aggiunta di un cluster HDInsight supporta solo hive per il momento.

![Screenshot dell'aggiornamento del cluster di Data Lake Tools per Visual Studio link](./media/apache-hadoop-visual-studio-tools-get-started/hdinsight-visual-studio-tools-link-cluster-update.png)

## <a name="explore-linked-resources"></a>Esplorare risorse collegate
Da Esplora server è possibile visualizzare l'account di archiviazione predefinito e qualsiasi account di archiviazione collegato. Se si espande l'account di archiviazione predefinito, è possibile visualizzare i contenitori presenti. L'account di archiviazione predefinito e il contenitore predefinito sono contrassegnati. Per visualizzare i contenuti dei contenitori, fare clic su uno dei contenitori con il pulsante destro del mouse.

![Screenshot dell'elenco di risorse collegate di Strumenti Data Lake per Visual Studio in Esplora server](./media/apache-hadoop-visual-studio-tools-get-started/hdinsight-visual-studio-tools-linked-resources.png "Elenco di risorse collegate")

Dopo aver aperto un contenitore, è possibile usare i pulsanti seguenti per caricare, eliminare e scaricare i BLOB:

![Screenshot delle operazioni sui BLOB per Strumenti Data Lake per Visual Studio in Esplora server](./media/apache-hadoop-visual-studio-tools-get-started/hdinsight-visual-studio-tools-blob-operations.png "Caricare, eliminare e scaricare BLOB in Esplora server")

## <a name="run-interactive-apache-hive-queries"></a>Eseguire query Interactive Apache Hive
[Apache Hive](https://hive.apache.org) è un'infrastruttura di data warehouse basata su Hadoop. Hive viene usato per riepilogo, le query e l'analisi dei dati. È possibile usare Strumenti Data Lake per Visual Studio per eseguire query Hive da Visual Studio. Per altre informazioni su Hive, vedere [Usare Apache Hive con HDInsight](hdinsight-use-hive.md).

[Interactive Query](../interactive-query/apache-interactive-query-get-started.md) usa [Hive in LLAP](https://cwiki.apache.org/confluence/display/Hive/LLAP) in Apache Hive 2.1. Interactive Query garantisce l'interattività alle query di tipo data warehouse complesse sui set di dati di grandi dimensioni archiviati. L'esecuzione di query Hive in Interactive Query è molto più veloce rispetto ai tradizionali processi batch Hive. 

> [!NOTE]  
> È possibile eseguire query Hive interattive solo quando ci si connette a un cluster [Interactive Query in HDInsight](../interactive-query/apache-interactive-query-get-started.md).

È anche possibile usare Strumenti Data Lake per Visual Studio per visualizzare il contenuto di un processo Hive. Strumenti Data Lake per Visual Studio raccoglie ed espone i log Yarn di determinati processi Hive.

Da Esplora server passare ad **Azure** > **HDInsight** e selezionare il cluster.  Questo sarà il punto iniziale in Esplora server per le sezioni da seguire.

### <a name="view-hivesampletable"></a>Visualizza hivesampletable
Tutti i cluster HDInsight hanno una tabella hive di esempio predefinita `hivesampletable`denominata.  

Dal cluster passare a **hive databases** > **default** > **hivesampletable**.

* Per visualizzare `hivesampletable` lo schema:  
Espandere **hivesampletable**.

* Per visualizzare `hivesampletable` i dati:  
Fare clic con il pulsante destro del mouse su **hivesampletable**e selezionare **Visualizza prime 100 righe**.  Questa operazione equivale a eseguire la query Hive seguente usando il driver ODBC di Hive:

   `SELECT * FROM hivesampletable LIMIT 100`

  È possibile personalizzare il numero delle righe.

  ![Screenshot di una query sullo schema Hive HDInsight in Visual Studio](./media/apache-hadoop-visual-studio-tools-get-started/hdinsight-visual-studio-tools-hive-schema.png "Risultati della query Hive")

### <a name="create-hive-tables"></a>Creare tabelle Hive
Per creare una tabella Hive, è possibile usare l'interfaccia utente grafica o query Hive. Per informazioni sull'uso di query Hive, vedere [Eseguire query Apache Hive](#run.queries).

1. Dal cluster passare a **hive databases** > **default**.

2. Fare clic con il pulsante destro del mouse su **predefinito**e scegliere **Crea tabella**.

3. Configurare la tabella nel modo desiderato.  

4. Selezionare **Crea tabella** per inviare il processo per creare la nuova tabella Hive.

    ![Screenshot della finestra Crea tabella di Strumenti di Visual Studio in HDInsight](./media/apache-hadoop-visual-studio-tools-get-started/hdinsight-visual-studio-tools-create-hive-table.png "Creare una tabella Hive")

### <a name="run.queries"></a>Creare ed eseguire query hive
Per la creazione e l'esecuzione di query Hive sono disponibili due opzioni:

* Creare query ad hoc
* Creare un'applicazione Hive

Per creare ed eseguire query ad hoc:

1. Fare clic con il pulsante destro del mouse sul cluster in cui si vuole eseguire la query e selezionare **Scrivi una query hive**.  

2. Immettere la query hive seguente:

    ```sql
    SELECT devicemodel, COUNT(devicemodel) AS deviceCount
    FROM hivesampletable
    GROUP BY devicemodel
    ORDER BY devicemodel
    ```

    L'editor Hive supporta IntelliSense. Strumenti Data Lake per Visual Studio supporta il caricamento di metadati remoti quando si modifica lo script Hive. Se, ad esempio, si `SELECT * FROM`digita, IntelliSense elenca tutti i nomi di tabella suggeriti. Quando si specifica un nome di tabella, IntelliSense elenca i nomi delle colonne. Gli strumenti supportano la maggior parte delle funzioni definite dall'utente predefinite, delle sottoquery e delle istruzioni DML Hive.

    ![Screenshot 1 di un esempio di IntelliSense in Strumenti di Visual Studio in HDInsight](./media/apache-hadoop-visual-studio-tools-get-started/hdinsight-visual-studio-tools-intellisense-table-names.png "IntelliSense in U-SQL")

    ![Screenshot 2 di un esempio di IntelliSense in Strumenti di Visual Studio in HDInsight](./media/apache-hadoop-visual-studio-tools-get-started/hdinsight-visual-studio-tools-intellisense-column-names.png "IntelliSense in U-SQL")

   > [!NOTE]  
   > IntelliSense suggerisce solo i metadati del cluster selezionato nella barra degli strumenti HDInsight.

3. Scegliere la modalità di esecuzione:

    * **Interattivo**  

      Assicurarsi che **Interactive** sia selezionato e quindi selezionare **Esegui**.

      ![Screenshot della query e dell'esecuzione](./media/apache-hadoop-visual-studio-tools-get-started/hdinsight-query-execute.png)  

    * **Batch**  

      Verificare che **batch** sia selezionato, quindi selezionare **Invia**.  Se si seleziona l'opzione di invio avanzato, configurare **nome processo**, **argomenti**, **configurazioni aggiuntive**e **directory di stato** per lo script.

      ![Screenshot di query e batch](./media/apache-hadoop-visual-studio-tools-get-started/hdinsight-query-batch.png)  

      ![Screenshot di una query Hive Hadoop HDInsight](./media/apache-hadoop-visual-studio-tools-get-started/hdinsight-visual-studio-tools-submit-jobs-advanced.png "Inviare query")

      > [!NOTE]  
      > Non è possibile inviare batch ai cluster Interactive query.  È necessario utilizzare la modalità interattiva.

Per creare ed eseguire una soluzione Hive:

1. Dalla barra dei menu passare a **file** > **nuovo** > **progetto...** .

2. Nel riquadro sinistro passare a **installato** > **Azure Data Lake** > **hive (HDInsight)** .  

3. Nel riquadro centrale selezionare **Hive Application** (Applicazione Hive). Immettere le proprietà e quindi selezionare **OK**.

    ![Screenshot di un nuovo progetto Hive in Strumenti di Visual Studio per HDInsight](./media/apache-hadoop-visual-studio-tools-get-started/hdinsight-visual-studio-tools-new-hive-project.png "Creare applicazioni Hive da Visual Studio")

4. In **Esplora soluzioni** fare doppio clic su **Script.hql** per aprire lo script.

### <a name="view-job-summary-and-output"></a>Visualizzare il riepilogo e l'output del processo

Il riepilogo del processo varia leggermente tra il **batch** e la modalità **interattiva** .

![Riepilogo processi](./media/apache-hadoop-visual-studio-tools-get-started/hdinsight-job-summary.png "Riepilogo del processo hive")

Utilizzare il pulsante **Aggiorna** per aggiornare lo stato fino a quando lo stato del processo diventa **completato**.  

* Per i dettagli del processo in modalità **batch** , selezionare i collegamenti in basso per visualizzare **query processo**, **output processo**, **log del processo**o **log Yarn**.

* Per informazioni dettagliate sul processo dalla modalità **interattiva** , vedere Tab **output** e **output HiveServer2**.

  ![Dettagli processo](./media/apache-hadoop-visual-studio-tools-get-started/hdinsight-job-details.png "Dettagli processo hive")

### <a name="view-job-graph"></a>Visualizzare il grafico del processo

Attualmente, i grafici dei processi vengono visualizzati solo per i processi hive che usano Tez come motore di esecuzione.  Per informazioni su come abilitare Tez, vedere [Usare Apache Hive in HDInsight](hdinsight-use-hive.md).  Vedere anche [usare Apache Tez invece della riduzione della mappa](../hdinsight-hadoop-optimize-hive-query.md#use-apache-tez-instead-of-map-reduce).  

Per visualizzare tutti gli operatori all'interno del vertice, fare doppio clic sui vertici del grafico del processo. È anche possibile puntare a un operatore specifico per visualizzare altri dettagli sull'operatore.

Il grafico del processo potrebbe non essere visualizzato anche se Tez viene specificato come motore di esecuzione se non viene avviata alcuna applicazione Tez.  Questo problema può verificarsi perché il processo non contiene istruzioni DML oppure le istruzioni DML possono restituire senza avviare un'applicazione Tez. Ad esempio, `SELECT * FROM table1` non avvierà l'applicazione Tez.

![Grafico del processo](./media/apache-hadoop-visual-studio-tools-get-started/hdinsight-fast-path-hive-execution.png "Riepilogo del processo hive")


### <a name="task-execution-detail"></a>Dettagli esecuzione attività

Dal grafico del processo è possibile selezionare i **Dettagli di esecuzione delle attività** per ottenere informazioni strutturate e visualizzate per i processi hive. Sono disponibili anche altri dettagli sui processi. Se si verificano problemi di prestazioni, è possibile usare la visualizzazione per ottenere altri dettagli sul problema. È ad esempio possibile ottenere informazioni sul funzionamento di ogni attività e informazioni dettagliate su ogni attività (lettura/scrittura dati, ora di pianificazione/inizio/fine e così via). Usare le informazioni per ottimizzare le configurazioni dei processi o l'architettura di sistema in base alle informazioni visualizzate.

![Screenshot della finestra di visualizzazione dell'esecuzione dell'attività in Strumenti di Visual Studio di Data Lake](./media/apache-hadoop-visual-studio-tools-get-started/hdinsight-visual-studio-tools-task-execution-view.png "Visualizzazione dell'esecuzione dell'attività")


### <a name="view-hive-jobs"></a>Visualizzare processi Hive
È possibile visualizzare query di processo, output di processo, log di processo e log Yarn per i processi Hive.

Nella versione più recente degli strumenti è possibile visualizzare i contenuti dei processi Hive raccogliendo ed esponendo i log Yarn. Un log Yarn consente di analizzare eventuali problemi di prestazioni. Per altre informazioni su come HDInsight raccoglie i log Yarn, vedere [Accedere ai log dell'applicazione HDInsight a livello di codice](../hdinsight-hadoop-access-yarn-app-logs.md).

Per visualizzare processi Hive:

1. Fare clic con il pulsante destro del mouse su un cluster HDInsight e selezionare **Visualizza processi**. Viene visualizzato l'elenco di processi Hive eseguiti nel cluster.  

2. Selezionare un processo. Nella finestra **Hive Job Summary** (Riepilogo processo Hive) selezionare uno dei collegamenti seguenti:
    - **Query processo**
    - **Output processo**
    - **Log processo**  
    - **Log Yarn**

    ![Screenshot della finestra di visualizzazione dei processi Hive in Strumenti di Visual Studio di HDInsight](./media/apache-hadoop-visual-studio-tools-get-started/hdinsight-visual-studio-tools-view-hive-jobs.png "Visualizzare processi Hive")


## <a name="run-apache-pig-scripts"></a>Eseguire script Apache Pig

1. Dalla barra dei menu passare a **file** > **nuovo** > **progetto...** .

2. Nel riquadro sinistro passare a **installato** > **Azure Data Lake** > **Pig (HDInsight)** .  

3. Nel riquadro centrale selezionare **applicazione Pig**. Immettere le proprietà e quindi selezionare **OK**.

4. In **Esplora soluzioni**fare doppio clic su **script. Pig** per aprire lo script.

## <a name="feedback-and-known-issues"></a>Commenti, suggerimenti e problemi noti
* È stato risolto un problema a causa del quale i risultati che iniziano con valori Null non vengono visualizzati. Se questo problema impedisce di lavorare, contattare il team di supporto.
* Lo script HQL creato da Visual Studio viene codificato, a seconda dell'impostazione dell'area locale dell'utente. Lo script non viene eseguito correttamente se lo si carica in un cluster come file binario.

## <a name="next-steps"></a>Passaggi successivi
In questo articolo è stato illustrato come usare il pacchetto Strumenti Data Lake per Visual Studio per connettersi ai cluster HDInsight da Visual Studio. È stato anche illustrato come eseguire una query Hive. Per altre informazioni, vedere questi articoli:

* [Eseguire query Apache Hive usando gli strumenti Data Lake per Visual Studio](apache-hadoop-use-hive-visual-studio.md)
* [Usare Hive di Hadoop in HDInsight](hdinsight-use-hive.md)
* [Introduzione all'uso di Apache Hadoop in HDInsight](apache-hadoop-linux-tutorial-get-started.md)
* [Inviare processi Apache Hadoop in HDInsight](submit-apache-hadoop-jobs-programmatically.md)
* [Analizzare i dati di Twitter con Apache Hadoop in HDInsight](../hdinsight-analyze-twitter-data.md)

