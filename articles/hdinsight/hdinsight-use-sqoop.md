<properties
	pageTitle="Usare Sqoop di Hadoop in HDInsight | Microsoft Azure"
	description="Informazioni su come usare Azure PowerShell da una workstation per eseguire importazioni ed esportazioni con Sqoop tra un cluster Hadoop e un database SQL di Azure."
	editor="cgronlun"
	manager="paulettm"
	services="hdinsight"
	documentationCenter=""
	tags="azure-portal"
	authors="mumian"/>

<tags
	ms.service="hdinsight"
	ms.workload="big-data"
	ms.tgt_pltfrm="na"
	ms.devlang="na"
	ms.topic="article"
	ms.date="03/03/2016"
	ms.author="jgao"/>

#Usare Sqoop con Hadoop in HDInsight (Windows)

[AZURE.INCLUDE [sqoop-selector](../../includes/hdinsight-selector-use-sqoop.md)]

Informazioni su come usare Sqoop in HDInsight per effettuare importazioni ed esportazioni tra un cluster HDInsight e un database SQL di Azure o un database di SQL Server.

> [AZURE.NOTE] La procedura illustrata in questo articolo può essere usata con un cluster HDInsight basato su Windows o su Linux, ma funziona solo se eseguita da un client Windows.
>
> Se si usa un client Linux, OS X o Unix e un server HDInsight basato su Linux, vedere [Usare Sqoop con Hadoop in HDInsight (SSH)](hdinsight-use-sqoop-mac-linux.md).

Benché Hadoop rappresenti una scelta ottimale per l'elaborazione di dati non strutturati e semistrutturati, ad esempio log e file, potrebbe essere necessario elaborare anche dati strutturati archiviati in database relazionali.

[Sqoop][sqoop-user-guide-1.4.4] è uno strumento progettato per il trasferimento di dati tra cluster Hadoop e database relazionali. Può essere usato per importare dati in HDFS (Hadoop Distributed File System) da un sistema di gestione di database relazionali (RDBMS), ad esempio SQL, MySQL oppure Oracle, trasformare i dati in Hadoop con MapReduce o Hive e quindi esportarli nuovamente in un sistema RDBMS. In questa esercitazione si userà un database SQL Server per il database relazionale.

Per informazioni sulle versioni di Sqoop supportate nei cluster HDInsight, vedere [Novità delle versioni cluster di Hadoop incluse in HDInsight][hdinsight-versions].

###Prerequisiti

Prima di iniziare questa esercitazione, è necessario disporre di quanto segue:

