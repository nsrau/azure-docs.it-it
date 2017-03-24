---
title: Sviluppare processi Python MapReduce con HDInsight | Documentazione Microsoft
description: Informazioni su come creare ed eseguire processi Python MapReduce nei cluster HDInsight basati su Linux.
services: hdinsight
documentationcenter: 
author: Blackmist
manager: jhubbard
editor: cgronlun
tags: azure-portal
ms.assetid: 7631d8d9-98ae-42ec-b9ec-ee3cf7e57fb3
ms.service: hdinsight
ms.custom: hdinsightactive
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: big-data
ms.date: 02/06/2017
ms.author: larryfr
translationtype: Human Translation
ms.sourcegitcommit: cf233279c0a26c3d2970596b1bb515508da20b89
ms.openlocfilehash: ad696f14d48452840805bc413d890309e523ce34
ms.lasthandoff: 02/07/2017


---
# <a name="develop-python-streaming-programs-for-hdinsight"></a>Sviluppo di programmi per la creazione di flussi Python per HDInsight

In Hadoop è disponibile un'API di flusso per MapReduce che consente di scrivere funzioni di mapping e riduzione in linguaggi diversi da Java. In questo articolo si apprenderà come usare Python per eseguire operazioni MapReduce.

Questo articolo si basa su informazioni ed esempi pubblicati da Michael Noll nell'articolo relativo alla [scrittura di un programma MapReduce di Hadoop in Python](http://www.michael-noll.com/tutorials/writing-an-hadoop-mapreduce-program-in-python/).

## <a name="prerequisites"></a>Prerequisiti

Per seguire la procedura descritta in questo articolo, è necessario quanto segue:

* Un cluster Hadoop basato su Linux in HDInsight

  > [!IMPORTANT]
  > I passaggi descritti in questo documento richiedono un cluster HDInsight che usa Linux. Linux è l'unico sistema operativo usato in HDInsight versione 3.4 o successiva. Per altre informazioni, vedere [HDInsight deprecato in Windows](hdinsight-component-versioning.md#hdi-version-32-and-33-nearing-deprecation-date).

