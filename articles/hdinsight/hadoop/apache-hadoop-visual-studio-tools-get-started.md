---
title: Connettersi ad Azure HDInsight usando Strumenti Data Lake per Visual Studio | Microsoft Docs
description: Informazioni su come installare e usare Strumenti Data Lake per Visual Studio per connettersi a cluster Hadoop in Azure HDInsight e quindi eseguire query Hive.
keywords: strumenti Hadoop, query Hive, Visual Studio, Hadoop in Visual Studio
services: HDInsight
documentationcenter: ''
tags: azure-portal
author: mumian
manager: jhubbard
editor: cgronlun
ms.assetid: ce9c572a-1e98-46bf-9581-13a9767f1fa5
ms.service: hdinsight
ms.custom: hdinsightactive,hdiseo17may2017
ms.devlang: na
ms.topic: get-started-article
ms.tgt_pltfrm: na
ms.workload: big-data
ms.date: 02/22/2018
ms.author: jgao
ms.openlocfilehash: afd40d75bb9c5fd3170a4da215925244994d7749
ms.sourcegitcommit: a0be2dc237d30b7f79914e8adfb85299571374ec
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 03/12/2018
---
# <a name="use-data-lake-tools-for-visual-studio-to-connect-to-azure-hdinsight-and-run-hive-queries"></a>Usare Strumenti Data Lake per Visual Studio per connettersi ad Azure HDInsight ed eseguire query Hive

Informazioni su come usare Strumenti Data Lake per Visual Studio (detti anche Strumenti Azure Data Lake e Analisi di flusso per Visual Studio) per connettersi a cluster Hadoop in [Azure HDInsight](../hdinsight-hadoop-introduction.md) e inviare query Hive. 

Per altre informazioni sull'uso di HDInsight, vedere [Introduzione a HDInsight](../hdinsight-hadoop-introduction.md) e [Introduzione all'uso di HDInsight](apache-hadoop-linux-tutorial-get-started.md). 

