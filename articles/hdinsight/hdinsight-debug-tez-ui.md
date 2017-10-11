---
title: Usare l'interfaccia utente di Tez con HDInsight basato su Windows - Azure | Microsoft Docs
description: Informazioni su come usare l'interfaccia utente di Tez per il debug di processi Tez in HDInsight basato su Windows.
services: hdinsight
documentationcenter: 
author: Blackmist
manager: jhubbard
editor: cgronlun
ms.assetid: a55bccb9-7c32-4ff2-b654-213a2354bd5c
ms.service: hdinsight
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: big-data
ms.date: 01/17/2017
ms.author: larryfr
ROBOTS: NOINDEX
ms.openlocfilehash: 3889fa1c3523eb0330cbe3b7640fd8590a5ceadf
ms.sourcegitcommit: f537befafb079256fba0529ee554c034d73f36b0
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 07/11/2017
---
# <a name="use-the-tez-ui-to-debug-tez-jobs-on-windows-based-hdinsight"></a>Usare l'interfaccia utente di Tez per il debug di processi Tez in HDInsight basato su Windows
L'interfaccia utente di Tez è una pagina Web che può essere usata per la comprensione e il debug di processi che usano Tez come motore di esecuzione nei cluster HDInsight basati su Windows. L'interfaccia utente di Tez consente di visualizzare il processo come grafico di elementi connessi, esaminare ogni elemento e recuperare statistiche e informazioni sulla registrazione.

> [!IMPORTANT]
> I passaggi descritti in questo documento richiedono un cluster HDInsight che usa Windows. Linux è l'unico sistema operativo usato in HDInsight versione 3.4 o successiva. Per altre informazioni, vedere la sezione relativa al [ritiro di HDInsight in Windows](hdinsight-component-versioning.md#hdinsight-windows-retirement).

