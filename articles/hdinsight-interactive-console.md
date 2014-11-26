<properties linkid="manage-services-hdinsight-howto-work-with-the-interactive-console" urlDisplayName="Interactive Console" pageTitle="How to work with the interactive console in HDInsight | Azure" metaKeywords="" description="In this guide, you'll learn how to perform common tasks such as file upload, run jobs, and visualize data using the interactive console for HDInsight Service." metaCanonical="" services="hdinsight" documentationCenter="" title="HDInsight Interactive JavaScript and Hive Consoles" authors="bradsev" solutions="" manager="paulettm" editor="cgronlun" />

Console JavaScript e Hive interattive per HDInsight
===================================================

Il servizio Microsoft Azure HDInsight include console interattive per JavaScript e Hive. Tali console offrono un'esperienza REPL (Read-Evaluate-Print Loop, Loop di lettura-valutazione-stampa) semplice e interattiva, che consente agli utenti di immettere espressioni, valutarle e quindi eseguire query e visualizzare immediatamente i risultati di un processo MapReduce. La console JavaScript esegue istruzioni in Pig Latin. La console Hive valuta istruzioni in Hive QL (Hive Query Language). Entrambi i tipi di istruzioni vengono compilate in programmi MapReduce. La gestione di processi Hadoop in tali console risulta molto più semplice rispetto alla connessione remota al nodo head del cluster Hadoop e all'utilizzo diretto dei programmi MapReduce.

**Console JavaScript**: shell dei comandi che offre un'interfaccia di tipo Fluent per l'ecosistema Hadoop. L'interfaccia di tipo Fluent utilizza un metodo per il concatenamento di istruzioni che inoltra il contesto di una chiamata in una sequenza alla chiamata successiva in tale sequenza. La console JavaScript offre gli elementi seguenti:

-   Accesso al cluster Hadoop, alle rispettive risorse e ai comandi HDFS (Hadoop Distributed File System).
-   Gestione e manipolazione dei dati in ingresso e in uscita dal cluster Hadoop.
-   Un'interfaccia di tipo Fluent per la valutazione di istruzioni in Pig Latin e JavaScript per definire una serie di programmi MapReduce, in modo da creare flussi di lavoro per l'elaborazione dei dati.

**Console Hive**: Hive è un framework di data warehouse, basato su Hadoop, che offre la gestione dei dati, l'esecuzione di query e l'analisi dei dati. Utilizza HiveQL, un dialetto SQL, per l'esecuzione di query sui dati archiviati in un cluster Hadoop. La console Hive offre gli elementi seguenti:

-   Accesso al cluster Hadoop, alle rispettive risorse e ai comandi HDFS.
-   Un'implementazione del framework Hive che consente di eseguire istruzioni HiveQL in un cluster Hadoop.
-   Un modello di database relazionale per HDFS che consente di interagire con i dati archiviati in un file system distribuito come se i dati fossero archiviati in tabelle. La console JavaScript utilizza Pig Latin, un linguaggio di flusso di dati, e la console Hive utilizza HiveQL, un linguaggio di query.

Tendenzialmente, gli utenti con maggiore esperienza degli approcci basati su script preferiranno utilizzare Pig e la console JavaScript, in cui una sequenza di trasformazioni concatenate (o di tipo Fluent) viene utilizzata per definire un flusso di lavoro per l'elaborazione dei dati. Tale console è inoltre consigliabile se si utilizzano dati decisamente non strutturati.

Gli utenti con maggiore esperienza in ambito di SQL e dell'ambiente di database relazionali preferiranno tendenzialmente Hive e la rispettiva console. Grazie all'utilizzo di uno schema e di un'astrazione di tabella in Hive, l'esperienza utente sarà infatti molto simile all'esperienza tipica in ambito RDBMS.

Pig e Hive offrono linguaggi di livello superiore, compilati in programmi MapReduce scritti in Java ed eseguiti su HDFS. Se si desidera ottenere un controllo molto preciso o prestazioni elevate, sarà necessario scrivere direttamente i programmi MapReduce.

Contenuto dell'esercitazione:
-----------------------------

