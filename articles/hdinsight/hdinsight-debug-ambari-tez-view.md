<properties
pageTitle="Usare la visualizzazione Tez di Ambari con HDInsight | Azure"
description="Informazioni sull'uso della visualizzazione Tez di Ambari per il debug di processi Tez in HDInsight."
services="hdinsight"
documentationCenter=""
authors="Blackmist"
manager="paulettm"
editor="cgronlun"/>

<tags
ms.service="hdinsight"
ms.devlang="na"
ms.topic="article"
ms.tgt_pltfrm="na"
ms.workload="big-data"
ms.date="07/19/2016"
ms.author="larryfr"/>

# Usare le visualizzazioni di Ambari per il debug di processi Tez in HDInsight

L'interfaccia utente Web di Ambari per HDInsight contiene una visualizzazione Tez che può essere usata per la comprensione e il debug di processi che usano Tez come motore di esecuzione. La visualizzazione Tez consente di visualizzare il processo come grafico di elementi connessi, esaminare ogni elemento e recuperare statistiche e informazioni sulla registrazione.

> [AZURE.NOTE] Le informazioni contenute in questo documento sono specifiche per i cluster HDInsight basati su Linux. Per informazioni sul debug di processi Tez con HDInsight basato su Windows, vedere [Usare l'interfaccia utente di Tez per il debug di processi Tez in HDInsight basato su Windows](hdinsight-debug-tez-ui.md).

##Prerequisiti

* Un cluster HDInsight basato su Linux. Per la procedura di creazione di un nuovo cluster, vedere [Esercitazione di Hadoop: Introduzione all'uso di Hadoop con Hive in HDInsight in Linux](hdinsight-hadoop-linux-tutorial-get-started.md).

* Un moderno Web browser che supporta HTML5.

##Informazioni su Tez

Tez è un framework estendibile per l'elaborazione dati in Hadoop, che garantisce una maggiore velocità rispetto alla tradizionale elaborazione di MapReduce. Per i cluster HDInsight basati su Linux si tratta del motore predefinito per Hive.

Quando riceve il lavoro, Tez crea un grafo aciclico diretto (DAG) che descrive l'ordine di esecuzione delle azioni necessarie per il processo. Le singole azioni sono chiamate vertici ed eseguono una parte dell'intero processo. L'esecuzione vera e propria del lavoro descritta da un vertice è chiamata attività e può essere distribuita in più nodi nel cluster.

###Informazioni sulla visualizzazione Tez

La visualizzazione Tez fornisce informazioni sui processi in esecuzione o eseguiti in precedenza usando Tez. Consente di visualizzare il DAG generato da Tez, come è distribuito nei cluster, i contatori, ad esempio la memoria usata dalle attività e dai vertici, e le informazioni sugli errori. Può offrire informazioni utili negli scenari seguenti:

* Monitoraggio di processi con esecuzione prolungata, visualizzazione dello stato delle attività di mapping e riduzione.

* Analisi dei dati cronologici per i processi riusciti o non riusciti per capire come migliorare l'elaborazione o perché non è riuscita.

##Generare un DAG

La visualizzazione Tez conterrà dati solo se un processo che usa il motore Tez è attualmente in esecuzione è o stato eseguito in passato. Le query Hive semplici in genere possono essere risolte senza usare Tez, ma quelle più complesse che eseguono operazioni di filtro, raggruppamento, ordinamento, join e così via di solito richiedono Tez.

Usare la procedura seguente per eseguire una query Hive che verrà eseguita con Tez.

1. In un Web browser passare a https://CLUSTERNAME.azurehdinsight.net dove __CLUSTERNAME__è il nome del cluster HDInsight.

2. Dal menu nella parte superiore della pagina selezionare l'icona delle __visualizzazioni__. L'icona ha l'aspetto di una serie di quadrati. Nell'elenco a discesa visualizzato, selezionare __Hive View__.

    ![Selezione della visualizzazione Hive](./media/hdinsight-debug-ambari-tez-view/selecthive.png)

3. Quando viene caricata la visualizzazione Hive, incollare il codice seguente nell'editor di query e quindi fare clic su __execute__.

        select market, state, country from hivesampletable where deviceplatform='Android' group by market, country, state;
    
    Al termine del processo, l'output verrà visualizzato nella sezione __Query Process Results__. I risultati dovrebbero essere simili a quanto segue:
    
        market  state       country
        en-GB   Hessen      Germany
        en-GB   Kingston    Jamaica
        
4. Selezionare la scheda __Log__. Verranno restituite informazioni simili a quanto segue:
    
        INFO : Session is already open
        INFO :

        INFO : Status: Running (Executing on YARN cluster with App id application_1454546500517_0063)

    Salvare il valore __App id__, che verrà usato nella sezione successiva.

##Usare la visualizzazione Tez

1. Dal menu nella parte superiore della pagina selezionare l'icona delle __visualizzazioni__. Nell'elenco a discesa visualizzato, selezionare __Tez View__.

    ![Selezione della visualizzazione Tez](./media/hdinsight-debug-ambari-tez-view/selecttez.png)

2. Quando la visualizzazione Tez viene caricata, verrà visualizzato un elenco di DAG che sono attualmente in esecuzione o che sono stati eseguiti nel cluster. La visualizzazione predefinita include il nome DAG, l'ID, la persona che invia la richiesta, lo stato, l'ora di inizio, l'ora di fine, la durata, l'ID applicazione e la coda. È possibile aggiungere altre colonne usando l'icona a forma di ingranaggio a destra nella pagina.

    ![Tutti i DAG](./media/hdinsight-debug-ambari-tez-view/alldags.png)

3. Se è presente una sola voce, sarà quella relativa alla query eseguita nella sezione precedente. Se sono presenti più voci, è possibile eseguire la ricerca immettendo l'ID applicazione nel campo __Application ID__ e premendo INVIO.

4. Selezionare il nome del DAG nella colonna __Dag Name__. Verranno visualizzate le informazioni sul DAG, oltre all'opzione per scaricare uno zip di file JSON contenenti informazioni sul DAG.

    ![DAG Details](./media/hdinsight-debug-ambari-tez-view/dagdetails.png)

5. Sopra __DAG Details__ sono presenti diversi collegamenti che possono essere usati per visualizzare informazioni sul DAG.

    * __DAG Counters__ visualizza informazioni sui contatori per questo DAG.
    
    * __Graphical View__ visualizza una rappresentazione grafica di questo DAG.
    
    * __All Vertices__ visualizza un elenco dei vertici in questo DAG.
    
    * __All Tasks__ visualizza un elenco delle attività per tutti i vertici in questo DAG.
    
    * __All TaskAttempts__ visualizza informazioni sui tentativi di eseguire attività per questo DAG.
    
    > [AZURE.NOTE] Se si scorre la visualizzazione colonne per Vertices, Tasks e TaskAttempts, si noti che sono presenti collegamenti per visualizzare i __contatori__ e per __visualizzare o scaricare i log__ per ogni riga.

    Se si è verificato un errore nel processo, in DAG Details sarà visualizzato lo stato FAILED, con i collegamenti alle informazioni sull'attività non riuscita. Le informazioni di diagnostica verranno visualizzate sotto i dettagli DAG.
    
    ![Schermata dei dettagli DAG che riporta i dettagli di un errore](./media/hdinsight-debug-ambari-tez-view/faileddag.png)

7. Selezionare __Graphical View__. Viene visualizzata una rappresentazione grafica del DAG. È possibile posizionare il mouse su ogni vertice nella visualizzazione per accedere alle relative informazioni.

    ![Graphical View](./media/hdinsight-debug-ambari-tez-view/dagdiagram.png)

8. Facendo clic su un vertice, verranno caricati i dati di __Vertex Details__ per tale elemento. Fare clic sul vertice __Map 1__ per visualizzare i dettagli per questo elemento.

    ![Vertex Details](./media/hdinsight-debug-ambari-tez-view/vertexdetails.png)

9. Si noti che ora nella parte superiore della pagina sono presenti i collegamenti relativi ai vertici e alle attività.

    > [AZURE.NOTE] Per accedere a questa pagina, è anche possibile tornare a __DAG Details__, selezionare __Vertex Details__ e quindi selezionare il vertice __Map 1__.

    * __Vertex Counters__ visualizza informazioni sui contatori per questo vertice.
    
    * __Tasks__ visualizza le attività per questo vertice.
    
    * __TaskAttempts__ visualizza informazioni sui tentativi di eseguire attività per questo vertice.
    
    * __Sources & Sinks__ visualizza le origini dati e i sink per questo vertice.

    > [AZURE.NOTE] Come per il menu precedente, è possibile scorrere la visualizzazione colonne per Tasks, Task Attempts e Sources & Sinks per visualizzare i collegamenti ad altre informazioni per ogni elemento.

10. Selezionare __Tasks__ (Attività) e quindi selezionare l'elemento denominato __00_000000_\_. Verranno visualizzati i dati di __Task Details__ (Dettagli attività) per questa attività. Da questa schermata è possibile visualizzare __Task Counters (Contatori attività) __ e __Task Attempts (Tentativi attività)\_\_.

    ![Dettagli dell'attività](./media/hdinsight-debug-ambari-tez-view/taskdetails.png)

##Passaggi successivi

A questo punto, dopo avere appreso come usare la visualizzazione Tez, è possibile trovare altre informazioni in [Uso di Hive in HDInsight](hdinsight-use-hive.md).

Per informazioni tecniche più dettagliate su Tez, vedere la [pagina di Tez in Hortonworks](http://hortonworks.com/hadoop/tez/).

Per altre informazioni sull'uso di Ambari con HDInsight, vedere [Gestire i cluster HDInsight usando l'interfaccia utente Web di Ambari](hdinsight-hadoop-manage-ambari.md).

<!---HONumber=AcomDC_0720_2016-->