## <a name="prerequisites"></a>Prerequisiti
* Un cluster HDInsight basato su Windows. Per la procedura di creazione di un nuovo cluster, vedere [Introduzione all'uso di HDInsight basato su Windows](hdinsight-hadoop-tutorial-get-started-windows.md).

  > [!IMPORTANT]
  > L'interfaccia utente di Tez è disponibile solo nei cluster HDInsight basati su Windows creati dopo l'8 febbraio 2016.
  >
  >
* Un client Desktop remoto basato su Windows.

## <a name="understanding-tez"></a>Informazioni su Tez
Tez è un framework estendibile per l'elaborazione dati in Hadoop, che garantisce una maggiore velocità rispetto alla tradizionale elaborazione di MapReduce. Per i cluster HDInsight basati su Windows, è un motore facoltativo che è possibile abilitare per Hive usando il comando seguente come parte della query Hive:

    set hive.execution.engine=tez;

Quando riceve il lavoro, Tez crea un grafo aciclico diretto (DAG) che descrive l'ordine di esecuzione delle azioni necessarie per il processo. Le singole azioni sono chiamate vertici ed eseguono una parte dell'intero processo. L'esecuzione vera e propria del lavoro descritta da un vertice è chiamata attività e può essere distribuita in più nodi nel cluster.

### <a name="understanding-the-tez-ui"></a>Informazioni sull'interfaccia utente di Tez
L'interfaccia utente di Tez è una pagina Web che fornisce informazioni sui processi in esecuzione o eseguiti in precedenza usando Tez. Consente di visualizzare il DAG generato da Tez, come è distribuito nei cluster, i contatori, ad esempio la memoria usata dalle attività e dai vertici, e le informazioni sugli errori. Può offrire informazioni utili negli scenari seguenti:

* Monitoraggio di processi con esecuzione prolungata, visualizzazione dello stato delle attività di mapping e riduzione.
* Analisi dei dati cronologici per i processi riusciti o non riusciti per capire come migliorare l'elaborazione o perché non è riuscita.

## <a name="generate-a-dag"></a>Generare un DAG
L'interfaccia utente di Tez conterrà dati solo se un processo che usa il motore Tez è attualmente in esecuzione è o stato eseguito in passato. Le query Hive semplici in genere possono essere risolte senza usare Tez, ma quelle più complesse che eseguono operazioni di filtro, raggruppamento, ordinamento, join e così via di solito richiedono Tez.

Usare la procedura seguente per eseguire una query Hive che verrà eseguita con Tez.

1. In un Web browser passare a https://NOMECLUSTER.azurehdinsight.net, dove **NOMECLUSTER** è il nome del cluster HDInsight.
2. Nel menu nella parte superiore della pagina selezionare **Editor Hive**. Verrà visualizzata una pagina con la query di esempio seguente.

        Select * from hivesampletable

    Cancellare la query di esempio e sostituirla con la seguente.

        set hive.execution.engine=tez;
        select market, state, country from hivesampletable where deviceplatform='Android' group by market, country, state;
3. Selezionare il pulsante **Invia**. La sezione **Sessione processo** nella parte inferiore della pagina visualizzerà lo stato della query. Quando lo stato diventa **Operazione completata**, selezionare il collegamento **Visualizza dettagli** per visualizzare i risultati. L'**output del processo** dovrebbe essere simile al seguente:

        en-GB   Hessen      Germany
        en-GB   Kingston    Jamaica
        en-GB   Nairobi Area    Kenya

## <a name="use-the-tez-ui"></a>Usare l'interfaccia utente di Tez
> [!NOTE]
> Poiché l'interfaccia utente di Tez è disponibile solo nel desktop dei nodi head del cluster, è necessario usare Desktop remoto per connettersi ai nodi head.
>
>

1. Nel [portale di Azure](https://portal.azure.com)selezionare il cluster HDInsight. Nella parte superiore del pannello HDInsight selezionare l'icona **Desktop remoto**. Verrà visualizzato il pannello Desktop remoto.

    ![Icona Desktop remoto](./media/hdinsight-debug-tez-ui/remotedesktopicon.png)
2. Nel pannello Desktop remoto selezionare **Connetti** per connettersi al nodo head del cluster. Quando richiesto, usare il nome utente e la password di Desktop remoto per autenticare la connessione.

    ![Icona Connetti di Desktop remoto](./media/hdinsight-debug-tez-ui/remotedesktopconnect.png)

   > [!NOTE]
   > Se la connettività Desktop remoto non è stata abilitata, specificare nome utente, password e data di scadenza e quindi selezionare **Abilita** per abilitare Desktop remoto. Una volta abilitata, seguire i passaggi precedenti per connettersi.
   >
   >
3. Dopo aver stabilito la connessione, aprire Internet Explorer sul desktop remoto, selezionare l'icona a forma di ingranaggio in alto a destra nel browser e quindi selezionare **Impostazioni Visualizzazione Compatibilità**.
4. Nella parte inferiore di **Impostazioni Visualizzazione Compatibilità** deselezionare le caselle di controllo **Visualizza siti Intranet in Visualizzazione Compatibilità** e **Usa elenchi di compatibilità Microsoft** e quindi selezionare **Chiudi**.
5. In Internet Explorer passare a http://headnodehost:8188/tezui/#/. Verrà visualizzata l'interfaccia utente di Tez.

    ![Interfaccia utente di Tez](./media/hdinsight-debug-tez-ui/tezui.png)

    Quando l'interfaccia utente di Tez viene caricata, verrà visualizzato un elenco di DAG che sono attualmente in esecuzione o che sono stati eseguiti nel cluster. La visualizzazione predefinita include il nome DAG, l'ID, la persona che invia la richiesta, lo stato, l'ora di inizio, l'ora di fine, la durata, l'ID applicazione e la coda. È possibile aggiungere altre colonne usando l'icona a forma di ingranaggio a destra nella pagina.

    Se è presente una sola voce, sarà quella relativa alla query eseguita nella sezione precedente. Se sono presenti più voci, è possibile eseguire una ricerca immettendo i criteri di ricerca nei campi sopra i DAG e quindi premendo **INVIO**.
6. In **Dag Name** (Nome DAG) selezionare la voce DAG più recente. Verranno visualizzate le informazioni sul DAG, oltre all'opzione per scaricare uno zip di file JSON contenenti informazioni sul DAG.

    ![DAG Details](./media/hdinsight-debug-tez-ui/dagdetails.png)
7. Sopra **DAG Details** (Dettagli DAG) sono presenti diversi collegamenti che possono essere usati per visualizzare informazioni sul DAG.

   * **DAG Counters** (Contatori DAG) visualizza informazioni sui contatori per il DAG.
   * **Graphical View** (Visualizzazione grafica) visualizza una rappresentazione grafica del DAG.
   * **All Vertices** (Tutti i vertici) visualizza un elenco dei vertici nel DAG.
   * **All Tasks** (Tutte le attività) visualizza un elenco delle attività per tutti i vertici nel DAG.
   * **All TaskAttempts** (Tutti i tentativi di attività) visualizza informazioni sui tentativi di eseguire attività per il DAG.

     > [!NOTE]
     > Scorrendo la visualizzazione colonne per vertici, attività e tentativi di attività, si noti che sono presenti collegamenti per visualizzare i **contatori** e per **visualizzare o scaricare i log** per ogni riga.
     >
     >

     Se si è verificato un errore nel processo, in DAG Details sarà visualizzato lo stato FAILED, con i collegamenti alle informazioni sull'attività non riuscita. Le informazioni di diagnostica verranno visualizzate sotto i dettagli DAG.
8. Selezionare **Graphical View** (Visualizzazione grafica). Viene visualizzata una rappresentazione grafica del DAG. È possibile posizionare il mouse su ogni vertice nella visualizzazione per accedere alle relative informazioni.

    ![Graphical View](./media/hdinsight-debug-tez-ui/dagdiagram.png)
9. Facendo clic su un vertice, verranno caricati i dati di **Vertex Details** (Dettagli vertice) per tale elemento. Fare clic sul vertice **Map 1** (Mappa 1) per visualizzare i dettagli per questo elemento. Selezionare **Confirm** (Conferma) per confermare lo spostamento.

    ![Vertex Details](./media/hdinsight-debug-tez-ui/vertexdetails.png)
10. Si noti che ora nella parte superiore della pagina sono presenti i collegamenti relativi ai vertici e alle attività.

    > [!NOTE]
    > È possibile accedere a questa pagina anche tornando a **DAG Details** (Dettagli DAG) e selezionando **Vertex Details** (Dettagli vertice) e quindi il vertice **Map 1** (Mappa 1).
    >
    >

    * **Vertex Counters** (Contatori vertice) visualizza informazioni sui contatori per il vertice.
    * **Tasks** (Attività) visualizza le attività per il vertice.
    * **Task Attempts** (Tentativi attività) visualizza informazioni sui tentativi di eseguire attività per il vertice.
    * **Sources & Sinks** (Origini e sink) visualizza le origini dati e i sink per il vertice.

      > [!NOTE]
      > Come per il menu precedente, è possibile scorrere la visualizzazione colonne per Tasks, Task Attempts e Sources & Sinks per visualizzare i collegamenti ad altre informazioni per ogni elemento.
      >
      >
11. Selezionare **Tasks** (Attività) e quindi l'elemento denominato **00_000000**. Verranno visualizzati i dati di **Task Details** (Dettagli attività) per questa attività. Da questa schermata è possibile visualizzare **Task Counters** (Contatori attività) e **Task Attempts** (Tentativi attività).

    ![Dettagli dell'attività](./media/hdinsight-debug-tez-ui/taskdetails.png)

## <a name="next-steps"></a>Passaggi successivi
A questo punto, dopo avere appreso come usare la visualizzazione Tez, è possibile trovare altre informazioni in [Uso di Hive in HDInsight](hdinsight-use-hive.md).

Per informazioni tecniche più dettagliate su Tez, vedere la [pagina di Tez in Hortonworks](http://hortonworks.com/hadoop/tez/).
