---
title: Usare la visualizzazione Tez di Ambari con HDInsight | Documentazione Microsoft
description: Informazioni sull&quot;uso della visualizzazione Tez di Ambari per il debug di processi Tez in HDInsight.
services: hdinsight
documentationcenter: 
author: Blackmist
manager: jhubbard
editor: cgronlun
ms.assetid: 9c39ea56-670b-4699-aba0-0f64c261e411
ms.service: hdinsight
ms.custom: hdinsightactive
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: big-data
ms.date: 04/14/2017
ms.author: larryfr
translationtype: Human Translation
ms.sourcegitcommit: 0d6f6fb24f1f01d703104f925dcd03ee1ff46062
ms.openlocfilehash: 08dac5205e50dd5c33d71ba15277da66fd7b22fe
ms.lasthandoff: 04/17/2017


---
# <a name="use-ambari-views-to-debug-tez-jobs-on-hdinsight"></a>Usare le visualizzazioni di Ambari per il debug di processi Tez in HDInsight

L'interfaccia utente Web di Ambari per HDInsight contiene una visualizzazione Tez che può essere usata per la comprensione e il debug di processi che usano Tez. La visualizzazione Tez consente di visualizzare il processo come grafico di elementi connessi, esaminare ogni elemento e recuperare statistiche e informazioni sulla registrazione.

> [!IMPORTANT]
> I passaggi descritti in questo documento richiedono un cluster HDInsight che usa Linux. Linux è l'unico sistema operativo usato in HDInsight versione 3.4 o successiva. Per altre informazioni, vedere [Componenti e versioni di Hadoop disponibili in HDInsight](hdinsight-component-versioning.md#hdi-version-33-nearing-deprecation-date).

## <a name="prerequisites"></a>Prerequisiti