Per altre informazioni sulla connessione a un cluster Storm, vedere [Sviluppare topologie C# per Apache Storm in HDInsight usando Visual Studio](../storm/apache-storm-develop-csharp-visual-studio-topology.md).

È possibile usare Strumenti Data Lake per Visual Studio per accedere ad Azure Data Lake Analytics e a HDInsight. Per informazioni su Strumenti Data Lake, vedere [Sviluppare script U-SQL tramite Strumenti Data Lake per Visual Studio](../../data-lake-analytics/data-lake-analytics-data-lake-tools-get-started.md).

## <a name="prerequisites"></a>prerequisiti

Per completare questa esercitazione e usare Strumenti Data Lake per Visual Studio, sono necessari gli elementi seguenti:

* Disporre di un cluster HDInsight di Azure. Per creare un cluster Azure HDInsight, vedere [Iniziare a usare Hadoop in Azure HDInsight](apache-hadoop-linux-tutorial-get-started.md). Per eseguire query Apache Hive interattive, è necessario un cluster [Interactive Query in HDInsight](../interactive-query/apache-interactive-query-get-started.md).
* Un computer con Visual Studio 2017, 2015 o 2013 installato.
    
    > [!NOTE]
    > Attualmente, è disponibile solo la versione in lingua inglese di Strumenti Data Lake per Visual Studio.
    > 
    > 

## <a name="install-or-update-data-lake-tools-for-visual-studio"></a>Installare o aggiornare Strumenti Azure Data Lake per Visual Studio

### <a name="install-data-lake-tools"></a>Installare gli strumenti di Data Lake

Gli strumenti Data Lake Tools vengono installati per impostazione predefinita per Visual Studio 2017. Per le versioni precedenti di Visual Studio, è possibile installare Strumenti Data Lake usando l'[Installazione guidata piattaforma Web](https://www.microsoft.com/web/downloads/platform.aspx). Scegliere la versione di Strumenti Data Lake che corrisponde alla versione di Visual Studio in uso. 

### <a name="install-visual-studio"></a>Installazione di Visual Studio

Se Visual Studio non è installato, usare l'[Installazione guidata piattaforma Web](https://www.microsoft.com/web/downloads/platform.aspx) per installare le versioni più recenti di Visual Studio Community e Azure SDK:

![Screenshot dell'Installazione guidata piattaforma Web per Strumenti Azure Data Lake per Visual Studio.](./media/apache-hadoop-visual-studio-tools-get-started/hdinsight.visual.studio.tools.wpi.png "Usare l'Installazione guidata piattaforma Web per installare Strumenti Azure Data Lake per Visual Studio")

### <a name="update-the-tools"></a>Aggiornare gli strumenti

1. Aprire Visual Studio.
2. Scegliere **Estensioni e aggiornamenti** dal menu **Strumenti**.
3. Espandere **Aggiornamenti** e quindi selezionare **Strumenti Azure Data Lake e Analisi di flusso** (se installato).

> [!NOTE]
>
> È possibile usare solo Strumenti Data Lake versione 2.3.0.0 o successiva per connettersi ai cluster Interactive Query ed eseguire query Hive interattive.

## <a name="connect-to-azure-subscriptions"></a>Connettersi alle sottoscrizioni di Azure
È possibile usare Strumenti Data Lake per Visual Studio per connettersi a cluster HDInsight ed eseguire alcune operazioni di gestione di base e query Hive.

> [!NOTE]
> Per informazioni sulla connessione a un cluster Hadoop generico, vedere [Write and submit Hive queries by using Visual Studio](http://blogs.msdn.com/b/xiaoyong/archive/2015/05/04/how-to-write-and-submit-hive-queries-using-visual-studio.aspx) (Scrivere e inviare query Hive con Visual Studio).
> 
> 

Per connettersi alla sottoscrizione di Azure:

1. Aprire Visual Studio.
2. Dal menu **Visualizza** selezionare **Esplora server**.
3. In Esplora server espandere **Azure** e quindi **HDInsight**.
   
   > [!NOTE]
   > La finestra **Elenco attività HDInsight** è aperta. Se la finestra non viene visualizzata, scegliere **Altre finestre** dal menu **Visualizza** e quindi selezionare **Finestra Elenco attività di HDInsight**.  
   > 
   > 
4. Immettere le credenziali della sottoscrizione di Azure e selezionare **Accedi**. L'autenticazione è necessaria solo se non si è mai eseguita la connessione alla sottoscrizione di Azure da Visual Studio sul computer.
5. In Esplora server viene visualizzato un elenco di cluster HDInsight esistenti. Se non sono disponibili cluster, è possibile crearne uno usando il portale di Azure, Azure PowerShell o HDInsight SDK. Per altre informazioni, vedere [Creare cluster Hadoop in HDInsight](../hdinsight-hadoop-provision-linux-clusters.md).
   
   ![Screenshot dell'elenco di cluster di Strumenti Data Lake per Visual Studio in Esplora server](./media/apache-hadoop-visual-studio-tools-get-started/hdinsight.visual.studio.tools.server.explorer.png "Elenco di cluster di Strumenti Data Lake per Visual Studio in Esplora server")
6. Espandere un cluster HDInsight. Vengono visualizzati i **database Hive**, un account di archiviazione predefinito, gli account di archiviazione collegati e il **log del servizio Hadoop**. È possibile espandere ulteriormente le entità.

Dopo la connessione alla sottoscrizione di Azure, è possibile eseguire le attività seguenti.

Per connettersi al portale di Azure da Visual Studio:

1. In Esplora server selezionare **Azure** > **HDInsight**.
2. Fare clic con il pulsante destro del mouse su un cluster HDInsight e quindi scegliere **Gestisci cluster nel portale di Azure**.

Per porre domande e fornire commenti e suggerimenti da Visual Studio:

1. Scegliere **HDInsight** dal menu **Strumenti**.
2. Per porre domande, selezionare **Forum MSDN**. Per inviare commenti, selezionare **Invia commenti**.

## <a name="explore-linked-resources"></a>Esplorare risorse collegate
In Esplora server è possibile visualizzare l'account di archiviazione predefinito e qualsiasi account di archiviazione collegato. Se si espande l'account di archiviazione predefinito, è possibile visualizzare i contenitori presenti. L'account di archiviazione predefinito e il contenitore predefinito sono contrassegnati. Per visualizzare i contenuti dei contenitori, fare clic su uno dei contenitori con il pulsante destro del mouse.

![Screenshot dell'elenco di risorse collegate di Strumenti Data Lake per Visual Studio in Esplora server](./media/apache-hadoop-visual-studio-tools-get-started/hdinsight.visual.studio.tools.linked.resources.png "Elenco di risorse collegate")

Dopo aver aperto un contenitore, è possibile usare i pulsanti seguenti per caricare, eliminare e scaricare i BLOB:

![Screenshot delle operazioni sui BLOB per Strumenti Data Lake per Visual Studio in Esplora server](./media/apache-hadoop-visual-studio-tools-get-started/hdinsight.visual.studio.tools.blob.operations.png "Caricare, eliminare e scaricare BLOB in Esplora server")

## <a name="run-interactive-hive-queries"></a>Eseguire query Hive interattive
[Apache Hive](http://hive.apache.org) è un'infrastruttura di data warehouse basata su Hadoop. Hive viene usato per riepilogo, le query e l'analisi dei dati. È possibile usare Strumenti Data Lake per Visual Studio per eseguire query Hive da Visual Studio. Per altre informazioni su Hive, vedere [Usare Hive con HDInsight](hdinsight-use-hive.md).

[Interactive Query](../interactive-query/apache-interactive-query-get-started.md) usa [Hive in LLAP](https://cwiki.apache.org/confluence/display/Hive/LLAP) in Apache Hive 2.1. Interactive Query garantisce l'interattività alle query di tipo data warehouse complesse sui set di dati di grandi dimensioni archiviati. L'esecuzione di query Hive in Interactive Query è molto più veloce rispetto ai tradizionali processi batch Hive. Per altre informazioni, vedere [Eseguire processi batch Hive](#run-hive-batch-jobs).

> [!NOTE]
>
> È possibile eseguire query Hive interattive solo quando ci si connette a un cluster [Interactive Query in HDInsight](../interactive-query/apache-interactive-query-get-started.md).

È anche possibile usare Strumenti Data Lake per Visual Studio per visualizzare il contenuto di un processo Hive. Strumenti Data Lake per Visual Studio raccoglie ed espone i log Yarn di determinati processi Hive.

### <a name="view-hivesampletable"></a>Visualizzare **hivesampletable**
I cluster HDInsight includono una tabella Hive di esempio predefinita denominata hivesampletable. La tabella Hive definisce come elencare tabelle Hive, visualizzare gli schemi di tabella ed elencare le righe nella tabella Hive.

Per elencare tabelle Hive e visualizzare lo schema di tabella Hive:

1. Per visualizzare lo schema di tabella, in **Esplora server** selezionare **Azure** > **HDInsight**. Selezionare il cluster e quindi selezionare **Database Hive** > **Predefinito** > **hivesampletable**.
2. Fare clic con il pulsante destro del mouse su **hivesampletable** e quindi scegliere **Visualizza prime 100 righe** per elencare le righe. Questa operazione equivale a eseguire la query Hive seguente usando il driver ODBC di Hive:
   
     `SELECT * FROM hivesampletable LIMIT 100`
   
   È possibile personalizzare il numero delle righe.
   
   ![Screenshot di una query sullo schema Hive HDInsight in Visual Studio](./media/apache-hadoop-visual-studio-tools-get-started/hdinsight.visual.studio.tools.hive.schema.png "Risultati della query Hive")

### <a name="create-hive-tables"></a>Creare tabelle Hive
Per creare una tabella Hive, è possibile usare l'interfaccia utente grafica o query Hive. Per informazioni sull'uso di query Hive, vedere [Eseguire query Hive](#run.queries).

Per creare una tabella Hive:

1. In **Esplora server** selezionare **Azure** > **Cluster HDInsight**. Selezionare il cluster HDInsight e quindi selezionare **Database Hive**.
2. Fare clic con il pulsante destro del mouse su **predefinito** e quindi scegliere **Crea tabella**.
3. Configurare la tabella.  
4. Selezionare **Crea tabella** per inviare il processo per creare la nuova tabella Hive.
   
    ![Screenshot della finestra Crea tabella di Strumenti di Visual Studio in HDInsight](./media/apache-hadoop-visual-studio-tools-get-started/hdinsight.visual.studio.tools.create.hive.table.png "Creare una tabella Hive")

### <a name="run.queries"></a>Convalidare ed eseguire query Hive
Per la creazione e l'esecuzione di query Hive sono disponibili due opzioni:

* Creare query ad hoc
* Creare un'applicazione Hive

Per creare, convalidare ed eseguire query ad hoc:

1. In **Esplora server** selezionare **Azure** > **Cluster HDInsight**.
2. Fare clic con il pulsante destro del mouse sul cluster in cui si vuole eseguire la query, quindi scegliere **Scrivi una query Hive**.  
3. Immettere le query Hive. 

    L'editor Hive supporta IntelliSense. Strumenti Data Lake per Visual Studio supporta il caricamento di metadati remoti quando si modifica lo script Hive. Se ad esempio si digita **SELECT * FROM**, IntelliSense elenca tutti i nomi di tabella suggeriti. Quando si specifica un nome di tabella, IntelliSense elenca i nomi delle colonne. Gli strumenti supportano la maggior parte delle funzioni definite dall'utente predefinite, delle sottoquery e delle istruzioni DML Hive.
   
    ![Screenshot 1 di un esempio di IntelliSense in Strumenti di Visual Studio in HDInsight](./media/apache-hadoop-visual-studio-tools-get-started/hdinsight.visual.studio.tools.intellisense.table.names.png "IntelliSense in U-SQL")
   
    ![Screenshot 2 di un esempio di IntelliSense in Strumenti di Visual Studio in HDInsight](./media/apache-hadoop-visual-studio-tools-get-started/hdinsight.visual.studio.tools.intellisense.column.names.png "IntelliSense in U-SQL")
   
   > [!NOTE]
   > IntelliSense suggerisce solo i metadati del cluster selezionato nella barra degli strumenti HDInsight.
   > 
   
4. (Facoltativo) Per verificare la presenza di eventuali errori di sintassi nello script, fare clic su **Convalida script**.
   
    ![Screenshot della convalida locale in Strumenti Data Lake per Visual Studio](./media/apache-hadoop-visual-studio-tools-get-started/hdinsight.visual.studio.tools.validate.hive.script.png "Convalidare uno script")
5. Selezionare **Submit** (Invio) o **Submit (Advanced)** (Invio - Avanzato). Se si seleziona l'opzione di invio avanzato, configurare le impostazioni per **Nome processo**, **Argomenti**, **Configurazioni aggiuntive** e **Directory di stato** per lo script:
   
    ![Screenshot di una query Hive Hadoop HDInsight](./media/apache-hadoop-visual-studio-tools-get-started/hdinsight.visual.studio.tools.submit.jobs.advanced.png "Inviare query")
   
    Dopo l'invio, viene visualizzata la finestra **Hive Job Summary** (Riepilogo processo Hive).
   
    ![Screenshot di un riepilogo di una query Hive di Hadoop di HDInsight](./media/apache-hadoop-visual-studio-tools-get-started/hdinsight.visual.studio.tools.run.hive.job.summary.png "Riepilogo processo Hive")
6. Usare il pulsante **Refresh** (Aggiorna) per aggiornare lo stato del processo finché non risulta **Completed** (Completato).
7. Selezionare i collegamenti in basso per visualizzare **Query processo**, **Output del processo**, **Log del processo** o **Log Yarn**.

Per creare ed eseguire una soluzione Hive:

1. Scegliere **Nuovo** dal menu **File**, quindi fare clic su **Progetto**.
2. Selezionare **HDInsight** nel riquadro sinistro. Nel riquadro centrale selezionare **Hive Application** (Applicazione Hive). Immettere le proprietà e quindi selezionare **OK**.
   
    ![Screenshot di un nuovo progetto Hive in Strumenti di Visual Studio per HDInsight](./media/apache-hadoop-visual-studio-tools-get-started/hdinsight.visual.studio.tools.new.hive.project.png "Creare applicazioni Hive da Visual Studio")
3. In **Esplora soluzioni** fare doppio clic su **Script.hql** per aprire lo script.
4. Per convalidare lo script Hive, selezionare il pulsante **Convalida script**. È anche possibile fare clic con il pulsante destro del mouse sullo script nell'editor Hive e quindi scegliere **Convalida script** dal menu di scelta rapida.

### <a name="view-hive-jobs"></a>Visualizzare processi Hive
È possibile visualizzare query di processo, output di processo, log di processo e log Yarn per i processi Hive. Per altre informazioni, vedere lo screenshot precedente.

Nella versione più recente degli strumenti è possibile visualizzare i contenuti dei processi Hive raccogliendo ed esponendo i log Yarn. Un log Yarn consente di analizzare eventuali problemi di prestazioni. Per altre informazioni su come HDInsight raccoglie i log Yarn, vedere [Accedere ai log dell'applicazione HDInsight a livello di codice](../hdinsight-hadoop-access-yarn-app-logs.md).

Per visualizzare processi Hive:

1. In **Esplora server** espandere **Azure** e quindi **HDInsight**.
2. Fare clic con il pulsante destro del mouse su un cluster HDInsight, quindi scegliere **Visualizza processi**. Viene visualizzato l'elenco di processi Hive eseguiti nel cluster.  
3. Selezionare un processo. Nella finestra **Hive Job Summary** (Riepilogo processo Hive) selezionare uno dei collegamenti seguenti:
    - **Query processo**
    - **Output processo**
    - **Log processo**  
    - **Log Yarn**
   
    ![Screenshot della finestra di visualizzazione dei processi Hive in Strumenti di Visual Studio di HDInsight](./media/apache-hadoop-visual-studio-tools-get-started/hdinsight.visual.studio.tools.view.hive.jobs.png "Visualizzare processi Hive")

### <a name="faster-path-hive-execution-via-hiveserver2"></a>Esecuzione di processi Hive più veloce tramite HiveServer2
> [!NOTE]
> Questa funzionalità può essere usata solo in un cluster in HDInsight versione 3.2 o successiva.
 
In precedenza, i processi Hive venivano inviati da Strumenti Data Lake per Visual Studio tramite [WebHCat](https://cwiki.apache.org/confluence/display/Hive/WebHCat), noto anche come Templeton. Questo metodo di invio dei processi Hive impiegava molto tempo per restituire i dettagli del processo e le informazioni sugli errori.

Per risolvere questo problema di prestazioni, Strumenti Data Lake per Visual Studio può ignorare i protocolli RDP/SSH ed eseguire i processi Hive direttamente nel cluster tramite HiveServer2.

Oltre a ottenere prestazioni migliori, con questo metodo, gli utenti possono visualizzare Hive in grafici Apache Tez e nei dettagli dell'attività.

In un cluster in HDInsight versione 3.2 o successiva è disponibile un pulsante **Esegui tramite HiveServer2**:

![Screenshot dell'opzione Esegui tramite HiveServer2 di Strumenti Data Lake per Visual Studio](./media/apache-hadoop-visual-studio-tools-get-started/hdinsight.visual.studio.tools.execute.via.hiveserver2.png "Eseguire query Hive usando HiveServer2")

È anche possibile visualizzare i log in streaming in tempo reale. È anche possibile visualizzare i grafici del processo, se si esegue la query Hive in Tez.

![Screenshot dell'esecuzione più rapida di Hive con HiveServer2 in Strumenti Data Lake per Visual Studio](./media/apache-hadoop-visual-studio-tools-get-started/hdinsight.fast.path.hive.execution.png "Visualizzare i grafici del processo")

### <a name="execute-queries-via-hiveserver2-vs-submit-queries-via-webhcat"></a>Eseguire query tramite HiveServer2 e inviare query tramite WebHCat

L'esecuzione di query tramite HiveServer2 presenta numerosi vantaggi in termini di prestazioni, ma anche diverse limitazioni. Alcune delle limitazioni rendono questo metodo non adatto all'uso per scopi di produzione. 

La tabella seguente illustra le differenze tra l'esecuzione di query tramite HiveServer2 e l'invio di query tramite WebHCat:

|  | Esecuzione tramite HiveServer2 | Invio tramite WebHCat |
| --- | --- | --- |
| Eseguire query |Elimina il sovraccarico in WebHCat, che avvia un processo MapReduce denominato TempletonControllerJob. |Se la query viene eseguita tramite WebHCat, WebHCat avvia un processo MapReduce con il quale viene introdotta latenza aggiuntiva. |
| Trasmettere log |In tempo quasi reale. |I log di esecuzione del processo sono disponibili solo al termine del processo. |
| Visualizzare la cronologia processo |Se una query viene eseguita tramite HiveServer2, la relativa cronologia processo (log del processo e output del processo) non viene mantenuta. È possibile visualizzare l'applicazione nell'interfaccia utente di Yarn con informazioni limitate. |Se una query viene eseguita tramite WebHCat, la relativa cronologia processo (log del processo e output del processo) viene mantenuta. È possibile visualizzare la cronologia processo usando Visual Studio, HDInsight SDK o PowerShell. |
| Chiudere la finestra |L'esecuzione tramite HiveServer2 è *sincrona*. Se le finestre vengono chiuse, l'esecuzione della query viene annullata. |L'invio tramite WebHCat è *asincrono*. È possibile inviare la query tramite WebHCat e quindi chiudere Visual Studio. È possibile tornare in questa pagina e visualizzare i risultati in qualsiasi momento. |

### <a name="tez-hive-job-performance-graph"></a>Grafico delle prestazioni del processo Hive Tez
In Strumenti Data Lake per Visual Studio è possibile visualizzare i grafici delle prestazioni per i processi Hive eseguiti dal motore di esecuzione Tez. Per informazioni su come abilitare Tez, vedere [Usare Hive in HDInsight](hdinsight-use-hive.md). 

Dopo avere inviato un processo Hive in Visual Studio, Visual Studio visualizza il grafico al termine del processo. Per visualizzare l'ultimo stato del processo, potrebbe essere necessario selezionare il pulsante **Aggiorna**.

> [!NOTE]
> Questa funzionalità è disponibile solo per un cluster in HDInsight versione 3.2.4.593 o successiva. La funzionalità può essere usata solo nei processi completati. Per usare questa funzionalità, è anche necessario inviare il processo tramite WebHCat. Quando si esegue la query tramite HiveServer2, viene visualizzata l'immagine seguente: 
> 
> ![Screenshot di un grafico delle prestazioni di Tez in Hive in Hadoop](./media/apache-hadoop-visual-studio-tools-get-started/hdinsight.hive.tez.performance.graph.png "Stato del processo")

Per comprendere meglio la query Hive, è stata aggiunta la funzionalità Visualizzazione operatore Hive in questa versione. Per visualizzare tutti gli operatori all'interno del vertice, fare doppio clic sui vertici del grafico del processo. È anche possibile puntare a un operatore specifico per visualizzare altri dettagli sull'operatore.

### <a name="task-execution-view-for-hive-on-tez-jobs"></a>Visualizzazione dell'esecuzione dell'attività per i processi Hive in Tez
È possibile usare la visualizzazione dell'esecuzione dell'attività per i processi Hive in Tez per ottenere informazioni strutturate e visive per i processi Hive. Sono disponibili anche altri dettagli sui processi. Se si verificano problemi di prestazioni, è possibile usare la visualizzazione per ottenere altri dettagli sul problema. È ad esempio possibile ottenere informazioni sul funzionamento di ogni attività e informazioni dettagliate su ogni attività (lettura/scrittura dati, ora di pianificazione/inizio/fine e così via). Usare le informazioni per ottimizzare le configurazioni dei processi o l'architettura di sistema in base alle informazioni visualizzate.

![Screenshot della finestra di visualizzazione dell'esecuzione dell'attività in Strumenti di Visual Studio di Data Lake](./media/apache-hadoop-visual-studio-tools-get-started/hdinsight.visual.studio.tools.task.execution.view.png "Visualizzazione dell'esecuzione dell'attività")

## <a name="run-hive-batch-jobs"></a>Eseguire processi batch Hive
Fatta eccezione per il cluster Interactive Query, testare script Hive in un cluster HDInsight è un'operazione che può richiedere molto tempo. Questo processo può richiedere diversi minuti. Strumenti Data Lake per Visual Studio consente di eseguire la convalida dello script Hive in locale senza connettersi a un cluster attivo. Per altre informazioni sull'esecuzione delle query interattive, vedere [Eseguire query Hive interattive](#run-interactive-hive-queries).

È possibile usare Strumenti Data Lake per Visual Studio per visualizzare i contenuti del processo Hive raccogliendo ed esponendo i log Yarn di specifici processi Hive.

Per altre informazioni su come eseguire i processi batch Hive, vedere [Eseguire query Hive interattive](#run-interactive-hive-queries). Le informazioni di questa sezione si applicano all'esecuzione di processi batch Hive con tempi di esecuzione più lunghi.

## <a name="run-pig-scripts"></a>Eseguire script Pig
È possibile usare Strumenti Data Lake per Visual Studio per creare e inviare script Pig a cluster HDInsight. Creare prima di tutto un progetto Pig da un modello, quindi inviare lo script ai cluster HDInsight.

## <a name="feedback-and-known-issues"></a>Commenti, suggerimenti e problemi noti
* Attualmente i risultati di HiveServer2 vengono visualizzati in formato di testo normale, che non è ideale. Microsoft sta risolvendo il problema.
* È stato risolto un problema a causa del quale i risultati che iniziano con valori Null non vengono visualizzati. Se questo problema impedisce di lavorare, contattare il team di supporto.
* Lo script HQL creato da Visual Studio viene codificato, a seconda dell'impostazione dell'area locale dell'utente. Lo script non viene eseguito correttamente se lo si carica in un cluster come file binario.

## <a name="next-steps"></a>Passaggi successivi
In questo articolo è stato illustrato come usare il pacchetto Strumenti Data Lake per Visual Studio per connettersi ai cluster HDInsight da Visual Studio. È stato anche illustrato come eseguire una query Hive. Per altre informazioni, vedere questi articoli:

* [Usare Hive di Hadoop in HDInsight](hdinsight-use-hive.md)
* [Introduzione all'uso di Hadoop in HDInsight](apache-hadoop-linux-tutorial-get-started.md)
* [Inviare processi Hadoop in HDInsight](submit-apache-hadoop-jobs-programmatically.md)
* [Analizzare i dati di Twitter con Hadoop in HDInsight](../hdinsight-analyze-twitter-data.md)

