<properties  linkid="manage-services-hdinsight-howto-upload-data-to-hdinsight" urlDisplayName="Upload Data" pageTitle="Upload data to HDInsight | Azure" metaKeywords="" description="Learn how to upload and access data in HDInsight using Azure Storage Explorer, the interactive console, the Hadoop command line, or Sqoop." metaCanonical="" services="storage,hdinsight" documentationCenter="" title="Upload data to HDInsight" authors="jgao" solutions="" manager="paulettm" editor="cgronlun" />

# Caricamento di dati in HDInsight

Azure HDInsight include un'interfaccia completa per il file system HDFS
(Hadoop Distributed File System) sull'archivio BLOB di Azure. È stata
progettata come estensione HDFS per offrire un'esperienza lineare ai clienti tramite l'abilitazione del set completo di componenti nell'ecosistema Hadoop per il funzionamento diretto sui dati gestiti. L'archiviazione BLOB e HDFS sono file system distinti, ottimizzati per l'archiviazione di dati e per l'esecuzione di calcoli su di essi. Per i vantaggi dell'utilizzo dell'archivio BLOB di Azure, vedere [Utilizzo dell'archivio BLOB di Azure con HDInsight](/en-us/manage/services/hdinsight/howto-blob-store/).

In genere i cluster HDInsight di Azure vengono distribuiti per l'esecuzione di processi MapReduce e rimossi dopo il completamento dei processi. Mantenere i dati nei cluster HDFS dopo il completamento dei calcoli rappresenterebbe una soluzione di archiviazione dei dati molto costosa. L'archiviazione BLOB di Azure offre un'opzione di archiviazione con caratteristiche di scalabilità e capacità elevata, a basso costo e condivisibile per i dati che devono essere elaborati mediante HDInsight. L'archiviazione dei dati in un BLOB consente l'eliminazione sicura dei cluster HDInsight utilizzati per i calcoli, senza perdita di dati utente.

L'archiviazione BLOB di Azure è accessibile utilizzando [AzCopy][1],
[Azure PowerShell][2], la [libreria client di archiviazione di Azure per
.NET](/en-us/develop/net/how-to-guides/blob-storage/) oppure gli
strumenti di esplorazione. Di seguito sono elencati alcuni degli strumenti disponibili:

* [Esplora archivi Azure][3]
* [Cloud Storage Studio 2][4]
* [CloudXplorer][5]
* [Azure Explorer][6]
* [Azure Explorer PRO][7]

**Prerequisiti**

Per eseguire le procedure descritte nell'articolo è necessario:

* Disporre di un cluster HDInsight di Azure. Per istruzioni, vedere
  [Introduzione all'utilizzo di Azure
  HDInsight](/en-us/manage/services/hdinsight/get-started-hdinsight/) o
  [Provisioning di cluster
  HDInsight](/en-us/manage/services/hdinsight/provision-hdinsight-clusters/).

## Contenuto dell'articolo

* [Caricamento di dati nell'archiviazione BLOB tramite AzCopy](#azcopy)
* [Caricamento di dati nell'archiviazione BLOB tramite Azure
  PowerShell](#powershell)
* [Caricamento di dati nell'archiviazione BLOB tramite Azure Storage
  Explorer](#storageexplorer)
* [Caricamento di dati nell'archiviazione BLOB tramite la riga di
  comando di Hadoop](#commandline)
* [Importazione di dati dal database SQL di Azure all'archiviazione BLOB
  tramite Sqoop](#sqoop)

## <a id="azcopy" ></a>Caricamento di dati nell'archiviazione BLOB tramite AzCopy

AzCopy è un'utilità della riga di comando progettata per semplificare il trasferimento di dati da e verso gli account di archiviazione di Azure. Può essere utilizzata come strumento autonomo o incorporata in un'applicazione esistente. [Download di AzCopy][8].

La sintassi di AzCopy è:

    AzCopy <Source> <Destination> [filePattern [filePattern...]] [Options]

Per ulteriori informazioni, vedere [AzCopy - Caricamento e download di file per BLOB di Azure][1]

## <a id="powershell" ></a>Caricamento di dati nell'archiviazione BLOB tramite Azure PowerShell

Azure PowerShell è un ambiente di scripting potente che può essere utilizzato per controllare e automatizzare la distribuzione e la gestione dei carichi di lavoro in Azure. È possibile utilizzare Azure PowerShell per caricare dati nell'archiviazione BLOB in modo che possano essere elaborati da processi MapReduce. Per informazioni sulla configurazione della workstation per l'esecuzione di Azure PowerShell, vedere [Come installare e configurare Azure PowerShell](/en-us/documentation/articles/install-configure-powershell/).

**Per caricare un file locale nell'archiviazione BLOB**

1.  Aprire la finestra della console Azure PowerShell come illustrato in
    [Come installare e configurare Azure
    PowerShell](/en-us/documentation/articles/install-configure-powershell/).
2.  Impostare i valori delle prime cinque variabili nello script
    seguente:
    
         $subscriptionName = "<WindowsAzureSubscriptionName>"
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

3.  Incollare lo script nella finestra della console Azure PowerShell
    per eseguirlo.

I contenitori di archiviazione BLOB archiviano i dati come coppie chiave-valore e non esiste una gerarchia di directory. È tuttavia possibile utilizzare il carattere "/" all'interno del nome della chiave per far sembrare che un file sia archiviato in una struttura di directory. Ad esempio, la chiave di un BLOB potrebbe essere
*input/log1.txt*. Non esiste realmente una directory "input", ma grazie
alla presenza del carattere "/", il nome della chiave ha l'aspetto di un percorso di file. Nello script precedente è possibile assegnare al file una struttura di cartelle impostando la variabile $blobname. Ad esempio
*$blobname="cartellapersonale/filepersonale.txt"*.

Utilizzando gli strumenti di esplorazione di Azure si potrebbe notare la presenza di file da 0 byte. Questi file servono a due scopi:

* In caso di cartelle vuote, fungono da indicatore dell'esistenza della
  cartella. L'archiviazione BLOB è in grado di comprendere che se esiste
  un BLOB denominato foo/bar, esiste una cartella denominata foo. La
  presenza di una cartella vuota denominata foo, tuttavia, è indicata
  esclusivamente dalla presenza di questo file speciale da 0 byte.
* Questi file contengono metadati speciali necessari per il file system
  Hadoop, in particolare relativi alle autorizzazioni e ai proprietari
  delle cartelle.

## <a id="storageexplorer" ></a>Caricamento di dati nell'archiviazione BLOB tramite Azure Storage Explorer

*Azure Storage Explorer* è uno strumento utile per esaminare e
modificare i dati nell'archiviazione di Azure. Si tratta di uno strumento gratuito che è possibile scaricare dalla pagina
[http://azurestorageexplorer.codeplex.com/][3].

Prima di utilizzare lo strumento è necessario conoscere il nome e la chiave dell'account di archiviazione di Azure. Per istruzioni su come ottenere queste informazioni, vedere la sezione "Procedura: Visualizzare, copiare e rigenerare le chiavi di accesso alle risorse di archiviazione" di [Come gestire gli account di archiviazione](/en-us/manage/services/storage/how-to-manage-a-storage-account/).

1.  Eseguire Azure Storage Explorer.
    
    ![HDI.AzureStorageExplorer](./media/hdinsight-upload-data/HDI.AzureStorageExplorer.png)

2.  Fare clic su **Add Account**. Dopo l'aggiunta di un account ad Azure
    Storage Explorer, non è necessario ripetere questo passaggio.
    
    ![HDI.ASEAddAccount](./media/hdinsight-upload-data/HDI.ASEAddAccount.png)

3.  Immettere il nome e la chiave dell'account di archiviazione
    rispettivamente nei campi **Storage account name** e **Storage
    account key**, quindi fare clic su **Add Storage Account**. È
    possibile aggiungere più account di archiviazione, ognuno dei quali
    verrà visualizzato in una scheda.
4.  In **Storage Type** fare clic su **Blobs**.
    
    ![HDI.ASEBlob](./media/hdinsight-upload-data/HDI.ASEBlob.png)

5.  In **Container** fare clic sul contenitore associato al cluster
    HDInsight. Quando si crea un cluster HDInsight è necessario
    specificare un contenitore. In caso contrario, il contenitore verrà
    creato automaticamente dal processo di creazione del cluster.
6.  In **Blob** fare clic su **Upload**. 
7.  Specificare un file da caricare e quindi fare clic su **Open**.

## <a id="commandline" ></a>Caricamento di dati nell'archiviazione BLOB tramite la riga di comando di Hadoop

Per utilizzare la riga di comando di Hadoop è innanzitutto necessario collegarsi al cluster tramite Desktop remoto.

1.  Accedere al [portale di gestione][9]. 
2.  Fare clic su **HDINSIGHT**. Verrà visualizzato un elenco dei cluster
    Hadoop distribuiti.
3.  Fare clic sul cluster HDInsight in cui si desidera caricare i dati. 
4.  Fare clic su **CONFIGURATION** nella parte superiore della pagina. 
5.  Fare clic su **ENABLE REMOTE** se non è stato abilitato Desktop
    remoto e quindi seguire le istruzioni. In caso contrario, continuare
    con il passaggio successivo.
6.  Fare clic su **CONNECT** nella parte inferiore della pagina. 
7.  Fare clic su **Open**. 
8.  Immettere le credenziali e quindi fare clic su **OK**. 
9.  Fare clic su **Yes**. 
10. Dal desktop fare clic su **Hadoop Command Line**. 
11. Nell'esempio seguente viene illustrato come copiare il file davinci.txt file dal file system locale nel nodo head di HDInsight alla directory /example/data.
    
        hadoop dfs -copyFromLocal C:\temp\davinci.txt /example/data/davinci.txt
    
    Poiché il file system predefinito si trova nell'archivio BLOB di
    Azure, /example/datadavinci.txt si trova in effetti nell'archivio
    BLOB di Azure. È inoltre possibile fare riferimento al file come
    segue:
    
        wasb:///example/data/davinci.txt 
    
    oppure
    
        wasbs://<ContainerName>@<StorageAccountName>.blob.core.windows.net/example/data/davinci.txt
    
    Quando si utilizza *wasbs* è necessario specificare un nome di
    dominio completo.

12. Utilizzare il comando seguente per ottenere un elenco dei file
    aggiornati:
    
        hadoop dfs -lsr /example/data

## <a id="sqoop" ></a> Importazione di dati in HDFS da un database SQL o da SQL Server tramite Sqoop

Sqoop è uno strumento progettato per il trasferimento di dati tra Hadoop e i database relazionali. Può essere utilizzato per importare dati in HDFS (Hadoop Distributed File System) da un sistema di gestione di database relazionali (RDBMS), ad esempio SQL, MySQL oppure Oracle, trasformare i dati in Hadoop con MapReduce o Hive e quindi esportarli nuovamente in un sistema RDBMS. Per ulteriori informazioni, vedere la
[Guida dell'utente di Sqoop][10].

Prima di importare i dati è necessario conoscere il nome del server di database SQL di Azure, il nome dell'account di database, la password dell'account e il nome del database.

Per impostazione predefinita un database SQL di Azure consente connessioni da servizi di Azure, ad esempio Azure HDinsight. Se questa impostazione del firewall è disabilitata, sarà necessario abilitarla nel portale di gestione di Azure. Per istruzioni sulla creazione di un database SQL e sulla configurazione di regole del firewall, vedere [Come creare e configurare database SQL](../sql-database-create-configure/).

Nella procedura seguente viene utilizzato PowerShell per inviare un processo Sqoop.

**Per importare dati in HDInsight utilizzando Sqoop e PowerShell**

1.  Aprire la finestra della console Azure PowerShell come illustrato in
    [Come installare e configurare Azure
    PowerShell](/en-us/documentation/articles/install-configure-powershell/).
2.  Impostare i valori delle prime otto variabili nello script seguente:
    
         $subscriptionName = "<WindowsAzureSubscriptionName>"
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

3.  Incollare lo script nella finestra della console Azure PowerShell
    per eseguirlo. Per un esempio di PowerShell per il recupero del file
    di dati dall'archivio BLOB di Azure, vedere [Introduzione
    all'utilizzo di Azure HDInsight
    ](/en-us/manage/services/hdinsight/get-started-hdinsight/).

Per ulteriori informazioni sull'utilizzo di Sqoop, vedere [Utilizzo di Sqoop con HDInsight](../hdinsight-use-sqoop/).

## Passaggi successivi

Dopo aver appreso come importare dati in HDInsight, utilizzare gli articoli seguenti per informazioni sull'esecuzione delle analisi:

* [Introduzione all'utilizzo di Azure
  HDInsight](/en-us/manage/services/hdinsight/get-started-hdinsight/)
* [Invio di processi Hadoop a livello di
  codice](/en-us/manage/services/hdinsight/submit-hadoop-jobs-programmatically/)
* [Utilizzo di Hive con
  HDInsight](/en-us/manage/services/hdinsight/using-hive-with-hdinsight/)
* [Utilizzo di Pig con
  HDInsight](/en-us/manage/services/hdinsight/using-pig-with-hdinsight/)



[1]: http://blogs.msdn.com/b/windowsazurestorage/archive/2012/12/03/azcopy-uploading-downloading-files-for-windows-azure-blobs.aspx
[2]: http://msdn.microsoft.com/en-us/library/windowsazure/jj152841.aspx
[3]: http://azurestorageexplorer.codeplex.com/
[4]: http://www.cerebrata.com/Products/CloudStorageStudio/
[5]: http://clumsyleaf.com/products/cloudxplorer
[6]: http://www.cloudberrylab.com/free-microsoft-azure-explorer.aspx
[7]: http://www.cloudberrylab.com/microsoft-azure-explorer-pro.aspx
[8]: http://aka.ms/WaCopy
[9]: https://manage.windowsazure.com
[10]: http://sqoop.apache.org/docs/1.4.4/SqoopUserGuide.html