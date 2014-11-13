<properties urlDisplayName="Upload Data" pageTitle="Caricare dati per processi Hadoop in HDInsight | Azure" metaKeywords="" description="Informazioni su come caricare i dati e accedervi in HDInsight con Esplora archivi Azure, Azure PowerShell, la riga di comando di Hadoop o Sqoop." metaCanonical="" services="storage,hdinsight" documentationCenter="" title="Caricare dati per processi Hadoop in HDInsight" authors="jgao" solutions="" manager="paulettm" editor="cgronlun" />

<tags ms.service="hdinsight" ms.workload="big-data" ms.tgt_pltfrm="na" ms.devlang="na" ms.topic="article" ms.date="01/01/1900" ms.author="jgao" />

# Caricare dati per processi Hadoop in HDInsight

Azure HDInsight include un'interfaccia completa per il file system HDFS (Hadoop Distributed File System) sull'archivio BLOB di Azure. È stata progettata come estensione HDFS per offrire un'esperienza lineare ai clienti tramite l'abilitazione del set completo di componenti nell'ecosistema Hadoop per il funzionamento diretto sui dati gestiti. L'archiviazione BLOB e HDFS sono file system distinti, ottimizzati per l'archiviazione di dati e per l'esecuzione di calcoli su di essi. Per i vantaggi dell'utilizzo dell'archivio BLOB di Azure, vedere [Usare l'archivio BLOB di Azure con HDInsight][Usare l'archivio BLOB di Azure con HDInsight].

In genere i cluster HDInsight di Azure vengono distribuiti per l'esecuzione di processi MapReduce e rimossi dopo il completamento dei processi. Mantenere i dati nei cluster HDFS dopo il completamento dei calcoli rappresenterebbe una soluzione di archiviazione dei dati molto costosa. L'archiviazione BLOB di Azure offre un'opzione di archiviazione con caratteristiche di scalabilità e capacità elevata, a basso costo e condivisibile per i dati che devono essere elaborati mediante HDInsight. L'archiviazione dei dati in un BLOB consente l'eliminazione sicura dei cluster HDInsight usati per i calcoli, senza perdita di dati utente.

L'archiviazione BLOB di Azure è accessibile usando [AzCopy][AzCopy], [Azure PowerShell][Azure PowerShell], la [libreria client di archiviazione di Azure per .NET][libreria client di archiviazione di Azure per .NET] oppure gli strumenti di esplorazione. Di seguito sono elencati alcuni degli strumenti disponibili:

-   [Azure Storage Explorer][Azure Storage Explorer]
-   [Cloud Storage Studio 2][Cloud Storage Studio 2]
-   [CloudXplorer][CloudXplorer]
-   [Azure Explorer][Azure Explorer]
-   [Azure Explorer PRO][Azure Explorer PRO]

**Prerequisiti**

Per eseguire le procedure descritte nell'articolo è necessario:

-   Disporre di un cluster HDInsight di Azure. Per istruzioni, vedere [Introduzione ad Azure HDInsight][Introduzione ad Azure HDInsight] o [Provisioning di cluster HDInsight][Provisioning di cluster HDInsight].

## Contenuto dell'articolo

-   [Caricare i dati nell'archiviazione BLOB tramite AzCopy][Caricare i dati nell'archiviazione BLOB tramite AzCopy]
-   [Caricare i dati nell'archiviazione BLOB tramite Azure PowerShell][Caricare i dati nell'archiviazione BLOB tramite Azure PowerShell]
-   [Caricare i dati nell'archiviazione BLOB tramite Azure Storage Explorer][Caricare i dati nell'archiviazione BLOB tramite Azure Storage Explorer]
-   [Caricare i dati nell'archiviazione BLOB tramite la riga di comando di Hadoop][Caricare i dati nell'archiviazione BLOB tramite la riga di comando di Hadoop]
-   [Importare i dati dal database SQL di Azure all'archiviazione BLOB tramite Sqoop][Importare i dati dal database SQL di Azure all'archiviazione BLOB tramite Sqoop]

## <span id="azcopy"></span></a>Caricare i dati nell'archiviazione BLOB tramite AzCopy

AzCopy è un'utilità della riga di comando progettata per semplificare il trasferimento di dati da e verso gli account di archiviazione di Azure. Può essere usata come strumento autonomo o incorporata in un'applicazione esistente. [Download di AzCopy][Download di AzCopy].

La sintassi di AzCopy è:

    AzCopy <Source> <Destination> [filePattern [filePattern...]] [Options]

Per altre informazioni, vedere [AzCopy - Caricamento e download di file per BLOB di Azure][AzCopy]

