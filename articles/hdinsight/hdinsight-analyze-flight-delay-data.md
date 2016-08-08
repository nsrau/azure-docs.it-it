<properties
	pageTitle="Analizzare i dati sui ritardi dei voli usando HDInsight | Microsoft Azure"
	description="Informazioni su come usare uno script di Windows PowerShell per creare di un cluster HDInsight, eseguire un processo Hive, eseguire un processo Sqoop ed eliminare il cluster."
	services="hdinsight"
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
	ms.date="07/25/2016"
	ms.author="jgao"/>

#Analizzare i dati sui ritardi dei voli con Hive in HDInsight

Hive fornisce un metodo per l'esecuzione di processi MapReduce mediante un linguaggio di scripting simile a SQL, denominato *[HiveQL][hadoop-hiveql]*, che può essere applicato per attività di riepilogo, query e analisi di volumi di dati molto elevati.

> [AZURE.NOTE] I passaggi descritti in questo documento richiedono un cluster HDInsight basato su Windows. Per i passaggi relativi a un cluster basato su Linux, vedere [Analizzare i dati sui ritardi dei voli con Hive in HDInsight (Linux)](hdinsight-analyze-flight-delay-data-linux.md).

Uno dei principali vantaggi di Azure HDInsight è la separazione tra archiviazione e calcolo dei dati. HDInsight usa l'archivio BLOB di Azure per l'archiviazione dei dati. Un tipico processo comporta 3 parti:

1. **Archiviare dati nell'archivio BLOB di Azure:** un processo che può essere continuo. Ad esempio, i dati meteo, i dati dei sensori, i blog e, in questo caso, i dati sui ritardi dei voli vengono salvati nell'archivio BLOB.
2. **Eseguire processi:** Quando giunge il momento di elaborare i dati, viene eseguito uno script di Windows PowerShell (o un'applicazione client) per creare un cluster HDInsight, eseguire i processi ed eliminare il cluster. I dati di output dei processi vengono salvati nell'archivio BLOB di Azure e vengono mantenuti anche in seguito all'eliminazione del cluster. In questo modo, l'utente paga solo in base al consumo effettivo.
3. **Recuperare l'output dall'archivio BLOB di Azure** oppure, in questo caso, esportare i dati in un database SQL di Azure.

Nel diagramma seguente vengono illustrati lo scenario e la struttura di questo articolo:

![HDI.FlightDelays.flow][img-hdi-flightdelays-flow]

**Nota**: i numeri nel diagramma corrispondono ai titoli delle sezioni. **M** sta per processo principale. **A** sta per contenuto nell'appendice.

La parte principale dell'esercitazione mostra come usare uno script di Windows PowerShell per eseguire le operazioni seguenti:

- Creare un cluster HDInsight
- Eseguire un processo Hive sul cluster per calcolare la media dei ritardi negli aeroporti. I dati relativi ai ritardi dei voli sono archiviati in un account di archiviazione BLOB di Azure.
- Eseguire un processo Sqoop per esportare l'output del processo Hive in un database SQL di Azure.
- Eliminare il cluster HDInsight.

Nelle appendici sono disponibili istruzioni per caricare i dati relativi ai ritardi dei voli, creare/caricare la stringa di query Hive e preparare il database SQL di Azure per il processo Sqoop.

> [AZURE.NOTE] I passaggi descritti in questo documento sono specifici per i cluster HDInsight basati su Windows. Per i passaggi relativi a un cluster basato su Linux, vedere [Analizzare i dati sui ritardi dei voli con Hive in HDInsight (Linux)](hdinsight-analyze-flight-delay-data-linux.md).

###Prerequisiti

Prima di iniziare questa esercitazione, è necessario disporre di quanto segue:

- **Una sottoscrizione di Azure**. Vedere [Ottenere una versione di prova gratuita di Azure](https://azure.microsoft.com/documentation/videos/get-azure-free-trial-for-testing-hadoop-in-hdinsight/).

- **Workstation con Azure PowerShell**.

    [AZURE.INCLUDE [upgrade-powershell](../../includes/hdinsight-use-latest-powershell.md)]

**File usati in questa esercitazione**

Questa esercitazione usa dati relativi alle prestazioni rispetto agli orari previsti dei voli delle compagnie aeree, che possono essere scaricati dalla pagina relativa a [Research and Innovative Technology Administration, Bureau of Transportation Statistics (RITA)][rita-website]. Una copia dei dati è stata caricata in un contenitore di archiviazione BLOB di Azure con autorizzazione di accesso al BLOB pubblico. Una parte dello script PowerShell copia i dati dal contenitore BLOB pubblico al contenitore BLOB predefinito del cluster. Anche lo script HiveQL viene copiato nello stesso contenitore BLOB. Per informazioni su come ottenere/caricare i dati nel proprio account di archiviazione e su come creare/caricare il file di script HiveQL, vedere [Appendice A](#appendix-a) e [Appendice B](#appendix-b).

Nella tabella seguente vengono elencati i file usati nell'esercitazione:

<table border="1">
<tr><th>File</th><th>Descrizione</th></tr>
<tr><td>wasbs://flightdelay@hditutorialdata.blob.core.windows.net/flightdelays.hql</td><td>File script HiveQL necessario per il processo Hive da eseguire. Lo script è stato caricato in un contenitore di archiviazione BLOB di Azure con autorizzazione di accesso pubblico. L'<a href="#appendix-b">Appendice B</a> include istruzioni su come preparare e caricare il file nel proprio account di archiviazione BLOB di Azure.</td></tr>
<tr><td>wasbs://flightdelay@hditutorialdata.blob.core.windows.net/2013Data</td><td>Dati di input per il processo Hive. I dati sono stati caricati in un account di archiviazione BLOB di Azure con autorizzazione di accesso pubblico. L'<a href="#appendix-a">Appendice A</a> include istruzioni su come ottenere i dati e caricarli nel proprio account di archiviazione BLOB di Azure.</td></tr>
<tr><td>\tutorials\flightdelays\output</td><td>Percorso di output per il processo Hive. Il contenitore predefinito viene usato per archiviare i dati di output.</td></tr>
<tr><td>\tutorials\flightdelays\jobstatus</td><td>Cartella di stato del processo Hive nel contenitore predefinito.</td></tr>
</table>


##Creare cluster ed eseguire processi Hive/Sqoop

Per Hadoop MapReduce è prevista l'elaborazione batch. Il modo più economico di eseguire un processo Hive consiste nel creare un cluster per il processo ed eliminare il processo dopo il completamento. Lo script seguente descrive l'intero processo. Per altre informazioni sulla creazione di un cluster HDInsight e sull'esecuzione di processi Hive, vedere [Creare cluster Hadoop in HDInsight][hdinsight-provision] e [Usare Hive con HDInsight][hdinsight-use-hive].

**Per eseguire le query Hive con Azure PowerShell**

1. Creare un database SQL di Azure e la tabella per l'output del processo Sqoop usando le istruzioni nell'[Appendice C](#appendix-c).
3. Aprire Windows PowerShell ISE ed eseguire lo script seguente:

		$subscriptionID = "<Azure Subscription ID>"
		$nameToken = "<Enter an Alias>" 
		
		###########################################
		# You must configure the follwing variables
		# for an existing Azure SQL Database
		###########################################
		$existingSqlDatabaseServerName = "<Azure SQL Database Server>"
		$existingSqlDatabaseLogin = "<Azure SQL Database Server Login>"
		$existingSqlDatabasePassword = "<Azure SQL Database Server login password>"
		$existingSqlDatabaseName = "<Azure SQL Database name>"
		
		$localFolder = "E:\Tutorials\Downloads" # A temp location for copying files. 
		$azcopyPath = "C:\Program Files (x86)\Microsoft SDKs\Azure\AzCopy" # depends on the version, the folder can be different
		
		###########################################
		# (Optional) configure the following variables
		###########################################
		
		$namePrefix = $nameToken.ToLower() + (Get-Date -Format "MMdd")

		$resourceGroupName = $namePrefix + "rg"
		$location = "EAST US 2"
		
		$HDInsightClusterName = $namePrefix + "hdi"
		$httpUserName = "admin"
		$httpPassword = "<Enter the Password>"
		
		$defaultStorageAccountName = $namePrefix + "store"
		$defaultBlobContainerName = $HDInsightClusterName # use the cluster name
		
		$existingSqlDatabaseTableName = "AvgDelays"
		$sqlDatabaseConnectionString = "jdbc:sqlserver://$existingSqlDatabaseServerName.database.windows.net;user=$existingSqlDatabaseLogin@$existingSqlDatabaseServerName;password=$existingSqlDatabaseLogin;database=$existingSqlDatabaseName"
		
		$hqlScriptFile = "/tutorials/flightdelays/flightdelays.hql" 
		
		$jobStatusFolder = "/tutorials/flightdelays/jobstatus"
		
		###########################################
		# Login
		###########################################
		try{
			$acct = Get-AzureRmSubscription
		}
		catch{
			Login-AzureRmAccount
		}
		Select-AzureRmSubscription -SubscriptionID $subscriptionID
		
		###########################################
		# Create a new HDInsight cluster
		###########################################
		
		# Create ARM group
		New-AzureRmResourceGroup -Name $resourceGroupName -Location $location
		
		# Create the default storage account
		New-AzureRmStorageAccount -ResourceGroupName $resourceGroupName -Name $defaultStorageAccountName -Location $location -Type Standard_LRS
		
		# Create the default Blob container
		$defaultStorageAccountKey = (Get-AzureRmStorageAccountKey -ResourceGroupName $resourceGroupName -Name $defaultStorageAccountName)[0].Value
		$defaultStorageAccountContext = New-AzureStorageContext -StorageAccountName $defaultStorageAccountName -StorageAccountKey $defaultStorageAccountKey 
		New-AzureStorageContainer -Name $defaultBlobContainerName -Context $defaultStorageAccountContext 
		
		# Create the HDInsight cluster
		$pw = ConvertTo-SecureString -String $httpPassword -AsPlainText -Force
		$httpCredential = New-Object System.Management.Automation.PSCredential($httpUserName,$pw)
		
		New-AzureRmHDInsightCluster `
			-ResourceGroupName $resourceGroupName `
			-ClusterName $HDInsightClusterName `
			-Location $location `
			-ClusterType Hadoop `
			-OSType Windows `
			-ClusterSizeInNodes 2 `
			-HttpCredential $httpCredential `
			-DefaultStorageAccountName "$defaultStorageAccountName.blob.core.windows.net" `
			-DefaultStorageAccountKey $defaultStorageAccountKey `
			-DefaultStorageContainer $existingDefaultBlobContainerName 
		
		
		###########################################
		# Prepare the HiveQL script and source data
		###########################################
		
		# Create the temp location  
		New-Item -Path $localFolder -ItemType Directory -Force 
		
		# Download the sample file from Azure Blob storage
		$context = New-AzureStorageContext -StorageAccountName "hditutorialdata" -Anonymous
		$blobs = Get-AzureStorageBlob -Container "flightdelay" -Context $context
		#$blobs | Get-AzureStorageBlobContent -Context $context -Destination $localFolder
		
		# Upload data to default container
		
		$azcopycmd = "cmd.exe /C '$azcopyPath\azcopy.exe' /S /Source:'$localFolder' /Dest:'https://$defaultStorageAccountName.blob.core.windows.net/$defaultBlobContainerName/tutorials/flightdelays' /DestKey:$defaultStorageAccountKey"
		
		Invoke-Expression -Command:$azcopycmd
		
		###########################################
		# Submit the Hive job
		###########################################
		Use-AzureRmHDInsightCluster -ClusterName $HDInsightClusterName -HttpCredential $httpCredential
		$response = Invoke-AzureRmHDInsightHiveJob `
						-Files $hqlScriptFile `
						-DefaultContainer $defaultBlobContainerName `
						-DefaultStorageAccountName $defaultStorageAccountName `
						-DefaultStorageAccountKey $defaultStorageAccountKey `
						-StatusFolder $jobStatusFolder 
		
		write-Host $response
		
		
		###########################################
		# Submit the Sqoop job
		###########################################
		$exportDir = "wasbs://$defaultBlobContainerName@$defaultStorageAccountName.blob.core.windows.net/tutorials/flightdelays/output"
		
		$sqoopDef = New-AzureRmHDInsightSqoopJobDefinition `
						-Command "export --connect $sqlDatabaseConnectionString --table $sqlDatabaseTableName --export-dir $exportDir --fields-terminated-by \001 "
		$sqoopJob = Start-AzureRmHDInsightJob `
						-ResourceGroupName $resourceGroupName `
						-ClusterName $hdinsightClusterName `
						-HttpCredential $httpCredential `
						-JobDefinition $sqoopDef #-Debug -Verbose
		
		Wait-AzureRmHDInsightJob `
			-ResourceGroupName $resourceGroupName `
			-ClusterName $HDInsightClusterName `
			-HttpCredential $httpCredential `
			-WaitTimeoutInSeconds 3600 `
			-Job $sqoopJob.JobId
		
		
		Get-AzureRmHDInsightJobOutput `
			-ResourceGroupName $resourceGroupName `
			-ClusterName $hdinsightClusterName `
			-HttpCredential $httpCredential `
			-DefaultContainer $existingDefaultBlobContainerName `
			-DefaultStorageAccountName $defaultStorageAccountName `
			-DefaultStorageAccountKey $defaultStorageAccountKey `
			-JobId $sqoopJob.JobId `
			-DisplayOutputType StandardError
		
		###########################################
		# Delete the cluster
		###########################################
		Remove-AzureRmHDInsightCluster -ResourceGroupName $resourceGroupName -ClusterName $hdinsightClusterName

6. Connettersi al database SQL e verificare la media dei ritardi dei voli per ogni città nella tabella AvgDelays:

	![HDI.FlightDelays.AvgDelays.Dataset][image-hdi-flightdelays-avgdelays-dataset]



---
##<a id="appendix-a"></a>Appendice A: caricare i dati relativi ai ritardi dei voli nell'archivio BLOB di Azure
Prima di caricare il file di dati e i file script HiveQL, vedere l'[Appendice B](#appendix-b), è richiesta un'attività di pianificazione. L'idea è quella di archiviare i file di dati e il file HiveQL prima di creare un cluster HDInsight e di eseguire il processo Hive. Sono disponibili due opzioni:

- **Usare lo stesso account di archiviazione di Azure che sarà usato come file system predefinito per il cluster HDInsight:** poiché il cluster HDInsight avrà la chiave di accesso dell'account di archiviazione, non è necessario apportare altre modifiche.
- **Usare un account di archiviazione di Azure diverso dal file system predefinito del cluster HDInsight:** In questo caso è necessario modificare la parte relativa alla creazione dello script di Windows PowerShell disponibile in [Creare un cluster HDInsight ed eseguire processi Hive/Sqoop](#runjob) per includere l'account di archiviazione come account di archiviazione aggiuntivo. Per istruzioni, vedere [Creare cluster Hadoop in HDInsight][hdinsight-provision]. Il cluster HDInsight conosce quindi la chiave di accesso per l'account di archiviazione.

>[AZURE.NOTE] Il percorso dell'archivio BLOB per il file di dati è hardcoded nel file di script HiveQL. È necessario aggiornarlo di conseguenza.

**Per scaricare i dati relativi ai voli**

1. Passare alla pagina [Research and Innovative Technology Administration, Bureau of Transportation Statistics][rita-website] (RITA).
2. Selezionare i valori seguenti nella pagina:

	<table border="1">
	<tr><th>Nome</th><th>Valore</th></tr>
	<tr><td>Filter Year</td><td>2013 </td></tr>
	<tr><td>Filter Period</td><td>January</td></tr>
	<tr><td>Fields</td><td>*Year*, *FlightDate*, *UniqueCarrier*, *Carrier*, *FlightNum*, *OriginAirportID*, *Origin*, *OriginCityName*, *OriginState*, *DestAirportID*, *Dest*, *DestCityName*, *DestState*, *DepDelayMinutes*, *ArrDelay*, *ArrDelayMinutes*, *CarrierDelay*, *WeatherDelay*, *NASDelay*, *SecurityDelay*, *LateAircraftDelay* (deselezionare tutti gli altri campi)</td></tr>
	</table>

3. Fare clic su **Download**.
4. Decomprimere il file nella cartella **C:\\Tutorials\\FlightDelay\\2013Data**. Ogni file è in formato CSV e ha dimensioni pari a circa 60 GB.
5.	Rinominare il file, specificando il nome del mese a cui fanno riferimento i dati. Ad esempio, al file contenente i dati relativi a gennaio verrà assegnato il nome *January.csv*.
6. Ripetere i passaggi 2 e 5 per scaricare un file per ognuno dei 12 mesi del 2013. Per eseguire l'esercitazione, è necessario avere almeno un file.

**Per caricare i dati relativi ai ritardi dei voli nell'archivio BLOB di Azure**

1. Preparare i parametri:

	<table border="1">
	<tr><th>Nome variabile</th><th>Note</th></tr>
	<tr><td>$storageAccountName</td><td>Account di archiviazione di Azure nel quale si desidera caricare i dati.</td></tr>
	<tr><td>$blobContainerName</td><td>Contenitore BLOB nel quale si desidera caricare i dati.</td></tr>
	</table>
2. Aprire Azure PowerShell ISE.
3. Incollare lo script seguente nel riquadro di script:

		[CmdletBinding()]
		Param(

		    [Parameter(Mandatory=$True,
		               HelpMessage="Enter the Azure storage account name for creating a new HDInsight cluster. If the account doesn't exist, the script will create one.")]
		    [String]$storageAccountName,

		    [Parameter(Mandatory=$True,
		               HelpMessage="Enter the Azure blob container name for creating a new HDInsight cluster. If not specified, the HDInsight cluster name will be used.")]
		    [String]$blobContainerName
		)

		#Region - Variables
		$localFolder = "C:\Tutorials\FlightDelay\2013Data"  # The source folder
		$destFolder = "tutorials/flightdelay/2013data"     #The blob name prefix for the files to be uploaded
		#EndRegion
		
		#Region - Connect to Azure subscription
		Write-Host "`nConnecting to your Azure subscription ..." -ForegroundColor Green
		try{Get-AzureRmContext}
		catch{Login-AzureRmAccount}
		#EndRegion
		
		#Region - Validate user input
		Write-Host "`nValidating the Azure Storage account and the Blob container..." -ForegroundColor Green
		# Validate the Storage account
		if (-not (Get-AzureRmStorageAccount|Where-Object{$_.StorageAccountName -eq $storageAccountName}))
		{
			Write-Host "The storage account, $storageAccountName, doesn't exist." -ForegroundColor Red
			exit
		}
		else{
			$resourceGroupName = (Get-AzureRmStorageAccount|Where-Object{$_.StorageAccountName -eq $storageAccountName}).ResourceGroupName
		}
		
		# Validate the container
		$storageAccountKey = (Get-AzureRmStorageAccountKey -StorageAccountName $storageAccountName -ResourceGroupName $resourceGroupName)[0].Value
		$storageContext = New-AzureStorageContext -StorageAccountName $storageAccountName -StorageAccountKey $storageAccountKey
		
		if (-not (Get-AzureStorageContainer -Context $storageContext |Where-Object{$_.Name -eq $blobContainerName}))
		{
			Write-Host "The Blob container, $blobContainerName, doesn't exist" -ForegroundColor Red
			Exit
		}
		#EngRegion
		
		#Region - Copy the file from local workstation to Azure Blob storage  
		if (test-path -Path $localFolder)
		{
			foreach ($item in Get-ChildItem -Path $localFolder){
				$fileName = "$localFolder\$item"
				$blobName = "$destFolder/$item"
		
				Write-Host "Copying $fileName to $blobName" -ForegroundColor Green
		
				Set-AzureStorageBlobContent -File $fileName -Container $blobContainerName -Blob $blobName -Context $storageContext
			}
		}
		else
		{
			Write-Host "The source folder on the workstation doesn't exist" -ForegroundColor Red
		}
		
		# List the uploaded files on HDInsight
		Get-AzureStorageBlob -Container $blobContainerName  -Context $storageContext -Prefix $destFolder
		#EndRegion




4. Premere **F5** per eseguire lo script.

Se si sceglie un metodo diverso per il caricamento dei file, verificare che il percorso sia tutorials/flightdelay/data. Di seguito viene riportata la sintassi per l'accesso ai file:

	wasbs://<ContainerName>@<StorageAccountName>.blob.core.windows.net/tutorials/flightdelay/data

Il percorso tutorials/flightdelay/data è la cartella virtuale creata durante il caricamento dei file. Verificare che siano disponibili 12 file, uno per ogni mese.

>[AZURE.NOTE] È necessario aggiornare la query Hive per consentire la lettura dal nuovo percorso.

> Occorre configurare l'autorizzazione di accesso pubblico al contenitore oppure associare l'account di archiviazione al cluster HDInsight. In caso contrario, la stringa di query Hive non potrà accedere ai file di dati.

---
##<a id="appendix-b"></a>Appendice B: creare e caricare uno script HiveQL

Azure PowerShell consente di eseguire più istruzioni HiveQL contemporaneamente o di inserire l'istruzione HiveQL in un file di script. Questa sezione illustra come creare uno script HiveQL e caricarlo nell'archivio BLOB di Azure con PowerShell. Hive richiede che gli script HiveQL siano archiviati nell'archivio BLOB di Azure.

Il file di script HiveQL eseguirà le operazioni seguenti:

1. **Eliminazione della tabella delays\_raw**, nel caso in cui la tabella esista già.
2. **Creazione della tabella Hive esterna delays\_raw**, che fa riferimento al percorso dell'archivio BLOB che include i file relativi ai ritardi dei voli. La query consente di specificare che i campi sono delimitati da "," e che le righe vengono interrotte da "\\n". Ciò costituisce un problema quando i valori dei campi contengono virgole, poiché Hive non è in grado di distinguere tra una virgola che delimita i campi e una virgola inclusa in un valore di campo, come ad esempio nel caso dei valori di campo per ORIGIN\_CITY\_NAME e DEST\_CITY\_NAME. Per risolvere questo problema, la query crea colonne TEMP in cui inserire i dati suddivisi erroneamente in colonne.
3. **Eliminazione della tabella delays**, se la tabella esiste già.
4. **Creazione della tabella delays**. È consigliabile ripulire i dati prima di procedere con l'elaborazione. La query crea una nuova tabella *delays* dalla tabella delays\_raw. Si noti che le colonne TEMP, come indicato in precedenza, non vengono copiate e che la funzione **substring** viene usata per rimuovere le virgolette dai dati.
5. **Calcolo della media dei ritardi dovuti alle condizioni climatiche e raggruppamento dei risultati in base al nome della città.** I risultati verranno anche inviati come output all'archivio BLOB. Si noti che la query rimuoverà gli apostrofi dai dati ed escluderà le righe in cui il valore per **weather\_delay** è Null. Ciò è necessario perché Sqoop, usato più avanti nell'esercitazione, non è in grado di gestire correttamente tali valori per impostazione predefinita.

Per un elenco completo di comandi di HiveQL, vedere la pagina relativa al [linguaggio di definizione dei dati Hive][hadoop-hiveql]. Ogni comando HiveQL deve terminare con un punto e virgola.

**Per creare un file di script HiveQL**

1. Preparare i parametri:

	<table border="1">
	<tr><th>Nome variabile</th><th>Note</th></tr>
	<tr><td>$storageAccountName</td><td>Account di archiviazione di Azure nel quale si desidera caricare lo script HiveQL.</td></tr>
	<tr><td>$blobContainerName</td><td>Contenitore BLOB nel quale si desidera caricare lo script HiveQL.</td></tr>
	</table>
2. Aprire Azure PowerShell ISE.

3. Copiare e incollare lo script seguente nel riquadro dello script:

		[CmdletBinding()]
		Param(

		    # Azure Blob storage variables
		    [Parameter(Mandatory=$True,
		               HelpMessage="Enter the Azure storage account name for creating a new HDInsight cluster. If the account doesn't exist, the script will create one.")]
		    [String]$storageAccountName,

		    [Parameter(Mandatory=$True,
		               HelpMessage="Enter the Azure blob container name for creating a new HDInsight cluster. If not specified, the HDInsight cluster name will be used.")]
		    [String]$blobContainerName
		)

		#region - Define variables
		# Treat all errors as terminating
		$ErrorActionPreference = "Stop"
		
		# The HiveQL script file is exported as this file before it's uploaded to Blob storage
		$hqlLocalFileName = "e:\tutorials\flightdelay\flightdelays.hql"
		
		# The HiveQL script file will be uploaded to Blob storage as this blob name
		$hqlBlobName = "tutorials/flightdelay/flightdelays.hql"
		
		# These two constants are used by the HiveQL script file
		#$srcDataFolder = "tutorials/flightdelay/data"
		$dstDataFolder = "/tutorials/flightdelay/output"
		#endregion

		#Region - Connect to Azure subscription
		Write-Host "`nConnecting to your Azure subscription ..." -ForegroundColor Green
		try{Get-AzureRmContext}
		catch{Login-AzureRmAccount}
		#EndRegion
		
		#Region - Validate user input
		Write-Host "`nValidating the Azure Storage account and the Blob container..." -ForegroundColor Green
		# Validate the Storage account
		if (-not (Get-AzureRmStorageAccount|Where-Object{$_.StorageAccountName -eq $storageAccountName}))
		{
			Write-Host "The storage account, $storageAccountName, doesn't exist." -ForegroundColor Red
			exit
		}
		else{
			$resourceGroupName = (Get-AzureRmStorageAccount|Where-Object{$_.StorageAccountName -eq $storageAccountName}).ResourceGroupName
		}
		
		# Validate the container
		$storageAccountKey = (Get-AzureRmStorageAccountKey -StorageAccountName $storageAccountName -ResourceGroupName $resourceGroupName)[0].Value
		$storageContext = New-AzureStorageContext -StorageAccountName $storageAccountName -StorageAccountKey $storageAccountKey
		
		if (-not (Get-AzureStorageContainer -Context $storageContext |Where-Object{$_.Name -eq $blobContainerName}))
		{
			Write-Host "The Blob container, $blobContainerName, doesn't exist" -ForegroundColor Red
			Exit
		}
		#EngRegion
		
		#region - Validate the file and file path
		
		# Check if a file with the same file name already exists on the workstation
		Write-Host "`nvalidating the folder structure on the workstation for saving the HQL script file ..."  -ForegroundColor Green
		if (test-path $hqlLocalFileName){
		
			$isDelete = Read-Host 'The file, ' $hqlLocalFileName ', exists.  Do you want to overwirte it? (Y/N)'
		
			if ($isDelete.ToLower() -ne "y")
			{
				Exit
			}
		}
		
		# Create the folder if it doesn't exist
		$folder = split-path $hqlLocalFileName
		if (-not (test-path $folder))
		{
			Write-Host "`nCreating folder, $folder ..." -ForegroundColor Green
		
			new-item $folder -ItemType directory  
		}
		#end region
		
		#region - Write the Hive script into a local file
		Write-Host "`nWriting the Hive script into a file on your workstation ..." `
					-ForegroundColor Green
		
		$hqlDropDelaysRaw = "DROP TABLE delays_raw;"
		
		$hqlCreateDelaysRaw = "CREATE EXTERNAL TABLE delays_raw (" +
				"YEAR string, " +
				"FL_DATE string, " +
				"UNIQUE_CARRIER string, " +
				"CARRIER string, " +
				"FL_NUM string, " +
				"ORIGIN_AIRPORT_ID string, " +
				"ORIGIN string, " +
				"ORIGIN_CITY_NAME string, " +
				"ORIGIN_CITY_NAME_TEMP string, " +
				"ORIGIN_STATE_ABR string, " +
				"DEST_AIRPORT_ID string, " +
				"DEST string, " +
				"DEST_CITY_NAME string, " +
				"DEST_CITY_NAME_TEMP string, " +
				"DEST_STATE_ABR string, " +
				"DEP_DELAY_NEW float, " +
				"ARR_DELAY_NEW float, " +
				"CARRIER_DELAY float, " +
				"WEATHER_DELAY float, " +
				"NAS_DELAY float, " +
				"SECURITY_DELAY float, " +
				"LATE_AIRCRAFT_DELAY float) " +
			"ROW FORMAT DELIMITED FIELDS TERMINATED BY ',' " +
			"LINES TERMINATED BY '\n' " +
			"STORED AS TEXTFILE " +
			"LOCATION 'wasbs://flightdelay@hditutorialdata.blob.core.windows.net/2013Data';"
		
		$hqlDropDelays = "DROP TABLE delays;"
		
		$hqlCreateDelays = "CREATE TABLE delays AS " +
			"SELECT YEAR AS year, " +
				"FL_DATE AS flight_date, " +
				"substring(UNIQUE_CARRIER, 2, length(UNIQUE_CARRIER) -1) AS unique_carrier, " +
				"substring(CARRIER, 2, length(CARRIER) -1) AS carrier, " +
				"substring(FL_NUM, 2, length(FL_NUM) -1) AS flight_num, " +
				"ORIGIN_AIRPORT_ID AS origin_airport_id, " +
				"substring(ORIGIN, 2, length(ORIGIN) -1) AS origin_airport_code, " +
				"substring(ORIGIN_CITY_NAME, 2) AS origin_city_name, " +
				"substring(ORIGIN_STATE_ABR, 2, length(ORIGIN_STATE_ABR) -1)  AS origin_state_abr, " +
				"DEST_AIRPORT_ID AS dest_airport_id, " +
				"substring(DEST, 2, length(DEST) -1) AS dest_airport_code, " +
				"substring(DEST_CITY_NAME,2) AS dest_city_name, " +
				"substring(DEST_STATE_ABR, 2, length(DEST_STATE_ABR) -1) AS dest_state_abr, " +
				"DEP_DELAY_NEW AS dep_delay_new, " +
				"ARR_DELAY_NEW AS arr_delay_new, " +
				"CARRIER_DELAY AS carrier_delay, " +
				"WEATHER_DELAY AS weather_delay, " +
				"NAS_DELAY AS nas_delay, " +
				"SECURITY_DELAY AS security_delay, " +
				"LATE_AIRCRAFT_DELAY AS late_aircraft_delay " +
			"FROM delays_raw;"
		
		$hqlInsertLocal = "INSERT OVERWRITE DIRECTORY '$dstDataFolder' " +
			"SELECT regexp_replace(origin_city_name, '''', ''), " +
				"avg(weather_delay) " +
			"FROM delays " +
			"WHERE weather_delay IS NOT NULL " +
			"GROUP BY origin_city_name;"
		
		$hqlScript = $hqlDropDelaysRaw + $hqlCreateDelaysRaw + $hqlDropDelays + $hqlCreateDelays + $hqlInsertLocal
		
		$hqlScript | Out-File $hqlLocalFileName -Encoding ascii -Force
		#endregion
		
		#region - Upload the Hive script to the default Blob container
		Write-Host "`nUploading the Hive script to the default Blob container ..." -ForegroundColor Green
		
		# Create a storage context object
		$storageAccountKey = (Get-AzureRmStorageAccountKey -StorageAccountName $storageAccountName -ResourceGroupName $resourceGroupName)[0].Value
		$destContext = New-AzureStorageContext -StorageAccountName $storageAccountName -StorageAccountKey $storageAccountKey
		
		# Upload the file from local workstation to Blob storage
		Set-AzureStorageBlobContent -File $hqlLocalFileName -Container $blobContainerName -Blob $hqlBlobName -Context $destContext
		#endregion
		
		Write-host "`nEnd of the PowerShell script" -ForegroundColor Green

	Ecco le variabili usate nello script:

	- **$hqlLocalFileName**: lo script salva il file di script HiveQL in locale prima di caricarlo nell'archivio BLOB. Questo è il nome file. Il valore predefinito è <u>C:\\tutorials\\flightdelay\\flightdelays.hql</u>.
	- **$hqlBlobName**: questo è il nome BLOB del file di script HiveQL usato per l'archivio BLOB di Azure. Il valore predefinito è tutorials/flightdelay/flightdelays.hql. Poiché il file verrà scritto direttamente nell'archiviazione BLOB di Azure, all'inizio del nome BLOB NON è presente il carattere "/". Per accedere al file dall'archivio BLOB di Azure sarà necessario aggiungere "/" all'inizio del nome file.
	- **$srcDataFolder** e **$dstDataFolder** - = "tutorials/flightdelay/data" = "tutorials/flightdelay/output"


---
##<a id="appendix-c"></a>Appendice C: preparare il database SQL di Azure per l'output del processo Sqoop
**Per preparare il database SQL (unirlo con lo script Sqoop)**

1. Preparare i parametri:

	<table border="1">
	<tr><th>Nome variabile</th><th>Note</th></tr>
	<tr><td>$sqlDatabaseServerName</td><td>Nome del server di database SQL di Azure. Lasciare vuoto per creare un nuovo server.</td></tr>
	<tr><td>$sqlDatabaseUsername</td><td>Nome di accesso del server di database SQL di Azure. Se $sqlDatabaseServerName è un server esistente, per l'autenticzione con il server vengono usati l'account di accesso e la password di accesso. In caso contrario, vengono usati per creare un nuovo server.</td></tr>
	<tr><td>$sqlDatabasePassword</td><td>Password di accesso del server di database SQL di Azure.</td></tr>
	<tr><td>$sqlDatabaseLocation</td><td>Questo valore viene usato solo durante la creazione di un nuovo server di database di Azure.</td></tr>
	<tr><td>$sqlDatabaseName</td><td>Database SQL usato per creare la tabella AvgDelays per il processo Sqoop. Lasciando il valore vuoto, verrà creato un database denominato "HDISqoop". Il nome della tabella per l'output del processo Sqoop è "AvgDelays". </td></tr>
	</table>
2. Aprire Azure PowerShell ISE.
3. Copiare e incollare lo script seguente nel riquadro dello script:

		[CmdletBinding()]
		Param(
		
			# Azure Resource group variables
			[Parameter(Mandatory=$True,
					HelpMessage="Enter the Azure resource group name. It will be created if it doesn't exist.")]
			[String]$resourceGroupName,
		
			# SQL database server variables
			[Parameter(Mandatory=$True,
					HelpMessage="Enter the Azure SQL Database Server Name. It will be created if it doesn't exist.")]
			[String]$sqlDatabaseServer, 
		
			[Parameter(Mandatory=$True,
					HelpMessage="Enter the Azure SQL Database admin user.")]
			[String]$sqlDatabaseLogin,
		
			[Parameter(Mandatory=$True,
					HelpMessage="Enter the Azure SQL Database admin user password.")]
			[String]$sqlDatabasePassword,
		
			[Parameter(Mandatory=$True,
					HelpMessage="Enter the region to create the Database in.")]
			[String]$sqlDatabaseLocation,   #For example, West US.
		
			# SQL database variables
			[Parameter(Mandatory=$True,
					HelpMessage="Enter the database name. It will be created if it doesn't exist.")]
			[String]$sqlDatabaseName # specify the database name if you have one created. Otherwise use "" to have the script create one for you.
		)
		
		# Treat all errors as terminating
		$ErrorActionPreference = "Stop"
		
		#region - Constants and variables
		
		# IP address REST service used for retrieving external IP address and creating firewall rules
		[String]$ipAddressRestService = "http://bot.whatismyipaddress.com"
		[String]$fireWallRuleName = "FlightDelay"
		
		# SQL database variables
		[String]$sqlDatabaseMaxSizeGB = 10
		
		#SQL query string for creating AvgDelays table
		[String]$sqlDatabaseTableName = "AvgDelays"
		[String]$sqlCreateAvgDelaysTable = " CREATE TABLE [dbo].[$sqlDatabaseTableName](
					[origin_city_name] [nvarchar](50) NOT NULL,
					[weather_delay] float,
				CONSTRAINT [PK_$sqlDatabaseTableName] PRIMARY KEY CLUSTERED
				(
					[origin_city_name] ASC
				)
				)"
		#endregion
		
		#Region - Connect to Azure subscription
		Write-Host "`nConnecting to your Azure subscription ..." -ForegroundColor Green
		try{Get-AzureRmContext}
		catch{Login-AzureRmAccount}
		#EndRegion
		
		#region - Create and validate Azure resouce group
		try{
			Get-AzureRmResourceGroup -Name $resourceGroupName
		}
		catch{
			New-AzureRmResourceGroup -Name $resourceGroupName -Location $sqlDatabaseLocation
		}
		
		#EndRegion
		
		#region - Create and validate Azure SQL database server
		try{
			Get-AzureRmSqlServer -ServerName $sqlDatabaseServer -ResourceGroupName $resourceGroupName}
		catch{
			Write-Host "`nCreating SQL Database server ..."  -ForegroundColor Green
		
			$sqlDatabasePW = ConvertTo-SecureString -String $sqlDatabasePassword -AsPlainText -Force
			$credential = New-Object System.Management.Automation.PSCredential($sqlDatabaseLogin,$sqlDatabasePW)
		
			$sqlDatabaseServer = (New-AzureRmSqlServer -ResourceGroupName $resourceGroupName -ServerName $sqlDatabaseServer -SqlAdministratorCredentials $credential -Location $sqlDatabaseLocation).ServerName
			Write-Host "`tThe new SQL database server name is $sqlDatabaseServer." -ForegroundColor Cyan
		
			Write-Host "`nCreating firewall rule, $fireWallRuleName ..." -ForegroundColor Green
			$workstationIPAddress = Invoke-RestMethod $ipAddressRestService
			New-AzureRmSqlServerFirewallRule -ResourceGroupName $resourceGroupName -ServerName $sqlDatabaseServer -FirewallRuleName "$fireWallRuleName-workstation" -StartIpAddress $workstationIPAddress -EndIpAddress $workstationIPAddress
		
			#To allow other Azure services to access the server add a firewall rule and set both the StartIpAddress and EndIpAddress to 0.0.0.0. Note that this allows Azure traffic from any Azure subscription to access the server.
			New-AzureRmSqlServerFirewallRule -ResourceGroupName $resourceGroupName -ServerName $sqlDatabaseServer -FirewallRuleName "$fireWallRuleName-Azureservices" -StartIpAddress "0.0.0.0" -EndIpAddress "0.0.0.0"
		}
		
		#endregion
		
		#region - Create and validate Azure SQL database
		
		try {
			Get-AzureRmSqlDatabase -ResourceGroupName $resourceGroupName -ServerName $sqlDatabaseServer -DatabaseName $sqlDatabaseName
		}
		catch {
			Write-Host "`nCreating SQL Database, $sqlDatabaseName ..."  -ForegroundColor Green
			New-AzureRMSqlDatabase -ResourceGroupName $resourceGroupName -ServerName $sqlDatabaseServer -DatabaseName $sqlDatabaseName -Edition "Standard" -RequestedServiceObjectiveName "S1"
		}
		
		#endregion
		
		#region -  Execute an SQL command to create the AvgDelays table
		
		Write-Host "`nCreating SQL Database table ..."  -ForegroundColor Green
		$conn = New-Object System.Data.SqlClient.SqlConnection
		$conn.ConnectionString = "Data Source=$sqlDatabaseServer.database.windows.net;Initial Catalog=$sqlDatabaseName;User ID=$sqlDatabaseLogin;Password=$sqlDatabasePassword;Encrypt=true;Trusted_Connection=false;"
		$conn.open()
		$cmd = New-Object System.Data.SqlClient.SqlCommand
		$cmd.connection = $conn
		$cmd.commandtext = $sqlCreateAvgDelaysTable
		$cmd.executenonquery()
		
		$conn.close()
		
		Write-host "`nEnd of the PowerShell script" -ForegroundColor Green

	>[AZURE.NOTE] Lo script usa un servizio REST (Representational State Transfer), http://bot.whatismyipaddress.com, per recuperare l'indirizzo IP esterno. L'indirizzo IP viene usato per creare una regola del firewall per il server di database SQL.

	Ecco alcune variabili usate nello script:

	- **$ipAddressRestService**: il valore predefinito è http://bot.whatismyipaddress.com. È un servizio REST per l'indirizzo IP pubblico che consente di ottenere l'indirizzo IP esterno. È anche possibile usare altri servizi. L'indirizzo IP esterno recuperato tramite il servizio verrà usato per creare una regola del firewall per il proprio server di database SQL di Azure, in modo che sia possibile accedere al database dalla workstation (usando uno script di Windows PowerShell).
	- **$fireWallRuleName**: è il nome della regola del firewall del server di database SQL di Azure. Il nome predefinito è <u>FlightDelay</u>. È anche possibile rinominarla.
	- **$sqlDatabaseMaxSizeGB**: questo valore viene usato solo durante la creazione di un nuovo server di database SQL di Azure. Il valore predefinito è 10GB. sufficiente per questa esercitazione.
	- **$sqlDatabaseName**: questo valore viene usato solo durante la creazione di un nuovo database SQL di Azure. Il valore predefinito è HDISqoop. Se viene rinominato, sarà necessario aggiornare anche lo script di Windows PowerShell Sqoop.

4. Premere **F5** per eseguire lo script.
5. Convalidare l'output dello script. Assicurarsi che lo script sia stato eseguito correttamente.

##<a id="nextsteps"></a> Passaggi successivi
È stato illustrato come caricare file nell'archivio BLOB di Azure, come popolare una tabella Hive con i dati disponibili nell'archivio BLOB di Azure, come eseguire query Hive e come usare Sqoop per esportare i dati da HDFS in un database SQL di Azure. Per altre informazioni, vedere gli articoli seguenti:

* [Introduzione a HDInsight][hdinsight-get-started]
* [Usare Hive con HDInsight][hdinsight-use-hive]
* [Usare Oozie con HDInsight][hdinsight-use-oozie]
* [Usare Sqoop con Hadoop in HDInsight][hdinsight-use-sqoop]
* [Usare Pig con HDInsight][hdinsight-use-pig]
* [Sviluppare programmi MapReduce Java per HDInsight][hdinsight-develop-mapreduce]



[azure-purchase-options]: http://azure.microsoft.com/pricing/purchase-options/
[azure-member-offers]: http://azure.microsoft.com/pricing/member-offers/
[azure-free-trial]: http://azure.microsoft.com/pricing/free-trial/


[rita-website]: http://www.transtats.bts.gov/DL_SelectFields.asp?Table_ID=236&DB_Short_Name=On-Time
[powershell-install-configure]: powershell-install-configure.md

[hdinsight-use-oozie]: hdinsight-use-oozie.md
[hdinsight-use-hive]: hdinsight-use-hive.md
[hdinsight-provision]: hdinsight-provision-clusters.md
[hdinsight-storage]: hdinsight-hadoop-use-blob-storage.md
[hdinsight-upload-data]: hdinsight-upload-data.md
[hdinsight-get-started]: hdinsight-hadoop-linux-tutorial-get-started.md
[hdinsight-use-sqoop]: hdinsight-use-sqoop.md
[hdinsight-use-pig]: hdinsight-use-pig.md
[hdinsight-develop-mapreduce]: hdinsight-develop-deploy-java-mapreduce-linux.md

[hadoop-hiveql]: https://cwiki.apache.org/confluence/display/Hive/LanguageManual+DDL
[hadoop-shell-commands]: http://hadoop.apache.org/docs/r0.18.3/hdfs_shell.html

[technetwiki-hive-error]: http://social.technet.microsoft.com/wiki/contents/articles/23047.hdinsight-hive-error-unable-to-rename.aspx

[image-hdi-flightdelays-avgdelays-dataset]: ./media/hdinsight-analyze-flight-delay-data/HDI.FlightDelays.AvgDelays.DataSet.png
[img-hdi-flightdelays-run-hive-job-output]: ./media/hdinsight-analyze-flight-delay-data/HDI.FlightDelays.RunHiveJob.Output.png
[img-hdi-flightdelays-flow]: ./media/hdinsight-analyze-flight-delay-data/HDI.FlightDelays.Flow.png

<!---HONumber=AcomDC_0727_2016-->