- **Workstation con Azure PowerShell**. Vedere [Installare Azure PowerShell 1.0 e versioni successive](hdinsight-administer-use-powershell.md#install-azure-powershell-10-and-greater).

##Informazioni sullo scenario

Il cluster HDInsight include alcuni dati di esempio. In questa esercitazione si useranno i due gruppi di dati di esempio seguenti:

- Un file di log log4j presente in */example/data/sample.log*. Dal file verranno estratti i log seguenti:

		2012-02-03 18:35:34 SampleClass6 [INFO] everything normal for id 577725851
		2012-02-03 18:35:34 SampleClass4 [FATAL] system problem at id 1991281254
		2012-02-03 18:35:34 SampleClass3 [DEBUG] detail for id 1304807656
		...

- Una tabella di Hive denominata *hivesampletable* che fa riferimento al file di dati presente in */hive/warehouse/hivesampletable*. La tabella contiene alcuni dati relativi al dispositivo mobile.

Sarà necessario innanzitutto esportare *sample.log* e *hivesampletable* nel database SQL di Azure o in SQL Server, quindi importare di nuovo in HDInsight la tabella contenente i dati del dispositivo mobile usando il percorso seguente:

	/tutorials/usesqoop/importeddata

## Creare un cluster e un database SQL

1. Fare clic sull'immagine seguente per aprire un modello di Gestione risorse di Azure nel portale di Azure.         

    <a href="https://portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Fhditutorialdata.blob.core.windows.net%2Fusesqoop%2Fcreate-linux-based-hadoop-cluster-in-hdinsight-and-sql-database.json" target="_blank"><img src="https://acom.azurecomcdn.net/80C57D/cdn/mediahandler/docarticles/dpsmedia-prod/azure.microsoft.com/it-IT/documentation/articles/hdinsight-hbase-tutorial-get-started-linux/20160201111850/deploy-to-azure.png" alt="Deploy to Azure"></a>
    
    Il modello di Azure Resource Manager è disponibile in un contenitore BLOB pubblico, **https://hditutorialdata.blob.core.windows.net/usesqoop/create-linux-based-hadoop-cluster-in-hdinsight-and-sql-database.json*.
    
    Il modello di Azure Resource Manager chiama un pacchetto bacpac per distribuire gli schemi della tabella nel database SQL. Il pacchetto bacpac è disponibile anche in un contenitore BLOB pubblico, https://hditutorialdata.blob.core.windows.net/usesqoop/SqoopTutorial-2016-2-23-11-2.bacpac. Se si vuole usare un contenitore privato per i file bacpac, usare i valori seguenti nel modello:
    
        "storageKeyType": "Primary",
        "storageKey": "<TheAzureStorageAccountKey>",
    
2. Nel pannello Parametri immettere le informazioni seguenti:

    - **ClusterName**: immettere un nome per il cluster Hadoop che verrà creato.
    - **Nome utente e password di accesso del cluster**: il nome dell'account di accesso predefinito è admin.
    - **Nome utente e password SSH**.
    - **Nome utente e password di accesso del server del database SQL**.

    I valori seguenti sono hardcoded nella sezione relativa alle variabili:
    
    |Nome dell'account di archiviazione predefinito|<CluterName>store|
    |----------------------------|-----------------|
    |Nome server del database SQL di Azure|<ClusterName>dbserver|
    |Nome del database SQL di Azure|<ClusterName>db|
    
    Annotare questi valori. Sarà necessario usarli più avanti nell'esercitazione.
    
3\. Fare clic su **OK** per salvare i parametri.

4\. Nel pannello **Distribuzione personalizzata** fare clic su **Gruppo di risorse** nella casella di riepilogo a discesa e quindi scegliere **Nuovo** per creare un nuovo gruppo di risorse. Il gruppo di risorse è un contenitore che raggruppa il cluster, l'account di archiviazione dipendente e altre risorse collegate.

5\. Fare clic su **Note legali** e quindi su **Crea**.

6\. Fare clic su **Crea**. Verrà visualizzato un nuovo riquadro denominato Invio della distribuzione per Distribuzione modello. La creazione del cluster e del database SQL richiede circa 20 minuti.

Se si sceglie di utilizzare un database SQL di Azure esistente o un Server SQL di Microsoft

- **Database SQL di Azure**: è necessario configurare una regola del firewall per il server di database SQL per consentire l'accesso dalla workstation. Per istruzioni sulla creazione di un database SQL di Azure e sulla configurazione del firewall, vedere [Introduzione al database SQL di Azure][sqldatabase-get-started]. 

    > [AZURE.NOTE] Per impostazione predefinita, un database SQL di Azure consente connessioni da servizi di Azure, ad esempio Azure HDinsight. Se questa impostazione del firewall è disabilitata, sarà necessario abilitarla nel portale di anteprima di Azure. Per istruzioni sulla creazione di un database SQL di Azure e sulla configurazione di regole del firewall, vedere [Come creare e configurare un database SQL di Azure][sqldatabase-create-configue].

- **SQL Server**: se il cluster HDInsight si trova sulla stessa rete virtuale di Azure di SQL Server, è possibile usare la procedura descritta in questo articolo per importare ed esportare i dati in un database SQL Server.

    > [AZURE.NOTE] HDInsight supporta solo reti virtuali basate sulla posizione e attualmente non funziona con le reti virtuali basate su gruppi di affinità.

    * Per creare e configurare una rete virtuale, vedere [Attività di configurazione della rete virtuale](../services/virtual-machines/).

        * Quando si usa SQL Server nel proprio data center, è necessario configurare la rete virtuale come *da sito a sito* o *da punto a sito*.

            > [AZURE.NOTE] Per le reti virtuali da **punto a sito**, è necessario che su SQL Server venga eseguita l'applicazione di configurazione del client VPN, disponibile nel **dashboard** di configurazione della rete virtuale di Azure.

        * Quando si usa SQL Server in una macchina virtuale di Azure, sarà possibile usare qualsiasi configurazione di rete virtuale a condizione che la macchina virtuale sulla quale è ospitato SQL Server sia un membro della stessa rete virtuale di HDInsight.

    * Per creare un cluster HDInsight in una rete virtuale, vedere [Creare cluster Hadoop in HDInsight con opzioni personalizzate](hdinsight-provision-clusters.md)

    > [AZURE.NOTE] SQL Server deve sempre consentire l'autenticazione. Per la procedura descritta in questo articolo è necessario usare un account di accesso di SQL Server.
	

## Eseguire Sqoop tramite PowerShell

Lo script di PowerShell seguente pre-elabora il file di origine e lo esporta in un database SQL di Azure:

    $resourceGroupName = "<AzureResourceGroupName>"
    $hdinsightClusterName = "<HDInsightClusterName>"

    $httpUserName = "admin"
    $httpPassword = "<Password>"

    $defaultStorageAccountName = $hdinsightClusterName + "store"
    $defaultBlobContainerName = $hdinsightClusterName


    $sqlDatabaseServerName = $hdinsightClusterName + "dbserver"
    $sqlDatabaseName = $hdinsightClusterName + "db"
    $sqlDatabaseLogin = "sqluser"
    $sqlDatabasePassword = "<Password>"

    #region - Connect to Azure subscription
    Write-Host "`nConnecting to your Azure subscription ..." -ForegroundColor Green
    try{Get-AzureRmContext}
    catch{Login-AzureRmAccount}
    #endregion
        
    #region - pre-process the source file
        
    Write-Host "`nPreprocessing the source file ..." -ForegroundColor Green
        
    # This procedure creates a new file with $destBlobName
    $sourceBlobName = "example/data/sample.log"
    $destBlobName = "tutorials/usesqoop/data/sample.log"
        
    # Define the connection string
    $defaultStorageAccountKey = Get-AzureRmStorageAccountKey `
                                    -ResourceGroupName $resourceGroupName `
                                    -Name $defaultStorageAccountName |  %{ $_.Key1 }
    $storageConnectionString = "DefaultEndpointsProtocol=https;AccountName=$defaultStorageAccountName;AccountKey=$defaultStorageAccountKey"
        
    # Create block blob objects referencing the source and destination blob.
    $storageAccount = [Microsoft.WindowsAzure.Storage.CloudStorageAccount]::Parse($storageConnectionString)
    $storageClient = $storageAccount.CreateCloudBlobClient();
    $storageContainer = $storageClient.GetContainerReference($defaultBlobContainerName)
    $sourceBlob = $storageContainer.GetBlockBlobReference($sourceBlobName)
    $destBlob = $storageContainer.GetBlockBlobReference($destBlobName)
        
    # Define a MemoryStream and a StreamReader for reading from the source file
    $stream = New-Object System.IO.MemoryStream
    $stream = $sourceBlob.OpenRead()
    $sReader = New-Object System.IO.StreamReader($stream)
        
    # Define a MemoryStream and a StreamWriter for writing into the destination file
    $memStream = New-Object System.IO.MemoryStream
    $writeStream = New-Object System.IO.StreamWriter $memStream
        
    # Pre-process the source blob
    $exString = "java.lang.Exception:"
    while(-Not $sReader.EndOfStream){
        $line = $sReader.ReadLine()
        $split = $line.Split(" ")
        
        # remove the "java.lang.Exception" from the first element of the array
        # for example: java.lang.Exception: 2012-02-03 19:11:02 SampleClass8 [WARN] problem finding id 153454612
        if ($split[0] -eq $exString){
            #create a new ArrayList to remove $split[0]
            $newArray = [System.Collections.ArrayList] $split
            $newArray.Remove($exString)
        
            # update $split and $line
            $split = $newArray
            $line = $newArray -join(" ")
        }
        
        # remove the lines that has less than 7 elements
        if ($split.count -ge 7){
            write-host $line
            $writeStream.WriteLine($line)
        }
    }
        
    # Write to the destination blob
    $writeStream.Flush()
    $memStream.Seek(0, "Begin")
    $destBlob.UploadFromStream($memStream)
        
    #endregion
        
    #region - export the log file from the cluster to the SQL database
        
    Write-Host "Exporting the log file ..." -ForegroundColor Green

    $pw = ConvertTo-SecureString -String $httpPassword -AsPlainText -Force
    $httpCredential = New-Object System.Management.Automation.PSCredential($httpUserName,$pw)
        
    # Connection string for Azure SQL Database.
    # Comment if using SQL Server
    $connectionString = "jdbc:sqlserver://$sqlDatabaseServerName.database.windows.net;user=$sqlDatabaseLogin@$sqlDatabaseServerName;password=$sqlDatabasePassword;database=$sqlDatabaseName"
    # Connection string for SQL Server.
    # Uncomment if using SQL Server.
    #$connectionString = "jdbc:sqlserver://$sqlDatabaseServerName;user=$sqlDatabaseLogin;password=$sqlDatabasePassword;database=$sqlDatabaseName"
        
    $tableName_log4j = "log4jlogs"
    $exportDir_log4j = "/tutorials/usesqoop/data"
        
    # Submit a Sqoop job
    $sqoopDef = New-AzureRmHDInsightSqoopJobDefinition `
        -Command "export --connect $connectionString --table $tableName_log4j --export-dir $exportDir_log4j --input-fields-terminated-by ' ' -m 1"

    $sqoopJob = Start-AzureRmHDInsightJob `
                    -ClusterName $hdinsightClusterName `
                    -HttpCredential $httpCredential `
                    -JobDefinition $sqoopDef #-Debug -Verbose

    Wait-AzureRmHDInsightJob `
        -ResourceGroupName $resourceGroupName `
        -ClusterName $hdinsightClusterName `
        -HttpCredential $httpCredential `
        -JobId $sqoopJob.JobId
        
    Write-Host "Standard Error" -BackgroundColor Green
    Get-AzureRmHDInsightJobOutput -ResourceGroupName $resourceGroupName -ClusterName $hdinsightClusterName -DefaultStorageAccountName $defaultStorageAccountName -DefaultStorageAccountKey $defaultStorageAccountKey -DefaultContainer $defaultBlobContainerName -HttpCredential $httpCredential -JobId $sqoopJob.JobId -DisplayOutputType StandardError
    Write-Host "Standard Output" -BackgroundColor Green
    Get-AzureRmHDInsightJobOutput -ResourceGroupName $resourceGroupName -ClusterName $hdinsightClusterName -DefaultStorageAccountName $defaultStorageAccountName -DefaultStorageAccountKey $defaultStorageAccountKey -DefaultContainer $defaultBlobContainerName -HttpCredential $httpCredential -JobId $sqoopJob.JobId -DisplayOutputType StandardOutput
    #endregion

## Eseguire Sqoop mediante .NET SDK

In questa sezione, si creerà un'applicazione console c# per esportare la hivesampletable alla tabella di Database SQL creata in precedenza in questa esercitazione.

**Per inviare un processo Sqoop**

1. Dalla Console di gestione pacchetti di Visual Studio, eseguire il comando NuGet seguente per importare il pacchetto.

		Install-Package Microsoft.Azure.Management.HDInsight.Job -Pre
2. Nel file Program.cs usare le istruzioni using seguenti:

		using System;
		using Microsoft.Azure.Management.HDInsight.Job;
		using Microsoft.Azure.Management.HDInsight.Job.Models;
		using Hyak.Common;
3. Nella funzione Main() aggiungere il codice seguente. Per informazioni generali sull'uso di .NET SDK per HDInsight, vedere [Invio di processi Hadoop a livello di codice][hdinsight-submit-jobs].

		var ExistingClusterName = "<HDInsightClusterName>";
		var ExistingClusterUri = ExistingClusterName + ".azurehdinsight.net";
		var ExistingClusterUsername = "<HDInsightClusterHttpUsername>";
		var ExistingClusterPassword = "<HDInsightClusterHttpUserPassword>";
		
		var sqlDatabaseServerName = "<AzureSQLDatabaseServerName>";
		var sqlDatabaseLogin = "<AzureSQLDatabaseLogin>";
		var sqlDatabaseLoginPassword = "<AzureSQLDatabaseLoginPassword>";
		var sqlDatabaseDatabaseName = "<AzureSQLDatabaseDatabaseName>";
		
		var sqlDatabaseTableName = "log4jlogs";
		var exportDir = "/hive/warehouse/hivesampletable";

		var cmdExport = @"export";
		// Connection string for using Azure SQL Database.
		// Comment if using SQL Server
		cmdExport = cmdExport + @" --connect 'jdbc:sqlserver://" + sqlDatabaseServerName + ".database.windows.net;user=" + sqlDatabaseLogin + "@" + sqlDatabaseServerName + ";password=" + sqlDatabaseLoginPassword + ";database=" + sqlDatabaseDatabaseName +"'"; 
		// Connection string for using SQL Server.
		// Uncomment if using SQL Server
		//cmdExport = cmdExport + @" --connect jdbc:sqlserver://" + sqlDatabaseServerName + ";user=" + sqlDatabaseLogin + ";password=" + sqlDatabaseLoginPassword + ";database=" + sqlDatabaseDatabaseName;
		cmdExport = cmdExport + @" --table " + sqlDatabaseTableName;
		cmdExport = cmdExport + @" --export-dir " + exportDir;
		cmdExport = cmdExport + @" --input-fields-terminated-by \0x20 -m 1";
		
		HDInsightJobManagementClient _hdiJobManagementClient;
		var clusterCredentials = new BasicAuthenticationCloudCredentials { Username = ExistingClusterUsername, Password = ExistingClusterPassword };
		_hdiJobManagementClient = new HDInsightJobManagementClient(ExistingClusterUri, clusterCredentials);

		var parameters = new SqoopJobSubmissionParameters
		{
		    UserName = ExistingClusterUsername,
		    Command = cmdExport
		};
		
		System.Console.WriteLine("Submitting the Sqoop job to the cluster...");
		var response = _hdiJobManagementClient.JobManagement.SubmitSqoopJob(parameters);
		System.Console.WriteLine("Validating that the response is as expected...");
		System.Console.WriteLine("Response status code is " + response.StatusCode);
		System.Console.WriteLine("Validating the response object...");
		System.Console.WriteLine("JobId is " + response.JobSubmissionJsonResponse.Id);
		Console.WriteLine("Press ENTER to continue ...");
		Console.ReadLine();
4. Premere **F5** per eseguire il programma. 

##Passaggi successivi

In questa esercitazione si è appreso come usare Sqoop. Per altre informazioni, vedere:

- [Usare Oozie con HDInsight][hdinsight-use-oozie]\: usare un'azione di Sqoop nel flusso di lavoro di Oozie.
- [Analizzare i dati sui ritardi dei voli usando HDInsight][hdinsight-analyze-flight-data]\: usare Hive nell'analisi dei dati sui ritardi dei voli e quindi usare Sqoop per esportare dati nel database SQL di Azure.
- [Caricare i dati in HDInsight][hdinsight-upload-data]\: per altri metodi per il caricamento di file in HDInsight o nell'archiviazione BLOB di Azure.


## Appendice A - Esempio di PowerShell

L'esempio di PowerShell esegue questa procedura:

1. Connettersi ad Azure.
2. Creare un gruppo di risorse di Azure. Per altre informazioni, vedere [Uso di Azure PowerShell con Gestione risorse di Azure](../powershell-azure-resource-manager.md).
3. Creare un server di Database SQL di Azure, un database SQL Azure e due tabelle. 

	Se invece si utilizza SQL Server, utilizzare le istruzioni seguenti per creare le tabelle:
	
		CREATE TABLE [dbo].[log4jlogs](
		 [t1] [nvarchar](50),
		 [t2] [nvarchar](50),
		 [t3] [nvarchar](50),
		 [t4] [nvarchar](50),
		 [t5] [nvarchar](50),
		 [t6] [nvarchar](50),
		 [t7] [nvarchar](50))

		CREATE TABLE [dbo].[mobiledata](
		 [clientid] [nvarchar](50),
		 [querytime] [nvarchar](50),
		 [market] [nvarchar](50),
		 [deviceplatform] [nvarchar](50),
		 [devicemake] [nvarchar](50),
		 [devicemodel] [nvarchar](50),
		 [state] [nvarchar](50),
		 [country] [nvarchar](50),
		 [querydwelltime] [float],
		 [sessionid] [bigint],
		 [sessionpagevieworder][bigint])

	Il modo più semplice per esaminare il database e le tabelle è quello di usare Visual Studio. Il server del database e il database possono essere esaminati tramite il portale di Azure.

4. Creare un cluster HDInsight

	Per esaminare il cluster, è possibile usare il portale di Azure o Azure PowerShell.

5. Pre-elaborare i file dei dati di origine.

	In questa esercitazione, verranno esportati nel database SQL di Azure un file di log log4j (un file con valori delimitati) e una tabella di Hive. Il file con valori delimitati è denominato */example/data/sample.log*. Alcuni esempi di file di log log4j sono stati visualizzati in precedenza in questa esercitazione. Nel file di log sono presenti alcune righe vuote e righe simili alle seguenti:
	
		java.lang.Exception: 2012-02-03 20:11:35 SampleClass2 [FATAL] unrecoverable system problem at id 609774657
			at com.osa.mocklogger.MockLogger$2.run(MockLogger.java:83)
	
	Questa condizione può andar bene per altri esempi in cui vengono usati questi dati, ma è necessario rimuovere queste eccezioni prima di poterli importare nel database SQL di Azure o in SQL Server. In presenza di una stringa vuota o di una riga contenente un numero di elementi inferiore al numero dei campi definiti nella tabella del database SQL di Azure, non sarà possibile eseguire l'esportazione con Sqoop. La tabella log4jlogs comprende 7 campi tipo stringa.

	Questa procedura crea un nuovo file nel cluster: tutorials/usesqoop/data/sample.log. Per esaminare il file di dati modificato, è possibile usare il portale di Azure, uno strumento di Esplora archivi Azure oppure Azure PowerShell. In [Introduzione all'uso di HDInsight][hdinsight-get-started] è presente un esempio di codice relativo all'uso di Azure PowerShell per scaricare un file e visualizzarne il contenuto.

6. Esportare un file dei dati nel database SQL di Azure.

	Il file di origine è tutorials/usesqoop/data/sample.log. La tabella in cui vengono esportati i dati è denominata log4jlogs.
	
	> [AZURE.NOTE] Ad eccezione delle informazioni sulla stringa di connessione, la procedura descritta in questa sezione dovrebbe funzionare per il database SQL di Azure e per SQL Server. La procedura è stata verificata con la configurazione seguente:
	>
	> * **Configurazione da punto a sito della rete virtuale di Azure**: una rete virtuale che connette il cluster HDInsight a un SQL Server in un data center privato. Per altre informazioni, vedere [Configurare una VPN da punto a sito nel portale di gestione](../vpn-gateway/vpn-gateway-point-to-site-create.md).
	> * **Azure HDInsight 3.1**: vedere [Creare cluster Hadoop in HDInsight con opzioni personalizzate](hdinsight-provision-clusters.md) per informazioni sulla creazione di un cluster in una rete virtuale.
	> * **SQL Server 2014**: configurato per consentire l'autenticazione SQL e l'esecuzione del pacchetto di configurazione del client VPN per eseguire la connessione sicura alla rete virtuale.

7. Esportare una tabella Hive nel database SQL di Azure.

8. Importare la tabella mobiledata nel cluster HDInsight.

	Per esaminare il file di dati modificato, è possibile usare il portale di anteprima di Azure, uno strumento di Esplora archivi Azure oppure Azure PowerShell. In [Introduzione all'uso di HDInsight][hdinsight-get-started] è presente un esempio di codice relativo all'uso di Azure PowerShell per scaricare un file e visualizzarne il contenuto.


### L’esempio di PowerShell.

	# Prepare an Azure SQL database to be used by the Sqoop tutorial
	
	#region - provide the following values
	
	$subscriptionID = "<Enter your Azure Subscription ID>"
	
	$sqlDatabaseLogin = "<Enter a SQL Database Login name>" #SQL Database server login
	$sqlDatabasePassword = "<Enter a Password>"
	
	$httpUserName = "admin"  #HDInsight cluster username
	$httpPassword = "<Enter a Password>"
	
	# used for creating Azure service names
	$nameToken = "<Enter an alias>" 
	$namePrefix = $nameToken.ToLower() + (Get-Date -Format "MMdd")
	#endregion
	
	#region - variables
	
	# Resource group variables
	$resourceGroupName = $namePrefix + "rg"
	$location = "East US 2" # used by all Azure services defined in this tutorial
	
	# SQL database varialbes
	$sqlDatabaseServerName = $namePrefix + "sqldbserver"
	$sqlDatabaseName = $namePrefix + "sqldb"
	$sqlDatabaseConnectionString = "Data Source=$sqlDatabaseServerName.database.windows.net;Initial Catalog=$sqlDatabaseName;User ID=$sqlDatabaseLogin;Password=$sqlDatabasePassword;Encrypt=true;Trusted_Connection=false;"
	$sqlDatabaseMaxSizeGB = 10
	
	# Used for retrieving external IP address and creating firewall rules
	$ipAddressRestService = "http://bot.whatismyipaddress.com"
	$fireWallRuleName = "UseSqoop"
	
	# Used for creating tables and clustered indexes
	$cmdCreateLog4jTable = "CREATE TABLE [dbo].[log4jlogs](
		[t1] [nvarchar](50),
		[t2] [nvarchar](50),
		[t3] [nvarchar](50),
		[t4] [nvarchar](50),
		[t5] [nvarchar](50),
		[t6] [nvarchar](50),
		[t7] [nvarchar](50))"
	
	$cmdCreateLog4jClusteredIndex = "CREATE CLUSTERED INDEX log4jlogs_clustered_index on log4jlogs(t1)"
	
	$cmdCreateMobileTable = " CREATE TABLE [dbo].[mobiledata](
	[clientid] [nvarchar](50),
	[querytime] [nvarchar](50),
	[market] [nvarchar](50),
	[deviceplatform] [nvarchar](50),
	[devicemake] [nvarchar](50),
	[devicemodel] [nvarchar](50),
	[state] [nvarchar](50),
	[country] [nvarchar](50),
	[querydwelltime] [float],
	[sessionid] [bigint],
	[sessionpagevieworder][bigint])"
	
	$cmdCreateMobileDataClusteredIndex = "CREATE CLUSTERED INDEX mobiledata_clustered_index on mobiledata(clientid)"
	
	# HDInsight variables
	$hdinsightClusterName = $namePrefix + "hdi"
	$defaultStorageAccountName = $namePrefix + "store"
	$defaultBlobContainerName = $hdinsightClusterName
	#endregion
	
	# Treat all errors as terminating
	$ErrorActionPreference = "Stop"
	
	#region - Connect to Azure subscription
	Write-Host "`nConnecting to your Azure subscription ..." -ForegroundColor Green
	try{Get-AzureRmContext}
	catch{Login-AzureRmAccount}
	#endregion
	
	#region - Create Azure resouce group
	Write-Host "`nCreating an Azure resource group ..." -ForegroundColor Green
	try{
		Get-AzureRmResourceGroup -Name $resourceGroupName
	}
	catch{
		New-AzureRmResourceGroup -Name $resourceGroupName -Location $location
	}
	#endregion
	
	#region - Create Azure SQL database server
	Write-Host "`nCreating an Azure SQL Database server ..." -ForegroundColor Green
	try{
		Get-AzureRmSqlServer -ServerName $sqlDatabaseServerName -ResourceGroupName $resourceGroupName}
	catch{
		Write-Host "`nCreating SQL Database server ..."  -ForegroundColor Green
	
		$sqlDatabasePW = ConvertTo-SecureString -String $sqlDatabasePassword -AsPlainText -Force
		$credential = New-Object System.Management.Automation.PSCredential($sqlDatabaseLogin,$sqlDatabasePW)
	
		$sqlDatabaseServerName = (New-AzureRmSqlServer `
									-ResourceGroupName $resourceGroupName `
									-ServerName $sqlDatabaseServerName `
									-SqlAdministratorCredentials $credential `
									-Location $location).ServerName
		Write-Host "`tThe new SQL database server name is $sqlDatabaseServerName." -ForegroundColor Cyan
	
		Write-Host "`nCreating firewall rule, $fireWallRuleName ..." -ForegroundColor Green
		$workstationIPAddress = Invoke-RestMethod $ipAddressRestService
		New-AzureRmSqlServerFirewallRule `
			-ResourceGroupName $resourceGroupName `
			-ServerName $sqlDatabaseServerName `
			-FirewallRuleName "$fireWallRuleName-workstation" `
			-StartIpAddress $workstationIPAddress `
			-EndIpAddress $workstationIPAddress
	
		#To allow other Azure services to access the server add a firewall rule and set both the StartIpAddress and EndIpAddress to 0.0.0.0. 
		#Note that this allows Azure traffic from any Azure subscription to access the server.
		New-AzureRmSqlServerFirewallRule `
			-ResourceGroupName $resourceGroupName `
			-ServerName $sqlDatabaseServerName `
			-FirewallRuleName "$fireWallRuleName-Azureservices" `
			-StartIpAddress "0.0.0.0" `
			-EndIpAddress "0.0.0.0"
	}
	
	#endregion
	
	#region - Create and validate Azure SQL database
	Write-Host "`nCreating an Azure SQL database ..." -ForegroundColor Green
	
	try {
		Get-AzureRmSqlDatabase `
			-ResourceGroupName $resourceGroupName `
			-ServerName $sqlDatabaseServerName `
			-DatabaseName $sqlDatabaseName
	}
	catch {
		Write-Host "`nCreating SQL Database, $sqlDatabaseName ..."  -ForegroundColor Green
		New-AzureRMSqlDatabase `
			-ResourceGroupName $resourceGroupName `
			-ServerName $sqlDatabaseServerName `
			-DatabaseName $sqlDatabaseName `
			-Edition "Standard" `
			-RequestedServiceObjectiveName "S1"
	}
	
	#endregion
	
	#region - Create tables
	Write-Host "Creating the log4jlogs table and the mobiledata table ..." -ForegroundColor Green
	
	$conn = New-Object System.Data.SqlClient.SqlConnection
	$conn.ConnectionString = $sqlDatabaseConnectionString
	$conn.Open()
	
	# Create the log4jlogs table and index
	$cmd = New-Object System.Data.SqlClient.SqlCommand
	$cmd.Connection = $conn
	$cmd.CommandText = $cmdCreateLog4jTable
	$ret = $cmd.ExecuteNonQuery()
	$cmd.CommandText = $cmdCreateLog4jClusteredIndex
	$cmd.ExecuteNonQuery()
	
	# Create the mobiledata table and index
	$cmd.CommandText = $cmdCreateMobileTable
	$cmd.ExecuteNonQuery()
	$cmd.CommandText = $cmdCreateMobileDataClusteredIndex
	$cmd.ExecuteNonQuery()
	
	$conn.close()
	
	#endregion
	
	
	#region - Create HDInsight cluster
	
	Write-Host "Creating the HDInsight cluster and the dependent services ..." -ForegroundColor Green
	
	# Create the default storage account
	New-AzureRmStorageAccount `
		-ResourceGroupName $resourceGroupName `
		-Name $defaultStorageAccountName `
		-Location $location `
		-Type Standard_LRS
	
	# Create the default Blob container
	$defaultStorageAccountKey = Get-AzureRmStorageAccountKey `
									-ResourceGroupName $resourceGroupName `
									-Name $defaultStorageAccountName |  %{ $_.Key1 }
	$defaultStorageAccountContext = New-AzureStorageContext `
										-StorageAccountName $defaultStorageAccountName `
										-StorageAccountKey $defaultStorageAccountKey 
	New-AzureStorageContainer `
		-Name $defaultBlobContainerName `
		-Context $defaultStorageAccountContext 
	
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
		-DefaultStorageContainer $defaultBlobContainerName 
	
	# Validate the cluster
	Get-AzureRmHDInsightCluster -ClusterName $hdinsightClusterName
	#endregion
	
	#region - pre-process the source file
	
	Write-Host "Preprocessing the source file ..." -ForegroundColor Green
	
	# This procedure creates a new file with $destBlobName
	$sourceBlobName = "example/data/sample.log"
	$destBlobName = "tutorials/usesqoop/data/sample.log"
	
	# Define the connection string
	$storageConnectionString = "DefaultEndpointsProtocol=https;AccountName=$defaultStorageAccountName;AccountKey=$defaultStorageAccountKey"
	
	# Create block blob objects referencing the source and destination blob.
	$storageAccount = [Microsoft.WindowsAzure.Storage.CloudStorageAccount]::Parse($storageConnectionString)
	$storageClient = $storageAccount.CreateCloudBlobClient();
	$storageContainer = $storageClient.GetContainerReference($defaultBlobContainerName)
	$sourceBlob = $storageContainer.GetBlockBlobReference($sourceBlobName)
	$destBlob = $storageContainer.GetBlockBlobReference($destBlobName)
	
	# Define a MemoryStream and a StreamReader for reading from the source file
	$stream = New-Object System.IO.MemoryStream
	$stream = $sourceBlob.OpenRead()
	$sReader = New-Object System.IO.StreamReader($stream)
	
	# Define a MemoryStream and a StreamWriter for writing into the destination file
	$memStream = New-Object System.IO.MemoryStream
	$writeStream = New-Object System.IO.StreamWriter $memStream
	
	# Pre-process the source blob
	$exString = "java.lang.Exception:"
	while(-Not $sReader.EndOfStream){
		$line = $sReader.ReadLine()
		$split = $line.Split(" ")
	
		# remove the "java.lang.Exception" from the first element of the array
		# for example: java.lang.Exception: 2012-02-03 19:11:02 SampleClass8 [WARN] problem finding id 153454612
		if ($split[0] -eq $exString){
			#create a new ArrayList to remove $split[0]
			$newArray = [System.Collections.ArrayList] $split
			$newArray.Remove($exString)
	
			# update $split and $line
			$split = $newArray
			$line = $newArray -join(" ")
		}
	
		# remove the lines that has less than 7 elements
		if ($split.count -ge 7){
			write-host $line
			$writeStream.WriteLine($line)
		}
	}
	
	# Write to the destination blob
	$writeStream.Flush()
	$memStream.Seek(0, "Begin")
	$destBlob.UploadFromStream($memStream)
	
	#endregion
	
	#region - export a log file from the cluster to the SQL database
	
	Write-Host "Preprocessing the source file ..." -ForegroundColor Green
	
	$tableName_log4j = "log4jlogs"
	
	# Connection string for Azure SQL Database.
	# Comment if using SQL Server
	$connectionString = "jdbc:sqlserver://$sqlDatabaseServerName.database.windows.net;user=$sqlDatabaseLogin@$sqlDatabaseServerName;password=$sqlDatabasePassword;database=$sqlDatabaseName"
	# Connection string for SQL Server.
	# Uncomment if using SQL Server.
	#$connectionString = "jdbc:sqlserver://$sqlDatabaseServerName;user=$sqlDatabaseLogin;password=$sqlDatabasePassword;database=$sqlDatabaseName"
	
	$exportDir_log4j = "/tutorials/usesqoop/data"
	
	# Submit a Sqoop job
	$sqoopDef = New-AzureRmHDInsightSqoopJobDefinition `
		-Command "export --connect $connectionString --table $tableName_log4j --export-dir $exportDir_log4j --input-fields-terminated-by \0x20 -m 1"
	$sqoopJob = Start-AzureRmHDInsightJob `
					-ClusterName $hdinsightClusterName `
					-HttpCredential $httpCredential `
					-JobDefinition $sqoopDef #-Debug -Verbose
	Wait-AzureRmHDInsightJob `
		-ResourceGroupName $resourceGroupName `
		-ClusterName $hdinsightClusterName `
		-HttpCredential $httpCredential `
		-JobId $sqoopJob.JobId
	
	Write-Host "Standard Error" -BackgroundColor Green
	Get-AzureRmHDInsightJobOutput -ResourceGroupName $resourceGroupName -ClusterName $hdinsightClusterName -DefaultStorageAccountName $defaultStorageAccountName -DefaultStorageAccountKey $defaultStorageAccountKey -DefaultContainer $defaultBlobContainerName -HttpCredential $httpCredential -JobId $sqoopJob.JobId -DisplayOutputType StandardError
	Write-Host "Standard Output" -BackgroundColor Green
	Get-AzureRmHDInsightJobOutput -ResourceGroupName $resourceGroupName -ClusterName $hdinsightClusterName -DefaultStorageAccountName $defaultStorageAccountName -DefaultStorageAccountKey $defaultStorageAccountKey -DefaultContainer $defaultBlobContainerName -HttpCredential $httpCredential -JobId $sqoopJob.JobId -DisplayOutputType StandardOutput
	
	#endregion
	
	#region - export a Hive table
	
	$tableName_mobile = "mobiledata"
	$exportDir_mobile = "/hive/warehouse/hivesampletable"
	
	$sqoopDef = New-AzureRmHDInsightSqoopJobDefinition `
		-Command "export --connect $connectionString --table $tableName_mobile --export-dir $exportDir_mobile --fields-terminated-by \t -m 1"
	$sqoopJob = Start-AzureRmHDInsightJob `
					-ClusterName $hdinsightClusterName `
					-HttpCredential $httpCredential `
					-JobDefinition $sqoopDef #-Debug -Verbose
	
	Wait-AzureRmHDInsightJob `
		-ResourceGroupName $resourceGroupName `
		-ClusterName $hdinsightClusterName `
		-HttpCredential $httpCredential `
		-JobId $sqoopJob.JobId
	
	Write-Host "Standard Error" -BackgroundColor Green
	Get-AzureRmHDInsightJobOutput `
		-ResourceGroupName $resourceGroupName `
		-ClusterName $hdinsightClusterName `
		-DefaultStorageAccountName $defaultStorageAccountName `
		-DefaultStorageAccountKey $defaultStorageAccountKey `
		-DefaultContainer $defaultBlobContainerName `
		-HttpCredential $httpCredential `
		-JobId $sqoopJob.JobId `
		-DisplayOutputType StandardError
	
	Write-Host "Standard Output" -BackgroundColor Green
	Get-AzureRmHDInsightJobOutput `
		-ResourceGroupName $resourceGroupName `
		-ClusterName $hdinsightClusterName `
		-DefaultStorageAccountName $defaultStorageAccountName `
		-DefaultStorageAccountKey $defaultStorageAccountKey `
		-DefaultContainer $defaultBlobContainerName `
		-HttpCredential $httpCredential `
		-JobId $sqoopJob.JobId `
		-DisplayOutputType StandardOutput
	
	#endregion
	
	#region - import a database
	
	$targetDir_mobile = "/tutorials/usesqoop/importeddata/"
	
	$sqoopDef = New-AzureRmHDInsightSqoopJobDefinition `
		-Command "import --connect $connectionString --table $tableName_mobile --target-dir $targetDir_mobile --fields-terminated-by \t --lines-terminated-by \n -m 1"
	
	$sqoopJob = Start-AzureRmHDInsightJob `
					-ClusterName $hdinsightClusterName `
					-HttpCredential $httpCredential `
					-JobDefinition $sqoopDef #-Debug -Verbose
	
	Wait-AzureRmHDInsightJob `
		-ResourceGroupName $resourceGroupName `
		-ClusterName $hdinsightClusterName `
		-HttpCredential $httpCredential `
		-JobId $sqoopJob.JobId
	
	Write-Host "Standard Error" -BackgroundColor Green
	Get-AzureRmHDInsightJobOutput `
		-ResourceGroupName $resourceGroupName `
		-ClusterName $hdinsightClusterName `
		-DefaultStorageAccountName $defaultStorageAccountName `
		-DefaultStorageAccountKey $defaultStorageAccountKey `
		-DefaultContainer $defaultBlobContainerName `
		-HttpCredential $httpCredential `
		-JobId $sqoopJob.JobId `
		-DisplayOutputType StandardError
	
	Write-Host "Standard Output" -BackgroundColor Green
	Get-AzureRmHDInsightJobOutput `
		-ResourceGroupName $resourceGroupName `
		-ClusterName $hdinsightClusterName `
		-DefaultStorageAccountName $defaultStorageAccountName `
		-DefaultStorageAccountKey $defaultStorageAccountKey `
		-DefaultContainer $defaultBlobContainerName `
		-HttpCredential $httpCredential `
		-JobId $sqoopJob.JobId `
		-DisplayOutputType StandardOutput
	
	#endregion



[azure-management-portal]: https://portal.azure.com/

[hdinsight-versions]: hdinsight-component-versioning.md
[hdinsight-provision]: hdinsight-provision-clusters.md
[hdinsight-get-started]: hdinsight-hadoop-linux-tutorial-get-started.md
[hdinsight-storage]: ../hdinsight-hadoop-use-blob-storage.md
[hdinsight-analyze-flight-data]: hdinsight-analyze-flight-delay-data.md
[hdinsight-use-oozie]: hdinsight-use-oozie.md
[hdinsight-upload-data]: hdinsight-upload-data.md
[hdinsight-submit-jobs]: hdinsight-submit-hadoop-jobs-programmatically.md

[sqldatabase-get-started]: ../sql-database-get-started.md
[sqldatabase-create-configue]: ../sql-database-create-configure.md

[powershell-start]: http://technet.microsoft.com/library/hh847889.aspx
[powershell-install]: powershell-install-configure.md
[powershell-script]: http://technet.microsoft.com/library/ee176949.aspx

[sqoop-user-guide-1.4.4]: https://sqoop.apache.org/docs/1.4.4/SqoopUserGuide.html

<!---HONumber=AcomDC_0309_2016-->