<properties
	pageTitle="Usare Oozie di Hadoop in HDInsight | Microsoft Azure"
	description="Usare Oozie di Hadoop in HDInsight, una servizio per Big Data. Informazioni su come definire un flusso di lavoro di Oozie e inviare un processo Oozie."
	services="hdinsight"
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
	ms.date="07/28/2015"
	ms.author="jgao"/>


# Usare Oozie con Hadoop per definire ed eseguire un flusso di lavoro in HDInsight

[AZURE.INCLUDE [oozie-selector](../../includes/hdinsight-oozie-selector.md)]

##Panoramica
Questo articolo illustra come usare Apache Oozie per definire un flusso di lavoro ed eseguirlo in HDInsight. Per informazioni sul coordinatore Oozie, vedere [Usare il coordinatore Oozie basato sul tempo con HDInsight][hdinsight-oozie-coordinator-time]. Per informazioni su Azure Data Factory, vedere [Usare Pig e Hive con Data factory][azure-data-factory-pig-hive].

##Informazioni su Oozie

Apache Oozie è un sistema di flusso di lavoro/coordinamento che consente di gestire i processi Hadoop. È integrato nello stack di Hadoop e supporta i processi Hadoop per Apache MapReduce, Apache Pig, Apache Hive e Apache Sqoop. Può anche essere usato per pianificare processi specifici di un sistema, come i programmi Java o gli script della shell.

Il flusso di lavoro che si implementerà seguendo le istruzioni di questa esercitazione contiene due azioni:

![Diagramma del flusso di lavoro][img-workflow-diagram]

1. Un'azione di Hive esegue uno script HiveQL per contare le occorrenze di ogni tipo di livello di log in un file log4j. Ogni file log4j è costituito da una riga di campi che contiene un campo [LOG LEVEL] per visualizzare il tipo e la gravità, ad esempio:

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

2.  Un'azione di Sqoop esporta l'output di HiveQL in una tabella di un database SQL di Azure. Per altre informazioni su Sqoop, vedere [Usare Sqoop con Hadoop in HDInsight][hdinsight-use-sqoop].

> [AZURE.NOTE]Per informazioni sulle versioni di Oozie supportate nei cluster HDInsight, vedere [Novità delle versioni cluster di Hadoop incluse in HDInsight][hdinsight-versions].

> [AZURE.NOTE]Questa esercitazione richiede HDInsight versione 3.0 o 2.1 e non è stata testata su HDInsight Emulator.


##Prerequisiti

Prima di iniziare questa esercitazione, è necessario disporre di quanto segue:

- **Workstation con Azure PowerShell**. Vedere [Installare e usare Azure PowerShell](http://azure.microsoft.com/documentation/videos/install-and-use-azure-powershell/). Per eseguire script di Windows PowerShell, è necessario accedere come amministratore e impostare i criteri di esecuzione su *RemoteSigned*. Per altre informazioni, vedere l'articolo relativo all'[esecuzione degli script di Windows PowerShell][powershell-script].
- **Un cluster HDInsight**. Per informazioni sulla creazione di un cluster HDInsight, vedere [Effettuare il provisioning di cluster Hadoop in HDInsight con opzioni personalizzate][hdinsight-provision] o [Introduzione all'uso di Hadoop con Hive in HDInsight per analizzare l'uso di telefoni cellulari][hdinsight-get-started]. Per completare l'esercitazione sono necessari i dati seguenti:

	<table border = "1">
	<tr><th>Proprietà del cluster</th><th>Nome variabile di Windows PowerShell</th><th>Valore</th><th>Descrizione</th></tr>
	<tr><td>Nome del cluster HDInsight</td><td>$clusterName</td><td></td><td>Cluster HDInsight su cui si eseguirà questa esercitazione.</td></tr>
	<tr><td>Nome utente del cluster HDInsight</td><td>$clusterUsername</td><td></td><td>Nome utente del cluster HDInsight. </td></tr>
	<tr><td>Password utente del cluster HDInsight </td><td>$clusterPassword</td><td></td><td>La password utente del cluster HDInsight.</td></tr>
	<tr><td>Nome dell'account di archiviazione di Azure</td><td>$storageAccountName</td><td></td><td>Un account di archiviazione di Azure disponibile per il cluster HDInsight. Ai fini di questa esercitazione, usare l'account di archiviazione predefinito, specificato durante il processo di provisioning del cluster.</td></tr>
	<tr><td>Nome del contenitore BLOB di Azure</td><td>$containerName</td><td></td><td>Per questo esempio, usare il nome del contenitore BLOB usato come file system predefinito del cluster HDInsight. Per impostazione predefinita, il nome del contenitore corrisponde al nome del cluster HDInsight.</td></tr>
	</table>

- **Un database SQL di Azure**. È necessario configurare una regola del firewall per il database SQL di Azure per consentire l'accesso dalla workstation. Per istruzioni sulla creazione di un database SQL Azure e la configurazione del firewall, vedere [Introduzione al database SQL di Microsoft Azure][sqldatabase-get-started]. Questo articolo fornisce uno script di Windows PowerShell per consentire la creazione della tabella del database SQL di Azure necessaria per questa esercitazione.

	<table border = "1">
	<tr><th>Proprietà del database SQL</th><th>Nome variabile di Windows PowerShell</th><th>Valore</th><th>Descrizione</th></tr>
	<tr><td>Nome del server di database SQL</td><td>$sqlDatabaseServer</td><td></td><td>Database SQL di Azure in cui Sqoop esporterà i dati. </td></tr>
	<tr><td>Nome di accesso al database SQL</td><td>$sqlDatabaseLogin</td><td></td><td>Nome di accesso al database SQL di Azure.</td></tr>
	<tr><td>Password di accesso al database SQL</td><td>$sqlDatabaseLoginPassword</td><td></td><td>Password di accesso al database SQL di Azure.</td></tr>
	<tr><td>Nome del database SQL</td><td>$sqlDatabaseName</td><td></td><td>Database SQL di Azure in cui Sqoop esporterà i dati. </td></tr>
	</table>

	> [AZURE.NOTE]Per impostazione predefinita, un database SQL di Azure consente connessioni da servizi di Azure, ad esempio Azure HDinsight. Se questa impostazione del firewall è disabilitata, sarà necessario abilitarla nel portale di Azure. Per istruzioni sulla creazione di un database SQL e sulla configurazione di regole del firewall, vedere [Come creare e configurare un database SQL di Azure][sqldatabase-create-configue].


> [AZURE.NOTE]L'inserimento dei valori nelle tabelle potrà essere utile per completare questa esercitazione.


##Definire il flusso di lavoro di Oozie e il relativo script HiveQL

Le definizioni dei flussi di lavoro di Oozie sono scritte in linguaggio hPDL (XML Process Definition Language). Il nome del file del flusso di lavoro predefinito è *workflow.xml*. Il file del flusso di lavoro verrà salvato a livello locale e distribuito nel cluster HDInsight tramite Windows PowerShell più avanti in questa esercitazione.

L'azione di Hive nel flusso di lavoro chiama un file di script HiveQL che contiene tre istruzioni HiveQL:

1. **L'istruzione DROP TABLE** consente di eliminare la tabella di Hive log4j, se esistente.
2. **L'istruzione CREATE TABLE** consente di creare una tabella di Hive log4j esterna che punti al percorso del file di log log4j. Il delimitatore di campo è ",". Il delimitatore di riga predefinito è "\\n". La tabella di Hive esterna viene usata per evitare che il file di dati venga rimosso dal percorso originale, nel caso in cui si desideri eseguire il flusso di lavoro di Oozie più volte.
3. **L'istruzione INSERT OVERWRITE** conta le occorrenze di ogni tipo di livello di log nella tabella di Hive log4j e salva l'output in un BLOB nell'archiviazione di Azure.

Il percorso di Hive è caratterizzato da un problema noto che si verifica durante l'invio di un processo Oozie. Le istruzioni per la correzione del problema sono disponibili nell'articolo della sezione Wiki di TechNet sull'[errore di HDInsight Hive "Impossibile rinominare"][technetwiki-hive-error].

**Per definire il file di script HiveQL che deve essere chiamato dal flusso di lavoro**

1. Creare un file di testo con il contenuto seguente:

		DROP TABLE ${hiveTableName};
		CREATE EXTERNAL TABLE ${hiveTableName}(t1 string, t2 string, t3 string, t4 string, t5 string, t6 string, t7 string) ROW FORMAT DELIMITED FIELDS TERMINATED BY ' ' STORED AS TEXTFILE LOCATION '${hiveDataFolder}';
		INSERT OVERWRITE DIRECTORY '${hiveOutputFolder}' SELECT t4 AS sev, COUNT(*) AS cnt FROM ${hiveTableName} WHERE t4 LIKE '[%' GROUP BY t4;

	Nello script vengono usate tre variabili:

	- ${hiveTableName}
	- ${hiveDataFolder}
	- ${hiveOutputFolder}

	Il file di definizione del flusso di lavoro (workflow.xml in questa esercitazione) passa questi valori allo script HiveQL in fase di esecuzione.

2. Salvare il file come **C:\\Tutorials\\UseOozie\\useooziewf.hql** usando la codifica **ANSI (ASCII)**. Usare il blocco note se l'editor di testo non offre questa opzione. Il file di script verrà distribuito nel cluster di HDInsight più avanti in questa esercitazione.



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

	Nel flusso di lavoro vengono definite due azioni. L'azione start-to è *RunHiveScript*. Se l'azione viene eseguita correttamente, l'azione successiva è *RunSqoopExport*.

	RunHiveScript è caratterizzato da diverse variabili. I valori verranno passati quando si invia il processo Oozie dalla workstation con Windows PowerShell.

	<table border = "1">
	<tr><th>Variabili del flusso di lavoro</th><th>Descrizione</th></tr>
	<tr><td>${jobTracker}</td><td>Specifica l'URL dell'utilità di analisi dei processi Hadoop. In HDInsight versione 3.0 o 2.1, usare <strong>jobtrackerhost: 9010</strong>.</td></tr>
	<tr><td>${nameNode}</td><td>Specifica l'URL del nodo dei nomi di Hadoop. Usare l'indirizzo del file system predefinito, ad esempio <i>wasb://&lt;containerName>@&lt;storageAccountName>.blob.core.windows.net</i>.</td></tr>
	<tr><td>${queueName}</td><td>Consente di specificare il nome della coda alla quale verrà inviato il processo. Usare il nome <strong>predefinito</strong>.</td></tr>
	</table>

	<table border = "1">
	<tr><th>Variabile azione Hive</th><th>Descrizione</th></tr>
	<tr><td>${hiveDataFolder}</td><td>Specifica la directory di origine per il comando Hive Create Table.</td></tr>
	<tr><td>${hiveOutputFolder}</td><td>Specifica la cartella di output per l'istruzione INSERT OVERWRITE.</td></tr>
	<tr><td>${hiveTableName}</td><td>Specifica il nome della tabella di Hive che fa riferimento ai file di dati log4j.</td></tr>
	</table>

	<table border = "1">
	<tr><th>Variabile azione Sqoop</th><th>Descrizione</th></tr>
	<tr><td>${sqlDatabaseConnectionString}</td><td>Specifica la stringa di connessione del database SQL di Azure.</td></tr>
	<tr><td>${sqlDatabaseTableName}</td><td>Specifica la tabella del database SQL di Azure in cui verranno esportati i dati.</td></tr>
	<tr><td>${hiveOutputFolder}</td><td>Specifica la cartella di output per l'istruzione INSERT OVERWRITE di Hive. È la stessa cartella dell'esportazione tramite Sqoop (export-dir).</td></tr>
	</table>

	Per altre informazioni sul flusso di lavoro di Oozie e sull'uso di azioni del flusso di lavoro, vedere la [documentazione di Apache Oozie 4.0][apache-oozie-400] (per HDInsight versione 3.0) o la [documentazione di Apache Oozie 3.3.2][apache-oozie-332] (per HDInsight versione 2.1).

2. Salvare il file come **C:\\Tutorials\\UseOozie\\workflow.xml** usando la codifica ANSI (ASCII). Usare il blocco note se l'editor di testo non offre questa opzione.

##Distribuzione del progetto Oozie e operazioni preliminari all'esercitazione

Eseguire uno script di Windows PowerShell per completare le attività seguenti:

- Copiare lo script HiveQL (useoozie.hql) nell'archiviazione di Azure (wasb:///tutorials/useoozie/useoozie.hql).
- Copiare il file workflow.xml in wasb:///tutorials/useoozie/workflow.xml.
- Copiare il file di dati (/example/data/sample.log) in wasb:///tutorials/useoozie/data/sample.log.
- Creare una tabella di database SQL di Azure per l'archiviazione dei dati di esportazione di Sqoop. Il nome della tabella è *log4jLogCount*.

**Informazioni sull'archiviazione in HDInsight**

HDInsight usa l'archiviazione BLOB di Azure per l'archiviazione dei dati. Per altre informazioni, vedere [Usare l'archivio BLOB di Azure con HDInsight][hdinsight-storage].

Quando si effettua il provisioning di un cluster HDInsight, vengono designati come file system predefinito un account di archiviazione di Azure e un contenitore di archivio BLOB specifico di tale account, proprio come in HDFS. Oltre a questo account di archiviazione, durante il processo di provisioning è possibile aggiungere anche altri account di archiviazione provenienti dalla stessa sottoscrizione di Azure o da sottoscrizioni di Azure diverse. Per istruzioni sull'aggiunta di altri account di archiviazione, vedere [Effettuare il provisioning di cluster Hadoop in HDInsight][hdinsight-provision]. Per semplificare lo script di Windows PowerShell usato in questa esercitazione, tutti i file vengono archiviati nel contenitore dell'archiviazione file predefinito, disponibile in */tutorials/useoozie*. Per impostazione predefinita, il nome di questo contenitore corrisponde al nome del cluster HDInsight. La sintassi è:

	wasb[s]://<ContainerName>@<StorageAccountName>.blob.core.windows.net/<path>/<filename>

> [AZURE.NOTE]Nei cluster HDInsight versione 3.0 è supportata solo la sintassi **wasb://*. La sintassi **asv://* precedente è supportata nei cluster HDInsight 2.1 e 1.6, ma non è supportata nei cluster HDInsight 3.0.

> [AZURE.NOTE]Il percorso wasb:// è un percorso virtuale. Per altre informazioni, vedere [Usare l'archivio BLOB di Azure con HDInsight][hdinsight-storage].

È possibile accedere da HDInsight a un file archiviato nel contenitore del file system predefinito usando uno degli URI seguenti (usare come esempio workflow.xml):

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

1. Aprire Windows PowerShell ISE. Nella schermata Start di Windows 8 digitare **PowerShell\_ISE** e quindi fare clic su **Windows PowerShell ISE**. Per altre informazioni, vedere la pagina dedicata all'[avvio di Windows PowerShell in Windows 8 e Windows][powershell-start].
2. Nel riquadro inferiore eseguire il comando seguente per connettersi alla sottoscrizione di Azure:

		Add-AzureAccount

	Verrà richiesto di immettere le credenziali dell'account Azure. Questo metodo, che prevede l'aggiunta di una connessione alla sottoscrizione, scade dopo 12 ore, di conseguenza sarà necessario eseguire nuovamente il cmdlet.

	> [AZURE.NOTE]Se si dispone di più sottoscrizioni di Azure e non si vuole usare quella predefinita, eseguire il cmdlet <strong>Select-AzureSubscription</strong> per selezionare la sottoscrizione corrente.

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
		$workflowDefinition = "C:\Tutorials\UseOozie\workflow.xml"
		$hiveQLScript = "C:\Tutorials\UseOozie\useooziewf.hql"

		# WASB folder for storing the Oozie tutorial files.
		$destFolder = "tutorials/useoozie"  # Do NOT use the long path here


	Per altre descrizioni di queste variabili, vedere la sezione [Prerequisiti](#prerequisites) di questa esercitazione.

3. Aggiungere il codice seguente allo script nel relativo riquadro:

		# Create a storage context object
		$storageaccountkey = get-azurestoragekey $storageAccountName | %{$_.Primary}
		$destContext = New-AzureStorageContext -StorageAccountName $storageAccountName -StorageAccountKey $storageaccountkey

		function uploadOozieFiles()
		{
		    Write-Host "Copy workflow definition and HiveQL script file ..." -ForegroundColor Green
			Set-AzureStorageBlobContent -File $workflowDefinition -Container $containerName -Blob "$destFolder/workflow.xml" -Context $destContext
			Set-AzureStorageBlobContent -File $hiveQLScript -Container $containerName -Blob "$destFolder/useooziewf.hql" -Context $destContext
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

##Eseguire il progetto Oozie

Attualmente Azure PowerShell non fornisce alcun cmdlet per la definizione dei processi Oozie. È possibile usare il cmdlet **Invoke-RestMethod** per richiamare i servizi Web di Oozie. L'API dei servizi Web di Oozie è una API HTTP REST JSON. Per altre informazioni sull'API dei servizi Web di Oozie, vedere la [documentazione di Apache Oozie 4.0][apache-oozie-400] (per HDInsight versione 3.0) o la [documentazione di Apache Oozie 3.3.2][apache-oozie-332] (per HDInsight versione 2.1).

**Per inviare un processo Oozie**

1. Aprire Windows PowerShell ISE. Nella schermata Start di Windows 8 digitare **PowerShell\_ISE** e quindi fare clic su **Windows PowerShell ISE**. Per altre informazioni, vedere la pagina dedicata all'[avvio di Windows PowerShell in Windows 8 e Windows][powershell-start].

3. Copiare lo script seguente nel riquadro di script e impostare le prime 10 variabili (ignorare la variabile $storageUri).

		#HDInsight cluster variables
		$clusterName = "<HDInsightClusterName>"
		$clusterUsername = "<HDInsightClusterUsername>"
		$clusterPassword = "<HDInsightClusterUserPassword>"

		#Azure Blob storage variables
		$storageAccountName = "<StorageAccountName>"
		$storageContainerName = "<BlobContainerName>"
		$storageUri="wasb://$storageContainerName@$storageAccountName.blob.core.windows.net"

		#Azure SQL database variables
		$sqlDatabaseServer = "<SQLDatabaseServerName>"
		$sqlDatabaseLogin = "<SQLDatabaseLoginName>"
		$sqlDatabaseLoginPassword = "<SQLDatabaseloginPassword>"
		$sqlDatabaseName = "<SQLDatabaseName>"  

		#Oozie WF variables
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

3. Aggiungere il codice seguente allo script. Questo script definisce il payload di Oozie.

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
		       <value>";$sqlDatabaseConnectionString";</value>
		   </property>

		   <property>
		       <name>sqlDatabaseTableName</name>
		       <value>$SQLDatabaseTableName</value>
		   </property>

		   <property>
		       <name>user.name</name>
		       <value>$clusterUsername</value>
		   </property>

		   <property>
		       <name>oozie.wf.application.path</name>
		       <value>$oozieWFPath</value>
		   </property>

		</configuration>
		"@

4. Aggiungere il codice seguente allo script. Questo script consente di verificare lo stato del servizio Web Oozie.

	    Write-Host "Checking Oozie server status..." -ForegroundColor Green
	    $clusterUriStatus = "https://$clusterName.azurehdinsight.net:443/oozie/v2/admin/status"
	    $response = Invoke-RestMethod -Method Get -Uri $clusterUriStatus -Credential $creds -OutVariable $OozieServerStatus

	    $jsonResponse = ConvertFrom-Json (ConvertTo-Json -InputObject $response)
	    $oozieServerSatus = $jsonResponse[0].("systemMode")
	    Write-Host "Oozie server status is $oozieServerSatus..."

5. Aggiungere il codice seguente allo script. Questa parte consente di creare e avviare un processo Oozie:

	    # create Oozie job
	    Write-Host "Sending the following Payload to the cluster:" -ForegroundColor Green
	    Write-Host "`n--------`n$OoziePayload`n--------"
	    $clusterUriCreateJob = "https://$clusterName.azurehdinsight.net:443/oozie/v2/jobs"
	    $response = Invoke-RestMethod -Method Post -Uri $clusterUriCreateJob -Credential $creds -Body $OoziePayload -ContentType "application/xml" -OutVariable $OozieJobName #-debug

	    $jsonResponse = ConvertFrom-Json (ConvertTo-Json -InputObject $response)
	    $oozieJobId = $jsonResponse[0].("id")
	    Write-Host "Oozie job id is $oozieJobId..."

	    # start Oozie job
	    Write-Host "Starting the Oozie job $oozieJobId..." -ForegroundColor Green
	    $clusterUriStartJob = "https://$clusterName.azurehdinsight.net:443/oozie/v2/job/" + $oozieJobId + "?action=start"
	    $response = Invoke-RestMethod -Method Put -Uri $clusterUriStartJob -Credential $creds | Format-Table -HideTableHeaders #-debug

6. Aggiungere il codice seguente allo script. Questo script consente di verificare lo stato del processo Oozie.

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

	    Write-Host "$(Get-Date -format 'G'): $oozieJobId is in $JobStatus state!" -ForegroundColor Green

7. Se si usa la versione 2.1 del cluster HDinsight, sostituire "https://$clusterName.azurehdinsight.net:443/oozie/v2/" con "https://$clusterName.azurehdinsight.net:443/oozie/v1/". La versione 2.1 del cluster HDInsight non supporta la versione 2 dei servizi Web.

8. Fare clic su **Esegui script** o premere **F5** per eseguire lo script. L'output sarà analogo al seguente:

	![Output dell'esecuzione del flusso di lavoro nell'esercitazione][img-runworkflow-output]

8. Connettersi al database SQL di Azure per visualizzare i dati esportati.

**Per verificare il log degli errori del processo**

Per risolvere i problemi relativi a un flusso di lavoro, consultare il file di log di Oozie in *C:\\apps\\dist\\oozie-3.3.2.1.3.2.0-05\\oozie-win-distro\\logs\\Oozie.log* o *C:\\apps\\dist\\oozie-4.0.0.2.0.7.0-1528\\oozie-win-distro\\logs\\Oozie.log* dal nodo head del cluster. Per altre informazioni su RDP, vedere [Gestire cluster Hadoop in HDInsight tramite il portale di anteprima di Azure][hdinsight-admin-portal].

**Per ripetere l'esecuzione dell'esercitazione**

Per eseguire nuovamente il flusso di lavoro sarà necessario eliminare gli elementi seguenti:

- Il file di output dello script Hive
- I dati nella tabella log4jLogsCount

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


##Passaggi successivi
In questa esercitazione si è appreso come definire un flusso di lavoro di Oozie e come eseguire un processo di Oozie con Windows PowerShell. Per altre informazioni, vedere gli articoli seguenti:

- [Usare il coordinatore Oozie basato sul tempo con HDInsight][hdinsight-oozie-coordinator-time]
- [Introduzione all'uso di Hadoop con Hive in HDInsight per analizzare l'uso di telefoni cellulari][hdinsight-get-started]
- [Introduzione all'uso di HDInsight Emulator][hdinsight-get-started-emulator]
- [Usare l'archivio BLOB di Azure con HDInsight][hdinsight-storage]
- [Amministrare HDInsight tramite PowerShell][hdinsight-admin-powershell]
- [Caricare dati per processi Hadoop in HDInsight][hdinsight-upload-data]
- [Usare Sqoop con Hadoop in HDInsight][hdinsight-use-sqoop]
- [Usare Hive con Hadoop in HDInsight][hdinsight-use-hive]
- [Usare Pig con Hadoop in HDInsight][hdinsight-use-pig]
- [Sviluppare programmi per la creazione di flussi Hadoop in C# per HDInsight][hdinsight-develop-streaming-jobs]
- [Sviluppare programmi MapReduce Java per HDInsight][hdinsight-develop-mapreduce]


[hdinsight-cmdlets-download]: http://go.microsoft.com/fwlink/?LinkID=325563



[azure-data-factory-pig-hive]: ../data-factory/data-factory-pig-hive-activities.md
[hdinsight-oozie-coordinator-time]: hdinsight-use-oozie-coordinator-time.md
[hdinsight-versions]: hdinsight-component-versioning.md
[hdinsight-storage]: ../hdinsight-use-blob-storage.md
[hdinsight-get-started]: ../hdinsight-get-started.md
[hdinsight-admin-portal]: hdinsight-administer-use-management-portal.md


[hdinsight-use-sqoop]: hdinsight-use-sqoop.md
[hdinsight-provision]: hdinsight-provision-clusters.md
[hdinsight-admin-powershell]: hdinsight-administer-use-powershell.md
[hdinsight-upload-data]: hdinsight-upload-data.md
[hdinsight-use-mapreduce]: hdinsight-use-mapreduce.md
[hdinsight-use-hive]: hdinsight-use-hive.md
[hdinsight-use-pig]: hdinsight-use-pig.md
[hdinsight-storage]: ../hdinsight-use-blob-storage.md
[hdinsight-get-started-emulator]: ../hdinsight-get-started-emulator.md

[hdinsight-develop-streaming-jobs]: hdinsight-hadoop-develop-deploy-streaming-jobs.md
[hdinsight-develop-mapreduce]: hdinsight-develop-deploy-java-mapreduce.md

[sqldatabase-create-configue]: ../sql-database-create-configure.md
[sqldatabase-get-started]: ../sql-database-get-started.md

[azure-management-portal]: https://portal.azure.com/
[azure-create-storageaccount]: ../storage-create-storage-account.md

[apache-hadoop]: http://hadoop.apache.org/
[apache-oozie-400]: http://oozie.apache.org/docs/4.0.0/
[apache-oozie-332]: http://oozie.apache.org/docs/3.3.2/

[powershell-download]: http://azure.microsoft.com/downloads/
[powershell-about-profiles]: http://go.microsoft.com/fwlink/?LinkID=113729
[powershell-install-configure]: ../powershell-install-configure.md
[powershell-start]: http://technet.microsoft.com/library/hh847889.aspx
[powershell-script]: https://technet.microsoft.com/it-IT/library/ee176961.aspx

[cindygross-hive-tables]: http://blogs.msdn.com/b/cindygross/archive/2013/02/06/hdinsight-hive-internal-and-external-tables-intro.aspx

[img-workflow-diagram]: ./media/hdinsight-use-oozie/HDI.UseOozie.Workflow.Diagram.png
[img-preparation-output]: ./media/hdinsight-use-oozie/HDI.UseOozie.Preparation.Output1.png
[img-runworkflow-output]: ./media/hdinsight-use-oozie/HDI.UseOozie.RunWF.Output.png

[technetwiki-hive-error]: http://social.technet.microsoft.com/wiki/contents/articles/23047.hdinsight-hive-error-unable-to-rename.aspx

<!---HONumber=Oct15_HO3-->