* Un cluster HDInsight basato su Linux. Per la procedura di creazione di un cluster, vedere [Esercitazione di Hadoop: Introduzione all'uso di Hadoop con Hive in HDInsight in Linux](hdinsight-hadoop-linux-tutorial-get-started.md).
* Un moderno Web browser che supporta HTML5.

## <a name="understanding-tez"></a>Informazioni su Tez

Tez è un framework estendibile per l'elaborazione dati in Hadoop, che garantisce una maggiore velocità rispetto alla tradizionale elaborazione di MapReduce. Per i cluster HDInsight basati su Linux si tratta del motore predefinito per Hive.

Tez crea un grafo aciclico diretto (DAG) che descrive l'ordine delle azioni necessarie per i processi. Le singole azioni sono chiamate vertici ed eseguono una parte dell'intero processo. L'esecuzione vera e propria del lavoro descritta da un vertice è chiamata attività e può essere distribuita in più nodi nel cluster.

### <a name="understanding-the-tez-view"></a>Informazioni sulla visualizzazione Tez

La visualizzazione Tez fornisce informazioni sulla cronologia e sui processi in esecuzione. Queste informazioni mostrano in che modo un processo viene distribuito tra i cluster. Visualizza anche i contatori usati da attività e vertici e le informazioni sull'errore relazionato al processo. Può offrire informazioni utili negli scenari seguenti:

* Monitoraggio di processi con esecuzione prolungata, visualizzazione dello stato delle attività di mapping e riduzione.
* Analisi dei dati cronologici per i processi riusciti o non riusciti per capire come migliorare l'elaborazione o perché non è riuscita.

## <a name="generate-a-dag"></a>Generare un DAG

La visualizzazione Tez contiene dati solo se un processo che usa il motore Tez è attualmente in esecuzione o è stato eseguito precedentemente. Le query Hive semplici possono essere risolte senza usare Tez. Query più complesse che eseguono filtraggio, raggruppamento, ordinamento, unione e così via. Usare il motore Tez.

Usare la procedura seguente per eseguire una query Hive che usa Tez:

1. In un Web browser passare a https://NOMECLUSTER.azurehdinsight.net, dove **NOMECLUSTER** è il nome del cluster HDInsight.

2. Dal menu nella parte superiore della pagina selezionare l'icona delle **visualizzazioni**. La presente icona ha l'aspetto di una serie di quadrati. Nell'elenco a discesa visualizzato, selezionare **Hive View** (Visualizzazione Hive).

    ![Selezione della visualizzazione Hive](./media/hdinsight-debug-ambari-tez-view/selecthive.png)

3. Quando viene caricata la visualizzazione Hive, incollare la query seguente nell'editor di query e quindi fare clic su **execute** (esegui).

        select market, state, country from hivesampletable where deviceplatform='Android' group by market, country, state;

    Al termine del processo, l'output verrà visualizzato nella sezione **Query Process Results** (Risultati elaborazione query). I risultati dovrebbero essere simili al testo seguente:

        market  state       country
        en-GB   Hessen      Germany
        en-GB   Kingston    Jamaica

4. Selezionare la scheda **Log**. Vengono restituite informazioni simili al seguente testo:

        INFO : Session is already open
        INFO :

        INFO : Status: Running (Executing on YARN cluster with App id application_1454546500517_0063)

    Salvare il valore **App id**, poiché tale valore viene usato nella sezione successiva.

## <a name="use-the-tez-view"></a>Usare la visualizzazione Tez

1. Dal menu nella parte superiore della pagina selezionare l'icona delle **visualizzazioni**. Nell'elenco a discesa visualizzato selezionare **Tez View** (Visualizzazione Tez).

    ![Selezione della visualizzazione Tez](./media/hdinsight-debug-ambari-tez-view/selecttez.png)

2. Quando la visualizzazione Tez viene caricata, viene visualizzato un elenco di DAG che sono attualmente in esecuzione o che sono stati eseguiti nel cluster.

    ![Tutti i DAG](./media/hdinsight-debug-ambari-tez-view/alldags.png)

3. Se è presente una sola voce, è quella relativa alla query eseguita nella sezione precedente. Se sono presenti più voci, è possibile eseguire la ricerca immettendo l'ID applicazione nel campo **Application ID** (ID applicazione) e quindi premendo INVIO.

4. Selezionare il nome del DAG nella colonna **Dag Name** (Nome DAG). Verranno visualizzate informazioni sul DAG. È anche possibile scaricare un file zip dei file JSON che contiene queste informazioni.

    ![DAG Details](./media/hdinsight-debug-ambari-tez-view/dagdetails.png)

5. Sopra **DAG Details** (Dettagli DAG) sono presenti diversi collegamenti che possono essere usati per visualizzare informazioni sul DAG.

   * **DAG Counters**: (Contatori DAG) visualizza informazioni sui contatori per il DAG.
   * **Graphical View**: (Visualizzazione grafica) visualizza una rappresentazione grafica del DAG.
   * **All Vertices** :(Tutti i vertici) visualizza un elenco dei vertici nel DAG.
   * **All Tasks**: (Tutte le attività) visualizza un elenco delle attività per tutti i vertici nel DAG.
   * **All TaskAttempts**: (Tutti i tentativi di attività) visualizza informazioni sui tentativi di eseguire attività per il DAG.

     > [!NOTE]
     > Scorrendo la visualizzazione colonne per vertici, attività e tentativi di attività, si noti che sono presenti collegamenti per visualizzare i **contatori** e per **visualizzare o scaricare i log** per ogni riga.

     Se si è verificato un errore nel processo, in DAG Details viene visualizzato lo stato FAILED, con i collegamenti alle informazioni sull'attività non riuscita. Le informazioni di diagnostica vengono visualizzate sotto i dettagli DAG.

     ![Schermata dei dettagli DAG che riporta i dettagli di un errore](./media/hdinsight-debug-ambari-tez-view/faileddag.png)

6. Selezionare **Graphical View** (Visualizzazione grafica). Questa visualizzazione mostra una rappresentazione grafica del DAG. È possibile posizionare il mouse su ogni vertice nella visualizzazione per accedere alle relative informazioni.

    ![Graphical View](./media/hdinsight-debug-ambari-tez-view/dagdiagram.png)

7. Selezionare un vertice per caricare i dati di **Vertex Details** (Dettagli vertice) per tale elemento. Selezionare il vertice **Map 1** (Mappa 1) per visualizzare i dettagli per questo elemento.

    ![Vertex Details](./media/hdinsight-debug-ambari-tez-view/vertexdetails.png)

8. Ora nella parte superiore della pagina sono presenti i collegamenti relativi ai vertici e alle attività.

   > [!NOTE]
   > È possibile accedere a questa pagina anche tornando a **DAG Details** (Dettagli DAG) e selezionando **Vertex Details** (Dettagli vertice) e quindi il vertice **Map 1** (Mappa 1).

   * **Vertex Counters**: (Contatori vertice) visualizza informazioni sui contatori per il vertice.
   * **Tasks**: (Attività) visualizza le attività per il vertice.
   * **Task Attempts**: (Tentativi attività) visualizza informazioni sui tentativi di eseguire attività per il vertice.
   * **Sources & Sinks**: (Origini e sink) visualizza le origini dati e i sink per il vertice.

     > [!NOTE]
     > Come per il menu precedente, è possibile scorrere la visualizzazione colonne per Tasks, Task Attempts e Sources & Sinks per visualizzare i collegamenti ad altre informazioni per ogni elemento.

9. Selezionare **Tasks** (Attività) e quindi l'elemento denominato **00_000000**. Appaiono i **Task Details** (Dettagli attività) per questa attività. Da questa schermata è possibile visualizzare **Task Counters** (Contatori attività) e **Task Attempts** (Tentativi attività).

   ![Dettagli dell'attività](./media/hdinsight-debug-ambari-tez-view/taskdetails.png)

## <a name="next-steps"></a>Passaggi successivi

A questo punto, dopo avere appreso come usare la visualizzazione Tez, è possibile trovare altre informazioni in [Uso di Hive in HDInsight](hdinsight-use-hive.md).

Per informazioni tecniche più dettagliate su Tez, vedere la [pagina di Tez in Hortonworks](http://hortonworks.com/hadoop/tez/).

Per altre informazioni sull'uso di Ambari con HDInsight, vedere [Gestire i cluster HDInsight usando l'interfaccia utente Web di Ambari](hdinsight-hadoop-manage-ambari.md)