## <span id="powershell"></span></a>Caricare i dati nell'archiviazione BLOB tramite Azure PowerShell

Azure PowerShell è un ambiente di scripting potente che può essere usato per controllare e automatizzare la distribuzione e la gestione dei carichi di lavoro in Azure. È possibile usare Azure PowerShell per caricare dati nell'archiviazione BLOB in modo che possano essere elaborati da processi MapReduce. Per informazioni sulla configurazione della workstation per l'esecuzione di Azure PowerShell, vedere [Come installare e configurare Azure PowerShell][Come installare e configurare Azure PowerShell].

**Per caricare un file locale nell'archiviazione BLOB**

1.  Aprire la finestra della console Azure PowerShell come illustrato in [Come installare e configurare Azure PowerShell][Come installare e configurare Azure PowerShell].
2.  Impostare i valori delle prime cinque variabili nello script seguente:

        $subscriptionName = "<AzureSubscriptionName>"
        $storageAccountName = "<StorageAccountName>"
        $containerName = "<ContainerName>"

        $fileName ="<LocalFileName>"
        $blobName = "<BlobName>"

        # Get the storage account key
        Select-AzureSubscription $subscriptionName
        $storageaccountkey = get-azurestoragekey $storageAccountName | %{$_.Primary}

        # Create the storage context object
        $destContext = New-AzureStorageContext -StorageAccountName $storageAccountName -StorageAccountKey $storageaccountkey

        # Copy the file from local workstation to the Blob container        
        Set-AzureStorageBlobContent -File $fileName -Container $containerName -Blob $blobName -context $destContext

3.  Incollare lo script nella finestra della console Azure PowerShell per eseguirlo.

I contenitori di archiviazione BLOB archiviano i dati come coppie chiave-valore e non esiste una gerarchia di directory. È tuttavia possibile usare il carattere "/" all'interno del nome della chiave per far sembrare che un file sia archiviato in una struttura di directory. Ad esempio, la chiave di un BLOB potrebbe essere *input/log1.txt*. Non esiste realmente una directory "input", ma grazie alla presenza del carattere "/", il nome della chiave ha l'aspetto di un percorso di file. Nello script precedente è possibile assegnare al file una struttura di cartelle impostando la variabile $blobname. Ad esempio *$blobname="cartellapersonale/filepersonale.txt"*.

Usando gli strumenti di esplorazione di Azure si potrebbe notare la presenza di file da 0 byte. Questi file servono a due scopi:

-   In caso di cartelle vuote, fungono da indicatore dell'esistenza della cartella. L'archiviazione BLOB è in grado di comprendere che se esiste un BLOB denominato foo/bar, esiste una cartella denominata foo. La presenza di una cartella vuota denominata foo, tuttavia, è indicata esclusivamente dalla presenza di questo file speciale da 0 byte.
-   Questi file contengono metadati speciali necessari per il file system Hadoop, in particolare relativi alle autorizzazioni e ai proprietari delle cartelle.

## <span id="storageexplorer"></span></a>Caricare i dati nell'archiviazione BLOB tramite Azure Storage Explorer

*Azure Storage Explorer* è uno strumento utile per esaminare e modificare i dati nell'archiviazione di Azure. Si tratta di uno strumento gratuito che è possibile scaricare dalla pagina <http://azurestorageexplorer.codeplex.com/>.

Prima di usare lo strumento è necessario conoscere il nome e la chiave dell'account di archiviazione di Azure. Per istruzioni su come ottenere queste informazioni, vedere la sezione "Procedura: Visualizzare, copiare e rigenerare le chiavi di accesso alle risorse di archiviazione" di [Come gestire gli account di archiviazione][Come gestire gli account di archiviazione].

1.  Eseguire Azure Storage Explorer.

    ![HDI.AzureStorageExplorer][HDI.AzureStorageExplorer]

2.  Fare clic su **Add Account**. Dopo l'aggiunta di un account ad Azure Storage Explorer, non è necessario ripetere questo passaggio.

    ![HDI.ASEAddAccount][HDI.ASEAddAccount]

3.  Immettere il nome e la chiave dell'account di archiviazione rispettivamente nei campi **Storage account name** e **Storage account key**, quindi fare clic su **Add Storage Account**. È possibile aggiungere più account di archiviazione, ognuno dei quali verrà visualizzato in una scheda.
4.  In **Storage Type** fare clic su **Blobs**.

    ![HDI.ASEBlob][HDI.ASEBlob]

