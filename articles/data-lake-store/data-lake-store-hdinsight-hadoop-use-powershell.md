<properties
   pageTitle="Creare cluster HDInsight con Archivio Data Lake di Azure tramite PowerShell | Azure"
   description="Utilizzare Azure PowerShell per creare e usare cluster HDInsight con Azure Data Lake"
   services="data-lake-store,hdinsight" 
   documentationCenter=""
   authors="nitinme"
   manager="jhubbard"
   editor="cgronlun"/>  

<tags
   ms.service="data-lake-store"
   ms.devlang="na"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="big-data"
   ms.date="07/01/2016"
   ms.author="nitinme"/>  

# Creare un cluster HDInsight con Archivio Data Lake tramite Azure PowerShell

> [AZURE.SELECTOR]
- [Uso del portale](data-lake-store-hdinsight-hadoop-use-portal.md)
- [Tramite PowerShell](data-lake-store-hdinsight-hadoop-use-powershell.md)


Informazioni su come usare Azure PowerShell per configurare un cluster HDInsight (Hadoop, HBase o Storm) con accesso ad Archivio Data Lake di Azure. Alcune considerazioni importanti per questa versione:

* **Per i cluster Spark (Linux) e Hadoop/Storm (Windows e Linux)**, Archivio Data Lake può essere utilizzato solo come account di archiviazione aggiuntivo. L'account di archiviazione predefinito per tali cluster continuerà a essere WASB (BLOB di Archiviazione di Azure).

* **Per i cluster HBase (Windows e Linux)**, Archivio Data Lake può essere usato come risorsa di archiviazione predefinita o aggiuntiva.

> [AZURE.NOTE] Alcuni elementi importanti da considerare:
> 
> * L'opzione per creare cluster HDInsight con accesso ad Archivio Data Lake è disponibile solo con HDInsight versioni 3.2 e 3.4 (per i cluster Hadoop, HBase e Storm sia per Windows che per Linux). Per i cluster Spark su Linux, questa opzione è disponibile solo sui cluster HDInsight 3.4.
>
> * Come indicato in precedenza, Archivio Data Lake è disponibile come risorsa di archiviazione predefinita per alcuni tipi di cluster (HBase) e come risorsa di archiviazione aggiuntiva per altri tipi (Hadoop, Spark, Storm). L'uso di Archivio Data Lake come account di archiviazione aggiuntivo non ha impatto sulle prestazioni o sulla possibilità di leggere/scrivere nella risorsa di archiviazione dal cluster. In uno scenario in cui Archivio Data Lake viene usato come risorsa di archiviazione aggiuntiva, i file correlati al cluster (ad esempio log e così via) vengono scritti nella risorsa di archiviazione predefinita (BLOB di Azure), mentre i dati da elaborare possono essere archiviati in un account di Archivio Data Lake.


In questo articolo si effettuerà il provisioning di un cluster Hadoop con Archivio Data Lake come risorsa di archiviazione aggiuntiva.

La configurazione di HDInsight perché funzioni con Archivio Data Lake tramite PowerShell prevede i passaggi seguenti:

* Creare un Archivio Azure Data Lake
* Configurare l'autenticazione per l'accesso basato sui ruoli all'Archivio Data Lake
* Creare un cluster HDInsight con l'autenticazione per Archivio Data Lake
* Eseguire un processo di test sul cluster

## Prerequisiti

Prima di iniziare questa esercitazione, è necessario disporre di quanto segue:

- **Una sottoscrizione di Azure**. Vedere [Ottenere una versione di valutazione gratuita di Azure](https://azure.microsoft.com/pricing/free-trial/).
- **Abilitare la sottoscrizione di Azure** per l'anteprima pubblica di Data Lake Store. Vedere le [istruzioni](data-lake-store-get-started-portal.md#signup).
- **Windows SDK**. Per installarlo, fare clic [qui](https://dev.windows.com/it-IT/downloads). Usarlo per creare un certificato di sicurezza.


##Installare Azure PowerShell 1.0 e versioni successive

Per iniziare, è necessario disinstallare le versioni di Azure PowerShell 0.9x. Per controllare la versione di PowerShell installata, eseguire il comando seguente da una finestra di PowerShell:

	Get-Module *azure*

Per disinstallare la versione precedente, eseguire **Programmi e funzionalità** nel pannello di controllo e rimuovere la versione installata se è precedente a PowerShell 1.0.

Sono disponibili due opzioni principali per l'installazione di Azure PowerShell.

- [Raccolta di PowerShell](https://www.powershellgallery.com/). Dalla console di Windows PowerShell con privilegi elevati o di PowerShell ISE con privilegi elevati, eseguire i comandi seguenti:

		# Install the Azure Resource Manager modules from PowerShell Gallery
		Install-Module AzureRM
		Install-AzureRM

		# Install the Azure Service Management module from PowerShell Gallery
		Install-Module Azure

		# Import AzureRM modules for the given version manifest in the AzureRM module
		Import-AzureRM

		# Import Azure Service Management module
		Import-Module Azure

	Per altre informazioni, vedere [Raccolta di PowerShell](https://www.powershellgallery.com/)

- [Installazione guidata piattaforma Web Microsoft (WebPI)](http://aka.ms/webpi-azps). Se si dispone di Azure PowerShell 0.9.x installata, verrà richiesto di disinstallare 0.9.x . Se sono stati installati dei moduli Azure PowerShell dalla raccolta di PowerShell, il programma di installazione richiede che i moduli vengano rimossi prima dell'installazione per garantire un ambiente di PowerShell Azure coerente. Per istruzioni, vedere [Come installare Azure PowerShell 1.0 tramite WebPI](https://azure.microsoft.com/blog/azps-1-0/).

WebPI riceverà degli aggiornamenti mensili. La Raccolta di PowerShell riceverà degli aggiornamenti su base continua. Se si ha familiarità con l'installazione dalla raccolta di PowerShell, quello sarà il primo canale per le ultimissime novità in Azure PowerShell.


## Creare un Archivio Azure Data Lake

Per creare un Archivio Data Lake, seguire questa procedura.

1. Sul desktop aprire una nuova finestra di Azure PowerShell e immettere il frammento di codice seguente. Quando viene richiesto di effettuare l'accesso, assicurarsi di accedere come amministratore/proprietario della sottoscrizione:

        # Log in to your Azure account
		Login-AzureRmAccount

		# List all the subscriptions associated to your account
		Get-AzureRmSubscription

		# Select a subscription
		Set-AzureRmContext -SubscriptionId <subscription ID>

		# Register for Data Lake Store
		Register-AzureRmResourceProvider -ProviderNamespace "Microsoft.DataLakeStore"

	>[AZURE.NOTE] Se si riceve un errore simile a `Register-AzureRmResourceProvider : InvalidResourceNamespace: The resource namespace 'Microsoft.DataLakeStore' is invalid` quando si registra il provider di risorse Archivio Data Lake, è possibile che la sottoscrizione non sia abilitata per Archivio Data Lake di Azure. Assicurarsi di abilitare la sottoscrizione di Azure per l'anteprima pubblica di Archivio Data Lake seguendo queste [istruzioni](data-lake-store-get-started-portal.md#signup).

3. Un account di Archivio Azure Data Lake è associato a un gruppo di risorse di Azure. Per iniziare, creare un gruppo di risorse di Azure.

		$resourceGroupName = "<your new resource group name>"
    	New-AzureRmResourceGroup -Name $resourceGroupName -Location "East US 2"

	![Creare un gruppo di risorse di Azure](./media/data-lake-store-hdinsight-hadoop-use-powershell/ADL.PS.CreateResourceGroup.png "Creare un gruppo di risorse di Azure")

2. Creare un account Archivio Azure Data Lake. Il nome dell'account specificato deve contenere solo lettere minuscole e numeri.

		$dataLakeStoreName = "<your new Data Lake Store name>"
    	New-AzureRmDataLakeStoreAccount -ResourceGroupName $resourceGroupName -Name $dataLakeStoreName -Location "East US 2"

	![Creare un account Azure Data Lake](./media/data-lake-store-hdinsight-hadoop-use-powershell/ADL.PS.CreateADLAcc.png "Creare un account Azure Data Lake")

3. Verificare che l'account sia stato creato correttamente.

		Test-AzureRmDataLakeStoreAccount -Name $dataLakeStoreName

	L'output di questa operazione deve essere **True**.

4. Caricare alcuni dati di esempio in Azure Data Lake. Questi dati saranno usati più avanti in questo articolo per verificare che siano accessibili da un cluster HDInsight. Se si stanno cercando dati di esempio da caricare, è possibile ottenere la cartella **Ambulance Data** dal [Repository GitHub per Azure Data Lake](https://github.com/MicrosoftBigData/usql/tree/master/Examples/Samples/Data/AmbulanceData).


		$myrootdir = "/"
		Import-AzureRmDataLakeStoreItem -AccountName $dataLakeStoreName -Path "C:<path to data>\vehicle1_09142014.csv" -Destination $myrootdir\vehicle1_09142014.csv


## Configurare l'autenticazione per l'accesso basato sui ruoli all'Archivio Data Lake

Ogni sottoscrizione di Azure è associata a una Azure Active Directory. Gli utenti e i servizi che accedono alle risorse della sottoscrizione tramite il portale di Azure classico o l'API di Azure Resource Manager devono prima autenticarsi con Azure Active Directory. L'accesso viene concesso alle sottoscrizioni e ai servizi di Azure tramite l'assegnazione del ruolo appropriato in una risorsa di Azure. Per i servizi, un'entità servizio identifica il servizio in Azure Active Directory (AAD). Questa sezione illustra come concedere a un servizio dell'applicazione, ad esempio HDInsight, l'accesso a una risorsa di Azure, ovvero l'account Archivio Azure Data Lake creato in precedenza, mediante la creazione di un'entità servizio per l'applicazione e l'assegnazione di ruoli a tale entità con Azure PowerShell.

Per configurare l'autenticazione di Active Directory per Azure Data Lake, è necessario eseguire queste attività.

* Creare un certificato autofirmato
* Creare un'applicazione in Azure Active Directory e un'entità servizio

### Creare un certificato autofirmato

Assicurarsi di avere installato [Windows SDK](https://dev.windows.com/it-IT/downloads) prima di continuare con i passaggi descritti in questa sezione. È necessario aver creato anche una directory, ad esempio **C:\\mycertdir**, in cui sarà creato il certificato.

1. Dalla finestra di PowerShell passare al percorso in cui è installato Windows SDK, in genere `C:\Program Files (x86)\Windows Kits\10\bin\x86`, e usare l'utilità [MakeCert][makecert] per creare un certificato autofirmato e una chiave privata. Usare il comandi seguenti:

		$certificateFileDir = "<my certificate directory>"
		cd $certificateFileDir
		$startDate = (Get-Date).ToString('MM/dd/yyyy')
		$endDate = (Get-Date).AddDays(365).ToString('MM/dd/yyyy')

		makecert -sv mykey.pvk -n "cn=HDI-ADL-SP" CertFile.cer -b $startDate -e $endDate -r -len 2048

	Verrà richiesto di immettere la password della chiave privata. Una volta completata l'esecuzione del comando, nella directory del certificato specificata verranno visualizzati **CertFile.cer** e **mykey.pvk**.

4. Usare l'utilità [Pvk2Pfx][pvk2pfx] per convertire i file con estensione PVK e CER creati da MakeCert in un file con estensione PFX. Eseguire il comando indicato di seguito.

		pvk2pfx -pvk mykey.pvk -spc CertFile.cer -pfx CertFile.pfx -po <password>

	Quando richiesto immettere la password della chiave privata specificata in precedenza. Il valore specificato per il parametro **-po** è la password associata al file PFX. Dopo il completamento del comando, nella directory del certificato specificata dovrebbe essere visualizzato un file denominato CertFile.pfx.

###  Creare un'applicazione Azure Active Directory e un'entità servizio

In questa sezione seguire la procedura per creare un'entità servizio per un'applicazione Azure Active Directory, assegnare un ruolo all'entità servizio e autenticarsi come entità servizio fornendo un certificato. Eseguire i comandi seguenti per creare un'applicazione in Azure Active Directory.

1. Incollare i cmdlet seguenti nella finestra della console di PowerShell. Assicurarsi che il valore specificato per la proprietà **-DisplayName** sia univoco. I valori per **-HomePage** e **-IdentiferUris** sono valori segnaposto e non vengono verificati.

		$certificateFilePath = "$certificateFileDir\CertFile.pfx"

		$password = Read-Host –Prompt "Enter the password" # This is the password you specified for the .pfx file

		$certificatePFX = New-Object System.Security.Cryptography.X509Certificates.X509Certificate2($certificateFilePath, $password)

		$rawCertificateData = $certificatePFX.GetRawCertData()

		$credential = [System.Convert]::ToBase64String($rawCertificateData)

		$application = New-AzureRmADApplication `
					-DisplayName "HDIADL" `
					-HomePage "https://contoso.com" `
					-IdentifierUris "https://mycontoso.com" `
					-KeyValue $credential  `
					-KeyType "AsymmetricX509Cert"  `
					-KeyUsage "Verify"  `
					-StartDate $startDate  `
					-EndDate $endDate

		$applicationId = $application.ApplicationId

2. Creare un'entità servizio usando l'ID applicazione.

		$servicePrincipal = New-AzureRmADServicePrincipal -ApplicationId $applicationId

		$objectId = $servicePrincipal.Id

3. Concedere l'accesso per l'entità servizio all'Archivio Data Lake creato in precedenza.

		Set-AzureRmDataLakeStoreItemAclEntry -AccountName $dataLakeStoreName -Path / -AceType User -Id $objectId -Permissions All

	Al prompt immettere **Y** per confermare.

## Creare un cluster HDInsight con l'autenticazione per Archivio Data Lake

In questa sezione si creerà un cluster Hadoop di HDInsight. Per questa versione il cluster HDInsight e Archivio Data Lake devono trovarsi nella stessa località (Stati Uniti orientali 2).

1. Iniziare recuperando l'ID tenant della sottoscrizione, che sarà necessario più avanti.

		$tenantID = (Get-AzureRmContext).Tenant.TenantId

2. In questa versione Archivio Data Lake può essere usato solo come risorsa di archiviazione aggiuntiva per un cluster Hadoop. L'archivio predefinito continuerà a essere BLOB di archiviazione di Azure (WABS). Si procederà quindi prima di tutto alla creazione dell'account di archiviazione e dei contenitori di archiviazione richiesti per il cluster.

		# Create an Azure storage account
		$location = "East US 2"
		$storageAccountName = "<StorageAcccountName>"   # Provide a Storage account name

		New-AzureRmStorageAccount -ResourceGroupName $resourceGroupName -StorageAccountName $storageAccountName -Location $location -Type Standard_GRS

		# Create an Azure Blob Storage container
		$containerName = "<ContainerName>"              # Provide a container name
		$storageAccountKey = Get-AzureRmStorageAccountKey -Name $storageAccountName -ResourceGroupName $resourceGroupName | %{ $_.Key1 }
		$destContext = New-AzureStorageContext -StorageAccountName $storageAccountName -StorageAccountKey $storageAccountKey
		New-AzureStorageContainer -Name $containerName -Context $destContext

3. Creare il cluster HDInsight. Eseguire i cmdlet seguenti.

		# Set these variables
		$clusterName = $containerName                   # As a best practice, have the same name for the cluster and container
		$clusterNodes = <ClusterSizeInNodes>            # The number of nodes in the HDInsight cluster
		$httpCredentials = Get-Credential
		$rdpCredentials = Get-Credential

		New-AzureRmHDInsightCluster -ClusterName $clusterName -ResourceGroupName $resourceGroupName -HttpCredential $httpCredentials -Location $location -DefaultStorageAccountName "$storageAccountName.blob.core.windows.net" -DefaultStorageAccountKey $storageAccountKey -DefaultStorageContainer $containerName  -ClusterSizeInNodes $clusterNodes -ClusterType Hadoop -Version "3.2" -RdpCredential $rdpCredentials -RdpAccessExpiry (Get-Date).AddDays(14) -ObjectID $objectId -AadTenantId $tenantID -CertificateFilePath $certificateFilePath -CertificatePassword $password

	Dopo il completamento del cmdlet, verrà visualizzato un output simile al seguente:

		Name                      : hdiadlcluster
		Id                        : /subscriptions/65a1016d-0f67-45d2-b838-b8f373d6d52e/resourceGroups/hdiadlgroup/providers/Mi
		                            crosoft.HDInsight/clusters/hdiadlcluster
		Location                  : East US 2
		ClusterVersion            : 3.2.7.707
		OperatingSystemType       : Windows
		ClusterState              : Running
		ClusterType               : Hadoop
		CoresUsed                 : 16
		HttpEndpoint              : hdiadlcluster.azurehdinsight.net
		Error                     :
		DefaultStorageAccount     :
		DefaultStorageContainer   :
		ResourceGroup             : hdiadlgroup
		AdditionalStorageAccounts :

## Eseguire i processi di test sul cluster HDInsight per usare Archivio Data Lake.

Dopo aver configurato un cluster HDInsight, è possibile eseguire processi di test sul cluster per verificare che il cluster HDInsight possa accedere ad Archivio Data Lake. A questo scopo, verrà eseguito un processo Hive di esempio che crea una tabella con i dati di esempio caricati in precedenza in Archivio Data Lake.

### Per un cluster Linux

In questa sezione viene usato SSH nel cluster e viene eseguita una query Hive di esempio. Windows non fornisce un client SSH incorporato. È consigliabile usare **PuTTY**, disponibile per il download all'indirizzo [http://www.chiark.greenend.org.uk/~sgtatham/putty/download.html](http://www.chiark.greenend.org.uk/~sgtatham/putty/download.html).

Per altre informazioni sull'uso di PuTTY, vedere [Uso di SSH con Hadoop basato su Linux in HDInsight da Windows](../hdinsight/hdinsight-hadoop-linux-use-ssh-windows.md).

1. Dopo la connessione, avviare l'interfaccia della riga di comando di Hive mediante il comando seguente:

    	hive

2. Usando l'interfaccia della riga di comando, immettere le istruzioni seguenti per creare una nuova tabella denominata **vehicles** con i dati di esempio in Archivio Data Lake:

		DROP TABLE vehicles;
		CREATE EXTERNAL TABLE vehicles (str string) LOCATION 'adl://<mydatalakestore>.azuredatalakestore.net:443/';
		SELECT * FROM vehicles LIMIT 10;

	L'output dovrebbe essere simile al seguente:

		1,1,2014-09-14 00:00:03,46.81006,-92.08174,51,S,1
		1,2,2014-09-14 00:00:06,46.81006,-92.08174,13,NE,1
		1,3,2014-09-14 00:00:09,46.81006,-92.08174,48,NE,1
		1,4,2014-09-14 00:00:12,46.81006,-92.08174,30,W,1
		1,5,2014-09-14 00:00:15,46.81006,-92.08174,47,S,1
		1,6,2014-09-14 00:00:18,46.81006,-92.08174,9,S,1
		1,7,2014-09-14 00:00:21,46.81006,-92.08174,53,N,1
		1,8,2014-09-14 00:00:24,46.81006,-92.08174,63,SW,1
		1,9,2014-09-14 00:00:27,46.81006,-92.08174,4,NE,1
		1,10,2014-09-14 00:00:30,46.81006,-92.08174,31,N,1


### Per un cluster Windows

Usare i cmdlet seguenti per eseguire la query Hive. In questa query si creerà una tabella dai dati disponibili in Archivio Data Lake e quindi si eseguirà una query select sulla tabella creata.

	$queryString = "DROP TABLE vehicles;" + "CREATE EXTERNAL TABLE vehicles (str string) LOCATION 'adl://$dataLakeStoreName.azuredatalakestore.net:443/';" + "SELECT * FROM vehicles LIMIT 10;"

	$hiveJobDefinition = New-AzureRmHDInsightHiveJobDefinition -Query $queryString

	$hiveJob = Start-AzureRmHDInsightJob -ResourceGroupName $resourceGroupName -ClusterName $clusterName -JobDefinition $hiveJobDefinition -ClusterCredential $httpCredentials

	Wait-AzureRmHDInsightJob -ResourceGroupName $resourceGroupName -ClusterName $clusterName -JobId $hiveJob.JobId -ClusterCredential $httpCredentials

L'output della query sarà il seguente. **ExitValue** pari a 0 nell'output suggerisce che il processo è stato completato correttamente.

	Cluster         : hdiadlcluster.
	HttpEndpoint    : hdiadlcluster.azurehdinsight.net
	State           : SUCCEEDED
	JobId           : job_1445386885331_0012
	ParentId        :
	PercentComplete :
	ExitValue       : 0
	User            : admin
	Callback        :
	Completed       : done

Recuperare l'output dal processo tramite il cmdlet seguente:

	Get-AzureRmHDInsightJobOutput -ClusterName $clusterName -JobId $hiveJob.JobId -DefaultContainer $containerName -DefaultStorageAccountName $storageAccountName -DefaultStorageAccountKey $storageAccountKey -ClusterCredential $httpCredentials

L'output del processo sarà simile al seguente:

	1,1,2014-09-14 00:00:03,46.81006,-92.08174,51,S,1
	1,2,2014-09-14 00:00:06,46.81006,-92.08174,13,NE,1
	1,3,2014-09-14 00:00:09,46.81006,-92.08174,48,NE,1
	1,4,2014-09-14 00:00:12,46.81006,-92.08174,30,W,1
	1,5,2014-09-14 00:00:15,46.81006,-92.08174,47,S,1
	1,6,2014-09-14 00:00:18,46.81006,-92.08174,9,S,1
	1,7,2014-09-14 00:00:21,46.81006,-92.08174,53,N,1
	1,8,2014-09-14 00:00:24,46.81006,-92.08174,63,SW,1
	1,9,2014-09-14 00:00:27,46.81006,-92.08174,4,NE,1
	1,10,2014-09-14 00:00:30,46.81006,-92.08174,31,N,1


## Accedere ad Archivio Data Lake tramite comandi HDFS

Dopo aver configurato il cluster HDInsight perché funzioni con Archivio Data Lake, è possibile usare i comandi della shell HDFS per accedere all'archivio.

### Per un cluster Linux

In questa sezione viene usato SSH nel cluster e vengono eseguiti i comandi HDFS. Windows non fornisce un client SSH incorporato. È consigliabile usare **PuTTY**, disponibile per il download all'indirizzo [http://www.chiark.greenend.org.uk/~sgtatham/putty/download.html](http://www.chiark.greenend.org.uk/~sgtatham/putty/download.html).

Per altre informazioni sull'uso di PuTTY, vedere [Uso di SSH con Hadoop basato su Linux in HDInsight da Windows](../hdinsight/hdinsight-hadoop-linux-use-ssh-windows.md).

Dopo avere stabilito la connessione, usare il comando del file system HDFS seguente per elencare i file nell'Archivio Data Lake.

	hdfs dfs -ls adl://<Data Lake Store account name>.azuredatalakestore.net:443/

Dovrebbe essere elencato anche il file precedentemente caricato in Archivio Data Lake.

	15/09/17 21:41:15 INFO web.CaboWebHdfsFileSystem: Replacing original urlConnectionFactory with org.apache.hadoop.hdfs.web.URLConnectionFactory@21a728d6
	Found 1 items
	-rwxrwxrwx   0 NotSupportYet NotSupportYet     671388 2015-09-16 22:16 adl://mydatalakestore.azuredatalakestore.net:443/mynewfolder

È inoltre possibile usare il comando `hdfs dfs -put` per caricare dei file in Archivio Data Lake e quindi usare `hdfs dfs -ls` per verificare che i file siano stati caricati correttamente.


### Per un cluster Windows

1. Accedere al nuovo [portale di Azure](https://portal.azure.com).

2. Fare clic su **Sfoglia**, su **Cluster HDInsight** e quindi sul cluster HDInsight creato.

3. Nel pannello del cluster fare clic su **Desktop remoto** e quindi nel pannello **Desktop remoto** fare clic su **Connetti**.

	![Accesso remoto al cluster HDI](./media/data-lake-store-hdinsight-hadoop-use-powershell/ADL.HDI.PS.Remote.Desktop.png "Creare un gruppo di risorse di Azure")

	Quando richiesto, immettere le credenziali fornite per l'utente desktop remoto.

4. Nella sessione remota, avviare Windows PowerShell e usare i comandi del file system HDFS per elencare i file presenti in Archivio Data Lake.

	 	hdfs dfs -ls adl://<Data Lake Store account name>.azuredatalakestore.net:443/

	Dovrebbe essere elencato anche il file precedentemente caricato in Archivio Data Lake.

		15/09/17 21:41:15 INFO web.CaboWebHdfsFileSystem: Replacing original urlConnectionFactory with org.apache.hadoop.hdfs.web.URLConnectionFactory@21a728d6
		Found 1 items
		-rwxrwxrwx   0 NotSupportYet NotSupportYet     671388 2015-09-16 22:16 adl://mydatalakestore.azuredatalakestore.net:443/vehicle1_09142014.csv

	È inoltre possibile usare il comando `hdfs dfs -put` per caricare dei file in Archivio Data Lake e quindi usare `hdfs dfs -ls` per verificare che i file siano stati caricati correttamente.

## Vedere anche

* [Portale: Creare un cluster HDInsight per usare Archivio Data Lake](data-lake-store-hdinsight-hadoop-use-portal.md)

[makecert]: https://msdn.microsoft.com/library/windows/desktop/ff548309(v=vs.85).aspx
[pvk2pfx]: https://msdn.microsoft.com/library/windows/desktop/ff550672(v=vs.85).aspx

<!---HONumber=AcomDC_0914_2016-->