<properties title="How to use Giraph with HDInsight" pageTitle="How to use Apache Giraph with Azure HDInsight" description="Learn how to use Apache Giraph to perform graph processing with Azure HDInsight" metaKeywords="Azure HDInsight Apache Giraph, hdinsight giraph, hdinsight graph, hadoop giraph, azure hadoop, hadoop graph" services="hdinsight" solutions="big-data" documentationCenter="" authors="larryfr" videoId="" scriptId="" />

<tags ms.service="hdinsight" ms.workload="big-data" ms.tgt_pltfrm="na" ms.devlang="na" ms.topic="article" ms.date="08/14/2014" ms.author="larryfr"></tags>

## Informazioni sull'uso di Apache Giraph con Azure HDInsight (Hadoop)

[Apache Giraph][Apache Giraph] consente di elaborare grafici con Hadoop e può essere usato con Azure HDInsight.

È possibile usare i grafici per modellare le relazioni tra gli oggetti, ad esempio le connessioni tra router in una rete di grandi dimensioni, come Internet, oppure le relazioni tra persone iscritte a social network, come nel cosiddetto grafico dei social network. Grazie all'elaborazione del grafico è possibile ottenere informazioni dettagliate sulle relazioni tra gli oggetti in un grafico e in particolare di:

-   Identificare possibili amici sulla base delle relazioni correnti

-   Identificare la route più breve tra due computer di una rete

-   Calcolare la posizione in classifica di pagine Web

## Contenuto dell'esercitazione

-   [Compilare e distribuire Apache Giraph in un cluster HDInsight][Compilare e distribuire Apache Giraph in un cluster HDInsight]

-   [Eseguire l'esempio SimpleShortestPathsComputation][Eseguire l'esempio SimpleShortestPathsComputation]

    Per un elenco di altri esempi forniti con Giraph, vedere la pagina relativa al [pacchetto org.apache.giraph.examples][pacchetto org.apache.giraph.examples].

-   [Risolvere gli eventuali problemi rilevati][Risolvere gli eventuali problemi rilevati]

## Requisiti

-   Un cluster HDInsight di Azure, versione 3.0 o 3.1

-   [Git][Git]

-   Java 1.6

-   [Maven][Maven] 3 o versioni successive

## <span id="build"></span></a>Compilare e distribuire Giraph

Giraph non viene fornito nel cluster HDInsight e quindi deve essere compilato dal codice sorgente. Per altre informazioni sulla compilazione di Giraph, vedere il [repository Giraph][repository Giraph].

1.  Alla data corrente (14/07/2014) per poter usare Giraph con l'archivio file WASB usato da HDInsight è necessaria una patch che è stata inviata al progetto Apache Giraph, ma non è stata ancora accettata. Scaricare la patch dalla sezione **attachments** di [GIRAPH-930][GIRAPH-930] e salvarla nell'unità locale come **giraph-930.diff**.

2.  A una riga di comando usare il comando Git seguente per creare un clone del repository Giraph.

        git clone https://github.com/apache/giraph.git

3.  Modificare le directory nella directory **giraph** creata durante l'operazione di clonazione al passaggio 2.

        cd giraph

4.  Unire la patch nel repository locale usando il comando seguente.

        git apply giraph-930.diff

    Sostituire **giraph-930.diff** il percorso del file creato al passaggio 1.

5.  Compilare Giraph per la versione del cluster HDInsight usando uno dei comandi seguenti.

    -   Per **HDInsight 3.0** (Hadoop 2.2)

            mvn package -Phadoop_0.20.203 - DskipTests

    -   Per **HDInsight 3.1** (Hadoop 2.4)

            mvn package -Phadoop_0.23 -DskipTests

    Una volta completata la compilazione, il file JAR di esempio è disponibile in **\\giraph\\giraph-examples\\target**.