5.  In **Container** fare clic sul contenitore associato al cluster HDInsight. Quando si crea un cluster HDInsight è necessario specificare un contenitore. In caso contrario, il contenitore verrà creato automaticamente dal processo di creazione del cluster.
6.  In **Blob** fare clic su **Upload**.
7.  Specificare un file da caricare e quindi fare clic su **Open**.

## <span id="commandline"></span></a>Caricare i dati nell'archiviazione BLOB tramite la riga di comando di Hadoop

Per usare la riga di comando di Hadoop è innanzitutto necessario collegarsi al cluster tramite Desktop remoto.

1.  Accedere al [portale di gestione][portale di gestione].
2.  Fare clic su **HDINSIGHT**. Verrà visualizzato un elenco dei cluster Hadoop distribuiti.
3.  Fare clic sul cluster HDInsight in cui si desidera caricare i dati.
4.  Fare clic su **CONFIGURATION** nella parte superiore della pagina.
5.  Fare clic su **ENABLE REMOTE** se non è stato abilitato Desktop remoto e quindi seguire le istruzioni. In caso contrario, continuare con il passaggio successivo.
6.  Fare clic su **CONNECT** nella parte inferiore della pagina.
7.  Fare clic su **Apri**.
8.  Immettere le credenziali e quindi fare clic su **OK**.
9.  Fare clic su **Yes**.
10. Dal desktop fare clic su **Hadoop Command Line**.
11. Nell'esempio seguente viene illustrato come copiare il file davinci.txt file dal file system locale nel nodo head di HDInsight alla directory /example/data.

        hadoop dfs -copyFromLocal C:\temp\davinci.txt /example/data/davinci.txt

    Poiché il file system predefinito si trova nell'archivio BLOB di Azure, /example/datadavinci.txt si trova in effetti nell'archivio BLOB di Azure. È inoltre possibile fare riferimento al file come segue:

        wasb:///example/data/davinci.txt 

    oppure

        wasbs://<ContainerName>@<StorageAccountName>.blob.core.windows.net/example/data/davinci.txt

    Quando si usa *wasbs* è necessario specificare un nome di dominio completo.

12. Usare il comando seguente per ottenere un elenco dei file aggiornati:

        hadoop dfs -lsr /example/data

## <span id="sqoop"></span></a> Importazione di dati in HDFS da un database SQL o da SQL Server tramite Sqoop

Sqoop è uno strumento progettato per il trasferimento di dati tra Hadoop e i database relazionali. Può essere usato per importare dati in HDFS (Hadoop Distributed File System) da un sistema di gestione di database relazionali (RDBMS), ad esempio SQL, MySQL oppure Oracle, trasformare i dati in Hadoop con MapReduce o Hive e quindi esportarli nuovamente in un sistema RDBMS. Per altre informazioni, vedere la [Guida dell'utente di Sqoop][Guida dell'utente di Sqoop].

Prima di importare i dati è necessario conoscere il nome del server di database SQL di Azure, il nome dell'account di database, la password dell'account e il nome del database.

Per impostazione predefinita un database SQL di Azure consente connessioni da servizi di Azure, ad esempio Azure HDinsight. Se questa impostazione del firewall è disabilitata, sarà necessario abilitarla nel portale di gestione di Azure. Per istruzioni sulla creazione di un database SQL e sulla configurazione di regole del firewall, vedere [Come creare e configurare database SQL][Come creare e configurare database SQL].

Nella procedura seguente viene usato PowerShell per inviare un processo Sqoop.

**Per importare dati in HDInsight usando Sqoop e PowerShell**

1.  Aprire la finestra della console Azure PowerShell come illustrato in [Come installare e configurare Azure PowerShell][Come installare e configurare Azure PowerShell].
2.  Impostare i valori delle prime otto variabili nello script seguente:

        $subscriptionName = "<AzureSubscriptionName>"
        $clusterName = "<HDInsightClusterName>"

        $sqlDatabaseServerName = "<SQLDatabaseServerName>"
        $sqlDatabaseUserName = "<SQLDatabaseDatabaseName>"
        $sqlDatabasePassword = "<SQLDatabasePassword>"
        $sqlDatabaseDatabaseName = "<SQLDatabaseDatabaseName>"
        $tableName = "<SQLDatabaseTableName>"

        $hdfsOutputDir = "<OutputPath>"  # This is the HDFS path for the output file, for example "/lineItemData".

        Select-AzureSubscription $subscriptionName      
        $sqoopDef = New-AzureHDInsightSqoopJobDefinition -Command "import --connect jdbc:sqlserver://$sqlDatabaseServerName.database.windows.net;user=$sqlDatabaseUserName@$sqlDatabaseServerName;password=$sqlDatabasePassword;database=$sqlDatabaseDatabaseName --table $tableName --target-dir $hdfsOutputDir -m 1" 

        $sqoopJob = Start-AzureHDInsightJob -Cluster $clusterName -JobDefinition $sqoopDef #-Debug -Verbose
        Wait-AzureHDInsightJob -WaitTimeoutInSeconds 3600 -Job $sqoopJob

        Write-Host "Standard Error" -BackgroundColor Green
        Get-AzureHDInsightJobOutput -Cluster $clusterName -JobId $sqoopJob.JobId -StandardError
        Write-Host "Standard Output" -BackgroundColor Green
        Get-AzureHDInsightJobOutput -Cluster $clusterName -JobId $sqoopJob.JobId -StandardOutput

