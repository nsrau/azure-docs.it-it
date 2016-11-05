---
title: Usare l'interfaccia utente di Tez con HDInsight basato su Windows | Microsoft Docs
description: Informazioni su come usare l'interfaccia utente di Tez per il debug di processi Tez in HDInsight basato su Windows.
services: hdinsight
documentationcenter: ''
author: Blackmist
manager: jhubbard
editor: cgronlun

ms.service: hdinsight
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: big-data
ms.date: 07/19/2016
ms.author: larryfr

---
# Usare l'interfaccia utente di Tez per il debug di processi Tez in HDInsight basato su Windows
L'interfaccia utente di Tez è una pagina Web che può essere usata per la comprensione e il debug di processi che usano Tez come motore di esecuzione nei cluster HDInsight basati su Windows. L'interfaccia utente di Tez consente di visualizzare il processo come grafico di elementi connessi, esaminare ogni elemento e recuperare statistiche e informazioni sulla registrazione.

> [!NOTE]
> Le informazioni contenute in questo documento sono specifiche per i cluster HDInsight basati su Windows. Per informazioni sulla visualizzazione e il debug di Tez in HDInsight basato su Linux, vedere [Usare le visualizzazioni di Ambari per il debug dei processi Tez in HDInsight](hdinsight-debug-ambari-tez-view.md).
> 
> 

