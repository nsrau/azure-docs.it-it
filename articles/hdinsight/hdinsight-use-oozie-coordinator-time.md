<properties 
	pageTitle="Usare il coordinatore Oozie di Hadoop basato sul tempo in HDInsight | Microsoft Azure" 
	description="Usare il coordinatore Oozie di Hadoop basato sul tempo in HDInsight, un servizio per Big Data. Informazioni su come definire i flussi di lavoro e i coordinatori di Oozie e come inviare i processi." 
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
	ms.date="03/31/2015" 
	ms.author="jgao"/>


# Usare il coordinatore di Oozie basato sul tempo con Hadoop in HDInsight per definire flussi di lavoro e coordinare processi

Questo articolo descrive come definire flussi di lavoro e coordinatori e come attivare i processi del coordinatore in base al tempo. Prima di procedere può essere utile consultare l'articolo [Usare Oozie con HDInsight][hdinsight-use-oozie]. Per informazioni su Azure Data Factory, vedere [Usare Pig e Hive con Data factory](../data-factory/data-factory-pig-hive-activities.md).


##<a id="whatisoozie"></a>Informazioni su Oozie

Apache Oozie è un sistema di flusso di lavoro/coordinamento che consente di gestire i processi Hadoop. È integrato nello stack di Hadoop e supporta i processi Hadoop per Apache MapReduce, Apache Pig, Apache Hive e Apache Sqoop. Può anche essere usato per pianificare processi specifici di un sistema, come i programmi Java o gli script della shell.

L'immagine seguente illustra il flusso di lavoro che verrà implementato:

![Diagramma del flusso di lavoro][img-workflow-diagram]

Il flusso di lavoro contiene due azioni:

1. Un'azione di Hive esegue uno script HiveQL per contare le occorrenze di ogni tipo di livello di log in un file log4j. Ogni log log4j è costituito da una riga di campi che contiene un campo [LOG LEVEL] per visualizzare il tipo e la gravità. Ad esempio:

		2012-02-03 18:35:34 SampleClass6 [INFO] everything normal for id 577725851
		2012-02-03 18:35:34 SampleClass4 [FATAL] system problem at id 1991281254
		2012-02-03 18:35:34 SampleClass3 [DEBUG] detail for id 1304807656
		...

	L'output dello script Hive è simile al seguente:
	
		[DEBUG] 434
		[ERROR] 3
		[FATAL] 1
		[INFO]  96
		[TRACE] 816
		[WARN]  4

	Per altre informazioni su Hive, vedere [Usare Hive con HDInsight][hdinsight-use-hive].
	
2.  Un'azione di Sqoop esporta l'output dell'azione di HiveQL in una tabella di un database SQL di Azure. Per altre informazioni su Sqoop, vedere [Usare Sqoop con HDInsight][hdinsight-use-sqoop].

> [AZURE.NOTE]Per informazioni sulle versioni di Oozie supportate nei cluster HDInsight, vedere [Novità delle versioni cluster di Hadoop incluse in HDInsight][hdinsight-versions].

> [AZURE.NOTE]Questa esercitazione usa il cluster HDInsight versioni 2.1 e 3.0. Il contenuto di questo articolo non è stato testato su HDInsight Emulator.


##<a id="prerequisites"></a>Prerequisiti

Prima di iniziare questa esercitazione, è necessario disporre di quanto segue:

- **Una workstation con Azure PowerShell**. Vedere [Installare e usare Azure PowerShell](http://azure.microsoft.com/documentation/videos/install-and-use-azure-powershell/). Per eseguire script di Windows PowerShell, è necessario eseguire Azure PowerShell come amministratore e impostare i criteri di esecuzione su *RemoteSigned*. Per altre informazioni, vedere l'articolo relativo all'[esecuzione degli script di Windows PowerShell][powershell-script].
- **Un cluster HDInsight**. Per informazioni sulla creazione di un cluster HDInsight, vedere [Effettuare il provisioning di cluster HDInsight][hdinsight-provision] o [Introduzione all'uso di HDInsight][hdinsight-get-started]. Per completare l'esercitazione sono necessari i dati seguenti:

	<table border = "1">
<tr><th>Proprietà del cluster</th><th>Nome variabile di Windows PowerShell</th><th>Valore</th><th>Descrizione</th></tr>
<tr><td>Nome del cluster HDInsight</td><td>$clusterName</td><td></td><td>Cluster HDInsight su cui si eseguirà questa esercitazione.</td></tr>
<tr><td>Nome utente del cluster HDInsight</td><td>$clusterUsername</td><td></td><td>Nome utente del cluster HDInsight. </td></tr>
<tr><td>Password utente del cluster HDInsight </td><td>$clusterPassword</td><td></td><td>La password utente del cluster HDInsight.</td></tr>
<tr><td>Nome dell'account di archiviazione di Azure</td><td>$storageAccountName</td><td></td><td>Un account di archiviazione di Azure disponibile per il cluster HDInsight. Ai fini di questa esercitazione, usare l'account di archiviazione predefinito, specificato durante il processo di provisioning del cluster.</td></tr>
<tr><td>Nome del contenitore BLOB di Azure</td><td>$containerName</td><td></td><td>Per questo esempio, usare il contenitore di archiviazione BLOB di Azure usato come file system predefinito del cluster HDInsight. Per impostazione predefinita, il nome del contenitore corrisponde al nome del cluster HDInsight.</td></tr>
</table>

- **Un database SQL di Azure**. È necessario configurare una regola del firewall per il server di database SQL per consentire l'accesso dalla workstation. Per istruzioni sulla creazione di un database SQL di Azure e sulla configurazione del firewall, vedere [Introduzione al database SQL di Azure][sqldatabase-get-started]. Questo articolo fornisce uno script di Windows PowerShell per consentire la creazione della tabella del database SQL di Azure necessaria per questa esercitazione.

	<table border = "1">
<tr><th>Proprietà del database SQL</th><th>Nome variabile di Windows PowerShell</th><th>Valore</th><th>Descrizione</th></tr>
<tr><td>Nome del server di database SQL</td><td>$sqlDatabaseServer</td><td></td><td>Il server di database SQL in cui Sqoop esporterà i dati. </td></tr>
<tr><td>Nome di accesso al database SQL</td><td>$sqlDatabaseLogin</td><td></td><td>Il nome di accesso al database SQL.</td></tr>
<tr><td>Password di accesso al database SQL</td><td>$sqlDatabaseLoginPassword</td><td></td><td>La password di accesso al database SQL.</td></tr>
<tr><td>Nome del database SQL</td><td>$sqlDatabaseName</td><td></td><td>Il database SQL di Azure in cui Sqoop esporterà i dati. </td></tr>
</table>> [AZURE.NOTE]Per impostazione predefinita, un database SQL di Azure consente connessioni da servizi di Azure, ad esempio Azure HDinsight. Se questa impostazione del firewall è disabilitata, sarà necessario abilitarla nel portale di Azure. Per istruzioni sulla creazione di un database SQL e sulla configurazione di regole del firewall, vedere [Come creare e configurare un database SQL][sqldatabase-create-configure].


> [AZURE.NOTE]L'inserimento dei valori nelle tabelle potrà essere utile per completare questa esercitazione.


##<a id="defineworkflow"></a>Definire il flusso di lavoro di Oozie e lo script HiveQL correlato

Le definizioni dei flussi di lavoro di Oozie sono scritte in linguaggio hPDL (XML Process Definition Language). Il nome del file del flusso di lavoro predefinito è *workflow.xml*. Il file del flusso di lavoro verrà salvato a livello locale e distribuito nel cluster HDInsight tramite Azure PowerShell più avanti in questa esercitazione.

L'azione di Hive nel flusso di lavoro chiama un file di script HiveQL che contiene tre istruzioni HiveQL:

1. **L'istruzione DROP TABLE** consente di eliminare la tabella di Hive log4j, se esistente.
2. **L'istruzione CREATE TABLE** consente di creare una tabella di Hive log4j esterna che punti al percorso del file di log log4j.
3.  **Il percorso del file di log log4j**. Il delimitatore di campo è ",". Il delimitatore di riga predefinito è "\n". La tabella esterna di Hive viene usata per evitare che il file di dati venga rimosso dal percorso originale, nel caso in cui si desideri eseguire il flusso di lavoro di Oozie più volte.
3. **L'istruzione INSERT OVERWRITE** conta le occorrenze di ogni tipo di livello di log nella tabella di Hive log4j e salva l'output in un percorso dell'archivio BLOB di Azure. 

**Nota**: il percorso di Hive è caratterizzato da un problema noto che si verifica durante l'invio di un processo Oozie. Le istruzioni per la correzione del problema sono disponibili nell'articolo della sezione Wiki di TechNet relativo all'[errore di HDInsight Hive "Impossibile rinominare"][technetwiki-hive-error].

**Per definire il file di script HiveQL che deve essere chiamato dal flusso di lavoro**

1. Creare un file di testo con il contenuto seguente:

		DROP TABLE ${hiveTableName};
		CREATE EXTERNAL TABLE ${hiveTableName}(t1 string, t2 string, t3 string, t4 string, t5 string, t6 string, t7 string) ROW FORMAT DELIMITED FIELDS TERMINATED BY ' ' STORED AS TEXTFILE LOCATION '${hiveDataFolder}';
		INSERT OVERWRITE DIRECTORY '${hiveOutputFolder}' SELECT t4 AS sev, COUNT(*) AS cnt FROM ${hiveTableName} WHERE t4 LIKE '[%' GROUP BY t4;

	Nello script vengono usate tre variabili:

	- ${hiveTableName}
	- ${hiveDataFolder}
	- ${hiveOutputFolder}
			
	Il file di definizione del flusso di lavoro (workflow.xml in questa esercitazione) passerà questi valori allo script HiveQL in fase di esecuzione.
		
2. Salvare il file come **C:\Tutorials\UseOozie\useooziewf.hql** usando la codifica ANSI (ASCII). Usare il blocco note se l'editor di testo non offre questa opzione. Il file di script verrà distribuito nel cluster HDInsight più avanti in questa esercitazione.



**Per definire un flusso di lavoro**

1. Creare un file di testo con il contenuto seguente:

		<workflow-app name="useooziewf" xmlns="uri:oozie:workflow:0.2">
		    <start to = "RunHiveScript"/> 
			
		    <action name="RunHiveScript">
		        <hive xmlns="uri:oozie:hive-action:0.2">
		            <job-tracker>${jobTracker}</job-tracker>
		            <name-node>${nameNode}</name-node>
		            <configuration>
		                <property>
		                    <name>mapred.job.queue.name</name>
		                    <value>${queueName}</value>
		                </property>
		            </configuration>
		            <script>${hiveScript}</script>
			    	<param>hiveTableName=${hiveTableName}</param>
		            <param>hiveDataFolder=${hiveDataFolder}</param>
		            <param>hiveOutputFolder=${hiveOutputFolder}</param>
		        </hive>
		        <ok to="RunSqoopExport"/>
		        <error to="fail"/>
		    </action>
		
		    <action name="RunSqoopExport">
		        <sqoop xmlns="uri:oozie:sqoop-action:0.2">
		            <job-tracker>${jobTracker}</job-tracker>
		            <name-node>${nameNode}</name-node>
		            <configuration>
		                <property>
		                    <name>mapred.compress.map.output</name>
		                    <value>true</value>
		                </property>
		            </configuration>
			    <arg>export</arg>
			    <arg>--connect</arg> 
			    <arg>${sqlDatabaseConnectionString}</arg> 
			    <arg>--table</arg>
			    <arg>${sqlDatabaseTableName}</arg> 
			    <arg>--export-dir</arg> 
			    <arg>${hiveOutputFolder}</arg> 
			    <arg>-m</arg> 
			    <arg>1</arg>
			    <arg>--input-fields-terminated-by</arg>
			    <arg>"\001"</arg>
		        </sqoop>
		        <ok to="end"/>
		        <error to="fail"/>
		    </action>
		
		    <kill name="fail">
		        <message>Job failed, error message[${wf:errorMessage(wf:lastErrorNode())}] </message>
		    </kill>
		
		   <end name="end"/>
		</workflow-app>

	Nel flusso di lavoro vengono definite due azioni. L'azione start-to è *RunHiveScript*. Se l'azione esegue *OK*, l'azione successiva è *RunSqoopExport*.

	RunHiveScript è caratterizzato da diverse variabili. I valori verranno passati quando si invia il processo Oozie dalla workstation con Azure PowerShell.

	<table border = "1">
<tr><th>Variabili del flusso di lavoro</th><th>Descrizione</th></tr>
<tr><td>${jobTracker}</td><td>Specifica l'URL dell'utilità di analisi dei processi Hadoop. Usare <strong>jobtrackerhost:9010</strong> nel cluster HDInsight versione 3.0 e 2.0.</td></tr>
<tr><td>${nameNode}</td><td>Specifica l'URL del nodo dei nomi di Hadoop. Usare l'indirizzo wasb:// del file system predefinito, ad esempio <i>wasb://&lt;containerName>@&lt;storageAccountName>.blob.core.windows.net</i>.</td></tr>
<tr><td>${queueName}</td><td>Consente di specificare il nome della coda alla quale verrà inviato il processo. Usare <strong>default</strong>.</td></tr>
</table><table border = "1">
<tr><th>Variabile azione Hive</th><th>Descrizione</th></tr>
<tr><td>${hiveDataFolder}</td><td>La directory di origine per il comando Hive Create Table.</td></tr>
<tr><td>${hiveOutputFolder}</td><td>La cartella di output per l'istruzione INSERT OVERWRITE.</td></tr>
<tr><td>${hiveTableName}</td><td>Il nome della tabella di Hive che fa riferimento ai file di dati log4j.</td></tr>
</table><table border = "1">
<tr><th>Variabile azione Sqoop</th><th>Descrizione</th></tr>
<tr><td>${sqlDatabaseConnectionString}</td><td>Stringa di connessione del database SQL.</td></tr>
<tr><td>${sqlDatabaseTableName}</td><td>La tabella del database SQL di Azure in cui verranno esportati i dati.</td></tr>
<tr><td>${hiveOutputFolder}</td><td>La cartella di output per l'istruzione INSERT OVERWRITE di Hive. È la stessa cartella dell'esportazione tramite Sqoop (export-dir).</td></tr>
</table>Per altre informazioni sul flusso di lavoro di Oozie e sull'utilizzo di azioni del flusso di lavoro, vedere la [documentazione di Apache Oozie 4.0][apache-oozie-400] (per cluster HDInsight versione 3.0) o la [documentazione di Apache Oozie 3.3.2][apache-oozie-332] (per cluster HDInsight versione 2.1).

2. Salvare il file come **C:\Tutorials\UseOozie\workflow.xml** usando la codifica ANSI (ASCII). Usare il blocco note se l'editor di testo non offre questa opzione.

**Per definire il coordinatore**

1. Creare un file di testo con il contenuto seguente:

		<coordinator-app name="my_coord_app" frequency="${coordFrequency}" start="${coordStart}" end="${coordEnd}" timezone="${coordTimezone}" xmlns="uri:oozie:coordinator:0.4">
		   <action>
		      <workflow>
		         <app-path>${wfPath}</app-path>
		      </workflow>
		   </action>
		</coordinator-app>

	Nel file di definizione vengono usate cinque variabili:

	| Variabile | Descrizione |
	| ------------------|------------ |
	| ${coordFrequency} | Tempo di sospensione del processo. La frequenza è sempre espressa in minuti. |
	| ${coordStart} | Ora di inizio del processo. |
	| ${coordEnd} | Ora di fine del processo. |
    | ${coordTimezone} | Oozie elabora i processi del coordinatore in un fuso orario predefinito che non tiene conto dell'ora legale (in genere rappresentato dall'acronimo UTC). Questo fuso orario viene definito "Fuso orario di elaborazione Oozie". |
	| ${wfPath} | Percorso del file workflow.xml. Se il nome file del flusso di lavoro non è quello predefinito (workflow.xml) sarà necessario specificarlo. |
	
2. Salvare il file come **C:\Tutorials\UseOozie\coordinator.xml** usando la codifica ANSI (ASCII). Usare il blocco note se l'editor di testo non offre questa opzione.
	
##<a id="deploy"></a>Distribuire il progetto Oozie e operazioni preliminari all'esercitazione

Eseguire uno script di Azure PowerShell per completare le seguenti attività:

- Copiare lo script HiveQL (useoozie.hql) nell'archiviazione BLOB di Azure, wasb:///tutorials/useoozie/useoozie.hql.
- Copiare il file workflow.xml in wasb:///tutorials/useoozie/workflow.xml.
- Copiare il file coordinator.xml in wasb:///tutorials/useoozie/coordinator.xml.
- Copiare il file di dati (/example/data/sample.log) in wasb:///tutorials/useoozie/data/sample.log. 
- Creare una tabella di database SQL di Azure per l'archiviazione dei dati di esportazione di Sqoop. Il nome della tabella è *log4jLogCount*.

**Informazioni sull'archiviazione in HDInsight**

HDInsight usa l'archiviazione BLOB di Azure per l'archiviazione dei dati. wasb: / / è l'implementazione Microsoft di HDFS (Hadoop Distributed File System) nell'archiviazione BLOB di Azure. Per altre informazioni, vedere [Usare l'archiviazione BLOB di Azure con HDInsight][hdinsight-storage].

Quando si effettua il provisioning di un cluster HDInsight, vengono designati come file system predefinito un account di archiviazione BLOB di Azure e un contenitore specifico di tale account, proprio come in HDFS. Oltre a questo account di archiviazione, durante il processo di provisioning è possibile aggiungere anche altri account di archiviazione provenienti dalla stessa sottoscrizione di Azure o da sottoscrizioni di Azure diverse. Per istruzioni sull'aggiunta di altri account di archiviazione, vedere [Effettuare il provisioning di cluster HDInsight][hdinsight-provision]. Per semplificare lo script di Azure PowerShell usato in questa esercitazione, tutti i file vengono archiviati nel contenitore del file system predefinito, presente in */tutorials/useoozie*. Per impostazione predefinita, il nome di questo contenitore corrisponde al nome del cluster HDInsight. La sintassi è:

	wasb[s]://<ContainerName>@<StorageAccountName>.blob.core.windows.net/<path>/<filename>

> [AZURE.NOTE]Nel cluster HDInsight versione 3.0 è supportata solo la sintassi *wasb://*. La sintassi *asv://* precedente è supportata nei cluster HDInsight 2.1 e 1.6, ma non è supportata nei cluster HDInsight 3.0.

> [AZURE.NOTE]Il percorso wasb:// è un percorso virtuale. Per altre informazioni, vedere [Usare l'archiviazione BLOB di Azure con HDInsight][hdinsight-storage].

È possibile accedere da HDInsight a un file archiviato nel contenitore del file system predefinito usando uno degli URI seguenti (in questo esempio si userà workflow.xml):

	wasb://mycontainer@mystorageaccount.blob.core.windows.net/tutorials/useoozie/workflow.xml
	wasb:///tutorials/useoozie/workflow.xml
	/tutorials/useoozie/workflow.xml

Per accedere al file direttamente dall'account di archiviazione, il nome del BLOB per questo file è:

	tutorials/useoozie/workflow.xml

**Informazioni sulle tabelle interne ed esterne di Hive**

Relativamente alle tabelle interne ed esterne di Hive è importante conoscere alcune informazioni:

- Il comando CREATE TABLE consente di creare una tabella interna, nota anche come tabella gestita. Il file di dati deve trovarsi nel contenitore predefinito.
- Il comando CREATE TABLE consente di spostare il file di dati nella cartella /hive/warehouse/<TableName> del contenitore predefinito.
- Il comando CREATE EXTERNAL TABLE consente di creare una tabella esterna. Il file di dati può trovarsi all'esterno del contenitore predefinito.
- Il comando CREATE EXTERNAL TABLE non consente di spostare il file di dati.
- Il comando CREATE EXTERNAL TABLE non consente di creare sottocartelle nella cartella specificata nella clausola LOCATION. È per questo motivo che nell'esercitazione si esegue una copia del file sample.log.

Per altre informazioni, vedere l'[articolo introduttivo alle tabelle interne ed esterne di Hive in HDInsight][cindygross-hive-tables].

**Operazioni preliminari all'esercitazione**

1. Aprire Windows PowerShell ISE (nella schermata Start di Windows 8 digitare **PowerShell_ISE** e quindi fare clic su **Windows PowerShell ISE**. Per altre informazioni, vedere la pagina dedicata all'[avvio di Windows PowerShell in Windows 8 e Windows][powershell-start].
2. Nel riquadro inferiore eseguire il comando seguente per connettersi alla sottoscrizione di Azure:

		Add-AzureAccount

	Verrà richiesto di immettere le credenziali dell'account Azure. Questo metodo, che prevede l'aggiunta di una connessione alla sottoscrizione, scade dopo 12 ore, di conseguenza sarà necessario eseguire nuovamente il cmdlet.

	> [AZURE.NOTE]Se si dispone di più sottoscrizioni di Azure e non si desidera usare quella predefinita, eseguire il cmdlet <strong>Select-AzureSubscription</strong> per selezionare una sottoscrizione.

3. Copiare lo script seguente nel riquadro di script e impostare le prime sei variabili:
			
		# WASB variables
		$storageAccountName = "<StorageAccountName>"
		$containerName = "<BlobStorageContainerName>"
		
		# SQL database variables
		$sqlDatabaseServer = "<SQLDatabaseServerName>"  
		$sqlDatabaseLogin = "<SQLDatabaseLoginName>"
		$sqlDatabaseLoginPassword = "SQLDatabaseLoginPassword>"
		$sqlDatabaseName = "<SQLDatabaseName>"  
		$sqlDatabaseTableName = "log4jLogsCount"
		
		# Oozie files for the tutorial	
		$hiveQLScript = "C:\Tutorials\UseOozie\useooziewf.hql"
		$workflowDefinition = "C:\Tutorials\UseOozie\workflow.xml"
		$coordDefinition =  "C:\Tutorials\UseOozie\coordinator.xml"
		
		# WASB folder for storing the Oozie tutorial files.
		$destFolder = "tutorials/useoozie"  # Do NOT use the long path here


	Per altre descrizioni di queste variabili, vedere la sezione [Prerequisiti](#prerequisites) di questa esercitazione.

3. Aggiungere il codice seguente allo script nel relativo riquadro:
		
		# Create a storage context object
		$storageaccountkey = get-azurestoragekey $storageAccountName | %{$_.Primary}
		$destContext = New-AzureStorageContext -StorageAccountName $storageAccountName -StorageAccountKey $storageaccountkey
		
		function uploadOozieFiles()
		{		
		    Write-Host "Copy HiveQL script, workflow definition and coordinator definition ..." -ForegroundColor Green
			Set-AzureStorageBlobContent -File $hiveQLScript -Container $containerName -Blob "$destFolder/useooziewf.hql" -Context $destContext
			Set-AzureStorageBlobContent -File $workflowDefinition -Container $containerName -Blob "$destFolder/workflow.xml" -Context $destContext
			Set-AzureStorageBlobContent -File $coordDefinition -Container $containerName -Blob "$destFolder/coordinator.xml" -Context $destContext
		}
				
		function prepareHiveDataFile()
		{
			Write-Host "Make a copy of the sample.log file ... " -ForegroundColor Green
			Start-CopyAzureStorageBlob -SrcContainer $containerName -SrcBlob "example/data/sample.log" -Context $destContext -DestContainer $containerName -destBlob "$destFolder/data/sample.log" -DestContext $destContext
		}
				
		function prepareSQLDatabase()
		{
			# SQL query string for creating log4jLogsCount table
			$cmdCreateLog4jCountTable = " CREATE TABLE [dbo].[$sqlDatabaseTableName](
				    [Level] [nvarchar](10) NOT NULL,
				    [Total] float,
				CONSTRAINT [PK_$sqlDatabaseTableName] PRIMARY KEY CLUSTERED   
				(
				[Level] ASC
				)
				)"
				
			#Create the log4jLogsCount table
		    Write-Host "Create Log4jLogsCount table ..." -ForegroundColor Green
			$conn = New-Object System.Data.SqlClient.SqlConnection
			$conn.ConnectionString = "Data Source=$sqlDatabaseServer.database.windows.net;Initial Catalog=$sqlDatabaseName;User ID=$sqlDatabaseLogin;Password=$sqlDatabaseLoginPassword;Encrypt=true;Trusted_Connection=false;"
			$conn.open()
			$cmd = New-Object System.Data.SqlClient.SqlCommand
			$cmd.connection = $conn
			$cmd.commandtext = $cmdCreateLog4jCountTable
			$cmd.executenonquery()
				
			$conn.close()
		}
				
		# upload workflow.xml, coordinator.xml, and ooziewf.hql
		uploadOozieFiles;
				
		# make a copy of example/data/sample.log to example/data/log4j/sample.log
		prepareHiveDataFile;
		
		# create log4jlogsCount table on SQL database
		prepareSQLDatabase;

4. Fare clic su **Esegui script** o premere **F5** per eseguire lo script. L'output sarà analogo al seguente:

	![Output delle operazioni preliminari all'esercitazione][img-preparation-output]

##<a id="run"></a>Eseguire il progetto Oozie

Attualmente Azure PowerShell non fornisce alcun cmdlet per la definizione dei processi Oozie. È possibile usare il cmdlet **Invoke-RestMethod** per richiamare i servizi Web di Oozie. L'API dei servizi Web di Oozie è una API HTTP REST JSON. Per altre informazioni sull'API dei servizi Web di Oozie, vedere la [documentazione di Apache Oozie 4.0][apache-oozie-400] (per cluster HDInsight versione 3.0) o la [documentazione di Apache Oozie 3.3.2][apache-oozie-332] (per cluster HDInsight versione 2.1).

**Per inviare un processo Oozie**

1. Aprire Windows PowerShell ISE (nella schermata Start di Windows 8 digitare **PowerShell_ISE** e quindi fare clic su **Windows PowerShell ISE**. Per altre informazioni, vedere la pagina dedicata all'[avvio di Windows PowerShell in Windows 8 e Windows][powershell-start].

3. Copiare lo script seguente nel riquadro di script e impostare le prime 14 variabili (ignorare **$storageUri**).

		#HDInsight cluster variables
		$clusterName = "<HDInsightClusterName>"
		$clusterUsername = "<HDInsightClusterUsername>"
		$clusterPassword = "<HDInsightClusterUserPassword>"
		
		#Azure Blob storage (WASB) variables
		$storageAccountName = "<StorageAccountName>"
		$storageContainerName = "<BlobContainerName>"
		$storageUri="wasb://$storageContainerName@$storageAccountName.blob.core.windows.net"
		
		#Azure SQL database variables
		$sqlDatabaseServer = "<SQLDatabaseServerName>"
		$sqlDatabaseLogin = "<SQLDatabaseLoginName>"
		$sqlDatabaseLoginPassword = "<SQLDatabaseloginPassword>"
		$sqlDatabaseName = "<SQLDatabaseName>"  
		
		#Oozie WF/coordinator variables
		$coordStart = "2014-03-21T13:45Z"
		$coordEnd = "2014-03-21T13:45Z"
		$coordFrequency = "1440"	# in minutes, 24h x 60m = 1440m
		$coordTimezone = "UTC"	#UTC/GMT

		$oozieWFPath="$storageUri/tutorials/useoozie"  # The default name is workflow.xml. And you don't need to specify the file name.
		$waitTimeBetweenOozieJobStatusCheck=10

		#Hive action variables
		$hiveScript = "$storageUri/tutorials/useoozie/useooziewf.hql"
		$hiveTableName = "log4jlogs"
		$hiveDataFolder = "$storageUri/tutorials/useoozie/data"
		$hiveOutputFolder = "$storageUri/tutorials/useoozie/output"
		
		#Sqoop action variables
		$sqlDatabaseConnectionString = "jdbc:sqlserver://$sqlDatabaseServer.database.windows.net;user=$sqlDatabaseLogin@$sqlDatabaseServer;password=$sqlDatabaseLoginPassword;database=$sqlDatabaseName"
		$sqlDatabaseTableName = "log4jLogsCount"

		$passwd = ConvertTo-SecureString $clusterPassword -AsPlainText -Force
		$creds = New-Object System.Management.Automation.PSCredential ($clusterUsername, $passwd)

	Per altre descrizioni di queste variabili, vedere la sezione [Prerequisiti](#prerequisites) di questa esercitazione.

	$coordstart e $coordend indicano l'ora di inizio e di fine del flusso di lavoro. Per individuare l'ora UTC/GMT, cercare "ora utc" in bing.com. Il valore di $coordFrequency corrisponde alla frequenza in minuti in base alla quale si vuole eseguire il flusso di lavoro.

3. Aggiungere il codice seguente allo script. Questa parte definisce il payload di Oozie:
		
		#OoziePayload used for Oozie web service submission
		$OoziePayload =  @"
		<?xml version="1.0" encoding="UTF-8"?>
		<configuration>
		
		   <property>
		       <name>nameNode</name>
		       <value>$storageUrI</value>
		   </property>
		
		   <property>
		       <name>jobTracker</name>
		       <value>jobtrackerhost:9010</value>
		   </property>
		
		   <property>
		       <name>queueName</name>
		       <value>default</value>
		   </property>
		
		   <property>
		       <name>oozie.use.system.libpath</name>
		       <value>true</value>
		   </property>

		   <property>
		       <name>oozie.coord.application.path</name>
		       <value>$oozieWFPath</value>
		   </property>

		   <property>
		       <name>wfPath</name>
		       <value>$oozieWFPath</value>
		   </property>

		   <property>
		       <name>coordStart</name>
		       <value>$coordStart</value>
		   </property>

		   <property>
		       <name>coordEnd</name>
		       <value>$coordEnd</value>
		   </property>

		   <property>
		       <name>coordFrequency</name>
		       <value>$coordFrequency</value>
		   </property>

		   <property>
		       <name>coordTimezone</name>
		       <value>$coordTimezone</value>
		   </property>

		   <property>
		       <name>hiveScript</name>
		       <value>$hiveScript</value>
		   </property>
		
		   <property>
		       <name>hiveTableName</name>
		       <value>$hiveTableName</value>
		   </property>
		
		   <property>
		       <name>hiveDataFolder</name>
		       <value>$hiveDataFolder</value>
		   </property>
		
		   <property>
		       <name>hiveOutputFolder</name>
		       <value>$hiveOutputFolder</value>
		   </property>
		
		   <property>
		       <name>sqlDatabaseConnectionString</name>
		       <value>&quot;$sqlDatabaseConnectionString&quot;</value>
		   </property>
		
		   <property>
		       <name>sqlDatabaseTableName</name>
		       <value>$SQLDatabaseTableName</value>
		   </property>
		
		   <property>
		       <name>user.name</name>
		       <value>admin</value>
		   </property>
		
		</configuration>
		"@

	>[AZURE.NOTE]La differenza principale rispetto al file di payload di invio del flusso di lavoro è la variabile **oozie.coord.application.path**. Quando si invia un processo del flusso di lavoro si usa invece la variabile **oozie.wf.application.path**.

4. Aggiungere il codice seguente allo script. Questa parte consente di verificare lo stato del servizio Web Oozie:
			
		function checkOozieServerStatus()
		{
		    Write-Host "Checking Oozie server status..." -ForegroundColor Green
		    $clusterUriStatus = "https://$clusterName.azurehdinsight.net:443/oozie/v2/admin/status"
		    $response = Invoke-RestMethod -Method Get -Uri $clusterUriStatus -Credential $creds -OutVariable $OozieServerStatus 
		    
		    $jsonResponse = ConvertFrom-Json (ConvertTo-Json -InputObject $response)
		    $oozieServerSatus = $jsonResponse[0].("systemMode")
		    Write-Host "Oozie server status is $oozieServerSatus..."
		
		    if($oozieServerSatus -notmatch "NORMAL")
		    {
		        Write-Host "Oozie server status is $oozieServerSatus...cannot submit Oozie jobs. Check the server status and re-run the job."
		        exit 1
		    }
		}
	
5. Aggiungere il codice seguente allo script. Questa parte consente di creare un processo Oozie:

		function createOozieJob()
		{
		    # create Oozie job
		    Write-Host "Sending the following Payload to the cluster:" -ForegroundColor Green
		    Write-Host "`n--------`n$OoziePayload`n--------"
		    $clusterUriCreateJob = "https://$clusterName.azurehdinsight.net:443/oozie/v2/jobs"
		    $response = Invoke-RestMethod -Method Post -Uri $clusterUriCreateJob -Credential $creds -Body $OoziePayload -ContentType "application/xml" -OutVariable $OozieJobName -debug -Verbose
		
		    $jsonResponse = ConvertFrom-Json (ConvertTo-Json -InputObject $response)
		    $oozieJobId = $jsonResponse[0].("id")
		    Write-Host "Oozie job id is $oozieJobId..."
		
		    return $oozieJobId
		}

	> [AZURE.NOTE]Quando si invia un processo del flusso di lavoro, è necessario creare un'altra chiamata del servizio Web per avviare il processo dopo che questo è stato creato. In tal caso, il processo del coordinatore viene attivato dal tempo. Il processo verrà avviato automaticamente.

6. Aggiungere il codice seguente allo script. Questa parte consente di verificare lo stato del processo Oozie:

		function checkOozieJobStatus($oozieJobId)
		{
		    # get job status
		    Write-Host "Sleeping for $waitTimeBetweenOozieJobStatusCheck seconds until the job metadata is populated in the Oozie metastore..." -ForegroundColor Green
		    Start-Sleep -Seconds $waitTimeBetweenOozieJobStatusCheck
		
		    Write-Host "Getting job status and waiting for the job to complete..." -ForegroundColor Green
		    $clusterUriGetJobStatus = "https://$clusterName.azurehdinsight.net:443/oozie/v2/job/" + $oozieJobId + "?show=info"
		    $response = Invoke-RestMethod -Method Get -Uri $clusterUriGetJobStatus -Credential $creds 
		    $jsonResponse = ConvertFrom-Json (ConvertTo-Json -InputObject $response)
		    $JobStatus = $jsonResponse[0].("status")
		
		    while($JobStatus -notmatch "SUCCEEDED|KILLED")
		    {
		        Write-Host "$(Get-Date -format 'G'): $oozieJobId is in $JobStatus state...waiting $waitTimeBetweenOozieJobStatusCheck seconds for the job to complete..."
		        Start-Sleep -Seconds $waitTimeBetweenOozieJobStatusCheck
		        $response = Invoke-RestMethod -Method Get -Uri $clusterUriGetJobStatus -Credential $creds 
		        $jsonResponse = ConvertFrom-Json (ConvertTo-Json -InputObject $response)
		        $JobStatus = $jsonResponse[0].("status")
		    }
		
		    Write-Host "$(Get-Date -format 'G'): $oozieJobId is in $JobStatus state!"
		    if($JobStatus -notmatch "SUCCEEDED")
		    {
		        Write-Host "Check logs at http://headnode0:9014/cluster for detais."
		        exit -1
		    }
		}

7. (Facoltativo) Aggiungere il codice seguente allo script.

		function listOozieJobs()
		{
		    Write-Host "Listing Oozie jobs..." -ForegroundColor Green
		    $clusterUriStatus = "https://$clusterName.azurehdinsight.net:443/oozie/v2/jobs"
		    $response = Invoke-RestMethod -Method Get -Uri $clusterUriStatus -Credential $creds 
		    
		    write-host "Job ID                                   App Name        Status      Started                         Ended"
		    write-host "----------------------------------------------------------------------------------------------------------------------------------"
		    foreach($job in $response.workflows)
		    {
		        Write-Host $job.id "`t" $job.appName "`t" $job.status "`t" $job.startTime "`t" $job.endTime
		    }
		}

		function ShowOozieJobLog($oozieJobId)
		{
		    Write-Host "Showing Oozie job info..." -ForegroundColor Green
		    $clusterUriStatus = "https://$clusterName.azurehdinsight.net:443/oozie/v2/job/$oozieJobId" + "?show=log"
		    $response = Invoke-RestMethod -Method Get -Uri $clusterUriStatus -Credential $creds 
		    write-host $response
		}

		function killOozieJob($oozieJobId)
		{
		    Write-Host "Killing the Oozie job $oozieJobId..." -ForegroundColor Green
		    $clusterUriStartJob = "https://$clusterName.azurehdinsight.net:443/oozie/v2/job/" + $oozieJobId + "?action=kill" #Valid values for the 'action' parameter are 'start', 'suspend', 'resume', 'kill', 'dryrun', 'rerun', and 'change'.
		    $response = Invoke-RestMethod -Method Put -Uri $clusterUriStartJob -Credential $creds | Format-Table -HideTableHeaders -debug
		}
  
7. Aggiungere il codice seguente allo script:

		checkOozieServerStatus
		# listOozieJobs
		$oozieJobId = createOozieJob($oozieJobId)
		checkOozieJobStatus($oozieJobId)
		# ShowOozieJobLog($oozieJobId)
		# killOozieJob($oozieJobId)

	Rimuovere i segni # se si desidera eseguire le funzioni aggiuntive.

7. Se si usa la versione 2.1 del cluster HDinsight, sostituire "https://$clusterName.azurehdinsight.net:443/oozie/v2/" con "https://$clusterName.azurehdinsight.net:443/oozie/v1/". La versione 2.1 del cluster HDInsight non supporta la versione 2 dei servizi Web.

7. Fare clic su **Esegui script** o premere **F5** per eseguire lo script. L'output sarà analogo al seguente:

	![Output dell'esecuzione del flusso di lavoro nell'esercitazione][img-runworkflow-output]

8. Connettersi al database SQL per visualizzare i dati esportati.

**Per verificare il log degli errori del processo**

Per risolvere i problemi relativi a un flusso di lavoro, consultare il file di log di Oozie in C:\apps\dist\oozie-3.3.2.1.3.2.0-05\oozie-win-distro\logs\Oozie.log dal nodo head del cluster. Per informazioni su RDP vedere [Amministrazione di cluster HDInsight tramite il portale di gestione][hdinsight-admin-portal].

**Per ripetere l'esecuzione dell'esercitazione**

Per eseguire nuovamente il flusso di lavoro sarà necessario eseguire i passaggi seguenti:

- Eliminare il file di output dello script Hive.
- Eliminare i dati nella tabella log4jLogsCount.

Di seguito è riportato un esempio di script di Windows PowerShell che è possibile usare:

	$storageAccountName = "<AzureStorageAccountName>"
	$containerName = "<ContainerName>"
	
	#SQL database variables
	$sqlDatabaseServer = "<SQLDatabaseServerName>"
	$sqlDatabaseLogin = "<SQLDatabaseLoginName>"
	$sqlDatabaseLoginPassword = "<SQLDatabaseLoginPassword>"
	$sqlDatabaseName = "<SQLDatabaseName>"
	$sqlDatabaseTableName = "log4jLogsCount"
	
	Write-host "Delete the Hive script output file ..." -ForegroundColor Green
	$storageaccountkey = get-azurestoragekey $storageAccountName | %{$_.Primary}
	$destContext = New-AzureStorageContext -StorageAccountName $storageAccountName -StorageAccountKey $storageaccountkey
	Remove-AzureStorageBlob -Context $destContext -Blob "tutorials/useoozie/output/000000_0" -Container $containerName
	
	Write-host "Delete all the records from the log4jLogsCount table ..." -ForegroundColor Green
	$conn = New-Object System.Data.SqlClient.SqlConnection
	$conn.ConnectionString = "Data Source=$sqlDatabaseServer.database.windows.net;Initial Catalog=$sqlDatabaseName;User ID=$sqlDatabaseLogin;Password=$sqlDatabaseLoginPassword;Encrypt=true;Trusted_Connection=false;"
	$conn.open()
	$cmd = New-Object System.Data.SqlClient.SqlCommand
	$cmd.connection = $conn
	$cmd.commandtext = "delete from $sqlDatabaseTableName"
	$cmd.executenonquery()
	
	$conn.close()


##<a id="nextsteps"></a>Passaggi successivi
In questa esercitazione si è appreso come definire un flusso di lavoro di Oozie e un coordinatore Oozie e come eseguire un processo del coordinatore Oozie con Azure PowerShell. Per altre informazioni, vedere gli articoli seguenti:

- [Introduzione all'uso di HDInsight][hdinsight-get-started]
- [Introduzione all'uso di HDInsight Emulator][hdinsight-get-started-emulator]
- [Usare l'archiviazione BLOB di Azure con HDInsight][hdinsight-storage]
- [Gestire cluster Hadoop in HDInsight tramite Azure PowerShell][hdinsight-admin-powershell]
- [Caricare dati in HDInsight][hdinsight-upload-data]
- [Usare Sqoop con Hadoop in HDInsight][hdinsight-use-sqoop]
- [Usare Hive con HDInsight][hdinsight-use-hive]
- [Usare Pig con HDInsight][hdinsight-use-pig]
- [Sviluppare programmi per la creazione di flussi Hadoop in C# per HDInsight][hdinsight-develop-streaming-jobs]
- [Sviluppare programmi MapReduce Java per HDInsight][hdinsight-develop-java-mapreduce]



[hdinsight-cmdlets-download]: http://go.microsoft.com/fwlink/?LinkID=325563


[hdinsight-versions]: hdinsight-component-versioning.md
[hdinsight-storage]: ../hdinsight-use-blob-storage.md
[hdinsight-get-started]: ../hdinsight-get-started.md
[hdinsight-admin-portal]: hdinsight-administer-use-management-portal.md


[hdinsight-use-sqoop]: hdinsight-use-sqoop.md
[hdinsight-provision]: hdinsight-provision-clusters.md
[hdinsight-admin-powershell]: hdinsight-administer-use-powershell.md
[hdinsight-upload-data]: hdinsight-upload-data.md
[hdinsight-use-hive]: hdinsight-use-hive.md
[hdinsight-use-pig]: hdinsight-use-pig.md
[hdinsight-storage]: ../hdinsight-use-blob-storage.md
[hdinsight-get-started-emulator]: ../hdinsight-get-started-emulator.md
[hdinsight-develop-streaming-jobs]: hdinsight-hadoop-develop-deploy-streaming-jobs.md
[hdinsight-develop-java-mapreduce]: hdinsight-develop-deploy-java-mapreduce.md
[hdinsight-use-oozie]: hdinsight-use-oozie.md

[sqldatabase-create-configue]: ../sql-database-create-configure.md
[sqldatabase-get-started]: ../sql-database-get-started.md

[azure-management-portal]: https://manage.windowsazure.com/
[azure-create-storageaccount]: ../storage-create-storage-account.md

[apache-hadoop]: http://hadoop.apache.org/
[apache-oozie-400]: http://oozie.apache.org/docs/4.0.0/
[apache-oozie-332]: http://oozie.apache.org/docs/3.3.2/

[powershell-download]: http://azure.microsoft.com/downloads/
[powershell-about-profiles]: http://go.microsoft.com/fwlink/?LinkID=113729
[powershell-install-configure]: ../powershell-install-configure.md
[powershell-start]: http://technet.microsoft.com/library/hh847889.aspx
[powershell-script]: http://technet.microsoft.com/library/ee176949.aspx

[cindygross-hive-tables]: http://blogs.msdn.com/b/cindygross/archive/2013/02/06/hdinsight-hive-internal-and-external-tables-intro.aspx

[img-workflow-diagram]: ./media/hdinsight-use-oozie-coordinator-time/HDI.UseOozie.Workflow.Diagram.png
[img-preparation-output]: ./media/hdinsight-use-oozie-coordinator-time/HDI.UseOozie.Preparation.Output1.png
[img-runworkflow-output]: ./media/hdinsight-use-oozie-coordinator-time/HDI.UseOozie.RunCoord.Output.png

[technetwiki-hive-error]: http://social.technet.microsoft.com/wiki/contents/articles/23047.hdinsight-hive-error-unable-to-rename.aspx
 

<!---HONumber=62-->