-   [Utilizzo della console JavaScript per l'esecuzione di un processo MapReduce](#runjob)
-   [Utilizzo della console JavaScript per la visualizzazione grafica dei risultati](#displayresults)
-   [Utilizzo della console Hive per l'esportazione dei risultati in una tabella Hive](#createhivetable)
-   [Utilizzo della console Hive per l'esecuzione di query sui dati della tabella Hive](#queryhivetable)

Utilizzo della console JavaScript per l'esecuzione di un processo MapReduce
---------------------------------------------------------------------------

In questa sezione verrà utilizzata la console JavaScript per l'esecuzione dell'esempio WordCount incluso nel servizio HDInsight. La query JavaScript eseguita in questa sezione utilizza l'API Fluent su più livelli su Pig, inclusa nella console interattiva. Il file di testo analizzato è l'edizione Project Gutenberg eBook di *The Notebooks of Leonardo Da Vinci*. Il filtro specificato consente di includere nei risultati del processo MapReduce solo le dieci parole che ricorrono più spesso.

1.  Accedere al [portale di gestione](https://manage.windowsazure.com).
2.  Fare clic su **HDINSIGHT**. Verrà visualizzato un elenco dei cluster Hadoop distribuiti.
3.  Fare clic sul nome del cluster HDInsight a cui ci si desidera connettere.
4.  Fare clic su **Manage Cluster**.
5.  Immettere le credenziali e quindi fare clic su **Log On**.
6.  Nel portale di HDInsight fare clic su **Samples**.

    ![HDI.Tiles.Samples](./media/hdinsight-interactive-console/HDI.TileSamples.PNG)

7.  Nella pagina **Hadoop Sample Gallery** fare clic sul riquadro **WordCount**.
8.  Fare clic su **WordCount.js** in alto a destra e quindi salvare il file in una directory locale, ad esempio la cartella ../downloads.

    ![HDI.JsConsole.WordCountDownloads](./media/hdinsight-interactive-console/HDI.JsConsole.WordCountDownloads.PNG)

9.  Fare clic su **Azure HDInsight** nell'angolo superiore sinistro per tornare alla pagina del dashboard del cluster.
10. Fare clic su **Interactive Cluster** per visualizzare la console JavaScript.

    ![HDI.Tiles.InteractiveConsole](./media/hdinsight-interactive-console/HDI.TileInteractiveConsole.PNG)

11. Fare clic su **JavaScript** in alto a destra.
12. Eseguire il comando seguente:

    fs.put()

13. Immettere i parametri seguenti nella finestra **Upload a file**:

    -   **Source:** \_..\\downloads\\Wordcount.js 
    - **Destination:** ./WordCount.js/

    ![HDI.JsConsole.UploadJs](./media/hdinsight-interactive-console/HDI.JsConsole.UploadJs.PNG)

    Passare alla posizione in cui si trova il file WordCount.js. Sarà necessario specificare il percorso completo. Il punto singolo all'inizio del percorso di destinazione è necessario come parte dell'indirizzo relativo in HDFS.

14. Fare clic sul pulsante **Upload**.

15. Eseguire i comandi seguenti per includere il file nell'elenco e visualizzare il contenuto:

        #ls
        #cat WordCount.js

    ![HDI.JsConsole.JsCode](./media/hdinsight-interactive-console/HDI.JsConsole.JsCode.PNG)

    Si noti che la funzione Map JavaScript rimuove le lettere maiuscole dal testo utilizzando il metodo "toLowerCase()" prima di contare il numero di occorrenze di una parola nella funzione Reduce.

16. Eseguire il comando seguente per includere nell'elenco il file di dati che verrà elaborato dal processo WordCount di MapReduce:

        \#ls /example/data/gutenberg

1.  Digitare il comando seguente per eseguire il programma MapReduce:

        pig.from("/example/data/gutenberg/davinci.txt").mapReduce("/user/admin/WordCount.js", "word, count:long").orderBy("count DESC").take(10).to("DaVinciTop10Words")

    Sostituire admin con il nome utente di accesso corrente.

    Si noti che le istruzioni sono "concatenate" mediante l'operatore punto e che il nome del file di output è *DaVinciTop10Words*. Nella sezione seguente si accederà al file di output.

    Al termine dell'esecuzione dovrebbe essere visualizzato quanto segue:

        pig.from("/example/data/gutenberg/davinci.txt").mapReduce("/user/admin/WordCount.js", "word, count:long").orderBy("count DESC").take(10).to("DaVinciTop10Words")
        2013-04-25 18:54:28,116 [main] INFO  org.apache.pig.backend.hadoop.executionengine.mapReduceLayer.MapReduceLauncher - Success! Visualizzazione log

2.  Se si desidera osservare l'avanzamento del processo, scorrere verso destra e quindi fare clic su **View Log**. Se non è possibile completare il processo, il log includerà anche informazioni di diagnostica. Il messaggio seguente verrà visualizzato alla fine del log al completamento del processo:

        [main] INFO org.apache.pig.backend.hadoop.executionengine.mapReduceLayer.MapReduceLauncher - Success! followed by a link to the log file.

3.  Eseguire il comando seguente per includere nell'elenco il file di output:

        #ls

    Si noti che è stata creata una cartella DavinciTop10Words.

Utilizzo della console JavaScript per la visualizzazione grafica dei risultati
------------------------------------------------------------------------------

Nella sezione precedente è stato eseguito un processo MapReduce per recuperare le 10 parole principali da un file di testo. Il file di output è ./DaVinciTop10Words.

1.  Eseguire il comando seguente per visualizzare i risultati nella directory DaVinciTop10Words:

         file = fs.read("DaVinciTop10Words")

    Il risultato sarà analogo al seguente:

         js> file=fs.read("DaVinciTop10Words")
         the    22966
         of 11228
         and    8428
         in 5737
         to 5296
         a  4791
         is 4261
         it 3073
         that   2903
         which  2544

2.  Eseguire il comando seguente per analizzare i contenuti del file in un file di dati:

         data = parse(file.data, "word, count:long")

3.  Eseguire il comando seguente per tracciare i dati:

         graph.bar(data)

    ![HDI.JsConsole.BarGraphTop10Words](./media/hdinsight-interactive-console/HDI.JsConsole.BarGraphTop10Words.PNG)

Utilizzo della console Hive per l'esportazione dei risultati in una tabella Hive
--------------------------------------------------------------------------------

In questa sezione viene presentata la console Hive interattiva. Verrà creata una tabella Hive dall'output del processo MapReduce. Nella sezione successiva viene illustrato come eseguire query sui dati in tale tabella.

**Creare la tabella Hive**

1.  Fare clic su Hive in alto a destra per aprire la console Hive.

2.  Immettere il comando seguente per creare una tabella con due colonne denominata *DaVinciWordCountTable* dall'output dell'esempio WordCount salvato nella cartella "DaVinciTop10Words":

         CREATE EXTERNAL TABLE DaVinciWordCountTable     
         (word STRING,
         count INT) 
         ROW FORMAT DELIMITED FIELDS TERMINATED BY '\t' 
         STORED AS TEXTFILE LOCATION '/user/admin/DaVinciTop10Words';   

    Sostituire admin con il nome utente di accesso.

    Si noti che la tabella viene creata come tabella di tipo EXTERNAL, in modo che la cartella a cui si fa riferimento rimanga indipendente dalla tabella. Si noti inoltre che è necessario specificare solo la cartella in cui si trova il file di output, non il nome del file stesso.

3.  Fare clic su **EVALUATE** in basso a sinistra.

4.  Immettere i comandi seguenti per confermare che è stata creata la tabella con due colonne:

         SHOW TABLES;
         DESCRIBE DaVinciWordCountTable;

5.  Fare clic su **EVALUATE**.

    ![HDI.Hive.ShowDescribeTable][hdi-hive-showdescribetable]

Utilizzo della console Hive per l'esecuzione di query sui dati della tabella Hive
---------------------------------------------------------------------------------

1.  Eseguire il comando seguente per eseguire la query relativa alle parole con i primi dieci numeri di occorrenze:

         SELECT word, count
         FROM DaVinciWordCountTable
         ORDER BY count DESC LIMIT 10

    The results of this query are:

         SELECT word, count FROM DaVinciWordCountTable ORDER BY count DESC LIMIT 10
         the 22966
         of 11228
         and 8428
         in 5737
         to 5296
         a 4791
         is 4261
         it 3073
         that 2903
         which 2544

Passaggi successivi
-------------------

È stato illustrato come eseguire un processo Hadoop dalla console JavaScript interattiva e come esaminare i risultati da un processo utilizzando tale console. È stato inoltre illustrato come utilizzare la console Hive interattiva per esaminare ed elaborare i risultati di un processo Hadoop tramite la creazione e l'esecuzione di query relative a una tabella contenente l'output da un programma MapReduce. Sono stati mostrati esempi di utilizzo di istruzioni Pig Latin e Hive QL nelle console. È stato infine illustrato il modo in cui la natura interattiva di tipo REPL delle console JavaScript e Hive semplifica l'utilizzo di un cluster Hadoop. Per ulteriori informazioni, vedere gli articoli seguenti:

-   [Utilizzo di Pig con HDInsight](/it-it/manage/services/hdinsight/using-pig-with-hdinsight/)
-   [Utilizzo di Hive con HDInsight](/it-it/manage/services/hdinsight/using-hive-with-hdinsight/)
-   [Utilizzo di MapReduce con HDInsight](/it-it/manage/services/hdinsight/using-mapreduce-with-hdinsight/)

[hdi-hive-showdescribetable]: ./media/hdinsight-interactive-console/HDI.Hive.ShowDescribeTable.PNG "Hive Table Confirmation")

