---
title: Generare raccomandazioni da PowerShell con HDInsight basato su Mahout | Microsoft Docs
description: Informazioni su come usare la libreria di Machine Learning Apache Mahout per generare raccomandazioni di film con HDInsight (Hadoop) da uno script PowerShell in esecuzione sul client dell&quot;utente.
services: hdinsight
documentationcenter: 
author: Blackmist
manager: jhubbard
editor: cgronlun
tags: azure-portal
ms.assetid: 07b57208-32aa-4e59-900a-6c934fa1b7a7
ms.service: hdinsight
ms.custom: hdinsightactive
ms.workload: big-data
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 05/25/2017
ms.author: larryfr
ms.translationtype: Human Translation
ms.sourcegitcommit: 125f05f5dce5a0e4127348de5b280f06c3491d84
ms.openlocfilehash: 102cd55a0b55e18ec5240bf6ddf94a1909c08ebb
ms.contentlocale: it-it
ms.lasthandoff: 05/22/2017


---
# <a name="generate-movie-recommendations-by-using-apache-mahout-with-hadoop-in-hdinsight-powershell"></a>Generare raccomandazioni di film mediante Apache Mahout con Hadoop in HDInsight (PowerShell)

[!INCLUDE [mahout-selector](../../includes/hdinsight-selector-mahout.md)]

