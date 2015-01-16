<properties urlDisplayName="Use Hadoo Sqoop in HDInsight" pageTitle="Usare Sqoop di Hadoop in HDInsight | Azure" metaKeywords="" description="Informazioni su come usare Azure PowerShell da una workstation per eseguire importazioni ed esportazioni con Sqoop tra un cluster Hadoop e un database SQL di Azure." umbracoNaviHide="0" disqusComments="1" editor="cgronlun" manager="paulettm" services="hdinsight" documentationCenter="" title="Use Hadoop Sqoop in HDInsight" authors="jgao" />

<tags ms.service="hdinsight" ms.workload="big-data" ms.tgt_pltfrm="na" ms.devlang="na" ms.topic="article" ms.date="11/12/2014" ms.author="jgao" />

# Usare Sqoop con Hadoop in HDInsight
 
Informazioni su come usare Azure PowerShell e .NET SDK per HDInsight da una workstation per eseguire importazioni ed esportazioni con Sqoop tra un cluster HDInsight e un database SQL di Azure o un database di SQL Server.

##Contenuto dell'articolo

- [Informazioni su Sqoop](#whatissqoop)
- [Prerequisiti](#prerequisites)
- [Informazioni sullo scenario dell'esercitazione](#scenario)
- [Preparazione dell'esercitazione](#prepare)
- [Uso di PowerShell per eseguire l'esportazione con Sqoop](#export)
- [Uso di SDK per HDInsight per eseguire l'esportazione con Sqoop](#export-sdk)
- [Uso di PowerShell per eseguire l'importazione con Sqoop](#import)
- [Passaggi successivi](#nextsteps)


## <a id="whatissqoop"></a> Informazioni su Sqoop

Benché Hadoop rappresenti una scelta ottimale per l'elaborazione di dati non strutturati e semistrutturati, ad esempio log e file, potrebbe essere tuttavia necessario elaborare anche dati strutturati archiviati in database relazionali.

[Sqoop][sqoop-user-guide-1.4.4] è uno strumento progettato per il trasferimento di dati tra cluster Hadoop e database relazionali. Può essere usato per importare dati in HDFS (Hadoop Distributed File System) da un sistema di gestione di database relazionali (RDBMS), ad esempio SQL, MySQL oppure Oracle, trasformare i dati in Hadoop con MapReduce o Hive e quindi esportarli nuovamente in un sistema RDBMS. In questa esercitazione verrà usato un database SQL per il database relazionale.

Per informazioni sulle versioni di Sqoop supportate nel cluster HDInsight, vedere [Novità delle versioni cluster di Hadoop incluse in HDInsight][hdinsight-versions].




##<a id="prerequisites"></a>Prerequisiti

Prima di iniziare questa esercitazione, è necessario disporre di quanto segue:

- **Workstation**: un computer in cui sia stato installato e configurato Azure PowerShell. Per istruzioni, vedere [Come installare e configurare Azure PowerShell][powershell-install]. Per eseguire script di PowerShell, è necessario eseguire Azure PowerShell come amministratore e impostare i criteri di esecuzione su *RemoteSigned*. Vedere la pagina relativa all'[esecuzione di script di Windows PowerShell][powershell-script].

- **Cluster HDInsight di Azure**. Per istruzioni sul provisioning del cluster, vedere [Introduzione all'uso di HDInsight][hdinsight-get-started] o [Provisioning di cluster HDInsight][hdinsight-provision]. Per completare l'esercitazione sono necessari i dati seguenti:

	<table border="1">
	<tr><th>Proprietà del cluster</th><th>Nome variabile di PowerShell</th><th>Valore</th><th>Descrizione</th></tr>
	<tr><td>Nome del cluster HDInsight</td><td>$clusterName</td><td></td><td>Corrisponde al nome del cluster HDInsight.</td></tr>
	<tr><td>Nome dell'account di archiviazione di Azure</td><td>$storageAccountName</td><td></td><td>Un account di archiviazione di Azure disponibile per il cluster HDInsight. Ai fini di questa esercitazione, usare l'account di archiviazione predefinito, specificato durante il processo di provisioning del cluster.</td></tr>
	<tr><td>Nome del contenitore BLOB di Azure</td><td>$containerName</td><td></td><td>Per questo esempio, usare il contenitore di archiviazione BLOB di Azure usato come file system predefinito del cluster HDInsight. Per impostazione predefinita, il nome del contenitore corrisponde al nome del cluster HDInsight.</td></tr>
	</table>

- **Database SQL di Azure**. È necessario configurare una regola del firewall per il server di database SQL per consentire l'accesso dalla workstation. Per istruzioni sulla creazione di un database SQL e sulla configurazione del firewall, vedere l'[introduzione all'uso del database SQL di Azure][sqldatabase-get-started]. Questo articolo contiene uno script PowerShell per la creazione della tabella di database SQL necessaria per l'esercitazione. 

	<table border="1">
	<tr><th>Proprietà del database SQL</th><th>Nome variabile di PowerShell</th><th>Valore</th><th>Descrizione</th></tr>
	<tr><td>Nome del server di database SQL</td><td>$sqlDatabaseServer</td><td></td><td>Il server di database SQL nel quale Sqoop esporterà o dal quale importerà i dati. </td></tr>
	<tr><td>Nome dell'account di accesso al database SQL</td><td>$sqlDatabaseLogin</td><td></td><td>Nome dell'account di accesso al database SQL.</td></tr>
	<tr><td>Password di accesso al database SQL</td><td>$sqlDatabasePassword</td><td></td><td>Password dell'account di accesso al database SQL.</td></tr>
	<tr><td>Nome del database SQL</td><td>$sqlDatabaseName</td><td></td><td>Il database SQL di Azure nel quale Sqoop esporterà o dal quale importerà i dati. </td></tr>
	</table>

	> [WACOM.NOTE] Per impostazione predefinita, un database SQL di Azure permette le connessioni da servizi di Azure come Azure HDInsight. Se questa impostazione del firewall è disabilitata, sarà necessario abilitarla nel portale di gestione di Azure. Per istruzioni sulla creazione di un database SQL e sulla configurazione di regole del firewall, vedere [Come creare e configurare database SQL][sqldatabase-create-configue]. 

* **SQL Server**. Se il cluster HDInsight si trova sulla stessa rete virtuale di Azure di SQL Server, è possibile usare la procedura descritta in questo articolo per importare ed esportare i dati in un database di SQL Server. Per altre informazioni, vedere gli articoli seguenti.

	> [WACOM.NOTE] > Azure HDInsight supporta solo reti virtuali basate sulla posizione e attualmente non funziona con le reti virtuali basate su gruppi di affinità.

	* Per **creare e configurare una rete virtuale**, vedere [Attività di configurazione della rete virtuale](http://msdn.microsoft.com/it-it/library/azure/jj156206.aspx).

		* Quando si usa SQL Server **nel proprio data center**, è necessario configurare la rete virtuale come *da sito a sito* o *da punto a sito*.

			> [WACOM.NOTE] Per le reti virtuali **da punto a sito**, è necessario che su SQL Server venga eseguita l'applicazione di configurazione del client VPN, disponibile nel **dashboard** di configurazione della rete virtuale di Azure.

		* Quando si usa SQL Server in una **macchina virtuale di Azure**, sarà possibile usare qualsiasi configurazione di rete virtuale a condizione che la macchina virtuale sulla quale è ospitato SQL Server sia un membro della stessa rete virtuale di HDInsight.

	* Per **effettuare il provisioning di un cluster HDInsight in una rete virtuale**, vedere [Provisioning di cluster Hadoop in HDInsight con opzioni personalizzate].(/it-it/documentation/articles/hdinsight-provision-clusters/)

	> [WACOM.NOTE] SQL Server deve sempre consentire l'autenticazione SQL. Per la procedura descritta in questo articolo è necessario usare un account di accesso SQL.

	<table border="1">
	<tr><th>Proprietà del database SQL</th><th>Nome variabile di PowerShell</th><th>Valore</th><th>Descrizione</th></tr>
	<tr><td>Nome SQL Server</td><td>$sqlDatabaseServer</td><td></td><td>SQL Server nel quale Sqoop esporterà o dal quale importerà i dati. </td></tr>
	<tr><td>Nome di accesso a SQL Server</td><td>$sqlDatabaseLogin</td><td></td><td>Nome di accesso SQL.</td></tr>
	<tr><td>Password di accesso SQL</td><td>$sqlDatabasePassword</td><td></td><td>Password di accesso SQL.</td></tr>
	<tr><td>Nome del database di SQL Server</td><td>$sqlDatabaseName</td><td></td><td>Il database nel quale Sqoop esporterà o dal quale importerà i dati. </td></tr>
	</table>


> [WACOM.NOTE] L'inserimento dei valori nelle tabelle precedenti  potrà essere utile per completare questa esercitazione.

## <a id="scenario"></a>Informazioni sullo scenario
Il cluster HDInsight include alcuni dati di esempio. Verranno usati i due seguenti:

- Un file di log log4j presente in */example/data/sample.log*. Dal file verranno estratti i log seguenti:

		2012-02-03 18:35:34 SampleClass6 [INFO] everything normal for id 577725851
		2012-02-03 18:35:34 SampleClass4 [FATAL] system problem at id 1991281254
		2012-02-03 18:35:34 SampleClass3 [DEBUG] detail for id 1304807656
		...

- Una tabella Hive denominata *hivesampletable* che fa riferimento al file di dati presente in */hive/warehouse/hivesampletable*. La tabella contiene alcuni dati relativi al dispositivo mobile. Lo schema della tabella Hive è il seguente:

	<table border="1">
	<tr><th>Campo</th><th>Tipo di dati</th></tr>
	<tr><td>clientid</td><td>stringa</td></tr>
	<tr><td>querytime</td><td>stringa</td></tr>
	<tr><td>market</td><td>stringa</td></tr>
	<tr><td>deviceplatform</td><td>stringa</td></tr>
	<tr><td>devicemake</td><td>stringa</td></tr>
	<tr><td>devicemodel</td><td>stringa</td></tr>
	<tr><td>state</td><td>stringa</td></tr>
	<tr><td>country</td><td>stringa</td></tr>
	<tr><td>querydwelltime</td><td>double</td></tr>
	<tr><td>sessionid</td><td>bigint</td></tr>
	<tr><td>sessionpagevieworder</td><td>bigint</td></tr>
	</table>

Sarà necessario innanzitutto esportare sample.log e hivesampletable nel database SQL o in SQL Server, quindi importare di nuovo in HDInsight la tabella contenente i dati del dispositivo mobile usando il percorso seguente:

	/tutorials/usesqoop/importeddata

###Informazioni sull'archiviazione in HDInsight

HDInsight usa l'archiviazione BLOB di Azure per l'archiviazione dei dati.  Questa risorsa è denominata *WASB* o *Archiviazione BLOB di Azure*. WASB è l'implementazione Microsoft di HDFS nell'archiviazione BLOB di Azure. Per altre informazioni, vedere [Usare l'archiviazione BLOB di Azure con HDInsight][hdinsight-storage]. 

Quando si effettua il provisioning di un cluster HDInsight, vengono designati come file system predefinito un account di archiviazione di Azure e un contenitore di archiviazione BLOB specifico di tale account, proprio come in HDFS. Durante il processo di provisioning è possibile aggiungere a tale account di archiviazione ulteriori account, dalla stessa sottoscrizione di Azure o da sottoscrizioni diverse. Per istruzioni sull'aggiunta di ulteriori account di archiviazione, vedere [Provisioning di cluster HDInsight][hdinsight-provision]. Per semplificare lo script di PowerShell usato in questa esercitazione, tutti i file vengono archiviati nel contenitore del file system predefinito, che si trova in */tutorials/usesqoop*. Per impostazione predefinita, il nome di questo contenitore corrisponde al nome del cluster HDInsight. 
La sintassi WASB è la seguente:

	wasb[s]://<ContainerName>@<StorageAccountName>.blob.core.windows.net/<path>/<filename>

> [WACOM.NOTE] Nel cluster HDInsight versione 3.0 è supportata solo la sintassi *wasb://*. La sintassi *asv://* precedente è supportata nei cluster HDInsight 2.1 e 1.6, ma non nei cluster HDInsight 3.0 né nelle versioni successive.

> [WACOM.NOTE] Il percorso WASB è un percorso virtuale.  Per altre informazioni, vedere [Usare l'archiviazione BLOB di Azure con HDInsight][hdinsight-storage]. 

È possibile accedere da HDInsight a un file archiviato nel contenitore del file system predefinito usando uno degli URI seguenti (usare come esempio sample.log):

	wasb://mycontainer@mystorageaccount.blob.core.windows.net/example/data/sample.log
	wasb:///example/data/sample.log
	/example/data/sample.log

Per accedere al file direttamente dall'account di archiviazione, il nome del BLOB per questo file è:

	example/data/sample.log


##<a id="prepare"></a>Preparazione dell'esercitazione

Verranno create due tabelle nel database SQL o in SQL Server, usate dall'esportazione di Sqoop più avanti nell'esercitazione. Sarà inoltre necessario pre-elaborare i file sample.log prima dell'elaborazione con Sqoop.

###Creare una tabella SQL

**Per il database SQL di Azure**

1. Aprire Windows PowerShell ISE. A questo scopo, nella schermata di avvio di Windows 8 digitare **PowerShell_ISE** e quindi fare clic su **Windows PowerShell ISE**. Vedere la pagina dedicata all'[avvio di Windows PowerShell in Windows 8 e Windows][powershell-start]).

2. Copiare lo script seguente nel riquadro di script e impostare le prime quattro variabili:
		
		#SQL database variables
		$sqlDatabaseServer = "<SQLDatabaseServerName>" 
		$sqlDatabaseLogin = "<SQLDatabaseUsername>"
		$sqlDatabasePassword = "<SQLDatabasePassword>"
		$sqlDatabaseName = "<SQLDatabaseName>" 

		$sqlDatabaseConnectionString = "Data Source=$sqlDatabaseServer.database.windows.net;Initial Catalog=$sqlDatabaseName;User ID=$sqlDatabaseLogin;Password=$sqlDatabasePassword;Encrypt=true;Trusted_Connection=false;"

	Per altre descrizioni delle variabili, vedere la sezione [Prerequisiti](#prerequisites) di questa esercitazione. 

3. Aggiungere lo script seguente nel riquadro di script. Si tratta delle istruzioni SQL che definiscono le due tabelle e i relativi indici cluster.  Per il database SQL è necessario un indice cluster.

		# SQL query strings for creating tables and clustered indexes
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

4. Per eseguire i comandi SQL, aggiungere lo script seguente nel riquadro di script:

		Write-Host "Connect to the SQL Database ..." -ForegroundColor Green
		$conn = New-Object System.Data.SqlClient.SqlConnection
		$conn.ConnectionString = $sqlDatabaseConnectionString
		$conn.Open()
		
		Write-Host "Create log4j table and clustered index ..." -ForegroundColor Green
		$cmd = New-Object System.Data.SqlClient.SqlCommand
		$cmd.Connection = $conn
		$cmd.CommandText = $cmdCreateLog4jTable
		$ret = $cmd.ExecuteNonQuery()
		$cmd.CommandText = $cmdCreateLog4jClusteredIndex
		$cmd.ExecuteNonQuery()
		
		Write-Host "Create log4j table and clustered index ..." -ForegroundColor Green
		$cmd.CommandText = $cmdCreateMobileTable
		$cmd.ExecuteNonQuery()
		$cmd.CommandText = $cmdCreateMobileDataClusteredIndex
		$cmd.ExecuteNonQuery()
		
		Write-Host "Close connection ..." -ForegroundColor Green		
		$conn.close()
		
		Write-Host "Done" -ForegroundColor Green
	
5. Fare clic su **Esegui script** o premere **F5** per eseguire lo script. 
6. Usare il [portale di gestione di Azure][azure-management-portal] per esaminare le tabelle e gli indici cluster.

**Per SQL Server**

1. Aprire **SQL Server Management Studio** e connettersi a SQL Server.

2. Creare un nuovo database denominato **sqoopdb**.

3. Selezionare il database **sqoopdb** e quindi selezionare **Nuova query** dalla barra multifunzione nella parte superiore di SQL Server Management Studio.

4. Immettere quanto segue nel riquadro della query:

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

5. Usare il tasto funzione **F5** oppure selezionare **. Esegui** sulla barra multifunzione per eseguire la query. Il messaggio seguente verrà visualizzato sotto la query.

		Command(s) completed successfully.

6. Chiudere SQL Server Management Studio.

###Generare i dati

In questa esercitazione, verranno esportati nel database SQL un file di log log4j (un file con valori delimitati) e una tabella Hive.  Il file con valori delimitati è */example/data/sample.log*. Alcuni esempi di file di log log4j sono stati visualizzati in precedenza in questa esercitazione. Nel file di log sono presenti alcune righe vuote e righe simili alle seguenti:

	java.lang.Exception: 2012-02-03 20:11:35 SampleClass2 [FATAL] unrecoverable system problem at id 609774657
		at com.osa.mocklogger.MockLogger$2.run(MockLogger.java:83)

Ciò va bene per altri esempi in cui vengono usati questi dati, ma è necessario rimuovere queste eccezioni prima di poterli importare nel database SQL o in SQL Server. In presenza di una stringa vuota o di una riga contenente un numero di elementi inferiore al numero dei campi definiti nella tabella di database SQL, non sarà possibile eseguire l'esportazione con Sqoop. La tabella log4jlogs comprende 7 campi tipo stringa.

**Per pre-elaborare il file sample.log**

1. Aprire Windows PowerShell ISE.
2. Nel riquadro inferiore eseguire il comando seguente per connettersi alla sottoscrizione di Azure:

		Add-AzureAccount

	Verrà richiesto di immettere le credenziali dell'account Azure. Questo metodo che prevede l'aggiunta di una connessione alla sottoscrizione scade dopo 12 ore, di conseguenza sarà necessario accedere nuovamente. 

	> [WACOM.NOTE] Se si dispone di più sottoscrizioni di Azure e non si vuole usare quella predefinita, eseguire il cmdlet <strong>Select-AzureSubscription</strong> per selezionare la sottoscrizione corrente.

3. Copiare lo script seguente nel riquadro di script e impostare le prime due variabili:
		
		$storageAccountName = "<AzureStorageAccountName>"
		$containerName = "<BlobContainerName>"
		
		$sourceBlobName = "example/data/sample.log"
		$destBlobName = "tutorials/usesqoop/data/sample.log"

	Per altre descrizioni delle variabili, vedere la sezione [Prerequisiti](#prerequisites) di questa esercitazione. 
 
4. Aggiungere lo script seguente nel riquadro di script:

		# Define the connection string
		$storageAccountKey = get-azurestoragekey $storageAccountName | %{$_.Primary}
		$storageConnectionString = "DefaultEndpointsProtocol=https;AccountName=$storageAccountName;AccountKey=$storageAccountKey"
		
		# Create block blob objects referencing the source and destination blob.
		$storageAccount = [Microsoft.WindowsAzure.Storage.CloudStorageAccount]::Parse($storageConnectionString)
		$storageClient = $storageAccount.CreateCloudBlobClient();
		$storageContainer = $storageClient.GetContainerReference($containerName)
		$sourceBlob = $storageContainer.GetBlockBlobReference($sourceBlobName)
		$destBlob = $storageContainer.GetBlockBlobReference($destBlobName)
		
		# Define a MemoryStream and a StreamReader for reading from the source file
		$stream = New-Object System.IO.MemoryStream
		$stream = $sourceBlob.OpenRead()
		$sReader = New-Object System.IO.StreamReader($stream)
		
		# Define a MemoryStream and a StreamWriter for writing into the destination file
		$memStream = New-Object System.IO.MemoryStream
		$writeStream = New-Object System.IO.StreamWriter $memStream
		
		# process the source blob
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

5. Fare clic su **Esegui script** o premere **F5** per eseguire lo script.  
6. Per esaminare il file di dati modificato, è possibile usare il portale di gestione di Azure o uno strumento di Azure Storage Explorer oppure Azure PowerShell.  [In Introduzione all'uso di HDInsight][hdinsight-get-started] è presente un esempio di codice relativo all'uso di PowerShell per scaricare un file e visualizzarne il contenuto.


##<a id="export"></a>Uso di PowerShell per eseguire l'esportazione con Sqoop

In questa sezione, verrà usato Azure PowerShell per eseguire un comando di esportazione di Sqoop per esportare una tabella Hive e un file di dati in un database SQL di Azure o in SQL Server. La sezione successiva offre un esempio di .NET per HDInsight.

> [WACOM.NOTE] A parte le informazioni sulla stringa di connessione, la procedura descritta in questa sezione dovrebbe funzionare per il database SQL di Azure oppure SQL Server. La procedura è stata verificata con la configurazione seguente:
> 
> * **Configurazione da punto a sito della rete virtuale di Azure**: una rete virtuale che connette il cluster HDInsight a un SQL Server in un data center privato. Per altre informazioni, vedere [Configurare una VPN da punto a sito nel portale di gestione](http://msdn.microsoft.com/it-it/library/azure/dn133792.aspx).
> * **Azure HDInsight 3.1**: vedere [Provisioning di cluster Hadoop in HDInsight con opzioni personalizzate](/it-it/documentation/articles/hdinsight-provision-clusters/) per informazioni sulla creazione di un cluster in una rete virtuale.
> * **SQL Server 2014**: configurato per consentire l'autenticazione SQL e l'esecuzione del pacchetto di configurazione del client VPN per eseguire la connessione sicura alla rete virtuale.

**Per esportare il file di log log4j**

1. Aprire Windows PowerShell ISE.
2. Nel riquadro inferiore eseguire il comando seguente per connettersi alla sottoscrizione di Azure:

		Add-AzureAccount

	Verrà richiesto di immettere le credenziali dell'account Azure.

3. Copiare lo script seguente nel riquadro di script e impostare le prime sette variabili:

		# Define the cluster variables
		$clusterName = "<HDInsightClusterName>"
		$storageAccountName = "<AzureStorageAccount>"
		$containerName = "<BlobStorageContainerName>"
		
		# Define the SQL database variables
		$sqlDatabaseServerName = "<SQLDatabaseServerName>"
		$sqlDatabaseLogin = "<SQLDatabaseUsername>"
		$sqlDatabasePassword = "<SQLDatabasePassword>"
		$databaseName = "<SQLDatabaseName>"

		$tableName_log4j = "log4jlogs"
		
		# Connection string for Azure SQL Database.
		# Comment if using SQL Server
		$connectionString = "jdbc:sqlserver://$sqlDatabaseServerName.database.windows.net;user=$sqlDatabaseLogin@$sqlDatabaseServerName;password=$sqlDatabasePassword;database=$databaseName"
		# Connection string for SQL Server.
		# Uncomment if using SQL Server.
		#$connectionString = "jdbc:sqlserver://$sqlDatabaseServerName;user=$sqlDatabaseLogin;password=$sqlDatabasePassword;database=$databaseName"
		
		$exportDir_log4j = "/tutorials/usesqoop/data"
	
	Per altre descrizioni delle variabili, vedere la sezione [Prerequisiti](#prerequisites) di questa esercitazione. 

	Si noti che per $exportDir_log4j non è stato specificato il nome file per il file sample.log. Sqoop esporterà i dati da tutti i file in quella cartella.

4. Aggiungere lo script seguente nel riquadro di script:

		# Submit a Sqoop job
		$sqoopDef = New-AzureHDInsightSqoopJobDefinition -Command "export --connect $connectionString --table $tableName_log4j --export-dir $exportDir_log4j --input-fields-terminated-by \0x20 -m 1"
		$sqoopJob = Start-AzureHDInsightJob -Cluster $clusterName -JobDefinition $sqoopDef #-Debug -Verbose
		Wait-AzureHDInsightJob -WaitTimeoutInSeconds 3600 -Job $sqoopJob
		
		Write-Host "Standard Error" -BackgroundColor Green
		Get-AzureHDInsightJobOutput -Cluster $clusterName -JobId $sqoopJob.JobId -StandardError
		Write-Host "Standard Output" -BackgroundColor Green
		Get-AzureHDInsightJobOutput -Cluster $clusterName -JobId $sqoopJob.JobId -StandardOutput

	Il delimitatore di campo è **\0x20**, che corrisponde a uno spazio. Il delimitatore è definito nello script PowerShell di pre-elaborazione del file sample.log. Per avere informazioni su **-m 1**, vedere la [Guida dell'utente di Sqoop][sqoop-user-guide-1.4.4].

5. Fare clic su **Esegui script** o premere **F5** per eseguire lo script.  
6. Usare il [portale di gestione di Azure][azure-management-portal] per esaminare i dati esportati.

**Per esportare la tabella Hive hivesampletable**

1. Aprire Windows PowerShell ISE.
2. Nel riquadro inferiore eseguire il comando seguente per connettersi alla sottoscrizione di Azure:

		Add-AzureAccount

	Verrà richiesto di immettere le credenziali dell'account Azure.

3. Copiare lo script seguente nel riquadro di script e impostare le prime sette variabili:

		# Define the cluster variables
		$clusterName = "<HDInsightClusterName>"
		$storageAccountName = "<AzureStorageAccount>"
		$containerName = "<BlobStorageContainerName>"
		
		# Define the SQL database variables
		$sqlDatabaseServerName = "<SQLDatabaseServerName>"
		$sqlDatabaseLogin = "<SQLDatabaseUsername>"
		$sqlDatabasePassword = "SQLDatabasePassword>"
		$databaseName = "SQLDatabaseName"

		$tableName_mobile = "mobiledata"
		
		# Connection string for Azure SQL Database.
		# Comment if using SQL Server
		$connectionString = "jdbc:sqlserver://$sqlDatabaseServerName.database.windows.net;user=$sqlDatabaseLogin@$sqlDatabaseServerName;password=$sqlDatabasePassword;database=$databaseName"
		# Connection string for SQL Server.
		# Uncomment if using SQL Server
		#$connectionString = "jdbc:sqlserver://$sqlDatabaseServerName;user=$sqlDatabaseLogin;password=$sqlDatabasePassword;database=$databaseName"
		
		$exportDir_mobile = "/hive/warehouse/hivesampletable"
	
	Per altre descrizioni delle variabili, vedere la sezione [Prerequisiti](#prerequisites) di questa esercitazione. 

4. Aggiungere lo script seguente nel riquadro di script:
		
		$sqoopDef = New-AzureHDInsightSqoopJobDefinition -Command "export --connect $connectionString --table $tableName_mobile --export-dir $exportDir_mobile --fields-terminated-by \t -m 1"
		
		
		$sqoopJob = Start-AzureHDInsightJob -Cluster $clusterName -JobDefinition $sqoopDef #-Debug -Verbose
		Wait-AzureHDInsightJob -WaitTimeoutInSeconds 3600 -Job $sqoopJob
		
		Write-Host "Standard Error" -BackgroundColor Green
		Get-AzureHDInsightJobOutput -Cluster $clusterName -JobId $sqoopJob.JobId -StandardError
		Write-Host "Standard Output" -BackgroundColor Green
		Get-AzureHDInsightJobOutput -Cluster $clusterName -JobId $sqoopJob.JobId -StandardOutput

5. Fare clic su **Esegui script** o premere **F5** per eseguire lo script.   
6. Usare il [portale di gestione di Azure][azure-management-portal] per esaminare i dati esportati.



##<a id="export-sdk"></a>Uso di HDInsight .NET SDK per eseguire l'esportazione con Sqoop

Il seguente è un esempio in C# che usa .NET SDK per HDInsight per eseguire l'esportazione con Sqoop. Per informazioni generali sull'uso di .NET SDK per HDInsight, vedere [Inviare processi Hadoop a livello di codice][hdinsight-submit-jobs].


	using System;
	using System.Collections.Generic;
	using System.Linq;
	using System.Text;
	using System.Threading.Tasks;
	using System.IO;
	using System.Threading;
	using System.Security.Cryptography.X509Certificates;
	using Microsoft.WindowsAzure.Management.HDInsight;
	using Microsoft.Hadoop.Client;
	
	namespace sqoopSDKSample
	{
	    class Program
	    {
	        static void Main(string[] args)
	        {
	            // Set the variables
	            string subscriptionID = "<AzureSubscriptionID>";
	            string clusterName = "<HDInsightClusterName>";
	            string certFriendlyName = "<AzureCertificateFriendlyName>";
	            string sqlDatabaseServerName = "<SQLDatabaseServerName>";
	            string sqlDatabaseLogin = "<SQLDatabaseLogin>";
	            string sqlDatabaseLoginPassword = "<SQLDatabaseLoginPassword>";
	            string sqlDatabaseDatabaseName = "hdisqoop";
	            string sqlDatabaseTableName = "log4jlogs";
	
	            cmdExport = @"export";
				// Connection string for using Azure SQL Database.
				// Comment if using SQL Server
	            cmdExport = cmdExport + @" --connect jdbc:sqlserver://" + sqlDatabaseServerName + ".database.windows.net;user=" + sqlDatabaseLogin + "@" + sqlDatabaseServerName + ";password=" + sqlDatabaseLoginPassword + ";database=" + sqlDatabaseDatabaseName;
				// Connection string for using SQL Server.
				// Uncomment if using SQL Server
				//cmdExport = cmdExport + @" --connect jdbc:sqlserver://" + sqlDatabaseServerName + ";user=" + sqlDatabaseLogin + ";password=" + sqlDatabaseLoginPassword + ";database=" + sqlDatabaseDatabaseName;
	            cmdExport = cmdExport + @" --table " + sqlDatabaseTableName;
	            cmdExport = cmdExport + @" --export-dir /tutorials/usesqoop/data";
	            cmdExport = cmdExport + @" --input-fields-terminated-by \0x20 -m 1";
	
	            SqoopJobCreateParameters sqoopJobDefinition = new SqoopJobCreateParameters()
	            {
	                Command = cmdExport,
	                StatusFolder = "/tutorials/usesqoop/jobStatus"
	            };
	
	            // Get the certificate object from certificate store using the friendly name to identify it
	            X509Store store = new X509Store();
	            store.Open(OpenFlags.ReadOnly);
	            X509Certificate2 cert = store.Certificates.Cast<X509Certificate2>().First(item => item.FriendlyName == certFriendlyName);
	            JobSubmissionCertificateCredential creds = new JobSubmissionCertificateCredential(new Guid(subscriptionID), cert, clusterName);
	
	            // Submit the Hive job
	            var jobClient = JobSubmissionClientFactory.Connect(creds);
	            JobCreationResults jobResults = jobClient.CreateSqoopJob(sqoopJobDefinition);
	
	            // Wait for the job to complete
	            WaitForJobCompletion(jobResults, jobClient);
	
	            // Print the Hive job output
	            System.IO.Stream stream = jobClient.GetJobErrorLogs(jobResults.JobId);
	
	            StreamReader reader = new StreamReader(stream);
	            Console.WriteLine(reader.ReadToEnd());
	
	            Console.WriteLine("Press ENTER to continue.");
	            Console.ReadLine();
	        }
	
	        private static void WaitForJobCompletion(JobCreationResults jobResults, IJobSubmissionClient client)
	        {
	            JobDetails jobInProgress = client.GetJob(jobResults.JobId);
	            while (jobInProgress.StatusCode != JobStatusCode.Completed && jobInProgress.StatusCode != JobStatusCode.Failed)
	            {
	                jobInProgress = client.GetJob(jobInProgress.JobId);
	                Thread.Sleep(TimeSpan.FromSeconds(10));
	            }
	        }
	    }
	}

Per eseguire un file di script, è possibile sostituire:

	Command = cmdExport,

 con:

	File = "/tutorials/usesqoop/sqoopexport.txt",

Lo script deve trovarsi in WASB.




##<a id="import"></a>Uso di PowerShell per eseguire l'importazione con Sqoop

In questa sezione i log log4j (esportati nel database SQL) verranno importati nuovamente in HDInsight.

1. Aprire Windows PowerShell ISE.
2. Nel riquadro inferiore eseguire il comando seguente per connettersi alla sottoscrizione di Azure:

		Add-AzureAccount

	Verrà richiesto di immettere le credenziali dell'account Azure.

3. Copiare lo script seguente nel riquadro di script e impostare le prime sette variabili:

		# Define the cluster variables
		$clusterName = "<HDInsightClusterName>"
		$storageAccountName = "<AzureStorageAccount>"
		$containerName = "<BlobStorageContainerName>"
		
		# Define the SQL database variables
		$sqlDatabaseServerName = "<SQLDatabaseServerName>"
		$sqlDatabaseLogin = "<SQLDatabaseUsername>"
		$sqlDatabasePassword = "SQLDatabasePassword>"
		$databaseName = "SQLDatabaseName"

		$tableName_log4j = "log4jlogs"
		
		# Connection string for Azure SQL Database
		# Comment if using SQL Server
		$connectionString = "jdbc:sqlserver://$sqlDatabaseServerName.database.windows.net;user=$sqlDatabaseLogin@$sqlDatabaseServerName;password=$sqlDatabasePassword;database=$databaseName"
		# Connection string for SQL Server
		# Uncomment if using SQL Server
		#$connectionString = "jdbc:sqlserver://$sqlDatabaseServerName;user=$sqlDatabaseLogin;password=$sqlDatabasePassword;database=$databaseName"
		
		$tableName_mobile = "mobiledata"
		$targetDir_mobile = "/tutorials/usesqoop/importeddata/"
	
	Per altre descrizioni delle variabili, vedere la sezione [Prerequisiti](#prerequisites) di questa esercitazione. 

4. Aggiungere lo script seguente nel riquadro di script:
	
		$sqoopDef = New-AzureHDInsightSqoopJobDefinition -Command "import --connect $connectionString --table $tableName_mobile --target-dir $targetDir_mobile --fields-terminated-by \t --lines-terminated-by \n -m 1"
		
		$sqoopJob = Start-AzureHDInsightJob -Cluster $clusterName -JobDefinition $sqoopDef #-Debug -Verbose
		Wait-AzureHDInsightJob -WaitTimeoutInSeconds 3600 -Job $sqoopJob
		
		Write-Host "Standard Error" -BackgroundColor Green
		Get-AzureHDInsightJobOutput -Cluster $clusterName -JobId $sqoopJob.JobId -StandardError
		Write-Host "Standard Output" -BackgroundColor Green
		Get-AzureHDInsightJobOutput -Cluster $clusterName -JobId $sqoopJob.JobId -StandardOutput

5. Fare clic su **Esegui script** o premere **F5** per eseguire lo script. 
6. Per esaminare il file di dati modificato, è possibile usare il portale di gestione di Azure o uno strumento di Azure Storage Explorer oppure Azure PowerShell.  [In Introduzione all'uso di HDInsight][hdinsight-get-started] è presente un esempio di codice relativo all'uso di PowerShell per scaricare un file e visualizzarne il contenuto.

##<a id="nextsteps"></a>Passaggi successivi

In questa esercitazione si è appreso come usare Sqoop. Per altre informazioni, vedere:

- [Usare Oozie con HDInsight][hdinsight-use-oozie]: usare un'azione di Sqoop nel flusso di lavoro di Oozie.
- [Analizzare i dati sui ritardi dei voli usando HDInsight][hdinsight-analyze-flight-data]: uso di Hive nell'analisi dei dati sui ritardi dei voli e quindi di Sqoop per esportare dati nel database SQL.
- [Caricare i dati in HDInsight][hdinsight-upload-data]: per altri metodi per il caricamento di file in HDInsight o nell'archiviazione BLOB di Azure.


 

[azure-management-portal]: https://manage.windowsazure.com/

[hdinsight-versions]:  ../hdinsight-component-versioning/
[hdinsight-provision]: ../hdinsight-provision-clusters/
[hdinsight-get-started]: ../hdinsight-get-started/
[hdinsight-storage]: ../hdinsight-use-blob-storage/
[hdinsight-analyze-flight-data]: ../hdinsight-analyze-flight-delay-data/
[hdinsight-use-oozie]: ../hdinsight-use-oozie/
[hdinsight-upload-data]: ../hdinsight-upload-data/
[hdinsight-submit-jobs]: ../hdinsight-submit-hadoop-jobs-programmatically/

[sqldatabase-get-started]: ../sql-database-get-started/
[sqldatabase-create-configue]: ../sql-database-create-configure/

[powershell-start]: http://technet.microsoft.com/it-it/library/hh847889.aspx
[powershell-install]: ../install-configure-powershell
[powershell-script]: http://technet.microsoft.com/it-it/library/ee176949.aspx

[sqoop-user-guide-1.4.4]: https://sqoop.apache.org/docs/1.4.4/SqoopUserGuide.html

<!--HONumber=35.1-->
