---
title: Usare la visualizzazione Tez di Ambari con HDInsight - Azure | Microsoft Docs
description: Informazioni sull'uso della visualizzazione Tez di Ambari per il debug di processi Tez in HDInsight.
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
ms.date: 12/05/2017
ms.author: larryfr
ms.openlocfilehash: b565ef0f7672d1288e922e28551ad3f6ec5b6cb7
ms.sourcegitcommit: 5d3e99478a5f26e92d1e7f3cec6b0ff5fbd7cedf
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 12/06/2017
---
# <a name="use-ambari-views-to-debug-tez-jobs-on-hdinsight"></a>Usare le visualizzazioni di Ambari per il debug di processi Tez in HDInsight

L'interfaccia utente Web di Ambari per HDInsight contiene una visualizzazione Tez che può essere usata per la comprensione e il debug di processi che usano Tez. La visualizzazione Tez consente di visualizzare il processo come grafico di elementi connessi, esaminare ogni elemento e recuperare statistiche e informazioni sulla registrazione.

> [!IMPORTANT]
> I passaggi descritti in questo documento richiedono un cluster HDInsight che usa Linux. Linux è l'unico sistema operativo usato in HDInsight versione 3.4 o successiva. Per altre informazioni, vedere [Componenti e versioni di Hadoop disponibili in HDInsight](hdinsight-component-versioning.md#hdinsight-windows-retirement).

## <a name="prerequisites"></a>Prerequisiti

* Un cluster HDInsight basato su Linux. Per la procedura di creazione di un cluster, vedere [Esercitazione di Hadoop: Introduzione all'uso di Hadoop con Hive in HDInsight in Linux](hadoop/apache-hadoop-linux-tutorial-get-started.md).
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

2. Quando la visualizzazione Tez viene caricata, viene visualizzato un elenco di query Hive che sono attualmente in esecuzione o che sono stati eseguiti nel cluster.

    ![Tutti i DAG](./media/hdinsight-debug-ambari-tez-view/tez-view-home.png)

3. Se è presente una sola voce, è quella relativa alla query eseguita nella sezione precedente. Se si dispone di più voci, è possibile eseguire una ricerca con i campi nella parte superiore della pagina.

4. Selezionare il **ID Query** per una query Hive. Verranno visualizzate informazioni sulla query.

    ![DAG Details](./media/hdinsight-debug-ambari-tez-view/query-details.png)

5. Le schede in questa pagina consentono di visualizzare le informazioni seguenti:

    * **Informazioni sulla query**: informazioni dettagliate sulla query Hive.
    * **Tempistiche**: informazioni sulla durata di ogni fase dell'elaborazione.
    * **Configurazioni**: la configurazione usata per questa query.

    Da __Dettagli query__ è possibile usare i collegamenti per trovare le informazioni sull'__applicazione__ o il __DAG__ per questa query.
    
    * Il collegamento __Applicazione__ consente di visualizzare informazioni sull'applicazione YARN per questa query. Da qui è possibile accedere ai registri dell'applicazione YARN.
    * Il collegamento __DAG__ consente di visualizzare le informazioni su un grafo aciclico diretto per questa query. Da qui è possibile visualizzare una rappresentazione grafica del DAG. È anche possibile trovare informazioni sui vertici all'interno del DAG.

## <a name="next-steps"></a>Passaggi successivi

A questo punto, dopo avere appreso come usare la visualizzazione Tez, è possibile trovare altre informazioni in [Uso di Hive in HDInsight](hadoop/hdinsight-use-hive.md).

Per informazioni tecniche più dettagliate su Tez, vedere la [pagina di Tez in Hortonworks](http://hortonworks.com/hadoop/tez/).

Per altre informazioni sull'uso di Ambari con HDInsight, vedere [Gestire i cluster HDInsight usando l'interfaccia utente Web di Ambari](hdinsight-hadoop-manage-ambari.md)