6.  Caricare il file JAR di esempio nell'archivio principale del cluster HDInsight usando [Azure PowerShell][Azure PowerShell] e [HDInsight-Tools][HDInsight-Tools].

        Add-HDInsightFile giraph-examples-1.1.0-SNAPSHOT-for-hadoop-0.23.1-jar-with-dependencies.jar example/jars/giraph.jar clustername

    Sostituire **giraph-examples-1.1.0-SNAPSHOT-for-hadoop-0.23.1-jar-with-dependencies.jar** con il percorso e il nome del file JAR ottenuto al passaggi precedente e **clustername** con il nome del cluster HDInsight. Ad esempio, se il pacchetto è stato compilato con il parametro `-Phadoop_0.20.203`, il nome del file JAR includerà **hadoop-0.20.203**.

    Una volta completato il comando, il file JAR risulterà caricato in wasb:///example/jars/giraph.jar.

    > [WACOM.NOTE] Per un elenco di utilità che è possibile usare per caricare file in HDInsight, vedere [Caricare dati per processi Hadoop in HDInsight][Caricare dati per processi Hadoop in HDInsight].

## <span id="run"></span></a>Eseguire l'esempio

In SimpleShortestPathsComputation è illustrata l'implementazione di base di [Pregel][Pregel] per individuare il percorso più breve tra oggetti di un grafico. Usare la procedura seguente per caricare i dati di esempio, eseguire un processo con l'esempio SimpleShortestPathsComputation e quindi visualizzare i risultati.

> [WACOM.NOTE] Il codice sorgente per questo è disponibile, insieme ad altri esempi, nel [branch release-1.1][branch release-1.1] del [repository GitHub][repository Giraph].

1.  Creare un nuovo file denominato **tiny\_graph.txt**. Questo file deve contenere le righe seguenti.

        [0,0,[[1,1],[3,3]]]
        [1,0,[[0,1],[2,2],[3,1]]]
        [2,0,[[1,2],[4,4]]]
        [3,0,[[0,3],[1,1],[4,4]]]
        [4,0,[[3,4],[2,4]]]

    Questi dati descrivono una relazione tra gli oggetti in un [grafico diretto][grafico diretto], usando il formato `[source_id,source_value,[[dest_id], [edge_value],...]]`. Ogni riga rappresenta una relazione tra un oggetto **source\_id** e uno o più oggetti **dest\_id**. Il valore **edge\_value** (o peso) costituisce la potenza o la distanza della connessione tra **source\_id** e **dest\_id**.

    I dati disegnati usando il valore (o peso) come distanza tra gli oggetti sono simili a quelli raffigurati di seguito.

    ![tiny\_graph.txt drawn as circles with lines of varying distance between][tiny\_graph.txt drawn as circles with lines of varying distance between]

2.  Caricare il file **tiny\_graph.txt** nell'archivio principale del cluster HDInsight usando [Azure PowerShell][Azure PowerShell] e [HDInsight-Tools][HDInsight-Tools].

        Add-HDInsightFile tiny_graph.txt example/data/tiny_graph.txt clustername

    Sostituire il nome del cluster con il nome del cluster HDInsight.

