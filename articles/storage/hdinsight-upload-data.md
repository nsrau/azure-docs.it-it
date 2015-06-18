<properties 
	pageTitle="Caricare dati per processi Hadoop in HDInsight | Microsoft Azure" 
	description="Informazioni su come caricare i dati e accedervi in HDInsight con Azure Storage Explorer, Azure PowerShell, la riga di comando di Hadoop o Sqoop." 
	services="storage, hdinsight" 
	documentationCenter="" 
	authors="mumian" 
	manager="paulettm" 
	editor="cgronlun"/>

<tags 
	ms.service="hdinsight" 
	ms.workload="big-data" 
	ms.tgt_pltfrm="na" 
	ms.devlang="na" 
	ms.topic="article" 
	ms.date="03/31/2015" 
	ms.author="jgao"/>



#Caricare dati per processi Hadoop in HDInsight

Azure HDInsight include un'interfaccia completa per il file system HDFS (Hadoop Distributed File System) sull'archivio BLOB di Azure. Tale interfaccia è stata progettata come estensione HDFS per offrire un'esperienza lineare ai clienti tramite l'abilitazione del set completo di componenti nell'ecosistema Hadoop, con possibilità di agire direttamente sui dati gestiti da Hadoop stesso. L'archivio BLOB di Azure e HDFS sono file system distinti, ottimizzati per l'archiviazione di dati e per l'esecuzione di calcoli su di essi. Per i vantaggi dell'uso dell'archivio BLOB di Azure, vedere l'articolo relativo all'[uso dell'archivio BLOB di Azure con HDInsight][hdinsight-storage].

In genere i cluster HDInsight di Azure vengono distribuiti per l'esecuzione di processi MapReduce e rimossi dopo il completamento di questi ultimi. Mantenere i dati nei cluster HDFS dopo il completamento dei calcoli rappresenterebbe una soluzione di archiviazione dei dati molto costosa. L'archivio BLOB di Azure offre un'opzione di archiviazione con caratteristiche di scalabilità e capacità elevata, a basso costo e condivisibile per i dati che devono essere elaborati mediante HDInsight. L'archiviazione dei dati in un BLOB consente l'eliminazione sicura dei cluster HDInsight utilizzati per i calcoli, senza perdita di dati utente.

L'archivio BLOB di Azure è accessibile usando [AzCopy][azure-azcopy], [Azure PowerShell][azure-powershell], la [libreria del client di archiviazione di Azure per .NET][azure-storage-client-library], l'[interfaccia della riga di comando di Azure per Mac, Linux e Windows][xplatcli] oppure gli strumenti di esplorazione. Di seguito sono elencati alcuni degli strumenti disponibili:

* [Azure Storage Explorer](http://azurestorageexplorer.codeplex.com/)
* [Cloud Storage Studio 2](http://www.cerebrata.com/Products/CloudStorageStudio/)
* [CloudXplorer](http://clumsyleaf.com/products/cloudxplorer)
* [Azure Explorer](http://www.cloudberrylab.com/free-microsoft-azure-explorer.aspx)
* [Azure Explorer PRO](http://www.cloudberrylab.com/microsoft-azure-explorer-pro.aspx)

##Prerequisiti

Per eseguire le procedure descritte nell'articolo è necessario:

* Disporre di un cluster HDInsight di Azure. Per istruzioni, vedere [Introduzione all'uso di Azure HDInsight][hdinsight-get-started] o [Effettuare il provisioning di cluster Hadoop in HDInsight con opzioni personalizzate][hdinsight-provision].


##<a id="azcopy"></a>Caricare dati nell'archivio BLOB di Azure mediante AzCopy##

AzCopy è un strumento della riga di comando progettato per semplificare l'attività di trasferimento dati in entrata e in uscita da un account di archiviazione di Azure. Può essere usato come strumento autonomo o può essere incorporato in un'applicazione esistente. [Download di AzCopy][azure-azcopy-download].

La sintassi di AzCopy è:

	AzCopy <Source> <Destination> [filePattern [filePattern...]] [Options]

Per altre informazioni, vedere l'articolo relativo a [AzCopy - Caricamento e download di file per BLOB di Azure][azure-azcopy]

##<a id="powershell"></a>Caricare dati nell'archivio BLOB di Azure mediante Azure PowerShell##

Azure PowerShell è un ambiente di scripting potente che può essere usato per controllare e automatizzare la distribuzione e la gestione dei carichi di lavoro in Azure. È possibile usare Azure PowerShell per caricare dati nell'archivio BLOB di Azure in modo che possano essere elaborati da processi MapReduce. Per informazioni sulla configurazione della workstation per l'esecuzione di Azure PowerShell, vedere [Come installare e configurare Azure PowerShell](../powershell-install-configure.md).

**Per caricare un file locale nell'archivio BLOB di Azure**

1. Aprire la console di Azure PowerShell come illustrato in [Come installare e configurare Azure PowerShell](../powershell-install-configure.md).
2. Impostare i valori delle prime cinque variabili nello script seguente:

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
		
3. Incollare lo script nella console di Azure PowerShell per eseguirlo.

I contenitori di archiviazione BLOB archiviano i dati come coppie chiave-valore e non esiste una gerarchia di directory. È tuttavia possibile usare il carattere "/" all'interno del nome della chiave, in modo che un file sembri archiviato in una struttura di directory. Ad esempio, la chiave di un BLOB potrebbe essere *input/log1.txt*. Non esiste realmente una directory "input" ma, grazie alla presenza del carattere "/", il nome della chiave ha l'aspetto di un percorso di file. Nello script precedente è possibile assegnare al file una struttura di cartelle impostando la variabile **$blobname** , ad esempio *$blobname="myfolder/myfile.txt"*.

Se si usano gli strumenti di esplorazione di Azure, è possibile notare la presenza di file da 0 byte. Questi file servono a due scopi:

- In caso di cartelle vuote, fungono da indicatore dell'esistenza della cartella. L'archivio BLOB è in grado di comprendere che, se esiste un BLOB denominato foo/bar, esiste una cartella denominata **foo**. La presenza di una cartella vuota denominata **foo**, tuttavia, è indicata esclusivamente da questo speciale file da 0 byte.
- Questi file contengono metadati speciali, in particolare relativi alle autorizzazioni e ai proprietari delle cartelle, necessari per il file system Hadoop.


##<a id="xplatcli"></a>Caricare dati nell'archivio BLOB di Azure mediante l'interfaccia della riga di comando di Azure

L'interfaccia della riga di comando di Azure per Mac, Linux e Windows è uno strumento multipiattaforma che consente di gestire i servizi di Azure. Per caricare dati nell'archivio BLOB di Azure, seguire questa procedura:

1. [Installare e configurare l'interfaccia della riga di comando di Azure per Mac, Linux e Windows](../xplat-cli.md).

2. Aprire un prompt dei comandi, una sessione Bash o un'altra shell e usare quanto riportato di seguito per eseguire l'autenticazione alla sottoscrizione di Azure.

		azure login

	Quando richiesto, immettere il nome utente e la password per la sottoscrizione.

3. Immettere il comando seguente per elencare gli account di archiviazione per la sottoscrizione:

		azure storage account list

4. Selezionare l'account di archiviazione contenente il BLOB che si desidera utilizzare, quindi usare il comando seguente per recuperare la chiave per tale account:

		azure storage account keys list <storage-account-name>

	Il comando dovrebbe restituire le chiavi **Primary** e **Secondary**. Copiare il valore della chiave **Primary** poiché varrà usato nei passaggi successivi.

5. Usare il comando seguente per recuperare un elenco di contenitori BLOB all'interno dell'account di archiviazione:

		azure storage container list -a <storage-account-name> -k <primary-key>

6. Per caricare e scaricare i file dal BLOB, usare i comandi seguenti:

	* Per caricare un file:

			azure storage blob upload -a <storage-account-name> -k <primary-key> <source-file> <container-name> <blob-name>

	* Per scaricare un file:

			azure storage blob download -a <storage-account-name> -k <primary-key> <container-name> <blob-name> <destination-file>

> [AZURE.NOTE]Se si utilizzerà sempre lo stesso account di archiviazione, anziché specificare l'account e la chiave per ogni comando è possibile impostare le variabili di ambiente seguenti:
> 
> * **AZURE_STORAGE_ACCOUNT**: nome dell'account di archiviazione
> 
> * **AZURE_STORAGE_ACCESS_KEY**: chiave dell'account di archiviazione

##<a id="storageexplorer"></a>Caricare dati nell'archivio BLOB mediante Azure Storage Explorer

*Azure Storage Explorer* è uno strumento utile per l'esame e la modifica dei dati nell'archivio di Azure. Si tratta di uno strumento gratuito che è possibile scaricare da CodePlex: [Azure Storage Explorer](http://azurestorageexplorer.codeplex.com/ "Esplora archivi Azure").

Prima di usare lo strumento è necessario conoscere il nome e la chiave dell'account di archiviazione di Azure. Per istruzioni sull'acquisizione di queste informazioni, vedere la sezione "Procedura: Visualizzare, copiare e rigenerare le chiavi di accesso alle risorse di archiviazione" dell'articolo [Informazioni sugli account di archiviazione di Azure][azure-create-storage-account].

1. Eseguire Azure Storage Explorer.

	![HDI.AzureStorageExplorer][image-azure-storage-explorer]

2. Fare clic su **Add Account**. Dopo l'aggiunta di un account ad Azure Storage Explorer, non è necessario ripetere questo passaggio.

	![HDI.ASEAddAccount][image-ase-addaccount]

3. Immettere il nome e la chiave dell'account di archiviazione rispettivamente nei campi **Storage account name** e **Storage account key** e quindi fare clic su **Add Storage Account**. È possibile aggiungere più account di archiviazione, ciascuno dei quali verrà visualizzato in una scheda.
4. In **Storage Type** fare clic su **Blobs**.

	![HDI.ASEBlob][image-ase-blob]

5. In **Container** fare clic sul nome del contenitore associato al cluster HDInsight. Quando si crea un cluster HDInsight è necessario specificare un contenitore. In caso contrario, il contenitore verrà creato automaticamente dal processo di creazione del cluster.
6. In **Blob** fare clic su **Upload**.
7. Specificare un file da caricare e quindi fare clic su **Open**.








































































##<a id="commandline"></a> Caricare dati nell'archivio BLOB di Azure mediante la riga di comando di Hadoop

Per usare la riga di comando di Hadoop è prima necessario collegarsi al cluster tramite Desktop remoto.



1. Accedere al [portale di Azure][azure-management-portal].
2. Fare clic su **HDINSIGHT**. Verrà visualizzato un elenco dei cluster Hadoop distribuiti.
3. Fare clic sul cluster HDInsight in cui si desidera caricare i dati.
4. Fare clic su **CONFIGURATION** nella parte superiore della pagina.
5. Se Desktop remoto non è stato abilitato, fare clic su **ENABLE REMOTE** e quindi seguire le istruzioni. In caso contrario, continuare con il passaggio successivo.
4. Fare clic su **CONNECT** nella parte inferiore della pagina.
7. Fare clic su **Apri**.
8. Immettere le credenziali e quindi fare clic su **OK**.
9. Fare clic su **Yes**.
10. Dal desktop fare clic su **Hadoop Command Line**.
12. Nell'esempio di codice seguente viene illustrato come copiare il file davinci.txt file dal file system locale presente nel nodo head di HDInsight alla directory /example/data.

		hadoop dfs -copyFromLocal C:\temp\davinci.txt /example/data/davinci.txt

	Poiché il file system predefinito si trova nell'archivio BLOB di Azure, /example/datadavinci.txt si trova in effetti nell'archivio BLOB di Azure. È inoltre possibile fare riferimento al file come segue:

		wasb:///example/data/davinci.txt 

	oppure

		wasbs://<ContainerName>@<StorageAccountName>.blob.core.windows.net/example/data/davinci.txt

	Quando si usa *wasbs* è necessario specificare un nome di dominio completo.

13. Usare il comando seguente per ottenere un elenco dei file aggiornati:

		hadoop dfs -lsr /example/data


##<a id="sqoop"></a> Importare dati in HDFS da un database SQL o da SQL Server mediante Sqoop

Sqoop è uno strumento progettato per il trasferimento di dati tra Hadoop e i database relazionali. Può essere usato per importare dati in HDFS (Hadoop Distributed File System) da un sistema di gestione di database relazionali (RDBMS), ad esempio SQL, MySQL oppure Oracle, trasformare i dati in Hadoop con MapReduce o Hive e quindi esportarli nuovamente in un sistema RDBMS. Per altre informazioni, vedere la [Guida dell'utente di Sqoop][apache-sqoop-guide].

Prima di importare i dati è necessario conoscere il nome del server che ospita il database SQL di Azure, il nome dell'account di database, la password dell'account e il nome del database.

Per impostazione predefinita, un database SQL di Azure consente connessioni da servizi di Azure, ad esempio Azure HDinsight. Se questa impostazione del firewall è disabilitata, sarà necessario abilitarla nel portale di Azure. Per istruzioni sulla creazione di un database SQL di Azure e sulla configurazione di regole del firewall, vedere [Come creare e configurare un database SQL di Azure][sqldatabase-create-configure].

La procedura seguente usa PowerShell per inviare un processo Sqoop.

**Per importare dati in HDInsight mediante Sqoop e PowerShell**

1. Aprire la console di Azure PowerShell come illustrato in [Come installare e configurare Azure PowerShell](../powershell-install-configure.md).
2. Impostare i valori delle prime otto variabili nello script seguente:

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

3. Incollare lo script nella console di Azure PowerShell per eseguirlo. Per un esempio di PowerShell per il recupero del file di dati dall'archivio BLOB di Azure, vedere [Introduzione all'uso di Azure HDInsight ][hdinsight-get-started].

Per altre informazioni sull'uso di Sqoop, vedere [Usare Sqoop con Hadoop in HDInsight][hdinsight-use-sqoop].

## Passaggi successivi
Dopo aver appreso come importare dati in HDInsight, leggere gli articoli seguenti per informazioni sull'esecuzione di analisi:

* [Introduzione all'uso di Azure HDInsight][hdinsight-get-started]
* [Inviare processi Hadoop in HDInsight][hdinsight-submit-jobs]
* [Usare Hive con HDInsight][hdinsight-use-hive]
* [Usare Pig con HDInsight][hdinsight-use-pig]




[azure-management-portal]: https://manage.windowsazure.com
[azure-powershell]: http://msdn.microsoft.com/library/windowsazure/jj152841.aspx

[azure-storage-client-library]: /develop/net/how-to-guides/blob-storage/
[azure-create-storage-account]: ../storage-create-storage-account.md
[azure-azcopy-download]: ../storage-use-azcopy.md
[azure-azcopy]: ../storage-use-azcopy.md

[hdinsight-use-sqoop]: hdinsight/hdinsight-use-sqoop.md

[hdinsight-storage]: ../hdinsight-use-blob-storage.md
[hdinsight-submit-jobs]: hdinsight/hdinsight-submit-hadoop-jobs-programmatically.md
[hdinsight-get-started]: ../hdinsight-get-started.md

[hdinsight-use-hive]: hdinsight/hdinsight-use-hive.md
[hdinsight-use-pig]: hdinsight/hdinsight-use-pig.md
[hdinsight-provision]: hdinsight/hdinsight-provision-clusters.md

[sqldatabase-create-configure]: ../sql-database/sql-database-create-configure.md

[apache-sqoop-guide]: http://sqoop.apache.org/docs/1.4.4/SqoopUserGuide.html

[Powershell-install-configure]: ../powershell-install-configure.md

[xplatcli]: ../xplat-cli.md


[image-azure-storage-explorer]: ./media/hdinsight-upload-data/HDI.AzureStorageExplorer.png
[image-ase-addaccount]: ./media/hdinsight-upload-data/HDI.ASEAddAccount.png
[image-ase-blob]: ./media/hdinsight-upload-data/HDI.ASEBlob.png


<!--HONumber=54--> 