<properties 
	pageTitle="Analizzare i dati sui ritardi dei voli usando HDInsight | Azure" 
	description="Informazioni su come usare uno script di PowerShell per eseguire il provisioning di un cluster HDInsight, eseguire un processo Hive, eseguire un processo Sqool ed eliminare il cluster." 
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
	ms.date="12/04/2014" 
	ms.author="jgao"/>

#Analizzare i dati sui ritardi dei voli usando HDInsight

Hive fornisce un metodo per l'esecuzione di processi MapReduce mediante un linguaggio di scripting simile a SQL, denominato *[HiveQL][hadoop-hiveql]*, che può essere applicato per attività di riepilogo, query e analisi di volumi di dati molto elevati. 

Uno dei principali vantaggi di HDInsight è la separazione tra archiviazione e calcolo dei dati. HDInsight usa l'archiviazione BLOB di Azure per l'archiviazione dei dati. Un comune processo di HDInsight può essere suddiviso in tre parti:

1. **Archiviare dati nell'archiviazione BLOB di Azure**.  Questo può essere un processo continuo. Ad esempio, i dati meteo, i dati dei sensori, i blog e, in questo caso, i dati sui ritardi dei voli vengono salvati nell'archiviazione BLOB.
2. **Eseguire processi**.  Quando giunge il momento di elaborare i dati, viene eseguito uno script PowerShell (o un'applicazione client) per effettuare il provisioning di un cluster HDInsight, eseguire i processi ed eliminare il cluster.  I dati di output dei processi vengono salvati nell'archiviazione BLOB di Azure e vengono mantenuti anche dopo  l'eliminazione del cluster. In questo modo, l'utente paga solo in base all'effettivo consumo. 
3. **Recuperare l'output dall'archiviazione BLOB** o, in questo caso, esportare i dati in un database SQL di Azure.

Nel seguente diagramma vengono illustrati lo scenario e la struttura di questo articolo:

![HDI.FlightDelays.flow][img-hdi-flightdelays-flow]

**Nota**: i numeri nel diagramma corrispondono ai titoli delle sezioni.

La parte principale dell'esercitazione mostra come usare uno script di PowerShell per eseguire le operazioni seguenti:

- Effettuare il provisioning di un cluster HDInsight.
- Eseguire un processo Hive sul cluster per cluster per calcolare la media dei ritardi negli aeroporti.  I dati relativi ai ritardi dei voli sono archiviati in un account di archiviazione BLOB di Azure. 
- Eseguire un processo Sqoop per esportare l'output del processo Hive in un database SQL di Azure.
- Eliminare il cluster HDInsight. 

Nelle appendici sono disponibili istruzioni per caricare i dati relativi ai ritardi dei voli, creare/caricare la stringa di query Hive e preparare il database SQL di Azure per il processo Sqoop.

##Contenuto dell'esercitazione:

* [Prerequisiti](#prerequisite)
* [Effettuare il provisioning di un cluster HDInsight ed eseguire processi Hive/Sqoop (M1)](#runjob)
* [Appendice A: Caricare i dati relativi ai ritardi dei voli nell'archiviazione BLOB di Azure (A1)](#appendix-a)
* [Appendice B: Creare e caricare uno script HiveQL (A2)](#appendix-b)
* [Appendice C: Preparare il database SQL di Azure per l'output del processo Sqoop (A3)](#appendix-c)
* [Passaggi successivi](#nextsteps)

##<a id="prerequisite"></a>Prerequisiti

Prima di iniziare questa esercitazione, è necessario disporre di quanto segue:

* Una workstation in cui sia stato installato e configurato Azure PowerShell. Per le istruzioni, vedere [Installare e configurare Azure PowerShell][powershell-install-configure].
* Una sottoscrizione di Azure. Per altre informazioni su come ottenere una sottoscrizione, vedere [Opzioni di acquisto][azure-purchase-options], [Offerte per i membri][azure-member-offers] oppure [Versione di valutazione gratuita][azure-free-trial].

###Informazioni sull'archiviazione in HDInsight

I cluster Hadoop in HDInsight usano l'archiviazione BLOB di Azure per l'archiviazione dei dati.  Questa risorsa è denominata *WASB* o *Azure Storage - Blob*. WASB è l'implementazione Microsoft di *HDFS* nell'archiviazione BLOB di Azure. Per altre informazioni, vedere [Usare l'archiviazione BLOB di Azure con HDInsight][hdinsight-storage]. 

Durante il provisioning di un cluster HDInsight, un contenitore di archiviazione BLOB di un account di archiviazione di Azure viene designato come file system predefinito, proprio come HDFS. Questo account di archiviazione è di solito definito *default storage account* e il contenitore BLOB viene definito come *default Blob container* o *default container*. L'account di archiviazione predefinito deve trovarsi nello stesso data center che include il cluster HDInsight. L'eliminazione di un cluster HDInsight non comporta l'eliminazione del contenitore predefinito o dell'account di archiviazione predefinito.

Oltre all'account di archiviazione predefinito, è possibile associare altri account di archiviazione di Azure a un cluster HDInsight durante il processo di provisioning. L'associazione consente di aggiungere l'account di archiviazione e la chiave dell'account di archiviazione al file di configurazione. Dunque, il cluster può accedere agli account di archiviazione in fase di esecuzione. Per istruzioni sull'aggiunta di altri account di archiviazione, vedere [Effettuare il provisioning di cluster Hadoop in HDInsight][hdinsight-provision]. 

La sintassi WASB è la seguente:

	wasb[s]://<ContainerName>@<StorageAccountName>.blob.core.windows.net/<path>/<filename>

>[AZURE.NOTE] Il percorso di WASB è un percorso virtuale.  Per altre informazioni, vedere [Usare l'archiviazione BLOB di Azure con HDInsight][hdinsight-storage]. 

È possibile accedere da HDInsight ai file archiviati nel contenitore predefinito usando uno degli URI seguenti (usare come esempio flightdelays.hql):

	wasb://mycontainer@mystorageaccount.blob.core.windows.net/tutorials/flightdelays/flightdelays.hql
	wasb:///tutorials/flightdelays/flightdelays.hql
	/tutorials/flightdelays/flightdelays.hql

Per accedere al file direttamente dall'account di archiviazione, il nome del BLOB per questo file è:

	tutorials/flightdelays/flightdelays.hql

Osservare che davanti al nome BLOB non è presente il carattere "/".

**File usati in questa esercitazione**

In questa esercitazione vengono usati dati relativi alle prestazioni rispetto agli orari previsti dei voli delle compagnie aeree, che possono essere scaricati dalla pagina relativa a [Research and Innovative Technology Administration, Bureau of Transportation Statistics][rita-website] (RITA). I dati sono stati caricati in un contenitore di archiviazione BLOB di Azure con autorizzazione di accesso al BLOB pubblico. Poiché si tratta di un contenitore BLOB pubblico, non è necessario associare questo account di archiviazione al cluster HDInsight che esegue lo script Hive Anche lo script HiveQL viene caricato nello stesso contenitore BLOB. Per informazioni su come ottenere/caricare i dati nel proprio account di archiviazione e su come creare/caricare il file di script HiveQL, vedere l'[Appendice A](#appendix-a) e l'[Appendice B](#appendix-b).

Nella tabella seguente vengono elencati i file usati nell'esercitazione:

<table border="1">
<tr><th>File</th><th>Descrizione</th></tr>
<tr><td>wasb://flightdelay@hditutorialdata.blob.core.windows.net/flightdelays.hql</td><td>File script HiveQL necessario per il processo Hive da eseguire. Lo script è stato caricato in un contenitore di archiviazione BLOB di Azure con autorizzazione di accesso pubblico.  L' <a href="#appendix-b">Appendice B</a> include istruzioni su come preparare e caricare il file nel proprio account di archiviazione BLOB di Azure.</td></tr>
<tr><td>wasb://flightdelay@hditutorialdata.blob.core.windows.net/2013Data</td><td>I dati di input per i processi Hive. I dati sono stati caricati in un contenitore di archiviazione BLOB di Azure con autorizzazione di accesso pubblico.  L' <a href="#appendix-a">Appendice A</a> include istruzioni su come ottenere i dati e caricarli nel proprio account di archiviazione BLOB di Azure.</td></tr>
<tr><td>\tutorials\flightdelays\output</td><td>Percorso di input per il processo Hive. Il contenitore predefinito viene usato per archiviare i dati di output.</td></tr>
<tr><td>\tutorials\flightdelays\jobstatus</td><td>Cartella di stato del processo Hive nel contenitore predefinito.</td></tr>
</table>



###Comprendere la tabella interna e la tabella esterna di Hive

Vi sono alcune informazioni che è necessario conoscere riguardo la tabella interna ed esterna di Hive:

- Il comando CREATE TABLE consente di creare una tabella interna. Il file di dati deve trovarsi nel contenitore predefinito.
- Il comando CREATE TABLE sposta il file di dati nella cartella /hive/warehouse/<NomeTabella>.
- Il comando CREATE EXTERNAL TABLE consente di creare una tabella esterna. Il file di dati può trovarsi all'esterno del contenitore predefinito.
- Il comando CREATE EXTERNAL TABLE non consente di spostare il file di dati.
- Il comando CREATE EXTERNAL TABLE non consente la creazione di cartelle in LOCATION. È per questo motivo che nell'esercitazione si esegue una copia del file sample.log.

Per altre informazioni vedere l'[articolo introduttivo alle tabelle interne ed esterne di HDInsight Hive][cindygross-hive-tables].

> [AZURE.NOTE] Una delle istruzioni di HiveQL crea una tabella esterna di Hive. La tabella esterna di Hive mantiene il file di dati nella posizione originale. La tabella interna di Hive sposta il file di dati in hive\warehouse. Per la tabella interna di Hive è necessario che il file di dati si trovi nel contenitore predefinito. Per i dati archiviati all'esterno del contenitore BLOB predefinito, è necessario usare le tabelle esterne di Hive.









##<a id="runjob"></a>Effettuare il provisioning di un cluster HDInsight ed eseguire processi Hive/Sqoop 

Per Hadoop MapReduce è prevista l'elaborazione batch. Il modo più conveniente per eseguire un processo Hive consiste nell'effettuare il provisioning di un cluster per il processo ed eliminare il processo dopo il suo completamento. Lo script seguente descrive l'intero processo. Per altre informazioni sul provisioning del cluster HDInsight e l'esecuzione di processi Hive, vedere [Provisioning di cluster Hadoop in HDInsight][hdinsight-provision] e  [Usare Hive con HDInsight][hdinsight-use-hive]. 

**Per eseguire le query Hive usando PowerShell**

1. Creare un database SQL di Azure e una tabella per l'output del processo Sqoop usando le istruzioni nell'[Appendice C](#appendix-c).
2. Preparare i parametri:

	<table border="1">
	<tr><th>Nome variabile</th><th>Note</th></tr>
	<tr><td>$hdinsightClusterName</td><td>Nome del cluster HDInsight. Se il cluster non esiste, lo script ne crea uno con il nome specificato.</td></tr>
	<tr><td>$storageAccountName</td><td>Account di archiviazione di Azure che verrà usato come account di archiviazione predefinito. Questo valore è necessario solo quando lo script deve creare un cluster HDInsight. Lasciarlo vuoto se è stato specificato il nome di un cluster HDInsight esistente per $hdinsightClusterName. Se l'account di archiviazione con il valore immesso non esiste, lo script ne crea uno con quel nome.</td></tr>
	<tr><td>$blobContainerName</td><td>Contenitore BLOB predefinito che verrà usato per il file system predefinito. Se lo si lascia vuoto, verrà usato il valore $hdinsightClusterName. </td></tr>
	<tr><td>$sqlDatabaseServerName</td><td>Nome server del database SQL di Azure. Deve essere un server esistente. Vedere l' <a href="#appendix-c">Appendice C</a> per crearne uno.</td></tr>
	<tr><td>$sqlDatabaseUsername</td><td>Nome di accesso del server di database SQL di Azure.</td></tr>
	<tr><td>$sqlDatabasePassword</td><td>Password di accesso del server di database SQL di Azure.</td></tr>
	<tr><td>$sqlDatabaseName</td><td>Database SQL nel quale Sqoop esporterà i dati. Il nome predefinito è "HDISqoop". Il nome della tabella per l'output del processo Sqooop è "AvgDelays". </td></tr>
	</table>
2. Aprire PowerShell ISE.
2. Copiare e incollare lo script seguente nel riquadro dello script:

		[CmdletBinding()]
		Param(
		
		    # HDInsight cluster variables
		    [Parameter(Mandatory=$True,
		               HelpMessage="Enter the HDInsight cluster name. If the cluster doesn't exist, the script will create one.")]
		    [String]$hdinsightClusterName,
		
		    [Parameter(Mandatory=$True,
		               HelpMessage="Enter the Azure storage account name for creating a new HDInsight cluster. If the account doesn't exist, the script will create one.")]
		    [AllowEmptyString()]
		    [String]$storageAccountName,
		
		    [Parameter(Mandatory=$True,
		               HelpMessage="Enter the Azure blob container name for creating a new HDInsight cluster. If not specified, the HDInsight cluster name will be used.")]
		    [AllowEmptyString()]
		    [String]$blobContainerName,
		
		    #SQL database server variables
		    [Parameter(Mandatory=$True,
		               HelpMessage="Enter the Azure SQL Database Server Name where to export data.")]
		    [String]$sqlDatabaseServerName,  # specify the Azure SQL database server name where you want to export data to.
		
		    [Parameter(Mandatory=$True,
		               HelpMessage="Enter the Azure SQL Database login username.")]
		    [String]$sqlDatabaseUsername,
		
		    [Parameter(Mandatory=$True,
		               HelpMessage="Enter the Azure SQL Database login user password.")]
		    [String]$sqlDatabasePassword,
		
		    [Parameter(Mandatory=$True,
		               HelpMessage="Enter the database name where data will be exported to.")]
		    [String]$sqlDatabaseName  # the default value is HDISqoop
		)
		
		# Treat all errors as terminating
		$ErrorActionPreference = "Stop"
		
		#region - HDinsight cluster variables
		[int]$clusterSize = 1                # One data node is sufficient for this tutorial.
		[String]$location = "Central US"     # For better performance, choose a data center near you.
		[String]$hadoopUserLogin = "admin"   # Use "admin" as the Hadoop login name
		[String]$hadoopUserpw = "Pass@word1" # Use "Pass@word1" as te the Hadoop login password
		
		[Bool]$isNewCluster = $false      # Indicates whether a new HDInsight cluster is created by the script.  
		                                  # If this variable is true, then the script can optionally delete the cluster after running the Hive and Sqoop jobs.
		
		[Bool]$isNewStorageAccount = $false
		
		$storageAccountName = $storageAccountName.ToLower() # Storage account names must be between 3 and 24 characters in length and use numbers and lower-case letters only.
		#endregion
		
		#region - Hive job variables
		[String]$hqlScriptFile = "wasb://flightdelay@hditutorialdata.blob.core.windows.net/flightdelays.hql" # The HiveQL script is located in a public Blob container. Update this URI if you want to use your own script file.
		
		[String]$jobStatusFolder = "/tutorials/flightdelays/jobstatus" # The script saves both the output data and the job status file to the default container.
		                                                               # The output data path is set in the HiveQL file.
		
		#[String]$jobOutputBlobName = "tutorials/flightdelays/output/000000_0" # This is the output file of the Hive job. The path is set in the HiveQL script.
		#endregion
		
		#region - Sqoop job variables
		[String]$sqlDatabaseTableName = "AvgDelays" 
		[String]$sqlDatabaseConnectionString = "jdbc:sqlserver://$sqlDatabaseServerName.database.windows.net;user=$sqlDatabaseUserName@$sqlDatabaseServerName;password=$sqlDatabasePassword;database=$sqlDatabaseName"
		#endregion Constants and variables
		
		#region - Connect to Azure subscription
		Write-Host "`nConnecting to your Azure subscription ..." -ForegroundColor Green
		Write-Host "`tCurrent system time: " (get-date) -ForegroundColor Yellow
		if (-not (Get-AzureAccount)){ Add-AzureAccount}
		#endregion
		
		#region - Validate user input, and provision HDInsight cluster if needed
		Write-Host "`nValidating user input ..." -ForegroundColor Green
		
		# Both the Azure SQL database server and database must exist.
		if (-not (Get-AzureSqlDatabaseServer|Where-Object{$_.ServerName -eq $sqlDatabaseServerName})){
		    Write-host "The Azure SQL database server, $sqlDatabaseServerName doesn't exist." -ForegroundColor Red
		    Exit
		}
		else
		{
		    if (-not ((Get-AzureSqlDatabase -ServerName $sqlDatabaseServerName)|Where-Object{$_.Name -eq $sqlDatabaseName})){
		        Write-host "The Azure SQL database, $sqlDatabaseName doesn't exist." -ForegroundColor Red
		        Exit
		    }
		}
		
		if (Test-AzureName -Service -Name $hdinsightClusterName)     # If it is an existing HDInsight cluster ...
		{
		    Write-Host "`tThe HDInsight cluster, $hdinsightClusterName, exists. This cluster will be used to run the Hive job." -ForegroundColor Cyan
		
		    #region - Retrieve the default storage account/container names of the cluster exists
		    # The Hive job output will be stored in the default container. The 
		    # information is used to download a copy of the output file from 
		    # Blob storage to workstation for the validation purpose.
		    Write-Host "`nRetrieving the HDInsight cluster default storage account information ..." `
		                -ForegroundColor Green
		
		    $hdi = Get-AzureHDInsightCluster -Name $HDInsightClusterName
		
		    # Use the default storage account and the default container even if the names are different from the user input
		    $storageAccountName = $hdi.DefaultStorageAccount.StorageAccountName `
		                            -replace ".blob.core.windows.net"
		    $blobContainerName = $hdi.DefaultStorageAccount.StorageContainerName
		
		    Write-Host "`tThe default storage account for the cluster is $storageAccountName." `
		                -ForegroundColor Cyan
		    Write-Host "`tThe default Blob container for the cluster is $blobContainerName." `
		                -ForegroundColor Cyan
		    #endregion
		}
		else     #If the cluster doesn't exist, a new one will be provisioned.
		{
		    if ([string]::IsNullOrEmpty($storageAccountName))
		    {
		        Write-Host "You must provide a storage account name" -ForegroundColor Red
		        EXit           
		    }
		    else
		    {
		        # If the container name is not specified, use the cluster name as the container name
		        if ([string]::IsNullOrEmpty($blobContainerName))
		        {
		            $blobContainerName = $hdinsightClusterName
		        }
		        $blobContainerName = $blobContainerName.ToLower()
		
		        #region - Provision HDInsigtht cluster
		        # Create an Azure storage account if it doesn't exist
		        if (-not (Get-AzureStorageAccount|Where-Object{$_.Label -eq $storageAccountName}))
		        {
		            Write-Host "`nCreating the Azure storage account, $storageAccountName ..." -ForegroundColor Green
		            if (-not (New-AzureStorageAccount -StorageAccountName $storageAccountName.ToLower() -Location $location)){
		                Write-Host "Error creating the storage account, $storageAccountName" -ForegroundColor Red
		                Exit
		            }
		            $isNewStorageAccount = $True
		        }
		
		        # Create a Blob container used as the default container
		        $storageAccountKey = get-azurestoragekey -StorageAccountName $storageAccountName | %{$_.Primary}
		        $storageContext = New-AzureStorageContext -StorageAccountName $storageAccountName -StorageAccountKey $storageAccountKey
		
		        if (-not (Get-AzureStorageContainer -Context $storageContext |Where-Object{$_.Name -eq $blobContainerName}))
		        {
		            Write-Host "`nCreating the Azure Blob container, $blobContainerName ..." -ForegroundColor Green
		            if (-not (New-AzureStorageContainer -name $blobContainerName -Context $storageContext)){
		                Write-Host "Error creating the Blob container, $blobContainerName" -ForegroundColor Red
		                Exit
		            }
		        }
		
		        # Create a new HDInsight cluster
		        Write-Host "`nProvisioning the HDInsight cluster, $hdinsightClusterName ..." -ForegroundColor Green
		        Write-Host "`tCurrent system time: " (get-date) -ForegroundColor Yellow
		        $hadoopUserPassword = ConvertTo-SecureString -String $hadoopUserpw -AsPlainText -Force     
		        $credential = New-Object System.Management.Automation.PSCredential($hadoopUserLogin,$hadoopUserPassword)
		        if (-not $credential)
		        {
		            Write-Host "Error creating the PSCredential object" -ForegroundColor Red
		            Exit
		        }
		
		        if (-not (New-AzureHDInsightCluster -Name $hdinsightClusterName -Location $location -Credential $credential -DefaultStorageAccountName "$storageAccountName.blob.core.windows.net" -DefaultStorageAccountKey $storageAccountKey -DefaultStorageContainerName $blobContainerName -ClusterSizeInNodes $clusterSize)){
		            Write-Host "Error provisioning the cluster, $hdinsightClusterName." -ForegroundColor Red
		            Exit
		        }
		        Else
		        {
		            $isNewCluster = $True
		        }
		        #endregion
		    }
		}
		#endregion
		
		#region - Submit Hive job
		Write-Host "`nSubmitting the Hive job ..." -ForegroundColor Green
		Write-Host "`tCurrent system time: " (get-date) -ForegroundColor Yellow
		
		Use-AzureHDInsightCluster $HDInsightClusterName
		$response = Invoke-Hive -File $hqlScriptFile -StatusFolder $jobStatusFolder
		
		Write-Host "`nThe Hive job status" -ForegroundColor Cyan
		Write-Host "---------------------------------------------------------" -ForegroundColor Cyan
		write-Host $response
		Write-Host "---------------------------------------------------------" -ForegroundColor Cyan
		#endregion 
		
		#region - run Sqoop job
		Write-Host "`nSubmitting the Sqoop job ..." -ForegroundColor Green
		Write-Host "`tCurrent system time: " (get-date) -ForegroundColor Yellow
		
		[String]$exportDir = "wasb://$blobContainerName@$storageAccountName.blob.core.windows.net/tutorials/flightdelays/output"
		
				$sqoopDef = New-AzureHDInsightSqoopJobDefinition -Command "export --connect $sqlDatabaseConnectionString --table $sqlDatabaseTableName --export-dir $exportDir --fields-terminated-by \001 "
		$sqoopJob = Start-AzureHDInsightJob -Cluster $hdinsightClusterName -JobDefinition $sqoopDef #-Debug -Verbose
		Wait-AzureHDInsightJob -WaitTimeoutInSeconds 3600 -Job $sqoopJob
		
		Write-Host "Standard Error" -BackgroundColor Green
		Get-AzureHDInsightJobOutput -Cluster $hdinsightClusterName -JobId $sqoopJob.JobId -StandardError
		Write-Host "Standard Output" -BackgroundColor Green
		Get-AzureHDInsightJobOutput -Cluster $hdinsightClusterName -JobId $sqoopJob.JobId -StandardOutput
		#endregion
		
		#region - Delete the HDInsight cluster
		if ($isNewCluster -eq $True)
		{
		    $isDelete = Read-Host 'Do you want to delete the HDInsight Hadoop cluster ' $hdinsightClusterName '? (Y/N)'
		
		    if ($isDelete.ToLower() -eq "y")
		    {
		        Write-Host "`nDeleting the HDInsight cluster ..." -ForegroundColor Green
		        Write-Host "`tCurrent system time: " (get-date) -ForegroundColor Yellow
		        Remove-AzureHDInsightCluster -Name $hdinsightClusterName
		    }
		}
		#endregion
		
		#region - Delete the storage account
		if ($isNewStorageAccount -eq $True)
		{
		    $isDelete = Read-Host 'Do you want to delete the Azure storage account ' $storageAccountName '? (Y/N)'
		
		    if ($isDelete.ToLower() -eq "y")
		    {
		        Write-Host "`nDeleting the Azure storage account ..." -ForegroundColor Green
		        Write-Host "`tCurrent system time: " (get-date) -ForegroundColor Yellow
		        Remove-AzureStorageAccount -StorageAccountName $storageAccountName
		    }
		}
		#endregion
		
		Write-Host "End of the PowerShell script" -ForegroundColor Green
		Write-Host "`tCurrent system time: " (get-date) -ForegroundColor Yellow

4. Premere **F5** per eseguire lo script. L'output deve essere analogo al seguente:

	![HDI.FlightDelays.RunHiveJob.output][img-hdi-flightdelays-run-hive-job-output]
		
5. Effettuare la connessione al database SQL e verificare la media dei ritardi dei voli per ogni città nella tabella  *AvgDelays*:

	![HDI.FlightDelays.AvgDelays.Dataset][image-hdi-flightdelays-avgdelays-dataset]



---
##<a id="appendix-a"></a>Appendice A - Caricare i dati relativi ai ritardi dei voli nell'archivio BLOB di Azure
Prima di caricare il file di dati e i file script HiveQL, vedere l'[Appendice B](#appendix-b); è richiesta un'attività di pianificazione. L'idea è di archiviare i file di dati e il file HiveQL prima di effettuare il provisioning di un cluster HDInsight e di eseguire il processo Hive.  Sono disponibili due opzioni:

- **Usare lo stesso account di archiviazione di Azure che verrà usato come file system predefinito per il cluster HDInsight**: poiché il cluster HDInsight avrà la chiave di accesso dell'account di archiviazione, non occorre apportare modifiche aggiuntive.
- **Usare un account di archiviazione di Azure diverso dal file system predefinito del cluster HDInsight**: in questo caso, è necessario modificare la parte relativa al provisioning dello script di PowerShell disponibile in [Effettuare il provisioning di un cluster HDInsight ed eseguire processi Hive/Sqoop](#runjob) per includere l'account di archiviazione come account di archiviazione aggiuntivo. Per le istruzioni, vedere [Effettuare il provisioning di cluster Hadoop in HDInsight][hdinsight-provision]. In questo modo, il cluster HDInsight conosce la chiave di accesso per l'account di archiviazione.

>[AZURE.NOTE] Il percorso WASB per il file di dati è hardcoded nel file di script HiveQL. È necessario aggiornarlo di conseguenza.

**Per scaricare i dati relativi ai voli**

1. Passare alla pagina [Research and Innovative Technology Administration, Bureau of Transportation Statistics][rita-website] (RITA).
2. Selezionare i valori seguenti nella pagina:

	<table border="1">
	<tr><th>Nome</th><th>Valore</th></tr>
	<tr><td>Filtra per anno</td><td>2013 </td></tr>
	<tr><td>Filtra per periodo</td><td>January</td></tr>
	<tr><td>Campi:</td><td>*Year*, *FlightDate*, *UniqueCarrier*, *Carrier*, *FlightNum*, *OriginAirportID*, *Origin*, *OriginCityName*, *OriginState*, *DestAirportID*, *Dest*, *DestCityName*, *DestState*, *DepDelayMinutes*, *ArrDelay*, *ArrDelayMinutes*, *CarrierDelay*, *WeatherDelay*, *NASDelay*, *SecurityDelay*, *LateAircraftDelay* (deselezionare tutti gli altri campi)</td></tr>
	</table>

3. Fare clic su **Download**. 
4. Decomprimere il file nella cartella **C:\Tutorials\FlightDelays\Data**.  Ogni file è in formato CSV e ha dimensione pari a circa 60 GB.
5.	Rinominare il file, specificando il nome del mese a cui fanno riferimento i dati. Ad esempio, al file contenente i dati relativi a gennaio verrà assegnato il nome  *January.csv*.
6. Ripetere i passaggi 2 e 5 per scaricare un file per ognuno dei 12 mesi del 2013. Per eseguire l'esercitazione, è necessario avere almeno un file.  

**Per caricare i dati relativi ai ritardi dei voli nell'archiviazione BLOB di Azure**

1. Preparare i parametri:

	<table border="1">
	<tr><th>Nome variabile</th><th>Note</th></tr>
	<tr><td>$storageAccountName</td><td>Account di archiviazione di Azure nel quale si desidera caricare i dati.</td></tr>
	<tr><td>$blobContainerName</td><td>Contenitore BLOB nel quale si desidera caricare i dati.</td></tr>
	</table>
2. Aprire PowerShell ISE.
2. Incollare lo script seguente nel riquadro di script:

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
		$localFolder = "C:\Tutorials\FlightDelays\Data"  # the source folder
		$destFolder = "tutorials/flightdelays/data"     #the blob name prefix for the files to be uploaded
		#EndRegion
		
		#Region - Connect to Azure subscription
		Write-Host "`nConnecting to your Azure subscription ..." -ForegroundColor Green
		if (-not (Get-AzureAccount)){ Add-AzureAccount}
		#EndRegion
		
		#Region - Validate user inpute
		# Validate the storage account
		if (-not (Get-AzureStorageAccount|Where-Object{$_.Label -eq $storageAccountName}))
		{
		    Write-Host "The storage account, $storageAccountName, doesn't exist." -ForegroundColor Red
		    exit
		}
		
		# Validate the container
		$storageAccountKey = get-azurestoragekey -StorageAccountName $storageAccountName | %{$_.Primary}
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
		
		# List the uploaded files on HDinsight
		Get-AzureStorageBlob -Container $blobContainerName  -Context $storageContext -Prefix $destFolder
		#EndRegion

3. Premere **F5** per eseguire lo script.

Se si sceglie un metodo diverso per il caricamento dei file, verificare che il percorso sia *tutorials/flightdelays/data*. Di seguito viene riportata la sintassi per l'accesso ai file:

	wasb://<ContainerName>@<StorageAccountName>.blob.core.windows.net/tutorials/flightdelays/data

*tutorials/flightdelays/data* è la cartella virtuale creata durante il caricamento dei file. Verificare che siano disponibili 12 file, uno per ogni mese.

>[AZURE.NOTE] È necessario aggiornare la query Hive per consentire la lettura dal nuovo percorso.

> Occorre configurare l'autorizzazione di accesso al contenitore oppure associare l'account di archiviazione al cluster HDInsight.  In caso contrario, la stringa di query Hive non potrà accedere ai file di dati. 

---
##<a id="appendix-b"></a>Appendice B - Creazione e caricamento di uno script HiveQL

Azure PowerShell consente di eseguire più istruzioni HiveQL contemporaneamente o di inserire l'istruzione HiveQL in un file di script. In questa sezione viene spiegato come creare uno script HiveQL e caricarlo nell'archiviazione BLOB di Azure con PowerShell. Hive richiede che gli script HiveQL siano archiviati su WASB.

Il file di script HiveQL eseguirà le operazioni seguenti:

1. **Eliminare la tabella delays_raw**, nel caso in cui la tabella esista già.
2. **Creare la tabella Hive esterna delays_raw**, che fa riferimento al percorso WASB con i file relativi ai ritardi dei voli. La query consente di specificare che i campi sono delimitati da "," e che le righe vengono interrotte da "\n". Ciò costituisce un problema quando i valori dei campi *contain* virgole, poiché Hive non è in grado di distinguere tra una virgola che delimita i campi e una virgola inclusa in un valore di campo, come ad esempio nel caso dei valori di campo per ORIGIN_CITY_NAME e DEST_CITY_NAME. Per risolvere questo problema, la query crea colonne TEMP in cui inserire i dati suddivisi erroneamente in colonne.  
3. **Eliminare la tabella delays**, nel caso in cui la tabella esista già.
4. **Creare la tabella delays**. È consigliabile ripulire i dati prima di procedere con l'elaborazione. Questa query crea una nuova tabella *delays* dalla tabella *delays_raw*. Si noti che le colonne TEMP, come indicato in precedenza, non vengono copiate e che la funzione *substring* viene usata per rimuovere le virgolette dai dati. 
5. **Calcola la media dei ritardi dovuti alle condizioni climatiche e i risultati vengono raggruppati in base al nome della città.** I risultati verranno anche inviati come output a WASB. Si noti che la query rimuoverà gli apostrofi dai dati ed escluderà le righe in cui il valore per *weather_deal*y è *null*. Ciò è necessario perché Sqoop, usato più avanti nell'esercitazione, non è in grado di gestire correttamente tali valori per impostazione predefinita.

Per un elenco completo di comandi di HiveQL, vedere la pagina relativa al [linguaggio di definizione dei dati Hive][hadoop-hiveql]. Ogni comando HiveQL deve terminare con un punto e virgola.

**Per creare un file di script HiveQL**

1. Preparare i parametri:

	<table border="1">
	<tr><th>Nome variabile</th><th>Note</th></tr>
	<tr><td>$storageAccountName</td><td>Account di archiviazione di Azure nel quale si desidera caricare lo script HiveQL.</td></tr>
	<tr><td>$blobContainerName</td><td>Contenitore BLOB nel quale si desidera caricare lo script HiveQL.</td></tr>
	</table>
2. Aprire Azure PowerShell ISE.

2. Copiare e incollare lo script seguente nel riquadro dello script:

		[CmdletBinding()]
		Param(
		
		    # Azure blob storage variables
		    [Parameter(Mandatory=$True,
		               HelpMessage="Enter the Azure storage account name for creating a new HDInsight cluster. If the account doesn't exist, the script will create one.")]
		    [String]$storageAccountName,
		
		    [Parameter(Mandatory=$True,
		               HelpMessage="Enter the Azure blob container name for creating a new HDInsight cluster. If not specified, the HDInsight cluster name will be used.")]
		    [String]$blobContainerName
		
		)
		
		#region - define variables
		# Treat all errors as terminating
		$ErrorActionPreference = "Stop"
		
		# the HQL script file is exported as this file before uploaded to WASB
		$hqlLocalFileName = "C:\tutorials\flightdelays\flightdelays.hql" 
		
		# the HQL script file will be upload to WASB as this blob name
		$hqlBlobName = "tutorials/flightdelays/flightdelays.hql" 
		
		# this two constants are used by the HQL scrpit file
		#$srcDataFolder = "tutorials/flightdelays/data" 
		$dstDataFolder = "/tutorials/flightdelays/output"
		#endregion
		
		#region - Validate the file and file path
		
		# check if a file with the same file name already exist on the workstation
		Write-Host "`nvalidating the folder structure on the workstation for saving the HQL script file ..."  -ForegroundColor Green
		if (test-path $hqlLocalFileName){
		
		    $isDelete = Read-Host 'The file, ' $hqlLocalFileName ', exists.  Do you want to overwirte it? (Y/N)'
		
		    if ($isDelete.ToLower() -ne "y")
		    {
		        Exit
		    }
		}
		
		# create the folder if it doesn't exist
		$folder = split-path $hqlLocalFileName
		if (-not (test-path $folder))
		{
		    Write-Host "`nCreating folder, $folder ..." -ForegroundColor Green
		
		    new-item $folder -ItemType directory  
		}
		#end region
		
		#region - Add the Azure account 
		Write-Host "`nConnecting to your Azure subscription ..." -ForegroundColor Green
		$azureAccounts= Get-AzureAccount
		if (! $azureAccounts)
		{
		    Add-AzureAccount
		}
		#endregion
		
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
		    "LOCATION 'wasb://flightdelay@hditutorialdata.blob.core.windows.net/2013Data';" 
		
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
		$storageAccountKey = get-azurestoragekey $storageAccountName | %{$_.Primary}
		$destContext = New-AzureStorageContext -StorageAccountName $storageAccountName -StorageAccountKey $storageAccountKey
		
		# Upload the file from local workstation to WASB
		Set-AzureStorageBlobContent -File $hqlLocalFileName -Container $blobContainerName -Blob $hqlBlobName -Context $destContext 
		#endregion
		
		Write-host "`nEnd of the PowerShell script" -ForegroundColor Green

	Ecco le variabili usate nello script:

	- **$hqlLocalFileName**: Lo script salva il file di script HiveQL in locale prima di caricarlo in WASB. Questo è il nome file. Il valore predefinito è <u>C:\tutorials\flightdelays\flightdelays.hql</u>.
	- **$hqlBlobName**: questo è il nome BLOB del file di script HiveQL usato per l'archiviazione BLOB di Azure. Il valore predefinito è <u>tutorials/flightdelays/flightdelays.hql</u>. Poiché il file verrà scritto direttamente nell'archiviazione BLOB di Azure, all'inizio del nome BLOB NON è presente il carattere "/". Per accedere al file da WASB sarà necessario aggiungere "/" all'inizio del nome file.
	- **$srcDataFolder** e **$dstDataFolder**:  = "tutorials/flightdelays/data" 
 = "tutorials/flightdelays/output"


---
##<a id="appendix-c"></a>Appendice C - Preparare il database SQL di Azure per l'output del processo Sqoop
**Per preparare il database SQL (unirlo con lo script di Sqoop)**

1. Preparare i parametri:

	<table border="1">
	<tr><th>Nome variabile</th><th>Note</th></tr>
	<tr><td>$sqlDatabaseServerName</td><td>Nome del server di database SQL di Azure. Lasciare vuoto per creare un nuovo server.</td></tr>
	<tr><td>$sqlDatabaseUsername</td><td>Nome di accesso del server di database SQL di Azure. Se $sqlDatabaseServerName è un server esistente, per l'autenticzione con il server vengono usati l'account di accesso e la password di accesso.  In caso contrario, vengono usati per creare un nuovo server.</td></tr>
	<tr><td>$sqlDatabasePassword</td><td>Password di accesso del server di database SQL di Azure.</td></tr>
	<tr><td>$sqlDatabaseLocation</td><td>Questo valore viene usato solo durante la creazione di un nuovo server di database di Azure.</td></tr>
	<tr><td>$sqlDatabaseName</td><td>Database SQL usato per creare la tabella AvgDelays per il processo Sqoop. Lasciando il valore vuoto, verrà creato un database denominato "HDISqoop". Il nome della tabella per l'output del processo Sqooop è "AvgDelays". </td></tr>
	</table>
2. Aprire PowerShell ISE. 
2. Copiare e incollare lo script seguente nel riquadro dello script:
	
		[CmdletBinding()]
		Param(
		
		    # SQL database server variables
		    [Parameter(Mandatory=$True,
		               HelpMessage="Enter the Azure SQL Database Server Name to use an existing one. Enter nothing to create a new one.")]
		    [AllowEmptyString()]
		    [String]$sqlDatabaseServer,  # specify the Azure SQL database server name if you have one created. Otherwise use "".
		
		    [Parameter(Mandatory=$True,
		               HelpMessage="Enter the Azure SQL Database admin user.")]
		    [String]$sqlDatabaseUsername,
		
		    [Parameter(Mandatory=$True,
		               HelpMessage="Enter the Azure SQL Database admin user password.")]
		    [String]$sqlDatabasePassword,
		
		    [Parameter(Mandatory=$True,
		               HelpMessage="Enter the region to create the Database in.")]
		    [AllowEmptyString()]
		    [String]$sqlDatabaseLocation,   #For example, West US.
		
		    # SQL database variables
		    [Parameter(Mandatory=$True,
		               HelpMessage="Enter the database name if you have created one. Enter nothing to create one.")]
		    [AllowEmptyString()]
		    [String]$sqlDatabaseName # specify the database name if you have one created.  Otherwise use "" to have the script create one for you.
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

		#region - Add the Azure account 
		Write-Host "`nConnecting to your Azure subscription ..." -ForegroundColor Green
		$azureAccounts= Get-AzureAccount
		if (! $azureAccounts)
		{
		Add-AzureAccount
		}
		#endregion
		
		#region - Create and validate Azure SQL Database server
		if ([string]::IsNullOrEmpty($sqlDatabaseServer))
		{
			Write-Host "`nCreating SQL Database server ..."  -ForegroundColor Green
			$sqlDatabaseServer = (New-AzureSqlDatabaseServer -AdministratorLogin $sqlDatabaseUsername -AdministratorLoginPassword $sqlDatabasePassword -Location $sqlDatabaseLocation).ServerName
		    Write-Host "`tThe new SQL database server name is $sqlDatabaseServer." -ForegroundColor Cyan
		
		    Write-Host "`nCreating firewall rule, $fireWallRuleName ..." -ForegroundColor Green
		    $workstationIPAddress = Invoke-RestMethod $ipAddressRestService
		    New-AzureSqlDatabaseServerFirewallRule -ServerName $sqlDatabaseServer -RuleName "$fireWallRuleName-workstation" -StartIpAddress $workstationIPAddress -EndIpAddress $workstationIPAddress	
		    New-AzureSqlDatabaseServerFirewallRule -ServerName $sqlDatabaseServer -RuleName "$fireWallRuleName-Azureservices" -AllowAllAzureServices 
		}
		else
		{
		    $dbServer = Get-AzureSqlDatabaseServer -ServerName $sqlDatabaseServer
		    if (! $dbServer)
		    {
		        throw "The Azure SQL database server, $sqlDatabaseServer, doesn't exist!" 
		    }
		    else
		    {
			    Write-Host "`nUse an existing SQL Database server, $sqlDatabaseServer" -ForegroundColor Green
		    }
		}
		#endregion
		
		#region - Create and validate Azure SQL database
		if ([string]::IsNullOrEmpty($sqlDatabaseName))
		{
			Write-Host "`nCreating SQL Database, HDISqoop ..."  -ForegroundColor Green
		
			$sqlDatabaseName = "HDISqoop"
			$sqlDatabaseServerCredential = new-object System.Management.Automation.PSCredential($sqlDatabaseUsername, ($sqlDatabasePassword  | ConvertTo-SecureString -asPlainText -Force)) 
		
		    $sqlDatabaseServerConnectionContext = New-AzureSqlDatabaseServerContext -ServerName $sqlDatabaseServer -Credential $sqlDatabaseServerCredential 
			
			$sqlDatabase = New-AzureSqlDatabase -ConnectionContext $sqlDatabaseServerConnectionContext -DatabaseName $sqlDatabaseName -MaxSizeGB $sqlDatabaseMaxSizeGB
		}
		else
		{
		    $db = Get-AzureSqlDatabase -ServerName $sqlDatabaseServer -DatabaseName $sqlDatabaseName
		    if (! $db)
		    {
		        throw "The Azure SQL database server, $sqlDatabaseServer, doesn't exist!" 
		    }
		    else
		    {
			    Write-Host "`nUse an existing SQL Database, $sqlDatabaseName" -ForegroundColor Green
		    }
		}
		#endregion
			
		#region -  Excute a SQL command to create the AvgDelays table
			
		Write-Host "`nCreating SQL Database table ..."  -ForegroundColor Green
		$conn = New-Object System.Data.SqlClient.SqlConnection
		$conn.ConnectionString = "Data Source=$sqlDatabaseServer.database.windows.net;Initial Catalog=$sqlDatabaseName;User ID=$sqlDatabaseUsername;Password=$sqlDatabasePassword;Encrypt=true;Trusted_Connection=false;"
		$conn.open()
		$cmd = New-Object System.Data.SqlClient.SqlCommand
		$cmd.connection = $conn
		$cmd.commandtext = $sqlCreateAvgDelaysTable
		$cmd.executenonquery()
			
		$conn.close()
		
		Write-host "`nEnd of the PowerShell script" -ForegroundColor Green

	>[AZURE.NOTE] Lo script usa un servizio REST, http://bot.whatismyipaddress.com, per recuperare l'indirizzo IP esterno. L'indirizzo IP viene usato per creare una regola del firewall per il server di database SQL.  

	Ecco alcune variabili usate nello script:

	- **$ipAddressRestService**: Il valore predefinito è <u>http://bot.whatismyipaddress.com</u>. È un servizio REST per l'indirizzo IP pubblico che consente di ottenere l'indirizzo IP esterno. È anche possibile usare altri servizi. L'indirizzo IP esterno recuperato usando il servizio verrà usato per creare una regola del firewall per il proprio server di database SQL, in modo che sia possibile accedere al database dalla workstation (usando lo script PowerShell).
	- **$fireWallRuleName**: È la regola del firewall del server di database SQL di Azure. Il nome predefinito è <u>FlightDelay</u>. È anche possibile rinominarlo.
	- **$sqlDatabaseMaxSizeGB**: Questo valore viene usato solo durante la creazione di un nuovo server di database SQL. Il valore predefinito è <u>10GB</u>. sufficiente per questa esercitazione.
	- **$sqlDatabaseName**: Questo valore viene usato solo durante la creazione di un nuovo database SQL di Azure. Il valore predefinito è <u>HDISqoop</u>. Se viene rinominato, sarà necessario aggiornare anche lo script PowerShell di Sqoop. 

4. Premere **F5** per eseguire lo script. 
5. Convalidare l'output dello script. Assicurarsi che lo script sia stato eseguito correttamente.	

##<a id="nextsteps"></a> Passaggi successivi
È stato illustrato come caricare file nell'archiviazione BLOB, come popolare una tabella Hive usando i dati disponibili nell'archiviazione BLOB, come eseguire query Hive e come usare Sqoop per esportare i dati da HDFS nel database SQL di Azure. Per altre informazioni, vedere gli articoli seguenti:

* [Introduzione all'uso di HDInsight][hdinsight-get-started]
* [Usare Hive con HDInsight][hdinsight-use-hive]
* [Usare Oozie con HDInsight][hdinsight-use-oozie]
* [Usare Sqoop con HDInsight][hdinsight-use-sqoop]
* [Usare Pig con HDInsight][hdinsight-use-pig]
* [Sviluppare programmi MapReduce Java per HDInsight][hdinsight-develop-mapreduce]
* [Sviluppare programmi per la creazione di flussi Hadoop in C# per HDInsight][hdinsight-develop-streaming]



[azure-purchase-options]: http://azure.microsoft.com/pricing/purchase-options/
[azure-member-offers]: http://azure.microsoft.com/pricing/member-offers/
[azure-free-trial]: http://azure.microsoft.com/pricing/free-trial/


[rita-website]: http://www.transtats.bts.gov/DL_SelectFields.asp?Table_ID=236&DB_Short_Name=On-Time
[cindygross-hive-tables]: http://blogs.msdn.com/b/cindygross/archive/2013/02/06/hdinsight-hive-internal-and-external-tables-intro.aspx
[powershell-install-configure]: ../install-configure-powershell/

[hdinsight-use-oozie]: ../hdinsight-use-oozie/
[hdinsight-use-hive]: ../hdinsight-use-hive/
[hdinsight-provision]: ../hdinsight-provision-clusters/
[hdinsight-storage]: ../hdinsight-use-blob-storage/
[hdinsight-upload-data]: ../hdinsight-upload-data/
[hdinsight-get-started]: ../hdinsight-get-started/
[hdinsight-use-sqoop]: ../hdinsight-use-sqoop/
[hdinsight-use-pig]: ../hdinsight-use-pig/
[hdinsight-develop-streaming]: ../hdinsight-hadoop-develop-deploy-streaming-jobs/
[hdinsight-develop-mapreduce]: ../hdinsight-develop-deploy-java-mapreduce/

[hadoop-hiveql]: https://cwiki.apache.org/confluence/display/Hive/LanguageManual+DDL
[hadoop-shell-commands]: http://hadoop.apache.org/docs/r0.18.3/hdfs_shell.html

[technetwiki-hive-error]: http://social.technet.microsoft.com/wiki/contents/articles/23047.hdinsight-hive-error-unable-to-rename.aspx

[image-hdi-flightdelays-avgdelays-dataset]: ./media/hdinsight-analyze-flight-delay-data/HDI.FlightDelays.AvgDelays.DataSet.png
[img-hdi-flightdelays-run-hive-job-output]: ./media/hdinsight-analyze-flight-delay-data/HDI.FlightDelays.RunHiveJob.Output.png
[img-hdi-flightdelays-flow]: ./media/hdinsight-analyze-flight-delay-data/HDI.FlightDelays.Flow.png


<!--HONumber=42-->