* Un editor di testo
  
  > [!IMPORTANT]
  > L'editor di testo deve usare LF come terminazione di riga. Se usa CRLF, ciò causerà errori in seguito all'esecuzione del processo MapReduce su cluster HDInsight basati su Linux. In caso di dubbi, usare il passaggio facoltativo nella sezione [Eseguire MapReduce](#run-mapreduce) per convertire ogni CRLF in LF.

* **Familiarità con SSH e SCP**. Per altre informazioni sull'uso di SSH e SCP con HDInsight, vedere gli articoli seguenti:
  
  * **Client Linux, Unix o OS X**: vedere [Usare SSH con Hadoop basato su Linux in HDInsight da Linux, OS X o Unix](hdinsight-hadoop-linux-use-ssh-unix.md)

  * **Client Windows**: vedere [Usare SSH con Hadoop basato su Linux in HDInsight da Windows](hdinsight-hadoop-linux-use-ssh-windows.md)

## <a name="word-count"></a>Conteggio di parole

In questo esempio viene implementato un conteggio di parole di base usando un mapper e un reducer. Il mapper scompone le frasi in singole parole e il reducer aggrega le parole e i conteggi per produrre l'output.

Il diagramma di flusso illustra ciò che accade durante le fasi di mapping e riduzione.

![illustrazione di MapReduce](./media/hdinsight-hadoop-streaming-python/HDI.WordCountDiagram.png)

## <a name="why-python"></a>Perché Python?

Python è un linguaggio di programmazione generale di alto livello che consente di esprimere concetti in un minor numero di righe di codice rispetto a molti altri linguaggi. Si è recentemente diffuso presso i data scientist come linguaggio per la creazione di prototipi grazie alla natura interpretata, alla tipizzazione dinamica e alla sintassi elegante che lo rendono appropriato per lo sviluppo rapido di applicazioni.

Python è installato in tutti i cluster HDInsight.

## <a name="streaming-mapreduce"></a>Flusso di MapReduce

Hadoop consente di specificare un file che contiene la logica di mapping e riduzione usata da un processo. I requisiti specifici per la logica di mapping e riduzione sono:

* **Input**: i componenti di mapping e riduzione devono leggere i dati di input da STDIN.
* **Output**: i componenti di mapping e riduzione devono scrivere i dati di output in STDOUT.
* **Formato dati**: i dati usati e prodotti devono essere una coppia chiave/valore, separati da un carattere di tabulazione.

Python è in grado di gestire facilmente questi requisiti usando il modulo **sys** per leggere da STDIN e usando **print** per stampare in STDOUT. Il resto dell'attività consiste semplicemente nella formattazione dei dati con un carattere di tabulazione (`\t`) tra la chiave e il valore.

## <a name="create-the-mapper-and-reducer"></a>Creare il mapper e il reducer

Il mapper e il reducer sono file di testo, in questo caso **mapper.py** e **reducer.py** (per indicare chiaramente le operazioni eseguite da ciascuno). È possibile crearli usando l'editor di propria scelta.

### <a name="mapperpy"></a>Mapper.py

Creare un nuovo file denominato **mapper.py** e usare il seguente codice come contenuto:

```python
#!/usr/bin/env python

# Use the sys module
import sys

# 'file' in this case is STDIN
def read_input(file):
    # Split each line into words
    for line in file:
        yield line.split()

def main(separator='\t'):
    # Read the data using read_input
    data = read_input(sys.stdin)
    # Process each words returned from read_input
    for words in data:
        # Process each word
        for word in words:
            # Write to STDOUT
            print '%s%s%d' % (word, separator, 1)

if __name__ == "__main__":
    main()
```

Soffermarsi per leggere il codice e comprenderne la funzione.

### <a name="reducerpy"></a>reducer.py

Creare un nuovo file denominato **reducer.py** e usare il seguente codice come contenuto:

```python
#!/usr/bin/env python

# import modules
from itertools import groupby
from operator import itemgetter
import sys

# 'file' in this case is STDIN
def read_mapper_output(file, separator='\t'):
    # Go through each line
    for line in file:
        # Strip out the separator character
        yield line.rstrip().split(separator, 1)

def main(separator='\t'):
    # Read the data using read_mapper_output
    data = read_mapper_output(sys.stdin, separator=separator)
    # Group words and counts into 'group'
    #   Since MapReduce is a distributed process, each word
    #   may have multiple counts. 'group' will have all counts
    #   which can be retrieved using the word as the key.
    for current_word, group in groupby(data, itemgetter(0)):
        try:
            # For each word, pull the count(s) for the word
            #   from 'group' and create a total count
            total_count = sum(int(count) for current_word, count in group)
            # Write to stdout
            print "%s%s%d" % (current_word, separator, total_count)
        except ValueError:
            # Count was not a number, so do nothing
            pass

if __name__ == "__main__":
    main()
```

## <a name="upload-the-files"></a>Caricare i file

Sia **mapper.py** che **reducer.py** devono essere contenuti nel nodo head del cluster perché sia possibile eseguirli. Questa sezione offre un esempio di comando `scp` e uno script di Azure PowerShell che può essere usato per caricare i file del cluster.

> [!IMPORTANT]
> C'è una differenza importante tra l'uso di `scp` e PowerShell per caricare i file:
>
> * Con `scp` i file vengono inseriti nel nodo head primario del cluster. Ciò presuppone che successivamente ci si connetterà al nodo head e il processo verrà eseguito da una sessione SSH.
> * Lo script di PowerShell inserisce i file nella risorsa di archiviazione predefinita per il cluster. Ciò presuppone che successivamente si userà uno script di PowerShell per eseguire il processo da un client remoto.

### <a name="upload-using-scp"></a>Caricamento tramite scp

Nell'ambiente di sviluppo, usare il comando seguente dalla stessa directory di **mapper.py** e **reducer.py**. Sostituire **username** con il nome utente SSH del cluster e **clustername** con il nome del cluster.

`scp mapper.py reducer.py username@clustername-ssh.azurehdinsight.net:`

In questo modo i file verranno copiati dal sistema locale nel nodo head.

> [!NOTE]
> Se è stata usata una password per proteggere l'account SSH, verrà richiesto di specificarla. Se è stata usata una chiave SSH, potrebbe essere necessario usare il parametro `-i` e il percorso della chiave privata, ad esempio `scp -i /path/to/private/key mapper.py reducer.py username@clustername-ssh.azurehdinsight.net:`.

### <a name="upload-using-powershell"></a>Caricamento tramite PowerShell

1. Nell'ambiente di sviluppo, creare un nuovo file denominato `Put-FilesInHDInsight.ps1` e usare quanto riportato di seguito come contenuto del file:

    ```PowerShell
    param(
        [Parameter(Mandatory = $true)]
        [String]$clusterName,
        [Parameter(Mandatory = $true)]
        [String]$source,
        [Parameter(Mandatory = $true)]
        [String]$destination
    )
    # Login to your Azure subscription
    # Is there an active Azure subscription?
    $sub = Get-AzureRmSubscription -ErrorAction SilentlyContinue
    if(-not($sub))
    {
        Add-AzureRmAccount
    }

    # Get the cluster info
    $clusterInfo = Get-AzureRmHDInsightCluster -ClusterName $clusterName
    $storageInfo = $clusterInfo.DefaultStorageAccount.split('.')

    # What type of default storage are we using?
    switch ($storageInfo[1])
    {
        "blob" {
            # Get the blob storage information for the cluster
            $resourceGroup = $clusterInfo.ResourceGroup
            $storageAccountName=$storageInfo[0]
            $storageContainer=$clusterInfo.DefaultStorageContainer
            $storageAccountKey=(Get-AzureRmStorageAccountKey `
                -Name $storageAccountName `
                -ResourceGroupName $resourceGroup)[0].Value
            # Create a storage context and upload the file
            $context = New-AzureStorageContext `
                -StorageAccountName $storageAccountName `
                -StorageAccountKey $storageAccountKey
            # Upload the file
            Set-AzureStorageBlobContent `
                -File $source `
                -Blob $destination `
                -Container $storageContainer `
                -Context $context
        }
        "azuredatalakestore" {
            # Get the Data Lake Store name
            $dataLakeStoreName=$storageInfo[0]
            # Get the root of the HDInsight cluster azuredatalakestore
            $clusterRoot=$clusterInfo.DefaultStorageRootPath
            # Upload the file. Prepend the destination with the cluster root
            Import-AzureRmDataLakeStoreItem -AccountName $dataLakeStoreName -Path $source -Destination "$clusterRoot$destination"
        }
        default {
            Throw "Unknown storage type: $storageInfo[1]"
        }
    }
    ```

2. Per usare questo script per caricare un file, inserire il comando seguente in un prompt di Azure PowerShell:

    `.\Put-FilesInHDInsight.ps1 -clusterName <your HDInsight cluster name> -source mapper.py -destination mapper.py`

    Quando richiesto, immettere le credenziali di accesso HTTPS del cluster.

3. Ripetere il comando, sostituendo `mapper.py` con `reducer.py`. In questo modo i file del mapper e del reducer vengono caricati nell'archivio predefinito del cluster.

## <a name="run-mapreduce-ssh"></a>Eseguire MapReduce (SSH)

Usare la procedura seguente per connettersi al cluster ed eseguire il processo streaming MapReduce da una sessione SSH.

1. Connettersi al cluster usando SSH:
   
   `ssh username@clustername-ssh.azurehdinsight.net`
   
   > [!NOTE]
   > Se è stata usata una password per proteggere l'account SSH, verrà richiesto di specificarla. Se è stata usata una chiave SSH, potrebbe essere necessario usare il parametro `-i` e il percorso della chiave privata, ad esempio `ssh -i /path/to/private/key username@clustername-ssh.azurehdinsight.net`.

2. (Facoltativo) Se, quando si creano i file mapper.py e reducer.py, si usa un editor di testo che inserisce CRLF come terminazione di riga oppure non si sa quale terminazione di riga verrà usata dall'editor, convertire le occorrenze di CRLF di mapper.py e reducer.py in LF mediante i seguenti comandi.
   
    `perl -pi -e 's/\r\n/\n/g' mappery.py`
    `perl -pi -e 's/\r\n/\n/g' reducer.py`

3. Usare il seguente comando per avviare il processo MapReduce.
   
    `yarn jar /usr/hdp/current/hadoop-mapreduce-client/hadoop-streaming.jar -files mapper.py,reducer.py -mapper mapper.py -reducer reducer.py -input /example/data/gutenberg/davinci.txt -output /example/wordcountout`
   
    Questo comando include le seguenti parti:
   
   * **hadoop-streaming.jar**: usato durante l'esecuzione di operazioni di flusso MapReduce. Consente ad Hadoop di interagire con il codice MapReduce esterno fornito dall'utente.

   * **-files**: indica ad Hadoop che i file specificati sono necessari per il processo MapReduce e devono essere copiati in tutti i nodi di lavoro.

   * **-mapper**: indica ad Hadoop quale file usare come mapper.

   * **-reducer**: indica ad Hadoop quale file usare come reducer.

   * **-input**: il file di input di cui devono essere contate le parole.

   * **-output**: la directory in cui verrà scritto l'output.
     
     > [!NOTE]
     > Questa directory verrà creata dal processo.

Si noteranno alcune istruzioni **INFO** all'avvio del processo e infine le operazioni **map** e **reduce** visualizzate come valori percentuali.

    15/02/05 19:01:04 INFO mapreduce.Job:  map 0% reduce 0%
    15/02/05 19:01:16 INFO mapreduce.Job:  map 100% reduce 0%
    15/02/05 19:01:27 INFO mapreduce.Job:  map 100% reduce 100%

Verranno visualizzate informazioni sullo stato del processo quando verrà completato.

## <a name="run-mapreduce-powershell"></a>Eseguire MapReduce (PowerShell)

Usare la procedura seguente per eseguire il processo streaming MapReduce da PowerShell nell'ambiente di sviluppo. Lo script di PowerShell esegue il processo connettendosi al cluster HDInsight tramite WebHCat.

1. Creare un nuovo file denominato `Start-HDInsightStreamingPythonJob` e usare quanto riportato di seguito come contenuto del file:

    ```PowerShell
    param(
        [Parameter(Mandatory = $true)]
        [String]$clusterName,
        [Parameter(Mandatory = $true)]
        [String]$inputPath,
        [Parameter(Mandatory = $true)]
        [String]$outputPath
    )
    # Login to your Azure subscription
    # Is there an active Azure subscription?
    $sub = Get-AzureRmSubscription -ErrorAction SilentlyContinue
    if(-not($sub))
    {
        Add-AzureRmAccount
    }

    # Get the login (HTTPS) credentials for the cluster
    $creds=Get-Credential -Message "Enter the login for the cluster" -UserName "admin"

    # Create the streaming job definition
    # Note: This assumes that the mapper.py and reducer.py
    #       are in the root of default storage. If you put them in a 
    #       subdirectory, change the -Files parameter to the correct path.
    $jobDefinition = New-AzureRmHDInsightStreamingMapReduceJobDefinition `
        -Files "/mapper.py", "/reducer.py" `
        -Mapper "mapper.py" `
        -Reducer "reducer.py" `
        -InputPath $inputPath `
        -OutputPath $outputPath

    # Start the job
    $job = Start-AzureRmHDInsightJob `
        -ClusterName $clusterName `
        -JobDefinition $jobDefinition `
        -HttpCredential $creds

    # Wait for the job to complete
    Wait-AzureRmHDInsightJob `
        -JobId $job.JobId `
        -ClusterName $clusterName `
        -HttpCredential $creds
    ```

2. Usare il frammento seguente da un prompt di Azure PowerShell per eseguire il processo:

    `.\Start-HDInsightStreamingPythonJob.ps1 -clusterName <your HDInsight cluster name> -inputPath "/example/data/gutenberg/davinci.txt" -outputPath "/example/wordcountout"`

    Ciò consente di usare i file `mapper.py` e `reducer.py` precedentemente caricati nel cluster per contare le parole nel file `davinci.txt`. L'output viene archiviato nella cartella `/example/wordcount` nella risorsa di archiviazione predefinita del cluster.

    Quando viene completato, il processo restituisce informazioni simili alle seguenti:

    ```
    Cluster         : myhdicluster
    HttpEndpoint    : myhdicluster.azurehdinsight.net
    State           : SUCCEEDED
    JobId           : job_1486046226168_0004
    ParentId        :
    PercentComplete : map 100% reduce 100%
    ExitValue       : 0
    User            : admin
    Callback        :
    Completed       : done
    StatusFolder    : 2017-02-06T19-14-28-a3dda3ca-f489-4287-afc9-b5e16e2e7c7a
    ```

## <a name="view-the-output"></a>Visualizzare l'output

L'output del processo viene archiviato nella directory `/example/wordcountout`. È possibile visualizzarlo da una sessione SSH o scaricarlo in locale e visualizzarlo da PowerShell.

Per visualizzare i dati da una sessione SSH nel cluster, usare il comando seguente:

`hdfs dfs -text /example/wordcountout/part-00000`

Viene visualizzato un elenco di parole con l'indicazione del numero di occorrenze di ciascuna. Di seguito è riportato un esempio di dati di output:

```
wrenching       1
wretched        6
wriggling       1
wrinkled,       1
wrinkles        2
wrinkling       2
```

Per visualizzare l'output dall'ambiente di sviluppo usando PowerShell, usare la procedura seguente:

1. Creare un nuovo file denominato `Get-FilesInHDInsight.ps1` e usare quanto riportato di seguito come contenuto del file:

    ```PowerShell
    param(
        [Parameter(Mandatory = $true)]
        [String]$clusterName,
        [Parameter(Mandatory = $true)]
        [String]$source
    )
    # Login to your Azure subscription
    # Is there an active Azure subscription?
    $sub = Get-AzureRmSubscription -ErrorAction SilentlyContinue
    if(-not($sub))
    {
        Add-AzureRmAccount
    }

    # Get the cluster info
    $clusterInfo = Get-AzureRmHDInsightCluster -ClusterName $clusterName
    $storageInfo = $clusterInfo.DefaultStorageAccount.split('.')

    # What type of default storage are we using?
    switch ($storageInfo[1])
    {
        "blob" {
            # Get the blob storage information for the cluster
            $resourceGroup = $clusterInfo.ResourceGroup
            $storageAccountName=$storageInfo[0]
            $storageContainer=$clusterInfo.DefaultStorageContainer
            $storageAccountKey=(Get-AzureRmStorageAccountKey `
                -Name $storageAccountName `
                -ResourceGroupName $resourceGroup)[0].Value
            # Create a storage context and download the file
            $context = New-AzureStorageContext `
                -StorageAccountName $storageAccountName `
                -StorageAccountKey $storageAccountKey
            # Download the file
            Get-AzureStorageBlobContent `
                -Container $storageContainer `
                -Blob $source `
                -Context $context `
                -Destination "./output.txt"
            # Display the output
            Get-Content "./output.txt"
        }
        "azuredatalakestore" {
            # Get the Data Lake Store name
            $dataLakeStoreName=$storageInfo[0]
            # Get the root of the HDInsight cluster azuredatalakestore
            $clusterRoot=$clusterInfo.DefaultStorageRootPath
            # Download the file. Prepend the destination with the cluster root
            # NOTE: Unlike getting a blob, this just gets the content and no
            #       file is created locally.
            Get-AzureRmDataLakeStoreItemContent -Account $dataLakeStoreName -Path $clusterRoot$source -Confirm
        }
        default {
            Throw "Unknown storage type: $storageInfo[1]"
        }
    }
    ```

2. Da un prompt di Azure PowerShell, usare quanto indicato di seguito per eseguire lo script e visualizzare l'output:

    `Get-FilesInHDInsight.ps1 -clusterName <your HDInsight cluster name> -source "example/wordcountout/part-00000"`

    Viene visualizzato un elenco di parole con l'indicazione del numero di occorrenze di ciascuna. Di seguito è riportato un esempio di dati di output:

    ```
    wrenching       1
    wretched        6
    wriggling       1
    wrinkled,       1
    wrinkles        2
    wrinkling       2
    ```

## <a name="next-steps"></a>Passaggi successivi

Dopo aver appreso come usare i processi di flusso MapReduce con HDInsight, vedere i seguenti collegamenti per esplorare altri modi di uso di Azure HDInsight.

* [Usare Hive con HDInsight](hdinsight-use-hive.md)
* [Usare Pig con HDInsight](hdinsight-use-pig.md)
* [Usare processi MapReduce con HDInsight](hdinsight-use-mapreduce.md)


