<properties title="Generate movie recommendations using Mahout" pageTitle="Generare raccomandazioni di film mediante Mahout con Microsoft Axure HDInsight (Hadoop)" description="Informazioni su come usare la libreria di Machine Learning Apache Mahout per generare raccomandazioni di film con Microsoft Azure HDInsight (Hadoop)." metaKeywords="Azure hdinsight mahout, Azure hdinsight machine learning, azure hadoop mahout, azure hadoop machine learning" services="hdinsight" solutions="" documentationCenter="big-data" authors="larryfr" videoId="" scriptId="" manager="paulettm" />

<tags ms.service="hdinsight" ms.workload="big-data" ms.tgt_pltfrm="na" ms.devlang="na" ms.topic="article" ms.date="09/17/2014" ms.author="larryfr" />

#Generare raccomandazioni di film mediante Apache Mahout con HDInsight (Hadoop)

Informazioni su come usare la libreria di Machine Learning [Apache Mahout](http://mahout.apache.org) per generare raccomandazioni di film con Microsoft Azure HDInsight (Hadoop).

> [WACOM.NOTE] Per usare le informazioni presenti in questo articolo, è necessario avere un cluster HDInsight. Per informazioni su come crearne uno, vedere [Introduzione all'uso di Azure HDInsight][getstarted].
>
> Mahout viene fornito con i cluster HDInsight 3.1. Se si usa una versione precedente di HDInsight, vedere [Installare Mahout](#install) prima di continuare.

##<a name="learn"></a>Contenuto dell'esercitazione

Mahout è una libreria di [Machine Learning][ml] per Apache Hadoop. Mahout contiene gli algoritmi per l'elaborazione dei dati, ad esempio applicazione di filtri, classificazione e clustering. In questo articolo viene usato un motore di raccomandazione per generare raccomandazioni di film in base ai film visti dai propri amici. Verrà anche descritto come eseguire classificazioni con una foresta delle decisioni. Nell'articolo verranno illustrate le procedure seguenti.

* Come eseguire i processi Mahout da PowerShell

* Come eseguire i processi Mahout dalla riga di comando di Hadoop

* Come installare Mahout nei cluster HDInsight 2.0 e 3.0

##Contenuto dell'articolo

* [Generare raccomandazioni con PowerShell](#recommendations)
* [Classificare i dati mediante la riga di comando di Hadoop](#classify)
* [Risoluzione dei problemi](#troubleshooting)


##<a name="recommendations"></a>Generare raccomandazioni con PowerShell

> [WACOM.NOTE] Mentre il processo usato in questa sezione funziona con PowerShell, molte delle classi fornite con Mahout al momento non funzionano con PowerShell e devono essere eseguite mediante la riga di comando di Hadoop. Per un elenco delle classi che non funzionano con PowerShell, vedere la sezione [Risoluzione dei problemi](#troubleshooting) .
>
> Per un esempio dell'uso della riga di comando di Hadoop per eseguire i processi Mahout, vedere [Classificare i dati mediante la riga di comando di Hadoop](#classify).

Una delle funzioni fornite da Mahout è un motore di raccomandazione. Questo motore accetta i dati nel formato `userID`, `itemId` e `prefValue` (le preferenze utente per l'elemento). Mahout può quindi eseguire l'analisi delle co-occorrenze per determinare che gli _utenti che hanno una preferenza per un elemento hanno anche una preferenza per altri elementi_. Mahout determinerà quindi gli utenti con preferenze di elementi simili che potranno essere usati per le raccomandazioni.

Di seguito è riportato un esempio molto semplice relativo ai film:

* _Co-occorrenza_: a Joe, Alice e Bob piacciono _Guerre stellari_, _L'Impero colpisce ancora_ e _Il ritorno dello Jedi_. Mahout determinerà che agli utenti che piace uno di questi film piacciono anche gli altri due.

* _Co-occorrenza_: a Bob e Alice piacciono anche _La minaccia fantasma_, _L'attacco dei cloni_ e _La vendetta dei Sith_. Mahout determinerà che agli utenti che piacciono i tre film precedenti piacciono anche questi tre.

* _Raccomandazione per somiglianza_: poiché a Joe piacciono i primi tre film, Mahout cercherà i film che piacciono ad altri utenti con preferenze simili, ma che Joe non ha guardato o per i quali non ha ancora espresso una preferenza o una valutazione. In questo caso, Mahout raccomanderà _La minaccia fantasma_, _L'attacco dei cloni_ e _La vendetta dei Sith_.

###Caricare i dati

Per fortuna, GroupLens Research fornisce i [dati di classificazione dei film][movielens] in un formato compatibile con Mahout.

1. Scaricare l'archivio [MovieLens 100k][100k], che contiene 100.000 classificazioni di 1.000 utenti su 1.700 film.

2. Estrarre l'archivio. Dovrebbe includere una directory ml-100k, che contiene molti file di dati con __u.__ come prefisso. Il file che verrà analizzato da Mahout è __u.data__. La struttura dei dati di questo file è `userID`, `movieID`, `userRating` e `timestamp`. Di seguito è riportato un esempio dei dati.


		196	242	3	881250949
		186	302	3	891717742
		22	377	1	878887116
		244	51	2	880606923
		166	346	1	886397596


3. Caricare il file __u.data__ in __example/data/u.data__ nel cluster HDInsight. Se si ha [Azure PowerShell][aps], è possibile usare il modulo [PowerShell HDInsight-Tools][tools] per caricare il file. Per informazioni su altri modi per caricare i file, vedere [Caricare dati per processi Hadoop in HDInsight][upload]. L'esempio seguente illustra l'uso di `Add-HDInsightFile` per caricare il file

    	PS C:\> Add-HDInsightFile -LocalPath "path\to\u.data" -DestinationPath "example/data/u.data" -ClusterName "nome del cluster"

    Questo frammento di codice caricherà il file __u.data__ in __example/data/u.dat__a nella risorsa di archiviazione predefinita del cluster. Sarà possibile accedere a tali dati con l'URI __wasb:///example/data/u.data__ dai processi di HDInsight.

###Eseguire il processo

Usare lo script di PowerShell seguente per eseguire un processo mediante il motore di raccomandazione di Mahout con il file __u.data__ caricato precedentemente.

	# The HDInsight cluster name.
	$clusterName = "the cluster name"

	# NOTE: The version number portion of the file path
	# may change in future versions of HDInsight.
	# So dynamically grab it using Hive.
	$mahoutPath = Invoke-Hive -Query '!${env:COMSPEC} /c dir /b /s ${env:MAHOUT_HOME}\examples\target\*-job.jar' | where {$_.startswith("C:\apps\dist")}
	$jarFile = "file:///$mahoutPath"
    #
	# If you are using an earlier version of HDInsight,
	# set $jarFile to the jar file you
	# uploaded.
	# For example,
	# $jarFile = "wasb:///example/jars/mahout-core-0.9-job.jar"

	# The arguments for this job
	# * input - the path to the data uploaded to HDInsight
	# * output - the path to store output data
	# * tempDir - the directory for temp files
	$jobArguments = "-s", "SIMILARITY_COOCCURRENCE",
	                "--input", "wasb:///example/data/u.data",
	                "--output", "wasb:///example/out",
	                "--tempDir", "wasb:///temp/mahout"

	# Create the job definition
	$jobDefinition = New-AzureHDInsightMapReduceJobDefinition `
	  -JarFile $jarFile `
	  -ClassName "org.apache.mahout.cf.taste.hadoop.item.RecommenderJob" `
	  -Arguments $jobArguments

	# Start the job
	$job = Start-AzureHDInsightJob -Cluster $clusterName -JobDefinition $jobDefinition

	# Wait on the job to complete
	Write-Host "Wait for the job to complete ..." -ForegroundColor Green
	Wait-AzureHDInsightJob -Job $job

	# Write out any error information
	Write-Host "STDERR"
	Get-AzureHDInsightJobOutput -Cluster $clusterName -JobId $job.JobId -StandardError

> [WACOM.NOTE] I processi Mahout non rimuovono i dati temporanei creati durante l'elaborazione del processo. Questo è il motivo per cui nel processo di esempio viene specificato il parametro `--tempDir`, ovvero per isolare i file temporanei in un percorso specifico per semplificarne l'eliminazione.
>
> Per rimuovere questi file, è possibile usare una delle utility riportate nell'articolo [Caricare dati per processi Hadoop in HDInsight][upload]. In alternativa, usare la funzione `Remove-HDInsightFile` nello script PowerShell [HDInsight-Tools][tools].
>
> Se non si rimuovono i file temporanei o il file di output, verrà visualizzato un errore alla successiva esecuzione del processo.

Il processo Mahout non restituisce l'output in STDOUT, ma lo archivia nella directory di output specificata come __part-r-00000__. Per scaricare e visualizzare il file, usare la funzione `Get-HDInsightFile` nel modulo PowerShell [HDInsight-Tools][tools].

Di seguito è riportato un esempio del contenuto del file:

	1	[234:5.0,347:5.0,237:5.0,47:5.0,282:5.0,275:5.0,88:5.0,515:5.0,514:5.0,121:5.0]
	2	[282:5.0,210:5.0,237:5.0,234:5.0,347:5.0,121:5.0,258:5.0,515:5.0,462:5.0,79:5.0]
	3	[284:5.0,285:4.828125,508:4.7543354,845:4.75,319:4.705128,124:4.7045455,150:4.6938777,311:4.6769233,248:4.65625,272:4.649266]
	4	[690:5.0,12:5.0,234:5.0,275:5.0,121:5.0,255:5.0,237:5.0,895:5.0,282:5.0,117:5.0]

La prima colonna è `userID`. I valori racchiusi tra "[" e "]" rappresentano `movieId`:`recommendationScore`.

###Visualizzare l'output

Sebbene l'output generato risulti appropriato per essere usato in un'applicazione, non è facilmente leggibile dagli utenti. Alcuni degli altri file estratti nella cartella __ml-100k__ precedente possono essere usati per risolvere il valore `movieId` nel nome di un film. Anche se nella cartella __ml-100k__ è incluso uno script Python che eseguirà tale operazione (__show_recommendations.py__), è anche possibile usare lo script PowerShell seguente.

	<#
	.SYNOPSIS
	    Displays recommendations for movies.
	.DESCRIPTION
	    Displays recommendations generated by Mahout
	    with HDInsight example in a human readable format.
	.EXAMPLE
	    .\Show-Recommendation -userId 4
	        -userDataFile "u.data"
	        -movieFile "u.item"
	        -recommendationFile "output.txt"
	#>

	[CmdletBinding(SupportsShouldProcess = $true)]
	param(
	    #The user ID
	    [Parameter(Mandatory = $true)]
	    [String]$userId,

	    [Parameter(Mandatory = $true)]
	    [String]$userDataFile,

	    [Parameter(Mandatory = $true)]
	    [String]$movieFile,

	    [Parameter(Mandatory = $true)]
	    [String]$recommendationFile
	)
	# Read movie ID & description into hash table
	Write-Host "Reading movies descriptions" -ForegroundColor Green
	$movieById = @{}
	foreach($line in Get-Content $movieFile)
	{
	    $tokens = $line.Split("|")
	    $movieById[$tokens[0]] = $tokens[1]
	}
	# Load movies user has already seen (rated)
	# into a hash table
	Write-Host "Reading rated movies" -ForegroundColor Green
	$ratedMovieIds = @{}
	foreach($line in Get-Content $userDataFile)
	{
	    $tokens = $line.Split("`t")
	    if($tokens[0] -eq $userId)
	    {
	        # Resolve the ID to the movie name
	        $ratedMovieIds[$movieById[$tokens[1]]] = $tokens[2]
	    }
	}
	# Read recommendations generated by Mahout
	Write-Host "Reading recommendations" -ForegroundColor Green
	$recommendations = @{}
	foreach($line in get-content $recommendationFile)
	{
	    $tokens = $line.Split("`t")
	    if($tokens[0] -eq $userId)
	    {
	        #Trim leading/treailing [] and split at ,
	        $movieIdAndScores = $tokens[1].TrimStart("[").TrimEnd("]").Split(",")
	        foreach($movieIdAndScore in $movieIdAndScores)
	        {
	            #Split at : and store title and score in a hash table
	            $idAndScore = $movieIdAndScore.Split(":")
	            $recommendations[$movieById[$idAndScore[0]]] = $idAndScore[1]
	        }
	        break
	    }
	}

	Write-Host "Rated movies" -ForegroundColor Green
	Write-Host "---------------------------" -ForegroundColor Green
	$ratedFormat = @{Expression={$_.Name};Label="Movie";Width=40}, `
	               @{Expression={$_.Value};Label="Rating"}
	$ratedMovieIds | format-table $ratedFormat
	Write-Host "---------------------------" -ForegroundColor Green

	write-host "Recommended movies" -ForegroundColor Green
	Write-Host "---------------------------" -ForegroundColor Green
	$recommendationFormat = @{Expression={$_.Name};Label="Movie";Width=40}, `
	                        @{Expression={$_.Value};Label="Score"}
	$recommendations | format-table $recommendationFormat

Per usare questo script, è necessario avere la cartella __ml-100k__ estratta precedentemente, nonché una copia locale del file di output __part-r-00000__ generato dal processo Mahout. Di seguito è riportato un esempio dell'esecuzione dello script.

	PS C:\> show-recommendation.ps1 -userId 4 -userDataFile .\ml-100k\u.data -movieFile .\ml-100k\u.item -recommendationFile .\output.txt


> [WACOM.NOTE] Lo script Python di esempio, __show\_recommendations.py__, accetta gli stessi parametri.

L'output sarà simile al seguente.

	Reading movies descriptions
	Reading rated movies
	Reading recommendations
	Rated movies
	---------------------------
	Movie                                    Rating
	-----                                    ------
	Devil's Own, The (1997)                  1
	Alien: Resurrection (1997)               3
	187 (1997)                               2
	(lines ommitted)

	---------------------------
	Recommended movies
	---------------------------

	Movie                                    Score
	-----                                    -----
	Good Will Hunting (1997)                 4.6504064
	Swingers (1996)                          4.6862745
	Wings of the Dove, The (1997)            4.6666665
	People vs. Larry Flynt, The (1996)       4.834559
	Everyone Says I Love You (1996)          4.707071
	Secrets & Lies (1996)                    4.818182
	That Thing You Do! (1996)                4.75
	Grosse Pointe Blank (1997)               4.8235292
	Donnie Brasco (1997)                     4.6792455
	Lone Star (1996)                         4.7099237  

##<a name="classify"></a>Classificare i dati mediante la riga di comando di Hadoop

Uno dei metodi di classificazione disponibili con Mahout consiste nel creare una [foresta casuale][forest]. Si tratta di un processo costituito da diversi passaggi che prevede l'uso di dati di training per generare un albero delle decisioni da usare per la classificazione dei dati. Questo processo usa la classe __org.apache.mahout.classifier.df.tools.Describe__ fornita da Mahout e al momento deve essere eseguito mediante la riga di comando di Hadoop.

###Caricare i dati

L'implementazione corrente di Mahout è compatibile con il formato di repository UCI (University of California, Irvine).[]

1. Scaricare i file seguenti da [http://nsl.cs.unb.ca/NSL-KDD/](http://nsl.cs.unb.ca/NSL-KDD/).

  * [KDDTrain+.ARFF](http://nsl.cs.unb.ca/NSL-KDD/KDDTrain+.arff): file di training

  * [KDDTest+.ARFF](http://nsl.cs.unb.ca/NSL-KDD/KDDTest+.arff): dati di test

2. Aprire ogni file e rimuovere le righe in alto che iniziano con '@', quindi salvare i file. Se queste righe non vengono rimosse, si riceveranno messaggi di errore quando tali dati verranno usati con Mahout.

2. Caricare i file in __example/data__. A tale scopo, è possibile usare la funzione `Add-HDInsightFile` nel modulo PowerShell [HDInsight-Tools][tools].

###Eseguire il processo

1. Poiché questo processo richiede la riga di comando di Hadoop, è necessario abilitare prima il desktop remoto tramite il [portale di gestione di Azure][management]. Nel portale selezionare il cluster HDInsight, quindi selezionare __Abilita modalità remota__ nella parte inferiore della pagina __Configurazione__.

    ![enable remote][enableremote]

    Quando richiesto, immettere il nome utente e la password da usare per le sessioni remote.

2. Dopo aver abilitato l'accesso remoto, selezionare __Connetti__ per iniziare la connessione. Verrà scaricato un file con estensione __rdp__ da usare per avviare la sessione Desktop remoto.

    ![connect][connect]

3. Dopo la connessione usare l'icona della __riga di comando di Hadoop__ per visualizzare la riga di comando.

	![hadoop cli][hadoopcli]

3. Usare il comando seguente per generare il descrittore di file (__KDDTrain+.info__) con Mahout.

		hadoop jar "c:/apps/dist/mahout-0.9.0.2.1.3.0-1887/examples/target/mahout-examples-0.9.0.2.1.3.0-1887-job.jar" org.apache.mahout.classifier.df.tools.Describe -p "wasb:///example/data/KDDTrain+.arff" -f "wasb:///example/data/KDDTrain+.info" -d N 3 C 2 N C 4 N C 8 N 2 C 19 N L

	La stringa `N 3 C 2 N C 4 N C 8 N 2 C 19 N L` descrive gli attributi dei dati nel file: il primo è un attributo numerico, il secondo un attributo categorico e così via. L indica un'etichetta.

4. Creare una foresta di alberi delle decisioni con il comando seguente.

		hadoop jar c:/apps/dist/mahout-0.9.0.2.1.3.0-1887/examples/target/mahout-examples-0.9.0.2.1.3.0-1887-job.jar org.apache.mahout.classifier.df.mapreduce.BuildForest -Dmapred.max.split.size=1874231 -d wasb:///example/data/KDDTrain+.arff -ds wasb:///example/data/KDDTrain+.info -sl 5 -p -t 100 -o nsl-forest

    L'output di questa operazione viene archiviato nella directory __nsl-forest__ che si trova nella risorsa di archiviazione del cluster HDInsight in __wasb://user/&lt;nomeutente>/nsl-forest/nsl-forest.seq, dove &lt;nomeutente> è il nome utente usato per la sessione Desktop remoto. Questo file non è in formato leggibile dall'utente.

5. Testare la foresta classificando il set di dati __KDDTest+.arff__ con il comando seguente.

    	hadoop jar c:/apps/dist/mahout-0.9.0.2.1.3.0-1887/examples/target/mahout-examples-0.9.0.2.1.3.0-1887-job.jar org.apache.mahout.classifier.df.mapreduce.TestForest -i wasb:///example/data/KDDTest+.arff -ds wasb:///example/data/KDDTrain+.info -m nsl-forest -a -mr -o wasb:///example/data/predictions

    Questo comando restituirà le informazioni di riepilogo sul processo di classificazione simili alle seguenti.

	    14/07/02 14:29:28 INFO mapreduce.TestForest:

	    =======================================================
	    Summary
	    -------------------------------------------------------
	    Correctly Classified Instances          :      17560       77.8921%
	    Incorrectly Classified Instances        :       4984       22.1079%
	    Total Classified Instances              :      22544

	    =======================================================
	    Confusion Matrix
	    -------------------------------------------------------
	    a       b       <--Classified as
	    9437    274      |  9711        a     = normal
	    4710    8123     |  12833       b     = anomaly

	    =======================================================
	    Statistics
	    -------------------------------------------------------
	    Kappa                                       0.5728
	    Accuracy                                   77.8921%
	    Reliability                                53.4921%
	    Reliability (standard deviation)            0.4933

  Questo processo crea anche un file che si trova in __wasb:///example/data/predictions/KDDTest+.arff.out__, che però non è in formato leggibile dall'utente.

> [WACOM.NOTE] I processi Mahout non sovrascrivono i file. Per eseguire di nuovo questi processi, è necessario eliminare prima i file creati dai processi precedenti.

##<a name="troubleshooting"></a>Risoluzione dei problemi

###<a name="install"></a>Installare Mahout

Mahout viene installato nei cluster HDInsight 3.1 e può essere installato manualmente nei cluster 3.0 o 2.1 con i passaggi seguenti.

1. La versione di Mahout da usare dipende dalla versione di HDInsight del cluster. Per sapere la versione del cluster, usare il comando seguente con [Azure PowerShell][aps]:

    	PS C:\> Get-AzureHDInsightCluster -Name YourClusterName | Select version


  * __Per HDInsight 2.1__ è possibile scaricare un file jar contenente [Mahout 0.9](http://repo2.maven.org/maven2/org/apache/mahout/mahout-core/0.9/mahout-core-0.9-job.jar).

  * __Per HDInsight 3.0__ è necessario [compilare Mahout dal codice sorgente][build] e specificare la versione di Hadoop fornita da HDInsight. Installare i prerequisiti elencati nella pagina di compilazione, scaricare il codice sorgente, quindi usare il comando seguente per creare i file jar di Mahout.

			mvn -Dhadoop2.version=2.2.0 -DskipTests clean package

    	Una volta completata la compilazione, verrà creato il file JAR in __mahout\mrlegacy\target\mahout-mrlegacy-1.0-SNAPSHOT-job.jar__.

    	> [WACOM.NOTE] Quando verrà rilasciato Mahout 1.0 sarà possibile usare i pacchetti predefiniti con HDInsight 3.0.

2. Caricare il file jar in __example/jars__ nella risorsa di archiviazione predefinita del cluster. L'esempio seguente usa lo script [send-hdinsight][sendhdinsight] per caricare il file.

    	PS C:\> .\Send-HDInsight -LocalPath "path\to\mahout-core-0.9-job.jar" -DestinationPath "example/jars/mahout-core-0.9-job.jar" -ClusterName "your cluster name"

###Impossibile sovrascrivere i file

I processi Mahout non eliminano i file temporanei creati durante l'elaborazione. Inoltre, i processi non sovrascrivono un file di output esistente.

Per evitare errori durante l'esecuzione dei processi Mahout, eliminare i file temporanei e di output tra le esecuzioni o usare nomi univoci per le directory temporanee e di output.

###Impossibile trovare il file jar

Mentre HDInsight 3.1 include Mahout, il percorso e il nome file includono il numero di versione di Mahout installato nel cluster. Lo script PowerShell di esempio in questa esercitazione usa un percorso valido a partire da luglio 2014, ma il numero di versione cambierà in HDInsight nei futuri aggiornamenti. Per determinare il percorso corrente del file jar di Mahout per il cluster, usare i comandi di PowerShell seguenti, quindi modificare lo script in modo da fare riferimento al percorso del file restituito.

	Use-AzureHDInsightCluster -Name $clusterName
	$jarFile = Invoke-Hive -Query '!${env:COMSPEC} /c dir /b /s ${env:MAHOUT_HOME}\examples\target\*-job.jar'

###<a name="nopowershell"></a>Classi che non funzionano con PowerShell

I processi Mahout che usano le classi seguenti restituiranno una serie di errori se usati da PowerShell.

* org.apache.mahout.utils.clustering.ClusterDumper
* org.apache.mahout.utils.SequenceFileDumper
* org.apache.mahout.utils.vectors.lucene.Driver
* org.apache.mahout.utils.vectors.arff.Driver
* org.apache.mahout.text.WikipediaToSequenceFile
* org.apache.mahout.clustering.streaming.tools.ResplitSequenceFiles
* org.apache.mahout.clustering.streaming.tools.ClusterQualitySummarizer
* org.apache.mahout.classifier.sgd.TrainLogistic
* org.apache.mahout.classifier.sgd.RunLogistic
* org.apache.mahout.classifier.sgd.TrainAdaptiveLogistic
* org.apache.mahout.classifier.sgd.ValidateAdaptiveLogistic
* org.apache.mahout.classifier.sgd.RunAdaptiveLogistic
* org.apache.mahout.classifier.sequencelearning.hmm.BaumWelchTrainer
* org.apache.mahout.classifier.sequencelearning.hmm.ViterbiEvaluator
* org.apache.mahout.classifier.sequencelearning.hmm.RandomSequenceGenerator
* org.apache.mahout.classifier.df.tools.Describe

Per eseguire i processi che usano queste classi, connettersi al cluster HDInsight ed eseguire i processi usando la riga di comando di Hadoop. Per un esempio, vedere la sezione [Classificare i dati mediante la riga di comando di Hadoop](#classify) .


[build]: http://mahout.apache.org/developers/buildingmahout.html
[aps]: http://azure.microsoft.com/it-it/documentation/articles/install-configure-powershell/
[movielens]: http://grouplens.org/datasets/movielens/
[100k]: http://files.grouplens.org/datasets/movielens/ml-100k.zip
[getstarted]: http://azure.microsoft.com/it-it/documentation/articles/hdinsight-get-started/
[upload]: http://azure.microsoft.com/it-it/documentation/articles/hdinsight-upload-data/
[ml]: http://en.wikipedia.org/wiki/Machine_learning
[forest]: http://en.wikipedia.org/wiki/Random_forest
[management]: https://manage.windowsazure.com/
[enableremote]: ./media/hdinsight-mahout/enableremote.png
[connect]: ./media/hdinsight-mahout/connect.png
[hadoopcli]: ./media/hdinsight-mahout/hadoopcli.png
[tools]: https://github.com/Blackmist/hdinsight-tools

<!--HONumber=35.1-->