3.  Usare il comando PowerShell seguente per eseguire l'esempio **SimpleShortstPathsComputation** specificando come input il file **tiny\_graph.txt**. In questo caso è necessario avere installato e configurato [Azure PowerShell][Azure PowerShell].

        $clusterName = "clustername"
        # Giraph examples JAR
        $jarFile = "wasb:///example/jars/giraph.jar"
        # Arguments for this job
        $jobArguments = "org.apache.giraph.examples.SimpleShortestPathsComputation", `
                        "-ca", "mapred.job.tracker=headnodehost:9010", `
                        "-vif", "org.apache.giraph.io.formats.JsonLongDoubleFloatDoubleVertexInputFormat", `
                        "-vip", "wasb:///example/data/tinygraph.txt", `
                        "-vof", "org.apache.giraph.io.formats.IdWithValueTextOutputFormat", `
                        "-op",  "wasb:///example/output/shortestpaths", `
                        "-w", "2"
        # Create the definition
        $jobDefinition = New-AzureHDInsightMapReduceJobDefinition `
          -JarFile $jarFile `
          -ClassName "org.apache.giraph.GiraphRunner" `
          -Arguments $jobArguments

        # Run the job, write output to the PowerShell window
        $job = Start-AzureHDInsightJob -Cluster $clusterName -JobDefinition $jobDefinition
        Write-Host "Wait for the job to complete ..." -ForegroundColor Green
        Wait-AzureHDInsightJob -Job $job
        Write-Host "STDERR"
        Get-AzureHDInsightJobOutput -Cluster $clusterName -JobId $job.JobId -StandardError
        Write-Host "Display the standard output ..." -ForegroundColor Green
        Get-AzureHDInsightJobOutput -Cluster $clusterName -JobId $job.JobId -StandardOutput

    Nell'esempio precedente sostituire **clustername** con il nome del cluster HDInsight.

### Visualizzare i risultati

Una volta completato il processo, i risultati verranno archiviati nella cartella **wasb:///example/out/shotestpaths** come file **part-m-\#\#\#\#\#**. Usare [Azure PowerShell][Azure PowerShell] e [HDInsight-Tools][HDInsight-Tools] per scaricare i file di output.

    Find-HDInsightFile example/output/shortestpaths/part* clustername | foreach-object { Get-HDInsightFile $_.name .  itsfullofstorage }
    Cat example/output/shortestpaths/part*

Nella directory corrente verrà creata la struttura di directory **example/output/shortestpaths** e verrà avviato il download dei file a partire da **part**. Nel cmdlet **Cat** verrà quindi visualizzato il contenuto dei file, che dovrebbe essere simile al seguente.

    0   1.0
    4   5.0
    2   2.0
    1   0.0
    3   1.0

L'esempio SimpleShortestPathComputation è hardcoded in modo da essere avviato con l'ID oggetto 1 e individuare il percorso più breve ad altri oggetti. L'output dovrebbe quindi essere `destination_id distance`, in cui distance è il valore (o il peso) dei confini attraversati tra l'ID oggetto 1 e l'ID di destinazione.

Con questa visualizzazione è possibile verificare i risultati attraversando i percorsi più brevi tra l'ID 1 e tutti gli altri oggetti. Notare che il percorso più breve tra l'ID 1 e l'ID 4 è 5, che corrisponde alla distanza totale tra <span style="color:orange">ID 1 e 3</span> e quindi tra <span style="color:red">ID 3 e 4</span>.

![Drawing of objects as circles with shortest paths drawn between][Drawing of objects as circles with shortest paths drawn between]

## <span id="tshoot"></span></a>Risoluzione dei problemi

### Directory di output già esistente

Con i processi Giraph la directory di output specificata viene creata in fase di esecuzione. Se la directory esiste già, si verificherà un errore e verrà visualizzato un apposito messaggio.

Se si vuole eseguire un processo più volte, è necessario rimuovere la directory di output tra un processo e l'altro oppure specificare una directory di output diversa per ogni processo.

### <span id="cmd"></span></a>Uso della riga di comando di Hadoop

Anche se in questo articolo viene spiegato come eseguire un processo Giraph tramite PowerShell, è possibile eseguire il processo anche tramite la riga di comando di Hadoop.

> [WACOM.NOTE] La riga di comando di Hadoop è disponibile solo quando ci si connette al cluster HDInsight con Desktop remoto.
>
> Le sessioni di Desktop remoto a risorse di calcolo di Azure, come il cluster HDInsight, possono essere eseguite solo da client di Desktop remoto basati su Windows.

Per connettersi al cluster HDInsight, eseguire la procedura seguente:

1.  Nel [portale di gestione di Azure][portale di gestione di Azure] selezionare il cluster HDInsight e quindi scegliere **Configurazione**.

2.  Nella parte inferiore della pagina selezionare **Abilita modalità remota** e specificare il nome utente, la password e la data di scadenza della Connessione Desktop remoto.

3.  Una volta elaborata la richiesta di abilitazione di Desktop remoto, nella parte inferiore della pagina verrà visualizzata una nuova voce **Connetti**. Selezionarla per scaricare il file RDP per la sessione di Desktop remoto.

4.  Il file RDP può essere salvato o aperto immediatamente per avviare il client di Desktop remoto. Durante il processo di connessione verrà chiesto di specificare il nome utente e la password usati durante l'abilitazione della connessione Desktop remoto.

5.  Una volta connessi, usare l'icona della **riga di comando di Hadoop** sul desktop per visualizzare la riga di comando.

6.  L'esempio seguente illustra come copiare il file **giraph.jar** nel nodo head del cluster e quindi eseguire il processo con la riga di comando di Hadoop.

        hadoop fs -copyToLocal wasb:///example/jar/giraph.jar
        hadoop jar giraph.jar org.apache.giraph.GiraphRunner org.apache.giraph.examples.SimpleShortestPathsComputation -ca "mapred.job.tracker=headnodehost:9010" -vif org.apache.giraph.io.formats.JsonLongDoubleFloatDoubleVertexInputFormat -vip wasb:///example/data/tinygraph.txt -vof org.apache.giraph.io.formats.IdWithValueTextOutputFormat -op wasb:///example/output/shortestpaths -w 2

### Versioni precedenti di HDInsight

Se si vuole usare Giraph con versioni precedenti di HDInsight, è necessario compilarlo per la versione di Hadoop specifica supportata. Per determinare la versione di Hadoop corrispondente alla versione di HDInsight in uso, vedere [Novità delle versioni del cluster HDInsight][Novità delle versioni del cluster HDInsight].

Con versioni precedenti di HDInsight potrebbe inoltre essere necessario eseguire il processo Giraph dalla riga di comando di Hadoop. Se si ricevono errori durante l'esecuzione del processo da PowerShell, provare a eseguirlo dalla [riga di comando di Hadoop][riga di comando di Hadoop].

## Passaggi successivi

A questo punto, dopo aver appreso come usare Giraph con HDInsight, provare a usare [Pig][Pig] e [Hive][Hive] con HDInsight.

  [Apache Giraph]: http://giraph.apache.org
  [Compilare e distribuire Apache Giraph in un cluster HDInsight]: #build
  [Eseguire l'esempio SimpleShortestPathsComputation]: #run
  [pacchetto org.apache.giraph.examples]: https://giraph.apache.org/apidocs/org/apache/giraph/examples/package-summary.html
  [Risolvere gli eventuali problemi rilevati]: #tshoot
  [Git]: http://git-scm.com/
  [Maven]: http://maven.apache.org/
  [repository Giraph]: https://github.com/apache/giraph
  [GIRAPH-930]: https://issues.apache.org/jira/browse/GIRAPH-930
  [Azure PowerShell]: http://azure.microsoft.com/it-it/documentation/articles/install-configure-powershell/
  [HDInsight-Tools]: https://github.com/Blackmist/hdinsight-tools
  [Caricare dati per processi Hadoop in HDInsight]: http://azure.microsoft.com/it-it/documentation/articles/hdinsight-upload-data/
  [Pregel]: http://people.apache.org/~edwardyoon/documents/pregel.pdf
  [branch release-1.1]: https://github.com/apache/giraph/tree/release-1.1
  [grafico diretto]: http://en.wikipedia.org/wiki/Directed_graph
  [tiny\_graph.txt drawn as circles with lines of varying distance between]: .\media\hdinsight-giraph\giraph-graph.png
  [Drawing of objects as circles with shortest paths drawn between]: .\media\hdinsight-giraph\giraph-graph-out.png
  [portale di gestione di Azure]: https://manage.windowsazure.com
  [Novità delle versioni del cluster HDInsight]: http://azure.microsoft.com/it-it/documentation/articles/hdinsight-component-versioning/
  [riga di comando di Hadoop]: #cmd
  [Pig]: http://azure.microsoft.com/it-it/documentation/articles/hdinsight-use-pig/
  [Hive]: http://azure.microsoft.com/it-it/documentation/articles/hdinsight-use-hive/