3.  Incollare lo script nella finestra della console Azure PowerShell per eseguirlo. Per un esempio di PowerShell per il recupero del file di dati dall'archivio BLOB di Azure, vedere [Introduzione ad Azure HDInsight][Introduzione ad Azure HDInsight].

Per altre informazioni sull'utilizzo di Sqoop, vedere [Usare Sqoop con HDInsight][Usare Sqoop con HDInsight].

## Passaggi successivi

Dopo aver appreso come importare dati in HDInsight, usare gli articoli seguenti per informazioni sull'esecuzione delle analisi:

-   [Introduzione all'utilizzo di Azure HDInsight][Introduzione ad Azure HDInsight]
-   [Inviare processi Hadoop a livello di codice][Inviare processi Hadoop a livello di codice]
-   [Usare Hive con HDInsight][Usare Hive con HDInsight]
-   [Usare Pig con HDInsight][Usare Pig con HDInsight]

  [Usare l'archivio BLOB di Azure con HDInsight]: ../hdinsight-use-blob-storage/
  [AzCopy]: http://blogs.msdn.com/b/windowsazurestorage/archive/2012/12/03/azcopy-uploading-downloading-files-for-windows-azure-blobs.aspx
  [Azure PowerShell]: http://msdn.microsoft.com/it-it/library/windowsazure/jj152841.aspx
  [libreria client di archiviazione di Azure per .NET]: /it-it/develop/net/how-to-guides/blob-storage/
  [Azure Storage Explorer]: http://azurestorageexplorer.codeplex.com/
  [Cloud Storage Studio 2]: http://www.cerebrata.com/Products/CloudStorageStudio/
  [CloudXplorer]: http://clumsyleaf.com/products/cloudxplorer
  [Azure Explorer]: http://www.cloudberrylab.com/free-microsoft-azure-explorer.aspx
  [Azure Explorer PRO]: http://www.cloudberrylab.com/microsoft-azure-explorer-pro.aspx
  [Introduzione ad Azure HDInsight]: ../hdinsight-get-started/
  [Provisioning di cluster HDInsight]: ../hdinsight-provision-clusters/
  [Caricare i dati nell'archiviazione BLOB tramite AzCopy]: #azcopy
  [Caricare i dati nell'archiviazione BLOB tramite Azure PowerShell]: #powershell
  [Caricare i dati nell'archiviazione BLOB tramite Azure Storage Explorer]: #storageexplorer
  [Caricare i dati nell'archiviazione BLOB tramite la riga di comando di Hadoop]: #commandline
  [Importare i dati dal database SQL di Azure all'archiviazione BLOB tramite Sqoop]: #sqoop
  [Download di AzCopy]: http://aka.ms/WaCopy
  [Come installare e configurare Azure PowerShell]: ../install-configure-powershell/
  [Come gestire gli account di archiviazione]: ../storage-manage-storage-account/
  [HDI.AzureStorageExplorer]: ./media/hdinsight-upload-data/HDI.AzureStorageExplorer.png
  [HDI.ASEAddAccount]: ./media/hdinsight-upload-data/HDI.ASEAddAccount.png
  [HDI.ASEBlob]: ./media/hdinsight-upload-data/HDI.ASEBlob.png
  [portale di gestione]: https://manage.windowsazure.com
  [Guida dell'utente di Sqoop]: http://sqoop.apache.org/docs/1.4.4/SqoopUserGuide.html
  [Come creare e configurare database SQL]: ../sql-database-create-configure/
  [Usare Sqoop con HDInsight]: ../hdinsight-use-sqoop/
  [Inviare processi Hadoop a livello di codice]: ../hdinsight-submit-hadoop-jobs-programmatically/
  [Usare Hive con HDInsight]: ../hdinsight-use-hive/
  [Usare Pig con HDInsight]: ../hdinsight-use-pig
