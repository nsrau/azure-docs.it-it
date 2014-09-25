<properties linkid="manage-services-hdinsight-use-sqoop" urlDisplayName="Use Sqoop with HDInsight Samples" pageTitle="Use Sqoop with HDInsight | Azure" metaKeywords="" description="Learn how to use Azure PowerShell from a workstation to run Sqoop import and export between an HDInsight cluster and an Azure SQL database." umbracoNaviHide="0" disqusComments="1" editor="cgronlun" manager="paulettm" services="hdinsight" documentationCenter="" title="Use Sqoop with HDInsight" authors="jgao" />

Utilizzo di Sqoop con HDInsight.
================================

Informazioni su come utilizzare Azure PowerShell e .NET SDK per HDInsight da una workstation per eseguire importazioni ed esportazioni con Sqoop tra un cluster HDInsight e un database SQL di Azure.

**Tempo previsto per il completamento:** 30 minuti

Contenuto dell'articolo
-----------------------

-   [Informazioni su Sqoop](#whatissqoop)
-   [Prerequisiti](#prerequisites)
-   [Informazioni sullo scenario dell'esercitazione](#scenario)
-   [Preparazione dell'esercitazione](#prepare)
-   [Utilizzo di PowerShell per eseguire l'esportazione con Sqoop](#export)
-   [Utilizzo di SDK per HDInsight per eseguire l'esportazione con Sqoop](#export-sdk)
-   [Utilizzo di PowerShell per eseguire l'importazione con Sqoop](#import)
-   [Passaggi successivi](#nextsteps)

Informazioni su Sqoop
---------------------

Benché Hadoop rappresenti una scelta ottimale per l'elaborazione di dati non strutturati e semistrutturati, ad esempio log e file, potrebbe essere tuttavia necessario elaborare anche dati strutturati archiviati in database relazionali.

[Sqoop](https://sqoop.apache.org/docs/1.4.4/SqoopUserGuide.html) è uno strumento progettato per il trasferimento di dati tra cluster Hadoop e database relazionali. Può essere utilizzato per importare dati in HDFS (Hadoop Distributed File System) da un sistema di gestione di database relazionali (RDBMS), ad esempio SQL, MySQL oppure Oracle, trasformare i dati in Hadoop con MapReduce o Hive e quindi esportarli nuovamente in un sistema RDBMS. In questa esercitazione verrà utilizzato un database SQL per il database relazionale.

Per informazioni sulle versioni di Sqoop supportate nei cluster HDInsight, vedere [Novità delle versioni cluster incluse con HDInsight](../hdinsight-component-versioning/).

Prerequisiti
------------

Prima di iniziare questa esercitazione, è necessario disporre di quanto segue:

-   **Una workstation** in cui sia stato installato e configurato Azure PowerShell. Per istruzioni, vedere [Come installare e configurare Azure PowerShell](../install-configure-powershell). Per eseguire script di PowerShell, è necessario eseguire Azure PowerShell come amministratore e impostare i criteri di esecuzione su *RemoteSigned*. Vedere la pagina relativa all'[esecuzione di script di Windows PowerShell](http://technet.microsoft.com/en-us/library/ee176949.aspx).

-   **Un cluster HDInsight di Azure**. Per istruzioni sul provisioning del cluster, vedere [Introduzione a HDInsight](../hdinsight-get-started/) o [Provisioning di cluster HDInsight](../hdinsight-provision-clusters/). Per completare l'esercitazione sono necessari i dati seguenti:

	<table data-morhtml="true" border="1">
  <tr data-morhtml="true"><th data-morhtml="true">Propriet&agrave; del cluster</th><th data-morhtml="true">Nome variabile di PowerShell</th><th data-morhtml="true">Valore</th><th data-morhtml="true">Descrizione</th></tr>
  <tr data-morhtml="true"><td data-morhtml="true">Nome del cluster HDInsight</td><td data-morhtml="true">$clusterName</td><td data-morhtml="true"></td><td data-morhtml="true">Corrisponde al nome del cluster HDInsight.</td></tr>
  <tr data-morhtml="true"><td data-morhtml="true">Nome dell'account di archiviazione di Azure</td><td data-morhtml="true">$storageAccountName</td><td data-morhtml="true"></td><td data-morhtml="true">Un account di archiviazione di Azure disponibile per il cluster HDInsight. Ai fini di questa esercitazione, utilizzare l'account di archiviazione predefinito, specificato durante il processo di provisioning del cluster.</td></tr>
  <tr data-morhtml="true"><td data-morhtml="true">Nome del contenitore BLOB di Azure</td><td data-morhtml="true">$containerName</td><td data-morhtml="true"></td><td data-morhtml="true">Per questo esempio, utilizzare il contenitore di archiviazione BLOB di Azure utilizzato come file system predefinito del cluster HDInsight. Per impostazione predefinita, il nome del contenitore corrisponde al nome del cluster HDInsight.</td></tr>
  </table>

-   **Un database SQL di Azure**. È necessario configurare una regola del firewall per il server di database SQL per consentire l'accesso dalla workstation. Per istruzioni sulla creazione di un database SQL e sulla configurazione del firewall, vedere l'[introduzione all'utilizzo del database SQL di Azure](../sql-database-get-started/). Questo articolo fornisce uno script PowerShell per la creazione della tabella di database SQL necessaria per questa esercitazione.

	<table data-morhtml="true" border="1">
  <tr data-morhtml="true"><th data-morhtml="true">Propriet&agrave; del database SQL</th><th data-morhtml="true">Nome variabile di PowerShell</th><th data-morhtml="true">Valore</th><th data-morhtml="true">Descrizione</th></tr>
  <tr data-morhtml="true"><td data-morhtml="true">Nome del server di database SQL</td><td data-morhtml="true">$sqlDatabaseServer</td><td data-morhtml="true"></td><td data-morhtml="true">Il server di database SQL nel quale Sqoop esporter&agrave; o dal quale importer&agrave; i dati. </td></tr>
  <tr data-morhtml="true"><td data-morhtml="true">Nome di accesso al database SQL</td><td data-morhtml="true">$sqlDatabaseLogin</td><td data-morhtml="true"></td><td data-morhtml="true">Il nome di accesso al database SQL.</td></tr>
  <tr data-morhtml="true"><td data-morhtml="true">Password di accesso al database SQL</td><td data-morhtml="true">$sqlDatabasePassword</td><td data-morhtml="true"></td><td data-morhtml="true">La password di accesso al database SQL.</td></tr>
  <tr data-morhtml="true"><td data-morhtml="true">Nome del database SQL</td><td data-morhtml="true">$sqlDatabaseName</td><td data-morhtml="true"></td><td data-morhtml="true">Il database SQL di Azure nel quale Sqoop esporter&agrave; o dal quale importer&agrave; i dati. </td></tr>
  </table>

    > [WACOM.NOTE] Per impostazione predefinita un database SQL di Azure consente connessioni da servizi di Azure, ad esempio Azure HDinsight. Se questa impostazione del firewall è disabilitata, sarà necessario abilitarla nel portale di gestione di Azure. Per istruzioni sulla creazione di un database SQL e sulla configurazione di regole del firewall, vedere [Come creare e configurare database SQL](../sql-database-create-configure/).

> [WACOM.NOTE] L'inserimento dei valori nelle tabelle potrà essere utile per completare questa esercitazione.

Informazioni sullo scenario
---------------------------

Il cluster HDInsight include alcuni dati di esempio. Verranno utilizzati i due seguenti:

-   Un file di log log4j presente in */example/data/sample.log*. Dal file verranno estratti i log seguenti:

          2012-02-03 18:35:34 SampleClass6 [INFO] everything normal for id 577725851
          2012-02-03 18:35:34 SampleClass4 [FATAL] system problem at id 1991281254
          2012-02-03 18:35:34 SampleClass3 [DEBUG] detail for id 1304807656
          ...

-   Una tabella Hive denominata *hivesampletable* che fa riferimento al file di dati presente in */hive/warehouse/hivesampletable*. La tabella contiene alcuni dati relativi al dispositivo mobile. Lo schema della tabella Hive è il seguente:

	<table data-morhtml="true" border="1">
  <tr data-morhtml="true"><th data-morhtml="true">Campo</th><th data-morhtml="true">Tipo di dati</th></tr>
  <tr data-morhtml="true"><td data-morhtml="true">clientid</td><td data-morhtml="true">string</td></tr>
  <tr data-morhtml="true"><td data-morhtml="true">querytime</td><td data-morhtml="true">string</td></tr>
  <tr data-morhtml="true"><td data-morhtml="true">market</td><td data-morhtml="true">string</td></tr>
  <tr data-morhtml="true"><td data-morhtml="true">deviceplatform</td><td data-morhtml="true">string</td></tr>
  <tr data-morhtml="true"><td data-morhtml="true">devicemake</td><td data-morhtml="true">string</td></tr>
  <tr data-morhtml="true"><td data-morhtml="true">devicemodel</td><td data-morhtml="true">string</td></tr>
  <tr data-morhtml="true"><td data-morhtml="true">state</td><td data-morhtml="true">string</td></tr>
  <tr data-morhtml="true"><td data-morhtml="true">country</td><td data-morhtml="true">string</td></tr>
  <tr data-morhtml="true"><td data-morhtml="true">querydwelltime</td><td data-morhtml="true">double</td></tr>
  <tr data-morhtml="true"><td data-morhtml="true">sessionid</td><td data-morhtml="true">bigint</td></tr>
  <tr data-morhtml="true"><td data-morhtml="true">sessionpagevieworder</td><td data-morhtml="true">bigint</td></tr>
  </table>

Sarà necessario innanzitutto esportare sample.log e hivesampletable nel database SQL quindi importare di nuovo in HDInsight la tabella contenente i dati del dispositivo mobile utilizzando il percorso seguente:

    /tutorials/usesqoop/importeddata

### Informazioni sull'archiviazione in HDInsight

HDInsight utilizza l'archivio BLOB di Azure per l'archiviazione dei dati. Questa risorsa è denominata *WASB* o *Archiviazione BLOB di Azure*. WASB è l'implementazione Microsoft di HDFS sull'archivio BLOB di Azure. Per ulteriori informazioni, vedere [Utilizzo dell'archivio BLOB di Azure con HDInsight](../hdinsight-use-blob-storage/).

Durante il provisioning di un cluster HDInsight, un account di archiviazione di Azure e uno specifico contenitore di archiviazione BLOB di tale account vengono designati come file system predefinito, come in HDFS. Durante il processo di provisioning è possibile aggiungere a tale account di archiviazione ulteriori account, dalla stessa sottoscrizione di Azure o da sottoscrizioni diverse. Per istruzioni sull'aggiunta di ulteriori account di archiviazione, vedere [Provisioning di cluster HDInsight](../hdinsight-provision-clusters/). Per semplificare lo script di PowerShell utilizzato in questa esercitazione, tutti i file vengono archiviati nel contenitore del file system predefinito, presente in */tutorials/usesqoop*. Per impostazione predefinita, il nome di questo contenitore corrisponde al nome del cluster HDInsight. La sintassi WASB è la seguente:

    wasb[s]://<ContainerName>@<StorageAccountName>.blob.core.windows.net/<path>/<filename>

> [WACOM.NOTE] Nella versione 3.0 del cluster HDInsight è supportata solo la sintassi *wasb://*. La sintassi *asv://*, meno recente, è supportata nei cluster HDInsight 2.1 e 1.6, ma non è supportata nei cluster HDInsight 3.0 e non sarà supportata nelle versioni successive.

> [WACOM.NOTE] Il percorso WASB è un percorso virtuale. Per ulteriori informazioni, vedere [Utilizzo dell'archivio BLOB di Azure con HDInsight](../hdinsight-use-blob-storage/).

È possibile accedere da HDInsight a un file archiviato nel contenitore del file system predefinito utilizzando uno degli URI seguenti (utilizzare come esempio sample.log):

    wasb://mycontainer@mystorageaccount.blob.core.windows.net/example/data/sample.log
    wasb:///example/data/sample.log
    /example/data/sample.log

Per accedere al file direttamente dall'account di archiviazione, il nome del BLOB per questo file è:

    example/data/sample.log

Preparazione dell'esercitazione
-------------------------------

Verranno create due tabelle di database SQL utilizzate dall'esportazione di Sqoop più avanti nell'esercitazione. Sarà inoltre necessario pre-elaborare i file sample.log prima dell'elaborazione con Sqoop.

**Per creare una tabella di database SQL**

1.  Aprire Windows PowerShell ISE (nella schermata Start di Windows 8 digitare **PowerShell\_ISE** e quindi fare clic su **Windows PowerShell ISE**. Vedere la pagina dedicata all'[avvio di Windows PowerShell in Windows 8 e Windows](http://technet.microsoft.com/en-us/library/hh847889.aspx)).

2.  Copiare lo script seguente nel riquadro di script e impostare le prime quattro variabili:

         #SQL database variables
         $sqlDatabaseServer = "<SQLDatabaseServerName>" 
         $sqlDatabaseLogin = "<SQLDatabaseUsername>"
         $sqlDatabasePassword = "<SQLDatabasePassword>"
         $sqlDatabaseName = "<SQLDatabaseName>" 

         $sqlDatabaseConnectionString = "Data Source=$sqlDatabaseServer.database.windows.net;Initial Catalog=$sqlDatabaseName;User ID=$sqlDatabaseLogin;Password=$sqlDatabasePassword;Encrypt=true;Trusted_Connection=false;"

    Per altre descrizioni di queste variabili, vedere la sezione [Prerequisiti](#prerequisites) di questa esercitazione.

3.  Aggiungere lo script seguente nel riquadro di script. Si tratta delle istruzioni SQL che definiscono le due tabelle e i relativi indici cluster. Per il database SQL è necessario un indice cluster.

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

4.  Per eseguire i comandi SQL, aggiungere lo script seguente nel riquadro di script:

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

5.  Fare clic su **Esegui script** o premere **F5** per eseguire lo script.
6.  Utilizzare il [portale di gestione di Azure](https://manage.windowsazure.com/) per esaminare le tabelle e gli indici cluster.

In questa esercitazione, verranno esportati nel database SQL un file di log log4j (un file con valori delimitati) e una tabella Hive. Il file con valori delimitati è */example/data/sample.log*. Alcuni esempi di file di log log4j sono stati visualizzati in precedenza in questa esercitazione. Nel file di log sono presenti alcune righe vuote e righe simili alle seguenti:

    java.lang.Exception: 2012-02-03 20:11:35 SampleClass2 [FATAL] unrecoverable system problem at id 609774657
        at com.osa.mocklogger.MockLogger$2.run(MockLogger.java:83)

È possibile rimuovere *java.lang.Exception:* dalla riga affinché il record possa essere analizzato correttamente.

In presenza di una stringa vuota o di una riga contenente un numero di elementi inferiore al numero dei campi definiti nella tabella di database SQL, non sarà possibile eseguire l'esportazione con Sqoop. La tabella log4jlogs comprende 7 campi tipo stringa.

**Per pre-elaborare il file sample.log**

1.  Aprire Windows PowerShell ISE.
2.  Nel riquadro inferiore eseguire il comando seguente per connettersi alla sottoscrizione di Azure:

         Add-AzureAccount

    Verrà richiesto di immettere le credenziali dell'account Azure. Questo metodo che prevede l'aggiunta di una connessione alla sottoscrizione scade dopo 12 ore, di conseguenza sarà necessario accedere nuovamente.

    > [WACOM.NOTE] Nel caso in cui siano disponibili più sottoscrizioni di Azure e non si desideri utilizzare la sottoscrizione predefinita, utilizzare il cmdlet **Select-AzureSubscription** per selezionare la sottoscrizione corrente.

3.  Copiare lo script seguente nel riquadro di script e impostare le prime due variabili:

         $storageAccountName = "<WindowsAzureStorageAccountName>"
         $containerName = "<BlobContainerName>"
            
         $sourceBlobName = "example/data/sample.log"
         $destBlobName = "tutorials/usesqoop/data/sample.log"

    Per altre descrizioni di queste variabili, vedere la sezione [Prerequisiti](#prerequisites) di questa esercitazione.

4.  Aggiungere lo script seguente nel riquadro di script:

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

5.  Fare clic su **Esegui script** o premere **F5** per eseguire lo script.
6.  Per esaminare il file di dati modificato, è possibile utilizzare il portale di gestione di Azure o uno strumento di Esplora archivi Azure oppure Azure PowerShell. [In](../hdinsight-get-started/)Introduzione a HDInsight è presente un esempio di codice relativo all'utilizzo di PowerShell per scaricare un file e visualizzarne il contenuto.

Utilizzo di PowerShell per eseguire l'esportazione con Sqoop
------------------------------------------------------------

In questa sezione, verrà utilizzato Azure PowerShell per eseguire un comando di esportazione di Sqoop per esportare una tabella Hive e un file di dati in un database SQL di Azure. La sezione successiva offre un esempio di .NET per HDInsight.

**Per esportare il file di log log4j**

1.  Aprire Windows PowerShell ISE.
2.  Nel riquadro inferiore eseguire il comando seguente per connettersi alla sottoscrizione di Azure:

         Add-AzureAccount

    Verrà richiesto di immettere le credenziali dell'account Azure.

3.  Copiare lo script seguente nel riquadro di script e impostare le prime sette variabili:

         # Define the cluster variables
         $clusterName = "<HDInsightClusterName>"
         $storageAccountName = "<WindowsAzureStorageAccount>"
         $containerName = "<BlobStorageContainerName>"
            
         # Define the SQL database variables
         $sqlDatabaseServerName = "<SQLDatabaseServerName>"
         $sqlDatabaseLogin = "<SQLDatabaseUsername>"
         $sqlDatabasePassword = "SQLDatabasePassword>"
         $databaseName = "<SQLDatabaseName>"

         $tableName_log4j = "log4jlogs"
            
         $connectionString = "jdbc:sqlserver://$sqlDatabaseServerName.database.windows.net;user=$sqlDatabaseLogin@$sqlDatabaseServerName;password=$sqlDatabasePassword;database=$databaseName"
            
         $exportDir_log4j = "/tutorials/usesqoop/data"

    Per altre descrizioni di queste variabili, vedere la sezione [Prerequisiti](#prerequisites) di questa esercitazione.

    Si noti che per \$exportDir\_log4j non è stato specificato il nome file per il file sample.log. Sqoop esporterà i dati da tutti i file in quella cartella.

4.  Aggiungere lo script seguente nel riquadro di script:

         # Submit a Sqoop job
         $sqoopDef = New-AzureHDInsightSqoopJobDefinition -Command "export --connect $connectionString --table $tableName_log4j --export-dir $exportDir_log4j --input-fields-terminated-by  # Submit a Sqoop job
         $sqoopDef = New-AzureHDInsightSqoopJobDefinition -Command "export --connect $connectionString --table $tableName_log4j --export-dir $exportDir_log4j --input-fields-terminated-by \0x20 -m 1"
         $sqoopJob = Start-AzureHDInsightJob -Cluster $clusterName -JobDefinition $sqoopDef #-Debug -Verbose
         Wait-AzureHDInsightJob -WaitTimeoutInSeconds 3600 -Job $sqoopJob
            
         Write-Host "Standard Error" -BackgroundColor Green
         Get-AzureHDInsightJobOutput -Cluster $clusterName -JobId $sqoopJob.JobId -StandardError
         Write-Host "Standard Output" -BackgroundColor Green
         Get-AzureHDInsightJobOutput -Cluster $clusterName -JobId $sqoopJob.JobId -StandardOutput
        x20 -m 1"
         $sqoopJob = Start-AzureHDInsightJob -Cluster $clusterName -JobDefinition $sqoopDef #-Debug -Verbose
         Wait-AzureHDInsightJob -WaitTimeoutInSeconds 3600 -Job $sqoopJob
            
         Write-Host "Standard Error" -BackgroundColor Green
         Get-AzureHDInsightJobOutput -Cluster $clusterName -JobId $sqoopJob.JobId -StandardError
         Write-Host "Standard Output" -BackgroundColor Green
         Get-AzureHDInsightJobOutput -Cluster $clusterName -JobId $sqoopJob.JobId -StandardOutput

    Il delimitatore di campo è **\\0x20**, che corrisponde a uno spazio. Il delimitatore è definito nello script PowerShell di pre-elaborazione del file sample.log. Per avere informazioni su **-m 1**, vedere la [Guida dell'utente di Sqoop](https://sqoop.apache.org/docs/1.4.4/SqoopUserGuide.html).

5.  Fare clic su **Esegui script** o premere **F5** per eseguire lo script.
6.  Utilizzare il [portale di gestione di Azure](https://manage.windowsazure.com/) per esaminare i dati esportati.

**Per esportare la tabella Hive hivesampletable**

1.  Aprire Windows PowerShell ISE.
2.  Nel riquadro inferiore eseguire il comando seguente per connettersi alla sottoscrizione di Azure:

         Add-AzureAccount

    Verrà richiesto di immettere le credenziali dell'account Azure.

3.  Copiare lo script seguente nel riquadro di script e impostare le prime sette variabili:

         # Define the cluster variables
         $clusterName = "<HDInsightClusterName>"
         $storageAccountName = "<WindowsAzureStorageAccount>"
         $containerName = "<BlobStorageContainerName>"
            
         # Define the SQL database variables
         $sqlDatabaseServerName = "<SQLDatabaseServerName>"
         $sqlDatabaseLogin = "<SQLDatabaseUsername>"
         $sqlDatabasePassword = "SQLDatabasePassword>"
         $databaseName = "SQLDatabaseName"

         $tableName_mobile = "mobiledata"
            
         $connectionString = "jdbc:sqlserver://$sqlDatabaseServerName.database.windows.net;user=$sqlDatabaseLogin@$sqlDatabaseServerName;password=$sqlDatabasePassword;database=$databaseName"
            
         $exportDir_mobile = "/hive/warehouse/hivesampletable"

    Per altre descrizioni di queste variabili, vedere la sezione [Prerequisiti](#prerequisites) di questa esercitazione.

4.  Aggiungere lo script seguente nel riquadro di script:

         $sqoopDef = New-AzureHDInsightSqoopJobDefinition -Command "export --connect $connectionString --table $tableName_mobile --export-dir $exportDir_mobile --fields-terminated-by \t -m 1"
            
            
         $sqoopJob = Start-AzureHDInsightJob -Cluster $clusterName -JobDefinition $sqoopDef #-Debug -Verbose
         Wait-AzureHDInsightJob -WaitTimeoutInSeconds 3600 -Job $sqoopJob
            
         Write-Host "Standard Error" -BackgroundColor Green
         Get-AzureHDInsightJobOutput -Cluster $clusterName -JobId $sqoopJob.JobId -StandardError
         Write-Host "Standard Output" -BackgroundColor Green
         Get-AzureHDInsightJobOutput -Cluster $clusterName -JobId $sqoopJob.JobId -StandardOutput

5.  Fare clic su **Esegui script** o premere **F5** per eseguire lo script.
6.  Utilizzare il [portale di gestione di Azure](https://manage.windowsazure.com/) per esaminare i dati esportati.

Utilizzo di .NET SDK per HDInsight per eseguire l'esportazione con Sqoop
------------------------------------------------------------------------

Il seguente è un esempio in C\# che utilizza .NET SDK per HDInsight per eseguire l'esportazione con Sqoop. Per informazioni generali sull'utilizzo di .NET SDK per HDInsight, vedere [Invio di processi Hadoop a livello di codice](../hdinsight-submit-hadoop-jobs-programmatically/).

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
                string subscriptionID = "<WindowsAzureSubscriptionID>";
                string clusterName = "<HDInsightClusterName>";
                string certFriendlyName = "<WindowsAzureCertificateFriendlyName>";
                string sqlDatabaseServerName = "<SQLDatabaseServerName>";
                string sqlDatabaseLogin = "<SQLDatabaseLogin>" + "@" + sqlDatabaseServerName;
                string sqlDatabaseLoginPassword = "<SQLDatabaseLoginPassword>";
                string sqlDatabaseDatabaseName = "hdisqoop";
                string sqlDatabaseTableName = "log4jlogs";

                cmdExport = @"export";
                cmdExport = cmdExport + @" --connect jdbc:sqlserver://" + sqlDatabaseServerName + ".database.windows.net;user=" + sqlDatabaseLogin + ";password=" + sqlDatabaseLoginPassword + ";database=" + sqlDatabaseDatabaseName;
                cmdExport = cmdExport + @" --table " + sqlDatabaseTableName;
                cmdExport = cmdExport + @" --export-dir /tutorials/usesqoop/data";
                cmdExport = cmdExport + @" --input-fields-terminated-by using System;
    using System.Collections.Generic;
    using System.Linq;
    using System.Text;
    using System.Threading.Tasks;
    using System.IO;
    using System.Threading;
    using System.Security.Cryptography.X509Certificates;
    using Microsoft.WindowsAzure.Management.HDInsight;
    using Microsoft.Hadoop.Client;

    namespace sqoopSDKSample?{?    class Program?    {?        static void Main(string[] args)?        {?            // Set the variables
                string subscriptionID = "<WindowsAzureSubscriptionID>";
                string clusterName = "<HDInsightClusterName>";
                string certFriendlyName = "<WindowsAzureCertificateFriendlyName>";
                string sqlDatabaseServerName = "<SQLDatabaseServerName>";
                string sqlDatabaseLogin = "<SQLDatabaseLogin>" + "@" + sqlDatabaseServerName;
                string sqlDatabaseLoginPassword = "<SQLDatabaseLoginPassword>";
                string sqlDatabaseDatabaseName = "hdisqoop";
                string sqlDatabaseTableName = "log4jlogs";

                cmdExport = @"export";
                cmdExport = cmdExport + @" --connect jdbc:sqlserver://" + sqlDatabaseServerName + ".database.windows.net;user=" + sqlDatabaseLogin + ";password=" + sqlDatabaseLoginPassword + ";database=" + sqlDatabaseDatabaseName;
                cmdExport = cmdExport + @" --table " + sqlDatabaseTableName;
                cmdExport = cmdExport + @" --export-dir /tutorials/usesqoop/data";
                cmdExport = cmdExport + @" --input-fields-terminated-by \0x20 -m 1";

                SqoopJobCreateParameters sqoopJobDefinition = new SqoopJobCreateParameters()?            {?                Command = cmdExport,
                    StatusFolder = "/tutorials/usesqoop/jobStatus"?            };??            // Get the certificate object from certificate store using the friendly name to identify it
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
                Console.ReadLine();?        }??        private static void WaitForJobCompletion(JobCreationResults jobResults, IJobSubmissionClient client)?        {?            JobDetails jobInProgress = client.GetJob(jobResults.JobId);
                while (jobInProgress.StatusCode != JobStatusCode.Completed && jobInProgress.StatusCode != JobStatusCode.Failed)?            {?                jobInProgress = client.GetJob(jobInProgress.JobId);
                    Thread.Sleep(TimeSpan.FromSeconds(10));?            }?        }?    }?}?x20 -m 1";

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

Utilizzo di PowerShell per eseguire l'importazione con Sqoop
------------------------------------------------------------

In questa sezione i log log4j (esportati nel database SQL) verranno importati nuovamente in HDInsight.

1.  Aprire Windows PowerShell ISE.
2.  Nel riquadro inferiore eseguire il comando seguente per connettersi alla sottoscrizione di Azure:

         Add-AzureAccount

    Verrà richiesto di immettere le credenziali dell'account Azure.

3.  Copiare lo script seguente nel riquadro di script e impostare le prime sette variabili:

         # Define the cluster variables
         $clusterName = "<HDInsightClusterName>"
         $storageAccountName = "<WindowsAzureStorageAccount>"
         $containerName = "<BlobStorageContainerName>"
            
         # Define the SQL database variables
         $sqlDatabaseServerName = "<SQLDatabaseServerName>"
         $sqlDatabaseLogin = "<SQLDatabaseUsername>"
         $sqlDatabasePassword = "SQLDatabasePassword>"
         $databaseName = "SQLDatabaseName"

         $tableName_log4j = "log4jlogs"
            
         $connectionString = "jdbc:sqlserver://$sqlDatabaseServerName.database.windows.net;user=$sqlDatabaseLogin@$sqlDatabaseServerName;password=$sqlDatabasePassword;database=$databaseName"
            
         $tableName_mobile = "mobiledata"
         $targetDir_mobile = "/tutorials/usesqoop/importeddata/"

    Per altre descrizioni di queste variabili, vedere la sezione [Prerequisiti](#prerequisites) di questa esercitazione.

4.  Aggiungere lo script seguente nel riquadro di script:

         $sqoopDef = New-AzureHDInsightSqoopJobDefinition -Command "import --connect $connectionString --table $tableName_mobile --target-dir $targetDir_mobile --fields-terminated-by \t --lines-terminated-by \n -m 1"
            
         $sqoopJob = Start-AzureHDInsightJob -Cluster $clusterName -JobDefinition $sqoopDef #-Debug -Verbose
         Wait-AzureHDInsightJob -WaitTimeoutInSeconds 3600 -Job $sqoopJob
            
         Write-Host "Standard Error" -BackgroundColor Green
         Get-AzureHDInsightJobOutput -Cluster $clusterName -JobId $sqoopJob.JobId -StandardError
         Write-Host "Standard Output" -BackgroundColor Green
         Get-AzureHDInsightJobOutput -Cluster $clusterName -JobId $sqoopJob.JobId -StandardOutput

5.  Fare clic su **Esegui script** o premere **F5** per eseguire lo script.
6.  Per esaminare il file di dati modificato, è possibile utilizzare il portale di gestione di Azure o uno strumento di Esplora archivi Azure oppure Azure PowerShell. [In](../hdinsight-get-started/)Introduzione a HDInsight è presente un esempio di codice relativo all'utilizzo di PowerShell per scaricare un file e visualizzarne il contenuto.

Passaggi successivi
-------------------

In questa esercitazione si è appreso come utilizzare Sqoop. Per ulteriori informazioni, vedere:

-   [Utilizzo di Oozie con HDInsight](../hdinsight-use-oozie/): utilizzo dell'azione di Sqoop in un flusso di lavoro Oozie.
-   [Analisi dei dati sui ritardi dei voli utilizzando HDInsight](../hdinsight-analyze-flight-delay-data/): utilizzo di Hive nell'analisi dei dati sui ritardi dei voli e quindi di Sqoop per esportare dati nel database SQL.
-   [Caricamento di dati in HDInsight](../hdinsight-upload-data/): per altri metodi per il caricamento di file in HDInsight o nell'archivio BLOB di Azure