## Prerequisiti
* Un cluster HDInsight basato su Windows. Per la procedura di creazione di un nuovo cluster, vedere [Introduzione all'uso di HDInsight basato su Windows](hdinsight-hadoop-tutorial-get-started-windows.md).
  
  > [!IMPORTANT]
  > L'interfaccia utente di Tez è disponibile solo nei cluster HDInsight basati su Windows creati dopo l'8 febbraio 2016.
  > 
  > 
* Un client Desktop remoto basato su Windows.

## Informazioni su Tez
Tez è un framework estendibile per l'elaborazione dati in Hadoop, che garantisce una maggiore velocità rispetto alla tradizionale elaborazione di MapReduce. Per i cluster HDInsight basati su Windows, è un motore facoltativo che è possibile abilitare per Hive usando il comando seguente come parte della query Hive:

    set hive.execution.engine=tez;

Quando riceve il lavoro, Tez crea un grafo aciclico diretto (DAG) che descrive l'ordine di esecuzione delle azioni necessarie per il processo. Le singole azioni sono chiamate vertici ed eseguono una parte dell'intero processo. L'esecuzione vera e propria del lavoro descritta da un vertice è chiamata attività e può essere distribuita in più nodi nel cluster.

### Informazioni sull'interfaccia utente di Tez
L'interfaccia utente di Tez è una pagina Web che fornisce informazioni sui processi in esecuzione o eseguiti in precedenza usando Tez. Consente di visualizzare il DAG generato da Tez, come è distribuito nei cluster, i contatori, ad esempio la memoria usata dalle attività e dai vertici, e le informazioni sugli errori. Può offrire informazioni utili negli scenari seguenti:

* Monitoraggio di processi con esecuzione prolungata, visualizzazione dello stato delle attività di mapping e riduzione.
* Analisi dei dati cronologici per i processi riusciti o non riusciti per capire come migliorare l'elaborazione o perché non è riuscita.

## Generare un DAG
L'interfaccia utente di Tez conterrà dati solo se un processo che usa il motore Tez è attualmente in esecuzione è o stato eseguito in passato. Le query Hive semplici in genere possono essere risolte senza usare Tez, ma quelle più complesse che eseguono operazioni di filtro, raggruppamento, ordinamento, join e così via di solito richiedono Tez.

Usare la procedura seguente per eseguire una query Hive che verrà eseguita con Tez.

1. In un Web browser passare a https://CLUSTERNAME.azurehdinsight.net dove __CLUSTERNAME__è il nome del cluster HDInsight.
2. Dal menu nella parte superiore della pagina selezionare **Hive Editor**. Verrà visualizzata una pagina con la query di esempio seguente.
   
        Select * from hivesampletable
   
    Cancellare la query di esempio e sostituirla con la seguente.
   
        set hive.execution.engine=tez;
        select market, state, country from hivesampletable where deviceplatform='Android' group by market, country, state;
3. Fare clic sul pulsante **Submit**. La sezione **Job Session** nella parte inferiore della pagina visualizzerà lo stato della query. Quando lo stato passa a **Completed**, selezionare il collegamento **View Details** per visualizzare i risultati. L'output **Job Output** dovrebbe essere simile al seguente:
   
        en-GB   Hessen      Germany
        en-GB   Kingston    Jamaica
        en-GB   Nairobi Area    Kenya

## Usare l'interfaccia utente di Tez
> [!NOTE]
> Poiché l'interfaccia utente di Tez è disponibile solo nel desktop dei nodi head del cluster, è necessario usare Desktop remoto per connettersi ai nodi head.
> 
> 

1. Nel [portale di Azure](https://portal.azure.com) selezionare il cluster HDInsight. Nella parte superiore del pannello HDInsight selezionare l'icona **Desktop remoto**. Verrà visualizzato il pannello Desktop remoto.
   
    ![Icona Desktop remoto](./media/hdinsight-debug-tez-ui/remotedesktopicon.png)
2. Nel pannello Desktop remoto selezionare **Connetti** per connettersi al nodo head del cluster. Quando richiesto, usare il nome utente e la password di Desktop remoto per autenticare la connessione.
   
    ![Icona Connetti di Desktop remoto](./media/hdinsight-debug-tez-ui/remotedesktopconnect.png)
   
   > [!NOTE]
   > Se la connettività Desktop remoto non è stata abilitata, specificare nome utente, password e data di scadenza, quindi selezionare **Abilita** per abilitare Desktop remoto. Una volta abilitata, seguire i passaggi precedenti per connettersi.
   > 
   > 
3. Una volta connessi, aprire Internet Explorer sul desktop remoto, selezionare l'icona a forma di ingranaggio nell'angolo in alto a destra del browser e quindi selezionare **Impostazioni Visualizzazione Compatibilità**.
4. Nella parte inferiore di **Impostazioni Visualizzazione Compatibilità** deselezionare la casella di controllo **Visualizza siti Intranet in Visualizzazione Compatibilità** e **Usa elenchi di compatibilità Microsoft** e quindi selezionare **Chiudi**.
5. In Internet Explorer passare a http://headnodehost:8188/tezui/#/. Verrà visualizzata l'interfaccia utente di Tez.
   
    ![Interfaccia utente di Tez](./media/hdinsight-debug-tez-ui/tezui.png)
   
    Quando l'interfaccia utente di Tez viene caricata, verrà visualizzato un elenco di DAG che sono attualmente in esecuzione o che sono stati eseguiti nel cluster. La visualizzazione predefinita include il nome DAG, l'ID, la persona che invia la richiesta, lo stato, l'ora di inizio, l'ora di fine, la durata, l'ID applicazione e la coda. È possibile aggiungere altre colonne usando l'icona a forma di ingranaggio a destra nella pagina.
   
    Se è presente una sola voce, sarà quella relativa alla query eseguita nella sezione precedente. Se sono presenti più voci, è possibile eseguire una ricerca immettendo i criteri di ricerca nei campi sopra i DAG, quindi premere **INVIO**.
6. In **Dag Name** selezionare la voce DAG più recente. Verranno visualizzate le informazioni sul DAG, oltre all'opzione per scaricare uno zip di file JSON contenenti informazioni sul DAG.
   
    ![DAG Details](./media/hdinsight-debug-tez-ui/dagdetails.png)
7. Sopra **DAG Details** sono presenti diversi collegamenti che possono essere usati per visualizzare informazioni sul DAG.
   
   * **DAG Counters** visualizza informazioni sui contatori per questo DAG.
   * **Graphical View** visualizza una rappresentazione grafica di questo DAG.
   * **All Vertices** visualizza un elenco dei vertici in questo DAG.
   * **All Tasks** visualizza un elenco delle attività per tutti i vertici in questo DAG.
   * **All TaskAttempts** visualizza informazioni sui tentativi di eseguire attività per questo DAG.
     
     > [!NOTE]
     > Se si scorre la visualizzazione colonne per Vertices, Tasks e TaskAttempts, si noti che sono presenti collegamenti per visualizzare i **contatori** e per **visualizzare o scaricare i log** per ogni riga.
     > 
     > 
     
     Se si è verificato un errore nel processo, in DAG Details sarà visualizzato lo stato FAILED, con i collegamenti alle informazioni sull'attività non riuscita. Le informazioni di diagnostica verranno visualizzate sotto i dettagli DAG.
8. Selezionare **Graphical View**. Viene visualizzata una rappresentazione grafica del DAG. È possibile posizionare il mouse su ogni vertice nella visualizzazione per accedere alle relative informazioni.
   
    ![Graphical View](./media/hdinsight-debug-tez-ui/dagdiagram.png)
9. Facendo clic su un vertice, verranno caricati i dati di **Vertex Details** per tale elemento. Fare clic sul vertice **Map 1** per visualizzare i dettagli per questo elemento. Selezionare **Confirm** per confermare lo spostamento.
   
    ![Vertex Details](./media/hdinsight-debug-tez-ui/vertexdetails.png)
10. Si noti che ora nella parte superiore della pagina sono presenti i collegamenti relativi ai vertici e alle attività.
    
    > [!NOTE]
    > Per accedere a questa pagina, è anche possibile tornare a **DAG Details**, selezionare **Vertex Details** e quindi selezionare il vertice **Map 1**.
    > 
    > 
    
    * **Vertex Counters** visualizza informazioni sui contatori per questo vertice.
    * **Tasks** visualizza le attività per questo vertice.
    * **TaskAttempts** visualizza informazioni sui tentativi di eseguire attività per questo vertice.
    * **Sources & Sinks** visualizza le origini dati e i sink per questo vertice.
      
      > [!NOTE]
      > Come per il menu precedente, è possibile scorrere la visualizzazione colonne per Tasks, Task Attempts e Sources & Sinks per visualizzare i collegamenti ad altre informazioni per ogni elemento.
      > 
      > 
11. Selezionare **Tasks** (Attività) e quindi selezionare l'elemento denominato **00_000000_\_. Verranno visualizzati i dati di __Task Details** (Dettagli attività) per questa attività. Da questa schermata è possibile visualizzare **Task Counters (Contatori attività) ** e *_Task Attempts (Tentativi attività)\*\_.
    
    ![Dettagli dell'attività](./media/hdinsight-debug-tez-ui/taskdetails.png)

## Passaggi successivi
A questo punto, dopo avere appreso come usare la visualizzazione Tez, è possibile trovare altre informazioni in [Uso di Hive in HDInsight](hdinsight-use-hive.md).

Per informazioni tecniche più dettagliate su Tez, vedere la [pagina di Tez in Hortonworks](http://hortonworks.com/hadoop/tez/).

<!---HONumber=AcomDC_0914_2016-->