Informazioni su come usare la libreria di Machine Learning [Apache Mahout](http://mahout.apache.org) con Azure HDInsight per generare raccomandazioni di film. L'esempio riportato in questo documento usa Azure PowerShell per eseguire i processi Mahout.

## <a name="prerequisites"></a>Prerequisiti

* Un cluster HDInsight basato su Linux. Per informazioni su come crearne uno, vedere [Introduzione all'uso di Hadoop basato su Linux in HDInsight][getstarted].

> [!IMPORTANT]
> Linux è l'unico sistema operativo usato in HDInsight versione 3.4 o successiva. Per altre informazioni, vedere la sezione relativa al [ritiro di HDInsight in Windows](hdinsight-component-versioning.md#hdi-version-33-nearing-retirement-date).

* [Azure PowerShell](/powershell/azure/overview)

## <a name="recommendations"></a>Generare raccomandazioni con Azure PowerShell

> [!WARNING]
> Il processo in questa sezione funziona con Azure PowerShell. Molte delle classi offerte da Mahout attualmente non funzionano con Azure PowerShell. Per l'elenco delle classi che non funzionano con Azure PowerShell, vedere la sezione [Risoluzione dei problemi](#troubleshooting).
>
> Per un esempio su come usare SSH per connettersi a HDInsight ed eseguire esempi Mahout direttamente nel cluster, vedere [Generare raccomandazioni mediante Mahout e HDInsight (SSH)](hdinsight-hadoop-mahout-linux-mac.md).

Una delle funzioni fornite da Mahout è un motore di raccomandazione. Questo motore accetta i dati nei formati `userID`, `itemId` e `prefValue` (la preferenza degli utenti per l'elemento). Mahout usa i dati per determinare gli utenti con preferenze di elementi simili, che possono essere usate per le raccomandazioni.

Nell'esempio seguente viene illustrata una procedura dettagliata semplificata del funzionamento del processo di raccomandazione:

* **Co-occorrenza**: a Joe, Alice e Bob piacciono *Guerre stellari*, *L'Impero colpisce ancora* e *Il ritorno dello Jedi*. Mahout determina che agli utenti a cui piace uno di questi film piacciono anche gli altri due.

* **Co-occorrenza**: a Bob e Alice piacciono anche *La minaccia fantasma*, *L'attacco dei cloni* e *La vendetta dei Sith*. Mahout determina che agli utenti a cui piacciono i tre film precedenti piacciono anche questi tre.

* **Raccomandazione per somiglianza**: poiché a Joe piacciono i primi tre film, Mahout cerca i film che piacciono ad altri utenti con preferenze simili ma che Joe non ha guardato o per i quali non ha ancora espresso una preferenza o una valutazione. In questo caso, Mahout raccomanda *La minaccia fantasma*, *L'attacco dei cloni* e *La vendetta dei Sith*.

### <a name="understanding-the-data"></a>Informazioni sui dati

[GroupLens Research][movielens] offre i dati di classificazione dei film in un formato compatibile con Mahout. Questi dati sono disponibili nello spazio di archiviazione predefinito del cluster in `/HdiSamples//HdiSamples/MahoutMovieData`.

Sono disponibili due file, `moviedb.txt` (informazioni sui film) e `user-ratings.txt`. Il file `user-ratings.txt` viene usato durante l'analisi. Il file `moviedb.txt` viene usato per generare testo descrittivo quando si visualizzano i risultati dell'analisi.

I dati contenuti in user-ratings.txt presentano una struttura `userID`, `movieID`, `userRating` e `timestamp` che indica come ogni utente ha classificato un film. Di seguito è riportato un esempio dei dati:

    196    242    3    881250949
    186    302    3    891717742
    22    377    1    878887116
    244    51    2    880606923
    166    346    1    886397596

### <a name="run-the-job"></a>Eseguire il processo

Usare lo script Windows PowerShell seguente per eseguire un processo che usi il motore di raccomandazione Mahout con i dati del film:

> [!NOTE]
> Il file richiede le informazioni usate per connettersi al cluster HDInsight ed eseguire i processi. Potrebbero volerci diversi minuti per completare i processi e scaricare il file output.txt.

[!code-powershell[main](../../powershell_scripts/hdinsight/mahout/use-mahout.ps1?range=5-98)]

> [!NOTE]
> I processi Mahout non rimuovono i dati temporanei creati durante l'elaborazione del processo. Nel processo di esempio è specificato il parametro `--tempDir` per isolare i file temporanei in una directory specifica.

Il processo Mahout non restituisce l'output in STDOUT, ma lo archivia nella directory di output specificata come **part-r-00000**. Lo script scaricherà questo file in **output.txt** nella directory corrente nella workstation.

Il testo seguente riporta un esempio del contenuto di questo file:

    1    [234:5.0,347:5.0,237:5.0,47:5.0,282:5.0,275:5.0,88:5.0,515:5.0,514:5.0,121:5.0]
    2    [282:5.0,210:5.0,237:5.0,234:5.0,347:5.0,121:5.0,258:5.0,515:5.0,462:5.0,79:5.0]
    3    [284:5.0,285:4.828125,508:4.7543354,845:4.75,319:4.705128,124:4.7045455,150:4.6938777,311:4.6769233,248:4.65625,272:4.649266]
    4    [690:5.0,12:5.0,234:5.0,275:5.0,121:5.0,255:5.0,237:5.0,895:5.0,282:5.0,117:5.0]

La prima colonna rappresenta il valore `userID`. I valori racchiusi tra "[" e "]" sono `movieId`:`recommendationScore`.

Lo script scarica anche i file `moviedb.txt` e `user-ratings.txt` , necessari per formattare l'output in modo più leggibile.

### <a name="view-the-output"></a>Visualizzare l'output

Anche se l'output generato risulta appropriato per l'uso in un'applicazione, non è facilmente leggibile. Il `moviedb.txt` dal server può essere utilizzato per risolvere il `movieId` nel nome di un film. Usare lo script PowerShell seguente per vedere le raccomandazioni con i nomi dei film:

[!code-powershell[main](../../powershell_scripts/hdinsight/mahout/use-mahout.ps1?range=106-180)]

Usare il comando seguente per visualizzare le indicazioni in un formato semplice: 

```powershell
.\show-recommendation.ps1 -userId 4 -userDataFile .\user-ratings.txt -movieFile .\moviedb.txt -recommendationFile .\output.txt
```

L'output è simile al testo seguente:

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

## <a name="troubleshooting"></a>Risoluzione dei problemi

### <a name="cannot-overwrite-files"></a>Impossibile sovrascrivere i file

I processi Mahout non eliminano i file temporanei creati durante l'elaborazione. Inoltre, i processi non sovrascrivono un file di output esistente.

Per evitare errori durante l'esecuzione dei processi Mahout, eliminare i file temporanei e di output da un'esecuzione all'altra. Utilizzare il seguente script PowerShell per rimuovere i file creati dagli script precedenti in questo documento:

```powershell
# Login to your Azure subscription
# Is there an active Azure subscription?
$sub = Get-AzureRmSubscription -ErrorAction SilentlyContinue
if(-not($sub))
{
    Add-AzureRmAccount
}

# Get cluster info
$clusterName = Read-Host -Prompt "Enter the HDInsight cluster name"
$creds=Get-Credential -Message "Enter the login for the cluster"

#Get the cluster info so we can get the resource group, storage, etc.
$clusterInfo = Get-AzureRmHDInsightCluster -ClusterName $clusterName
$resourceGroup = $clusterInfo.ResourceGroup
$storageAccountName = $clusterInfo.DefaultStorageAccount.split('.')[0]
$container = $clusterInfo.DefaultStorageContainer
$storageAccountKey = (Get-AzureRmStorageAccountKey `
    -Name $storageAccountName `
-ResourceGroupName $resourceGroup)[0].Value

#Create a storage context and upload the file
$context = New-AzureStorageContext `
    -StorageAccountName $storageAccountName `
    -StorageAccountKey $storageAccountKey

#Azure PowerShell can't delete blobs using wildcard,
#so have to get a list and delete one at a time
# Start with the output
$blobs = Get-AzureStorageBlob -Container $container -Context $context -Prefix "example/out"
foreach($blob in $blobs)
{
    Remove-AzureStorageBlob -Blob $blob.Name -Container $container -context $context
}
# Next the temp files
$blobs = Get-AzureStorageBlob -Container $container -Context $context -Prefix "example/temp"
foreach($blob in $blobs)
{
    Remove-AzureStorageBlob -Blob $blob.Name -Container $container -context $context
}
```

### <a name="nopowershell"></a>Classi che non funzionano con Azure PowerShell

I processi Mahout che usano le classi seguenti restituiscono una serie di messaggi di errore quando vengono usati da Windows PowerShell:

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

Per eseguire i processi che usano queste classi, connettersi al cluster HDInsight usando SSH ed eseguire i processi dalla riga di comando. Per un esempio su come usare SSH per eseguire processi Mahout, vedere [Generare raccomandazioni mediante Mahout e HDInsight (SSH)](hdinsight-hadoop-mahout-linux-mac.md).

## <a name="next-steps"></a>Passaggi successivi

A questo punto, dopo aver appreso come usare Mahout, trovare altri modi per usare i dati in HDInsight:

* [Hive con HDInsight](hdinsight-use-hive.md)
* [Pig con HDInsight](hdinsight-use-pig.md)
* [MapReduce con HDInsight](hdinsight-use-mapreduce.md)

[build]: http://mahout.apache.org/developers/buildingmahout.html
[aps]: /powershell/azureps-cmdlets-docs
[movielens]: http://grouplens.org/datasets/movielens/
[100k]: http://files.grouplens.org/datasets/movielens/ml-100k.zip
[getstarted]: hdinsight-hadoop-linux-tutorial-get-started.md
[upload]: hdinsight-upload-data.md
[ml]: http://en.wikipedia.org/wiki/Machine_learning
[forest]: http://en.wikipedia.org/wiki/Random_forest
[enableremote]: ./media/hdinsight-mahout/enableremote.png
[connect]: ./media/hdinsight-mahout/connect.png
[hadoopcli]: ./media/hdinsight-mahout/hadoopcli.png
[tools]: https://github.com/Blackmist/hdinsight-tools

