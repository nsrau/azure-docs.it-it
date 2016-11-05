---
title: Generare raccomandazioni utilizzando HDInsight basate su Mahout e Windows | Microsoft Docs
description: Informazioni su come usare la libreria di Machine Learning Apache Mahout per generare raccomandazioni di film con HDInsight basato su Windows (Hadoop).
services: hdinsight
documentationcenter: ''
author: Blackmist
manager: jhubbard
editor: cgronlun
tags: azure-portal

ms.service: hdinsight
ms.workload: big-data
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 10/11/2016
ms.author: larryfr

---
# <a name="generate-movie-recommendations-by-using-apache-mahout-with-hadoop-in-hdinsight"></a>Generare raccomandazioni di film mediante Apache Mahout con Hadoop in HDInsight
[!INCLUDE [mahout-selector](../../includes/hdinsight-selector-mahout.md)]

Informazioni su come usare la libreria di Machine Learning [Apache Mahout](http://mahout.apache.org) con Azure HDInsight per generare raccomandazioni di film.

> [!NOTE]
> I passaggi descritti in questo documento richiedono un client Windows e un cluster HDInsight basato su Windows. Per informazioni sull'uso di Mahout da un client Linux, OS X o Unix con un cluster HDInsight basato su Windows, vedere [Generare raccomandazioni di film con Apache Mahout con Hadoop basato su Linux in HDInsight](hdinsight-hadoop-mahout-linux-mac.md)
> 
> 

## <a name="<a-name="learn"></a>what-you-will-learn"></a><a name="learn"></a>Contenuto dell'esercitazione
Mahout è una libreria di [Machine Learning][ml] per Apache Hadoop. Mahout contiene gli algoritmi per l'elaborazione dei dati, ad esempio applicazione di filtri, classificazione e clustering. In questo articolo si userà un motore di raccomandazione per generare raccomandazioni di film in base ai film visti dai propri amici. Si apprenderà inoltre a eseguire classificazioni con una foresta delle decisioni. Nell'articolo verranno illustrate le procedure seguenti:

* Come eseguire i processi Mahout mediante Windows PowerShell
* Come eseguire i processi Mahout dalla riga di comando di Hadoop
* Come installare Mahout nei cluster HDInsight 3.0 e 2.0
  
  > [!NOTE]
  > Mahout viene fornito con la versione HDInsight 3.1 dei cluster. Se si usa una versione precedente di HDInsight, vedere [Installare Mahout](#install) prima di continuare.
  > 
  > 

## <a name="prerequisites"></a>prerequisiti
* **Un cluster Hadoop basato su Windows in HDInsight**. Per informazioni su come crearne uno, vedere [Introduzione all'utilizzo di Hadoop in HDInsight][getstarted]
* **Workstation con Azure PowerShell**.
  
    [!INCLUDE [upgrade-powershell](../../includes/hdinsight-use-latest-powershell.md)]

## <a name="<a-name="recommendations"></a>generate-recommendations-by-using-windows-powershell"></a><a name="recommendations"></a>Generare raccomandazioni con Windows PowerShell
> [!NOTE]
> Anche se il processo usato in questa sezione funziona con Windows PowerShell, molte delle classi fornite con Mahout al momento non funzionano con Windows PowerShell e devono essere eseguite mediante la riga di comando di Hadoop. Per l'elenco delle classi che non funzionano con Windows PowerShell, vedere la sezione [Risoluzione dei problemi](#troubleshooting) .
> 
> Per un esempio di uso della riga di comando di Hadoop per eseguire i processi Mahout, vedere [Classificare i dati mediante la riga di comando di Hadoop](#classify).
> 
> 

Una delle funzioni fornite da Mahout è un motore di raccomandazione. Questo motore accetta i dati nei formati `userID`, `itemId` e `prefValue` (la preferenza degli utenti per l'elemento). Mahout può quindi eseguire l'analisi delle co-occorrenze per determinare che gli *utenti con una preferenza per un elemento hanno anche una preferenza per altri elementi*. Mahout determina quindi gli utenti con preferenze di elementi simili, che possono essere usate per le raccomandazioni.

Di seguito è riportato un esempio molto semplice relativo ai film:

* **Co-occorrenza**: a Joe, Alice e Bob piacciono *Guerre stellari*, *L'Impero colpisce ancora* e *Il ritorno dello Jedi*. Mahout determina che agli utenti a cui piace uno di questi film piacciono anche gli altri due.
* **Co-occorrenza**: a Bob e Alice piacciono anche *La minaccia fantasma*, *L'attacco dei cloni* e *La vendetta dei Sith*. Mahout determina che agli utenti a cui piacciono i tre film precedenti piacciono anche questi tre.
* **Raccomandazione per somiglianza**: poiché a Joe piacciono i primi tre film, Mahout cerca i film che piacciono ad altri utenti con preferenze simili ma che Joe non ha guardato o per i quali non ha ancora espresso una preferenza o una valutazione. In questo caso, Mahout raccomanda *La minaccia fantasma*, *L'attacco dei cloni* e *La vendetta dei Sith*.

### <a name="understanding-the-data"></a>Informazioni sui dati
[GroupLens Research][movielens]fornisce i dati di classificazione dei film in un formato compatibile con Mahout. Questi dati sono disponibili nello spazio di archiviazione predefinito del cluster in `/HdiSamples/MahoutMovieData`.

Sono disponibili due file, `moviedb.txt` (informazioni sui film) e `user-ratings.txt`. Il file user-ratings.txt viene usato durante l'analisi, mentre moviedb.txt viene usato per offrire informazioni di testo descrittivo quando si visualizzano i risultati dell'analisi.

I dati contenuti in user-ratings.txt presentano una struttura `userID`, `movieID`, `userRating` e `timestamp` che indica come ogni utente ha classificato un film. Di seguito è riportato un esempio dei dati:

    196 242 3   881250949
    186 302 3   891717742
    22  377 1   878887116
    244 51  2   880606923
    166 346 1   886397596

### <a name="run-the-job"></a>Eseguire il processo
Usare lo script Windows PowerShell seguente per eseguire un processo che usi il motore di raccomandazione Mahout con i dati del film:

    # The HDInsight cluster name.
    $clusterName = "the cluster name"

    #Get HTTPS/Admin credentials for submitting the job later
    $creds = Get-Credential
    #Get the cluster info so we can get the resource group, storage, etc.
    $clusterInfo = Get-AzureRmHDInsightCluster -ClusterName $clusterName
    $resourceGroup = $clusterInfo.ResourceGroup
    $storageAccountName=$clusterInfo.DefaultStorageAccount.split('.')[0]
    $container=$clusterInfo.DefaultStorageContainer
    $storageAccountKey=(Get-AzureRmStorageAccountKey `
        -Name $storageAccountName `
        -ResourceGroupName $resourceGroup)[0].Value

    #Create a storage content and upload the file
    $context = New-AzureStorageContext `
        -StorageAccountName $storageAccountName `
        -StorageAccountKey $storageAccountKey

    # NOTE: The version number in the file path
    # may change in future versions of HDInsight.
    $jarFile =  "file:///C:/apps/dist/mahout-0.9.0.2.2.9.1-8/examples/target/mahout-examples-0.9.0.2.2.9.1-8-job.jar"
    #
    # If you are using an earlier version of HDInsight,
    # set $jarFile to the jar file you
    # uploaded.
    # For example,
    # $jarFile = "wasbs:///example/jars/mahout-core-0.9-job.jar"

    # The arguments for this job
    # * input - the path to the data uploaded to HDInsight
    # * output - the path to store output data
    # * tempDir - the directory for temp files
    $jobArguments = "--similarityClassname", "recommenditembased", `
                    "-s", "SIMILARITY_COOCCURRENCE", `
                    "--input", "wasbs:///HdiSamples/MahoutMovieData/user-ratings.txt",
                    "--output", "wasbs:///example/out",
                    "--tempDir", "wasbs:///example/temp"

    # Create the job definition
    $jobDefinition = New-AzureRmHDInsightMapReduceJobDefinition `
      -JarFile $jarFile `
      -ClassName "org.apache.mahout.cf.taste.hadoop.item.RecommenderJob" `
      -Arguments $jobArguments

    # Start the job
    $job = Start-AzureRmHDInsightJob `
        -ClusterName $clusterName `
        -JobDefinition $jobDefinition `
        -HttpCredential $creds

    # Wait on the job to complete
    Write-Host "Wait for the job to complete ..." -ForegroundColor Green
    Wait-AzureRmHDInsightJob `
            -ClusterName $clusterName `
            -JobId $job.JobId `
            -HttpCredential $creds
    # Download the output
    Get-AzureStorageBlobContent `
            -Blob example/out/part-r-00000 `
            -Container $container `
            -Destination output.txt `
            -Context $context

    # Write out any error information
    Write-Host "STDERR"
    Get-AzureRmHDInsightJobOutput `
            -Clustername $clusterName `
            -JobId $job.JobId `
            -DefaultContainer $container `
            -DefaultStorageAccountName $storageAccountName `
            -DefaultStorageAccountKey $storageAccountKey `
            -HttpCredential $creds `
            -DisplayOutputType StandardError

> [!NOTE]
> I processi Mahout non rimuovono i dati temporanei creati durante l'elaborazione del processo. Nel processo di esempio è specificato il parametro `--tempDir` per isolare i file temporanei in una directory specifica.
> 
> 

Il processo Mahout non restituisce l'output in STDOUT, ma lo archivia nella directory di output specificata come **part-r-00000**. Lo script scaricherà questo file in **output.txt** nella directory corrente nella workstation.

Di seguito è riportato un esempio del contenuto di questo file:

    1   [234:5.0,347:5.0,237:5.0,47:5.0,282:5.0,275:5.0,88:5.0,515:5.0,514:5.0,121:5.0]
    2   [282:5.0,210:5.0,237:5.0,234:5.0,347:5.0,121:5.0,258:5.0,515:5.0,462:5.0,79:5.0]
    3   [284:5.0,285:4.828125,508:4.7543354,845:4.75,319:4.705128,124:4.7045455,150:4.6938777,311:4.6769233,248:4.65625,272:4.649266]
    4   [690:5.0,12:5.0,234:5.0,275:5.0,121:5.0,255:5.0,237:5.0,895:5.0,282:5.0,117:5.0]

La prima colonna rappresenta il valore `userID`. I valori racchiusi tra "[" e "]" sono `movieId`:`recommendationScore`.

### <a name="view-the-output"></a>Visualizzare l'output
Anche se l'output generato risulta appropriato per l'uso in un'applicazione, non è facilmente leggibile. Si può usare `moviedb.txt` dal server per risolvere `movieId` in un nome di film, ma prima è necessario scaricarlo dal server insieme al file delle classificazioni usando lo script seguente:

    # The HDInsight cluster name.
    $clusterName = "the cluster name"

    #Get the cluster info so we can get the resource group, storage, etc.
    $clusterInfo = Get-AzureRmHDInsightCluster -ClusterName $clusterName
    $resourceGroup = $clusterInfo.ResourceGroup
    $storageAccountName=$clusterInfo.DefaultStorageAccount.split('.')[0]
    $container=$clusterInfo.DefaultStorageContainer
    $storageAccountKey=(Get-AzureRmStorageAccountKey `
        -Name $storageAccountName `
        -ResourceGroupName $resourceGroup)[0].Value
    #Create a storage content and upload the file
    $context = New-AzureStorageContext `
        -StorageAccountName $storageAccountName `
        -StorageAccountKey $storageAccountKey
    #Download the files
    Get-AzureStorageBlobContent -blob "HdiSamples/MahoutMovieData/moviedb.txt" `
    -Container $container `
    -Destination moviedb.txt `
    -Context $context
    Get-AzureStorageBlobContent -blob "HdiSamples/MahoutMovieData/user-ratings.txt" `
    -Container $container `
    -Destination user-ratings.txt `
    -Context $context

Dopo avere scaricato i file, usare lo script PowerShell seguente per vedere le raccomandazioni con i nomi dei film:

    <#
    .SYNOPSIS
        Displays recommendations for movies.
    .DESCRIPTION
        Displays recommendations generated by Mahout
        with HDInsight example in a human readable format.
    .EXAMPLE
        .\Show-Recommendation -userId 4
            -userDataFile "user-ratings.txt"
            -movieFile "moviedb.txt"
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

Di seguito è riportato un esempio di esecuzione dello script:

    PS C:\> show-recommendation.ps1 -userId 4 -userDataFile .\user-ratings.txt -movieFile .\moviedb.txt -recommendationFile .\output.txt

L'output sarà simile al seguente:

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

## <a name="<a-name="classify"></a>classify-data-by-using-the-hadoop-command-line"></a><a name="classify"></a>Classificare i dati mediante la riga di comando di Hadoop
Uno dei metodi di classificazione disponibili con Mahout consiste nel creare una [foresta casuale][forest]. Si tratta di un processo costituito da diversi passaggi che prevede l'uso di dati di training per generare alberi delle decisioni da usare per la classificazione dei dati. Questo processo usa la classe **org.apache.mahout.classifier.df.tools.Describe** fornita da Mahout. Al momento deve essere eseguito mediante la riga di comando di Hadoop.

### <a name="load-the-data"></a>Caricare i dati
1. Scaricare i file seguenti dal [set di dati NSL-KDD](http://nsl.cs.unb.ca/NSL-KDD/).
   
   * [KDDTrain+.ARFF](http://nsl.cs.unb.ca/NSL-KDD/KDDTrain+.arff): file di training
   * [KDDTest+.ARFF](http://nsl.cs.unb.ca/NSL-KDD/KDDTest+.arff): dati di test
2. Aprire ogni file e rimuovere le righe in alto che iniziano con '@',, quindi salvare i file. Se queste righe non vengono rimosse, si riceveranno messaggi di errore quando tali dati verranno usati con Mahout.
3. Caricare i file in **example/data**. È possibile farlo usando lo script seguente: Sostituire **CLUSTERNAME** con il nome del cluster HDInsight. Sostituire FILENAME con il nome del file da caricare.
   
        #Get the cluster info so we can get the resource group, storage, etc.
        $clusterName="CLUSTERNAME"
        $fileToUpload="FILENAME"
        $blobPath="example/data/FILENAME"
        $clusterInfo = Get-AzureRmHDInsightCluster -ClusterName $clusterName
        $resourceGroup = $clusterInfo.ResourceGroup
        $storageAccountName=$clusterInfo.DefaultStorageAccount.split('.')[0]
        $container=$clusterInfo.DefaultStorageContainer
        $storageAccountKey=(Get-AzureRmStorageAccountKey `
            -Name $storageAccountName `
        -ResourceGroupName $resourceGroup)[0].Value
   
        #Create a storage content and upload the file
        $context = New-AzureStorageContext `
            -StorageAccountName $storageAccountName `
            -StorageAccountKey $storageAccountKey
   
        Set-AzureStorageBlobContent `
            -File $fileToUpload `
            -Blob $blobPath `
            -Container $container `
            -Context $context

### <a name="run-the-job"></a>Eseguire il processo
1. Questo processo richiede la riga di comando di Hadoop. Abilitare Desktop remoto per il cluster HDInsight e quindi connettersi seguendo le istruzioni disponibili in [Connettersi a cluster HDInsight tramite RDP](hdinsight-administer-use-management-portal.md#rdp).
2. Dopo la connessione usare l'icona della **riga di comando di Hadoop** per visualizzare la riga di comando.
   
    ![Riga di comando di Hadoop][hadoopcli]
3. Eseguire il comando seguente per generare il descrittore di file (**KDDTrain+.info**) che usa Mahout.
   
        hadoop jar "c:/apps/dist/mahout-0.9.0.2.2.9.1-8/examples/target/mahout-examples-0.9.0.2.2.9.1-8-job.jar" org.apache.mahout.classifier.df.tools.Describe -p "wasbs:///example/data/KDDTrain+.arff" -f "wasbs:///example/data/KDDTrain+.info" -d N 3 C 2 N C 4 N C 8 N 2 C 19 N L
   
    La stringa `N 3 C 2 N C 4 N C 8 N 2 C 19 N L` descrive gli attributi dei dati nel file. Ad esempio, L indica un'etichetta.
4. Creare una foresta di alberi delle decisioni con il comando seguente:
   
        hadoop jar c:/apps/dist/mahout-0.9.0.2.2.9.1-8/examples/target/mahout-examples-0.9.0.2.2.9.1-8-job.jar org.apache.mahout.classifier.df.mapreduce.BuildForest -Dmapred.max.split.size=1874231 -d wasbs:///example/data/KDDTrain+.arff -ds wasbs:///example/data/KDDTrain+.info -sl 5 -p -t 100 -o nsl-forest
   
    L'output di questa operazione viene archiviato nella directory **nsl-forest** che si trova nella risorsa di archiviazione del cluster HDInsight in __wasb://user/&lt;nomeutente>/nsl-forest/nsl-forest.seq, dove &lt;nomeutente> è il nome utente usato per la sessione Desktop remoto. Questo file non è in formato leggibile dagli utenti.
5. Testare la foresta classificando il set di dati **KDDTest+.arff** . Usare il comando seguente:
   
        hadoop jar c:/apps/dist/mahout-0.9.0.2.2.9.1-8/examples/target/mahout-examples-0.9.0.2.2.9.1-8-job.jar org.apache.mahout.classifier.df.mapreduce.TestForest -i wasbs:///example/data/KDDTest+.arff -ds wasbs:///example/data/KDDTrain+.info -m nsl-forest -a -mr -o wasbs:///example/data/predictions
   
    Questo comando restituisce informazioni di riepilogo sul processo di classificazione simili alle seguenti:
   
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
   
   Questo processo crea anche un file che si trova in **wasb:///example/data/predictions/KDDTest+.arff.out** in un formato non leggibile dagli utenti.

> [!NOTE]
> I processi Mahout non sovrascrivono i file. Per eseguire di nuovo questi processi, è necessario eliminare prima i file creati dai processi precedenti.
> 
> 

## <a name="<a-name="troubleshooting"></a>troubleshooting"></a><a name="troubleshooting"></a>Risoluzione dei problemi
### <a name="<a-name="install"></a>install-mahout"></a><a name="install"></a>Installare Mahout
Mahout viene installato nei cluster HDInsight 3.1 e può essere installato manualmente nei cluster HDInsight 3.0 o 2.1 con i passaggi seguenti:

1. La versione di Mahout da usare dipende dalla versione di HDInsight del cluster. È possibile trovare la versione cluster visualizzando le proprietà per il cluster nel portale di Azure.
   
   * **Per HDInsight 2.1**, è possibile scaricare un file di archivio Java (JAR) che contiene [Mahout 0.9](http://repo2.maven.org/maven2/org/apache/mahout/mahout-core/0.9/mahout-core-0.9-job.jar).
   * **Per HDInsight 3.0**, è necessario [compilare Mahout] dal codice sorgente[build] e specificare la versione di Hadoop fornita da HDInsight. Installare i prerequisiti elencati nella pagina di compilazione, scaricare il codice sorgente e quindi usare il comando seguente per creare i file JAR di Mahout.
     
            mvn -Dhadoop2.version=2.2.0 -DskipTests clean package
     
        After the build completes, you can find the JAR file at **mahout\mrlegacy\target\mahout-mrlegacy-1.0-SNAPSHOT-job.jar**.
     
     > [!NOTE]
     > Dopo il rilascio di Mahout 1.0 dovrebbe essere possibile usare i pacchetti predefiniti con HDInsight 3.0.
     > 
     > 
2. Caricare il file JAR in **example/jars** nella risorsa di archiviazione predefinita del cluster. Sostituire CLUSTERNAME nello script seguente con il nome del cluster HDInsight, e sostituire FILENAME con il percorso per il file **mahout-coure-0.9-job.jar** .
   
        #Get the cluster info so we can get the resource group, storage, etc.
        $clusterName = "CLUSTERNAME"
        $fileToUpload = "FILENAME"
        $clusterInfo = Get-AzureRmHDInsightCluster -ClusterName $clusterName
        $resourceGroup = $clusterInfo.ResourceGroup
        $storageAccountName=$clusterInfo.DefaultStorageAccount.split('.')[0]
        $container=$clusterInfo.DefaultStorageContainer
        $storageAccountKey=(Get-AzureRmStorageAccountKey `
            -Name $storageAccountName `
        -ResourceGroupName $resourceGroup)[0].Value
   
        #Create a storage content and upload the file
        $context = New-AzureStorageContext `
            -StorageAccountName $storageAccountName `
            -StorageAccountKey $storageAccountKey
   
        Set-AzureStorageBlobContent `
            -File $fileToUpload `
            -Blob "example/jars/mahout-core-0.9-job.jar" `
            -Container $container `
            -Context $context

### <a name="cannot-overwrite-files"></a>Impossibile sovrascrivere i file
I processi Mahout non eliminano i file temporanei creati durante l'elaborazione. Inoltre, i processi non sovrascrivono un file di output esistente.

Per evitare errori durante l'esecuzione dei processi Mahout, eliminare i file temporanei e di output da un'esecuzione all'altra o usare nomi univoci per le directory temporanee e di output.

### <a name="cannot-find-the-jar-file"></a>Impossibile trovare il file JAR
I cluster HDInsight 3.1 includono Mahout. Il percorso e il nome file includono il numero della versione di Mahout installata nel cluster. Lo script di esempio di Windows PowerShell in questa esercitazione usa un percorso valido a partire da novembre 2015, ma il numero di versione cambierà nei futuri aggiornamenti di HDInsight. Per determinare il percorso corrente del file JAR di Mahout per il cluster, usare i comandi di Windows PowerShell seguenti e quindi modificare lo script in modo da fare riferimento al percorso del file restituito:

    Use-AzureRmHDInsightCluster -ClusterName $clusterName
    #Get the cluster info so we can get the resource group, storage, etc.
        $clusterInfo = Get-AzureRmHDInsightCluster -ClusterName $clusterName
        $resourceGroup = $clusterInfo.ResourceGroup
        $storageAccountName=$clusterInfo.DefaultStorageAccount.split('.')[0]
        $container=$clusterInfo.DefaultStorageContainer
        $storageAccountKey=(Get-AzureRmStorageAccountKey `
            -Name $storageAccountName `
        -ResourceGroupName $resourceGroup)[0].Value
    Invoke-AzureRmHDInsightHiveJob `
            -StatusFolder "wasbs:///example/statusout" `
            -DefaultContainer $container `
            -DefaultStorageAccountName $storageAccountName `
            -DefaultStorageAccountKey $storageAccountKey `
            -Query '!${env:COMSPEC} /c dir /b /s ${env:MAHOUT_HOME}\examples\target\*-job.jar'

### <a name="<a-name="nopowershell"></a>classes-that-do-not-work-with-windows-powershell"></a><a name="nopowershell"></a>Classi che non funzionano con Windows PowerShell
I processi Mahout che usano le classi seguenti restituiscono una serie di messaggi di errore se usati da Windows PowerShell:

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

## <a name="next-steps"></a>Passaggi successivi
A questo punto, dopo aver appreso come usare Mahout, trovare altri modi per usare i dati in HDInsight:

* [Hive con HDInsight](hdinsight-use-hive.md)
* [Pig con HDInsight](hdinsight-use-pig.md)
* [MapReduce con HDInsight](hdinsight-use-mapreduce.md)

[build]: http://mahout.apache.org/developers/buildingmahout.html
[aps]: ../powershell-install-configure.md
[movielens]: http://grouplens.org/datasets/movielens/
[100k]: http://files.grouplens.org/datasets/movielens/ml-100k.zip
[getstarted]: hdinsight-hadoop-linux-tutorial-get-started.md
[upload]: hdinsight-upload-data.md
[ml]: http://en.wikipedia.org/wiki/Machine_learning
[forest]: http://en.wikipedia.org/wiki/Random_forest
[management]: https://manage.windowsazure.com/
[enableremote]: ./media/hdinsight-mahout/enableremote.png
[connect]: ./media/hdinsight-mahout/connect.png
[hadoopcli]: ./media/hdinsight-mahout/hadoopcli.png
[tools]: https://github.com/Blackmist/hdinsight-tools




<!--HONumber=Oct16_HO2-->


