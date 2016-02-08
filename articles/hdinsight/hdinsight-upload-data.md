<properties
	pageTitle="Caricare dati per processi Hadoop in HDInsight | Microsoft Azure"
	description="Informazioni su come caricare i dati per processi Hadoop e accedervi in HDInsight con Interfaccia della riga di comando di Azure, Azure Storage Explorer, Azure PowerShell, la riga di comando di Hadoop o Sqoop."
	services="hdinsight,storage"
	documentationCenter=""
	tags="azure-portal"
	authors="mumian"
	manager="paulettm"
	editor="cgronlun"/>

<tags
	ms.service="hdinsight"
	ms.workload="big-data"
	ms.tgt_pltfrm="na"
	ms.devlang="na"
	ms.topic="article"
	ms.date="12/29/2015"
	ms.author="jgao"/>



#Caricare dati per processi Hadoop in HDInsight

Azure HDInsight include un'interfaccia completa per il file system HDFS (Hadoop Distributed File System) sull'archivio BLOB di Azure. Tale interfaccia è stata progettata come estensione HDFS per offrire un'esperienza lineare ai clienti tramite l'abilitazione del set completo di componenti nell'ecosistema Hadoop, con possibilità di agire direttamente sui dati gestiti da Hadoop stesso. L'archivio BLOB di Azure e HDFS sono file system distinti, ottimizzati per l'archiviazione di dati e per l'esecuzione di calcoli su di essi. Per i vantaggi dell'uso dell'archivio BLOB di Azure, vedere l'articolo relativo all'[uso dell'archivio BLOB di Azure con HDInsight][hdinsight-storage].

**Prerequisiti**

Per eseguire le procedure descritte nell'articolo è necessario:

* Disporre di un cluster HDInsight di Azure. Per istruzioni, vedere [Introduzione all'uso di Azure HDInsight][hdinsight-get-started] o [Effettuare il provisioning di cluster Hadoop in HDInsight con opzioni personalizzate][hdinsight-provision].

##Informazioni sull'archivio BLOB

In genere i cluster HDInsight di Azure vengono distribuiti per l'esecuzione di processi MapReduce e rimossi dopo il completamento di questi ultimi. Mantenere i dati nei cluster HDFS dopo il completamento dei calcoli rappresenterebbe una soluzione di archiviazione dei dati molto costosa. L'archivio BLOB di Azure offre un'opzione di archiviazione con caratteristiche di scalabilità e capacità elevata, a basso costo e condivisibile per i dati che devono essere elaborati mediante HDInsight. L'archiviazione dei dati in un BLOB consente l'eliminazione sicura dei cluster HDInsight usati per i calcoli, senza perdita di dati utente.

###Directory

I contenitori di archiviazione BLOB archiviano i dati come coppie chiave-valore e non esiste una gerarchia di directory. È tuttavia possibile usare il carattere "/" all'interno del nome della chiave, in modo che un file sembri archiviato in una struttura di directory. HDInsight le rileva come directory effettive.

Ad esempio, la chiave di un BLOB potrebbe essere *input/log1.txt*. Non esiste realmente una directory "input" ma, grazie alla presenza del carattere "/", il nome della chiave ha l'aspetto di un percorso di file.

Per questo motivo, se si usano gli strumenti di esplorazione di Azure, è possibile notare la presenza di file da 0 byte. Questi file servono a due scopi:

- In caso di cartelle vuote, fungono da indicatore dell'esistenza della cartella. L'archivio BLOB è in grado di comprendere che, se esiste un BLOB denominato foo/bar, esiste una cartella denominata **foo**. La presenza di una cartella vuota denominata **foo**, tuttavia, è indicata esclusivamente da questo speciale file da 0 byte.

- Questi file contengono metadati speciali, in particolare relativi alle autorizzazioni e ai proprietari delle cartelle, necessari per il file system Hadoop.

##Utilità della riga di comando

Microsoft fornisce le utilità seguenti da usare con l'archivio BLOB di Azure:

| Strumento | Linux | OS X | Windows |
| ---- |:-----:|:----:|:-------:|
| [Interfaccia della riga di comando di Azure][azurecli] | ✔ | ✔ | ✔ |
| [Azure PowerShell][azure-powershell] | | | ✔ |
| [AzCopy][azure-azcopy] | | | ✔ |
| [Comando Hadoop](#commandline) | ✔ | ✔ | ✔ |

> [AZURE.NOTE] Mentre l'interfaccia della riga di comando di Azure, Azure PowerShell e AzCopy possono tutti essere usati all'esterno di Azure, il comando Hadoop è disponibile solo nei cluster HDInsight e consente solo il caricamento dei dati dal file system locale nell'archivio BLOB di Azure.

###<a id="xplatcli"></a>Interfaccia della riga di comando di Azure

L'interfaccia della riga di comando di Azure è uno strumento multipiattaforma che consente di gestire i servizi di Azure. Per caricare dati nell'archivio BLOB di Azure, seguire questa procedura:

1. [Installare e configurare l'interfaccia della riga di comando di Azure per Mac, Linux e Windows](../xplat-cli-install.md).

2. Aprire un prompt dei comandi, una sessione Bash o un'altra shell e usare quanto riportato di seguito per eseguire l'autenticazione alla sottoscrizione di Azure.

		azure login

	Quando richiesto, immettere il nome utente e la password per la sottoscrizione.

3. Immettere il comando seguente per elencare gli account di archiviazione per la sottoscrizione:

		azure storage account list

4. Selezionare l'account di archiviazione contenente il BLOB che si desidera usare, quindi usare il comando seguente per recuperare la chiave per tale account:

		azure storage account keys list <storage-account-name>

	Il comando dovrebbe restituire le chiavi **Primary** e **Secondary**. Copiare il valore della chiave **Primary** poiché varrà usato nei passaggi successivi.

5. Usare il comando seguente per recuperare un elenco di contenitori BLOB all'interno dell'account di archiviazione:

		azure storage container list -a <storage-account-name> -k <primary-key>

6. Per caricare e scaricare i file dal BLOB, usare i comandi seguenti:

	* Per caricare un file:

			azure storage blob upload -a <storage-account-name> -k <primary-key> <source-file> <container-name> <blob-name>

	* Per scaricare un file:

			azure storage blob download -a <storage-account-name> -k <primary-key> <container-name> <blob-name> <destination-file>

> [AZURE.NOTE] Se si userà sempre lo stesso account di archiviazione, anziché specificare l'account e la chiave per ogni comando è possibile impostare le variabili di ambiente seguenti:
>
> * **AZURE_STORAGE_ACCOUNT**: nome dell'account di archiviazione
>
> * **AZURE_STORAGE_ACCESS_KEY**: chiave dell'account di archiviazione

###<a id="powershell"></a>Azure PowerShell

Azure PowerShell è un ambiente di scripting che può essere usato per controllare e automatizzare la distribuzione e la gestione dei carichi di lavoro in Azure. Per informazioni sulla configurazione della workstation per l'esecuzione di Azure PowerShell, vedere [Come installare e configurare Azure PowerShell](../powershell-install-configure.md).

**Per caricare un file locale nell'archivio BLOB di Azure**

1. Aprire la console di Azure PowerShell come illustrato in [Come installare e configurare Azure PowerShell](../powershell-install-configure.md).
2. Impostare i valori delle prime cinque variabili nello script seguente:

		$subscriptionName = "<AzureSubscriptionName>"
		$resourceGroupName = "<AzureResourceGroupName>"
		$storageAccountName = "<StorageAccountName>"
		$containerName = "<ContainerName>"

		$fileName ="<LocalFileName>"
		$blobName = "<BlobName>"

		Switch-AzureMode -Name AzureResourceManager

		Add-AzureAccount
		Select-AzureSubscription $subscriptionName

		# Get the storage account key
		$storageAccountKey = Get-AzureRmStorageAccountKey -ResourceGroupName $resourceGroupName -Name $storageAccountName | %{ $_.Key1 }
		# Create the storage context object
		$destContext = New-AzureStorageContext -StorageAccountName $storageAccountName -StorageAccountKey $storageaccountkey

		# Copy the file from local workstation to the Blob container
		Set-AzureStorageBlobContent -File $fileName -Container $containerName -Blob $blobName -context $destContext

3. Incollare lo script nella console di Azure PowerShell per eseguirlo e copiare il file.

Per script di PowerShell di esempio creati per funzionare con HDInsight, vedere [Strumenti HDInsight](https://github.com/blackmist/hdinsight-tools).

###<a id="azcopy"></a>AzCopy

AzCopy è un strumento della riga di comando progettato per semplificare l'attività di trasferimento dati in entrata e in uscita da un account di archiviazione di Azure. Può essere usato come strumento autonomo o può essere incorporato in un'applicazione esistente. [Download di AzCopy][azure-azcopy-download].

La sintassi di AzCopy è:

	AzCopy <Source> <Destination> [filePattern [filePattern...]] [Options]

Per altre informazioni, vedere l'articolo relativo a [AzCopy - Caricamento e download di file per BLOB di Azure][azure-azcopy]


###<a id="commandline"></a>Riga di comando di Hadoop

La riga di comando di Hadoop è utile solo per archiviare i dati nell'archivio BLOB quando i dati sono già presenti nel nodo head del cluster.

Per usare il comando Hadoop, è innanzitutto necessario connettersi nel nodo head mediante uno dei metodi seguenti:

* **HDInsight basato su Windows**: [connettersi tramite Desktop remoto](hdinsight-administer-use-management-portal.md#connect-to-hdinsight-clusters-by-using-rdp)

* **HDInsight basato su LInux**: connettersi tramite SSH ([il comando SSH](hdinsight-hadoop-linux-use-ssh-unix.md#connect-to-a-linux-based-hdinsight-cluster) o [PuTTY](hdinsight-hadoop-linux-use-ssh-windows.md#connect-to-a-linux-based-hdinsight-cluster))

Dopo essersi connessi, è possibile usare la sintassi seguente per caricare un file nell'archiviazione.

	hadoop -copyFromLocal <localFilePath> <storageFilePath>

Ad esempio, `hadoop fs -copyFromLocal data.txt /example/data/data.txt`

Poiché il file system predefinito per HDInsight si trova nell'archivio BLOB di Azure, /example/datadavinci.txt si trova in effetti nell'archivio BLOB di Azure. È inoltre possibile fare riferimento al file come segue:

	wasb:///example/data/data.txt

oppure

	wasbs://<ContainerName>@<StorageAccountName>.blob.core.windows.net/example/data/davinci.txt

Per un elenco di altri comandi Hadoop che funzionano con i file, vedere [http://hadoop.apache.org/docs/r2.7.0/hadoop-project-dist/hadoop-common/FileSystemShell.html](http://hadoop.apache.org/docs/r2.7.0/hadoop-project-dist/hadoop-common/FileSystemShell.html)

##Client con interfaccia grafica

Esistono diverse applicazioni che forniscono un'interfaccia grafica per usare Archiviazione di Azure. Di seguito è riportato un elenco di alcune di queste applicazioni:

| Client | Linux | OS X | Windows |
| ------ |:-----:|:----:|:-------:|
| [Azure Storage Explorer](http://storageexplorer.com/) | ✔ | ✔ | ✔ |
| [Cloud Storage Studio 2](http://www.cerebrata.com/Products/CloudStorageStudio/) | | | ✔ |
| [CloudXplorer](http://clumsyleaf.com/products/cloudxplorer) | | | ✔ |
| [Azure Explorer](http://www.cloudberrylab.com/free-microsoft-azure-explorer.aspx) | | | ✔ |
| [Zudio](https://zudio.co/) | ✔ | ✔ | ✔ |
| [Cyberduck](https://cyberduck.io/) | | ✔ | ✔ |

###<a id="storageexplorer"></a>Azure Storage Explorer

*Azure Storage Explorer* è uno strumento utile per l'esame e la modifica dei dati nei BLOB. Si tratta di uno strumento gratuito open-source che è possibile scaricare da [http://storageexplorer.com/](http://storageexplorer.com/). Anche il codice sorgente è disponibile da questo collegamento.

Prima di usare lo strumento è necessario conoscere il nome e la chiave dell'account di archiviazione di Azure. Per istruzioni sull'acquisizione di queste informazioni, vedere la sezione "Procedura: Visualizzare, copiare e rigenerare le chiavi di accesso alle risorse di archiviazione" dell'articolo [Informazioni sugli account di archiviazione di Azure][azure-create-storage-account].

1. Eseguire Azure Storage Explorer. Se questa è la prima volta che si esegue Esplora risorse, verrà richiesto il ___\_Nome account di archiviazione__ e la __Chiave account di archiviazione__. Se è stato eseguiti in precedenza, utilizzare il pulsante __Aggiungi__ per aggiungere un nuovo nome e chiave dell’account di archiviazione.

    Immettere il nome e la chiave dell'account di archiviazione utilizzato dal cluster HDinsight e quindi selezionare __Salva e apri__.

	![HDI.AzureStorageExplorer][image-azure-storage-explorer]

5. Nell'elenco dei contenitori a sinistra dell'interfaccia, fare clic sul nome del contenitore associato al cluster HDInsight. Per impostazione predefinita, questo è il nome del cluster HDInsight, ma potrebbe essere diverso se è stato immesso un nome specifico durante la creazione del cluster.

6. Sulla barra degli strumenti, selezionare l'icona di caricamento.

    ![Barra degli strumenti con icona di caricamento evidenziata](./media/hdinsight-upload-data/toolbar.png)

7. Specificare un file da caricare e quindi fare clic su **Open**. Quando richiesto, selezionare __Carica__ per caricare il file nella directory principale del contenitore di archiviazione. Se si desidera caricare il file in un percorso specifico, immettere il percorso nel campo __Destinazione__, quindi selezionare __Carica__.

    ![Finestra di dialogo di caricamento file](./media/hdinsight-upload-data/fileupload.png)
    
    Dopo che il file ha terminato il caricamento, è possibile utilizzarlo dai processi nel cluster HDInsight.

##Montare l'archiviazione BLOB di Azure come unità locale

Vedere [Montaggio dell’archiviazione BLOB di Azure come unità locale](http://blogs.msdn.com/b/bigdatasupport/archive/2014/01/09/mount-azure-blob-storage-as-local-drive.aspx).

##Servizi

###Data factory di Azure

Data factory di Azure è un servizio completamente gestito per la composizione di servizi di archiviazione, elaborazione e spostamento dei dati in pipeline di produzione dei dati ottimizzate, scalabili e affidabili.

Data factory di Azure può essere usato per spostare i dati nell'archivio BLOB di Azure o per creare pipeline di dati che usano direttamente le funzionalità di HDInsight, ad esempio Hive e Pig.

Per altre informazioni, vedere [Documentazione di Data factory](https://azure.microsoft.com/documentation/services/data-factory/).

###<a id="sqoop"></a>Apache Sqoop

Sqoop è uno strumento progettato per il trasferimento di dati tra Hadoop e i database relazionali. Può essere usato per importare dati in HDFS (Hadoop Distributed File System) da un sistema di gestione di database relazionali (RDBMS), ad esempio SQL, MySQL oppure Oracle, trasformare i dati in Hadoop con MapReduce o Hive e quindi esportarli nuovamente in un sistema RDBMS.

Per altre informazioni, vedere [Usare Sqoop con HDInsight][hdinsight-use-sqoop].

##SDK di sviluppo

È possibile accedere all'archivio BLOB di Azure anche tramite un SDK di Azure dai linguaggi di programmazione seguenti:

* .NET
* Java
* Node.js
* PHP
* Python
* Ruby

Per altre informazioni sull'installazione di SDK di Azure, vedere [Download di Azure](https://azure.microsoft.com/downloads/)


## Passaggi successivi
Dopo aver appreso come importare dati in HDInsight, leggere gli articoli seguenti per informazioni sull'esecuzione di analisi:

* [Introduzione all'uso di Azure HDInsight][hdinsight-get-started]
* [Inviare processi Hadoop in HDInsight][hdinsight-submit-jobs]
* [Usare Hive con HDInsight][hdinsight-use-hive]
* [Usare Pig con HDInsight][hdinsight-use-pig]




[azure-management-portal]: https://porta.azure.com
[azure-powershell]: http://msdn.microsoft.com/library/windowsazure/jj152841.aspx

[azure-storage-client-library]: /develop/net/how-to-guides/blob-storage/
[azure-create-storage-account]: ../storage-create-storage-account.md
[azure-azcopy-download]: ../storage-use-azcopy.md
[azure-azcopy]: ../storage-use-azcopy.md

[hdinsight-use-sqoop]: hdinsight-use-sqoop.md

[hdinsight-storage]: ../hdinsight-use-blob-storage.md
[hdinsight-submit-jobs]: hdinsight-submit-hadoop-jobs-programmatically.md
[hdinsight-get-started]: ../hdinsight-get-started.md

[hdinsight-use-hive]: hdinsight-use-hive.md
[hdinsight-use-pig]: hdinsight-use-pig.md
[hdinsight-provision]: hdinsight-provision-clusters.md

[sqldatabase-create-configure]: ../sql-database-create-configure.md

[apache-sqoop-guide]: http://sqoop.apache.org/docs/1.4.4/SqoopUserGuide.html

[Powershell-install-configure]: ../powershell-install-configure.md

[azurecli]: ../xplat-cli-install.md


[image-azure-storage-explorer]: ./media/hdinsight-upload-data/HDI.AzureStorageExplorer.png
[image-ase-addaccount]: ./media/hdinsight-upload-data/HDI.ASEAddAccount.png
[image-ase-blob]: ./media/hdinsight-upload-data/HDI.ASEBlob.png

<!---HONumber=AcomDC_0128_2016-->