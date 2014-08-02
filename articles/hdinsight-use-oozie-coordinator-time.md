<properties linkid="hdinsight-use-time-based-oozie-coordinator-with-hdinsight" urlDisplayName="Use time-based Oozie Coordinator with HDInsight" pageTitle="Use time-based Oozie Coordinator with HDInsight | Microsoft Azure" metaKeywords="" description="Use time-based Oozie Coordinator with HDInsight, a big data solution. Learn how to define Oozie workflows and coordinators, and submit Oozie coordinator jobs." metaCanonical="" services="hdinsight" documentationCenter="" title="Use time-based Oozie Coordinator with HDInsight" authors="jgao" solutions="" manager="paulettm" editor="cgronlun" />

Utilizzo del coordinatore Oozie basato sul tempo con HDInsight
==============================================================

In questo articolo verrà illustrato come definire i flussi di lavoro e i coordinatori e come attivare i processi del coordinatore in base al tempo. Prima di procedere è utile consultare [Utilizzo di Oozie con HDInsight](../hdinsight-use-oozie/).

**Tempo previsto per il completamento:** 40 minuti

Contenuto dell'articolo
-----------------------

1.  [Informazioni su Oozie](#whatisoozie)
2.  [Prerequisiti](#prerequisites)
3.  [Definizione del file del flusso di lavoro di Oozie](#defineworkflow)
4.  [Distribuzione del progetto Oozie e operazioni preliminari all'esercitazione](#deploy)
5.  [Esecuzione del flusso di lavoro](#run)
6.  [Passaggi successivi](#nextsteps)

Informazioni su Oozie
---------------------

Apache Oozie è un sistema di flusso di lavoro/coordinamento che consente di gestire i processi Hadoop. È integrato nello stack di Hadoop e supporta i processi Hadoop per Apache MapReduce, Apache Pig, Apache Hive e Apache Sqoop. Può anche essere utilizzato per pianificare processi specifici di un sistema, come i programmi Java o gli script della shell.

Il flusso di lavoro che verrà implementato contiene due azioni:

![Diagramma del flusso di lavoro](./media/hdinsight-use-oozie-coordinator-time/HDI.UseOozie.Workflow.Diagram.png)

1.  Un'azione di Hive esegue uno script HiveQL per contare le occorrenze di ciascun tipo di livello di log in un file di log denominato log4j. Ogni log log4j è costituito da una riga di campi che contiene un campo [LOG LEVEL] per visualizzare il tipo e la gravità. Ad esempio:

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

    Per ulteriori informazioni su Hive, vedere [Utilizzo di Hive con HDInsight](/en-us/documentation/articles/hdinsight-use-hive/).

2.  Un'azione di Sqoop esporta l'output dell'azione di HiveQL in una tabella nel database SQL di Azure. Per ulteriori informazioni su Sqoop, vedere [Utilizzo di Sqoop con HDInsight](../hdinsight-use-sqoop/).

> [WACOM.NOTE] Per informazioni sulle versioni di Oozie supportate nei cluster HDInsight, vedere [Novità delle versioni cluster incluse con HDInsight](/en-us/documentation/articles/hdinsight-component-versioning/).

> [WACOM.NOTE] In questa esercitazione viene illustrato l'utilizzo di cluster HDInsight versione 2.1 e 3.0. Il contenuto di questo articolo non è stato testato su HDInsight Emulator.

Prerequisiti
------------

Prima di iniziare questa esercitazione, è necessario disporre di quanto segue:

-   **Una workstation** in cui sia stato installato e configurato Azure PowerShell. Per istruzioni, vedere [Come installare e configurare Azure PowerShell](/en-us/manage/install-and-configure-windows-powershell/). Per eseguire script di PowerShell, è necessario eseguire Azure PowerShell come amministratore e impostare i criteri di esecuzione su *RemoteSigned*. Vedere la pagina relativa all'[esecuzione di script di Windows PowerShell](http://technet.microsoft.com/en-us/library/ee176949.aspx).
-   **Un cluster HDInsight**. Per informazioni sulla creazione di un cluster HDInsight, vedere [Provisioning di cluster HDInsight](/en-us/documentation/articles/hdinsight-provision-clusters/) o [Introduzione a HDInsight](/en-us/documentation/articles/hdinsight-get-started/). Per completare l'esercitazione sono necessari i dati seguenti:

    <table data-morhtml="true" border="1">
	<tr data-morhtml="true"><th data-morhtml="true">Propriet&agrave; del cluster</th><th data-morhtml="true">Nome variabile di PowerShell</th><th data-morhtml="true">Valore</th><th data-morhtml="true">Descrizione</th></tr>
	<tr data-morhtml="true"><td data-morhtml="true">Nome del cluster HDInsight</td><td data-morhtml="true">$clusterName</td><td data-morhtml="true"></td><td data-morhtml="true">Cluster HDInsight su cui si eseguir&agrave; questa esercitazione.</td></tr>
	<tr data-morhtml="true"><td data-morhtml="true">Nome utente del cluster HDInsight</td><td data-morhtml="true">$clusterUsername</td><td data-morhtml="true"></td><td data-morhtml="true">Il nome utente del cluster HDInsight. </td></tr>
	<tr data-morhtml="true"><td data-morhtml="true">Password utente del cluster HDInsight </td><td data-morhtml="true">$clusterPassword</td><td data-morhtml="true"></td><td data-morhtml="true">La password utente del cluster HDInsight.</td></tr>
	<tr data-morhtml="true"><td data-morhtml="true">Nome dell'account di archiviazione di Azure</td><td data-morhtml="true">$storageAccountName</td><td data-morhtml="true"></td><td data-morhtml="true">Un account di archiviazione di Azure disponibile per il cluster HDInsight. Ai fini di questa esercitazione, utilizzare l'account di archiviazione predefinito, specificato durante il processo di provisioning del cluster.</td></tr>
	<tr data-morhtml="true"><td data-morhtml="true">Nome del contenitore BLOB di Azure</td><td data-morhtml="true">$containerName</td><td data-morhtml="true"></td><td data-morhtml="true">Per questo esempio, utilizzare il contenitore di archiviazione BLOB di Azure utilizzato come file system predefinito del cluster HDInsight. Per impostazione predefinita, il nome del contenitore corrisponde al nome del cluster HDInsight.</td></tr>
	</table>

-   **Un database SQL di Azure**. È necessario configurare una regola del firewall per il server di database SQL per consentire l'accesso dalla workstation. Per istruzioni sulla creazione di un database SQL e sulla configurazione del firewall, vedere l'[introduzione all'utilizzo del database SQL di Azure](../sql-database-get-started/). Questo articolo fornisce uno script PowerShell per la creazione della tabella di database SQL necessaria per questa esercitazione.

    <table data-morhtml="true" border="1">
	<tr data-morhtml="true"><th data-morhtml="true">Propriet&agrave; del database SQL</th><th data-morhtml="true">Nome variabile di PowerShell</th><th data-morhtml="true">Valore</th><th data-morhtml="true">Descrizione</th></tr>
	<tr data-morhtml="true"><td data-morhtml="true">Nome del server di database SQL</td><td data-morhtml="true">$sqlDatabaseServer</td><td data-morhtml="true"></td><td data-morhtml="true">Il server di database SQL nel quale Sqoop esporter&agrave; i dati. </td></tr>
	<tr data-morhtml="true"><td data-morhtml="true">Nome di accesso al database SQL</td><td data-morhtml="true">$sqlDatabaseLogin</td><td data-morhtml="true"></td><td data-morhtml="true">Il nome di accesso al database SQL.</td></tr>
	<tr data-morhtml="true"><td data-morhtml="true">Password di accesso al database SQL</td><td data-morhtml="true">$sqlDatabaseLoginPassword</td><td data-morhtml="true"></td><td data-morhtml="true">La password di accesso al database SQL.</td></tr>
	<tr data-morhtml="true"><td data-morhtml="true">Nome del database SQL</td><td data-morhtml="true">$sqlDatabaseName</td><td data-morhtml="true"></td><td data-morhtml="true">Il server di database SQL di Azure nel quale Sqoop esporter&agrave; i dati. </td></tr>
	</table>

    > [WACOM.NOTE] Per impostazione predefinita un database SQL di Azure consente connessioni da servizi di Azure, ad esempio Azure HDinsight. Se questa impostazione del firewall è disabilitata, sarà necessario abilitarla nel portale di gestione di Azure. Per istruzioni sulla creazione di un database SQL e sulla configurazione di regole del firewall, vedere [Come creare e configurare database SQL](../sql-database-create-configure/).

> [WACOM.NOTE] L'inserimento dei valori nelle tabelle potrà essere utile per completare questa esercitazione.

Definizione del flusso di lavoro di Oozie e del relativo script HiveQL
----------------------------------------------------------------------

Le definizioni dei flussi di lavoro di Oozie sono scritte in linguaggio hPDL (XML Process Definition Language). Il nome del file del flusso di lavoro predefinito è *workflow.xml*. Il file del flusso di lavoro verrà salvato a livello locale e distribuito nel cluster HDInsight con Azure PowerShell più avanti in questa esercitazione.

L'azione di Hive nel flusso di lavoro chiama un file di script HiveQL che contiene tre istruzioni HiveQL:

1.  **L'istruzione DROP TABLE** consente di eliminare la tabella Hive log4j, se esistente.
2.  **L'istruzione CREATE TABLE** consente di creare una tabella esterna Hive log4j che punti al
3.  percorso del file di log log4j. Il delimitatore di campo è ",". Il delimitatore di riga predefinito è "\\n". La tabella esterna di Hive viene utilizzata per evitare che il file di dati sia rimosso dal percorso originale, nel caso in cui si desideri eseguire il flusso di lavoro di Oozie più volte.
4.  **L'istruzione INSERT OVERWRITE** conta le occorrenze di ciascun tipo di livello di log nella tabella Hive log4j e salva l'output in un percorso di archiviazione BLOB di Azure (WASB).

Il percorso di Hive è caratterizzato da un problema noto che si verifica durante l'invio di un processo Oozie. Le istruzioni per la risoluzione del problema sono disponibili nella [Wiki di TechNet](http://social.technet.microsoft.com/wiki/contents/articles/23047.hdinsight-hive-error-unable-to-rename.aspx).

**Per definire il file di script HiveQL che sarà chiamato dal flusso di lavoro:**

1.  Creare un file di testo con il contenuto seguente:

         DROP TABLE ${hiveTableName};
         CREATE EXTERNAL TABLE ${hiveTableName}(t1 string, t2 string, t3 string, t4 string, t5 string, t6 string, t7 string) ROW FORMAT DELIMITED FIELDS TERMINATED BY ' ' STORED AS TEXTFILE LOCATION '${hiveDataFolder}';
         INSERT OVERWRITE DIRECTORY '${hiveOutputFolder}' SELECT t4 AS sev, COUNT(*) AS cnt FROM ${hiveTableName} WHERE t4 LIKE '[%' GROUP BY t4;

    Nello script vengono utilizzate tre variabili:

    -   ${hiveTableName}
    -   ${hiveDataFolder}
    -   ${hiveOutputFolder}

    Il file di definizione del flusso di lavoro (workflow.xml in questa esercitazione) passerà questi valori allo script di HiveQL al runtime.

2.  Salvare il file come **C:\\Tutorials\\UseOozie\\useooziewf.hql** utilizzando la codifica **ANSI (ASCII)** (utilizzare il Blocco note se l'editor di testo in uso non offre questa opzione). Il file di script verrà distribuito nel cluster di HDInsight più avanti in questa esercitazione.

**Per definire un flusso di lavoro**

1.  Creare un file di testo con il contenuto seguente:

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

    Nel flusso di lavoro vengono definite due azioni. L'azione start-to è *RunHiveScript*. Se l'azione esegue *ok*, l'azione successiva è *RunSqoopExport*.

    RunHiveScript è caratterizzato da diverse variabili. I valori verranno passati quando si invia un processo Oozie dalla workstation con Azure PowerShell.

    <table data-morhtml="true" border="1">
	<tr data-morhtml="true"><th data-morhtml="true">Variabili del flusso di lavoro</th><th data-morhtml="true">Descrizione</th></tr>
	<tr data-morhtml="true"><td data-morhtml="true">${jobTracker}</td><td data-morhtml="true">Specificare l'URL dell'analisi dei processi Hadoop. Utilizzare <strong data-morhtml="true">jobtrackerhost:9010</strong> sul cluster HDInsight versione 2.0 e 3.0.</td></tr>
	<tr data-morhtml="true"><td data-morhtml="true">${nameNode}</td><td data-morhtml="true">Specificare l'URL del namenode di Hadoop. Utilizzare l'indirizzo WASB predefinito del file system. Ad esempio, <i data-morhtml="true">wasb://&lt;nomecontenitore&gt;@&lt;nomeaccountdiarchiviazione&gt;.blob.core.windows.net</i>.</td></tr>
	<tr data-morhtml="true"><td data-morhtml="true">${queueName}</td><td data-morhtml="true">Consente di specificare il queuename al quale verr&agrave; inviato il processo. Utilizzare <strong data-morhtml="true">default</strong>.</td></tr>
	</table>
	
	<table data-morhtml="true" border="1">
	<tr data-morhtml="true"><th data-morhtml="true">Variabile azione Hive</th><th data-morhtml="true">Descrizione</th></tr>
	<tr data-morhtml="true"><td data-morhtml="true">${hiveDataFolder}</td><td data-morhtml="true">La directory di origine per il comando Hive Create Table.</td></tr>
	<tr data-morhtml="true"><td data-morhtml="true">${hiveOutputFolder}</td><td data-morhtml="true">La cartella di output per l'istruzione INSERT OVERWRITE.</td></tr>
	<tr data-morhtml="true"><td data-morhtml="true">${hiveTableName}</td><td data-morhtml="true">Il nome della tabella Hive che fa riferimento ai file di dati log4j.</td></tr>
	</table>
	
	<table data-morhtml="true" border="1">
	<tr data-morhtml="true"><th data-morhtml="true">Variabile azione Sqoop</th><th data-morhtml="true">Descrizione</th></tr>
	<tr data-morhtml="true"><td data-morhtml="true">${sqlDatabaseConnectionString}</td><td data-morhtml="true">Stringa di connessione del database SQL.</td></tr>
	<tr data-morhtml="true"><td data-morhtml="true">${sqlDatabaseTableName}</td><td data-morhtml="true">La tabella del database SQL in cui verranno esportati i dati.</td></tr>
	<tr data-morhtml="true"><td data-morhtml="true">${hiveOutputFolder}</td><td data-morhtml="true">La cartella di output per l'istruzione INSERT OVERWRITE di Hive. &Egrave; la stessa cartella dell'argomento export-dir di Sqoop Export.</td></tr>
	</table>

    Per ulteriori informazioni sul flusso di lavoro di Oozie e l'utilizzo di azioni del flusso di lavoro vedere la [documentazione di Apache Oozie 4.0](http://oozie.apache.org/docs/4.0.0/) (per il cluster HDInsight versione 3.0) o la [documentazione di Apache Oozie 3.3.2](http://oozie.apache.org/docs/3.3.2/) (per il cluster HDInsight versione 2.1).

2.  Salvare il file come **C:\\Tutorials\\UseOozie\\workflow.xml** utilizzando la codifica ANSI (ASCII) (utilizzare il Blocco note se l'editor di testo in uso non offre questa opzione).

**Per definire il coordinatore**

1.  Creare un file di testo con il contenuto seguente:

         <coordinator-app name="my_coord_app" frequency="${coordFrequency}" start="${coordStart}" end="${coordEnd}" timezone="${coordTimezone}" xmlns="uri:oozie:coordinator:0.4">
            <action>
               <workflow>
                  <app-path>${wfPath}</app-path>
               </workflow>
            </action>
         </coordinator-app>

    Nel file di definizione vengono utilizzate cinque variabili:

    Variabile | Descrizione 
	------------------|------------ 
	${coordFrequency} | Intervallo di pausa del processo. La frequenza è sempre espressa in minuti. 
	${coordStart}	  | Ora di inizio del processo. 
	${coordEnd} 	  | Ora di fine del processo. 
	${coordTimezone}  | Oozie elabora i processi del coordinatore in un fuso orario fisso senza DST (di solito UTC ); questo fuso orario è di solito definito "Fuso orario di elaborazione Oozie". 
	${wfPath} 		  | Il percorso del file workflow.xml. Se il nome file del flusso di lavoro non è quello predefinito (workflow.xml) sarà necessario specificarlo.

2.  Salvare il file come **C:\\Tutorials\\UseOozie\\coordinator.xml** utilizzando la codifica ANSI (ASCII) (utilizzare il Blocco note se l'editor di testo in uso non offre questa opzione).

Distribuzione del progetto Oozie e operazioni preliminari all'esercitazione
---------------------------------------------------------------------------

Eseguire uno script di Azure PowerShell per completare le seguenti attività:

-   Copiare lo script di HiveQL (useoozie.hql) nell'archivio BLOB di Azure, wasb:///tutorials/useoozie/useoozie.hql.
-   Copiare il file workflow.xml in wasb:///tutorials/useoozie/workflow.xml.
-   Copiare il file coordinator.xml in wasb:///tutorials/useoozie/coordinator.xml.
-   Copiare il file di dati (/example/data/sample.log) in wasb:///tutorials/useoozie/data/sample.log.
-   Creare un database SQL per l'archiviazione dei dati di esportazione Sqoop. Il nome della tabella è *log4jLogCount*.

**Informazioni sull'archiviazione in HDInsight**

HDInsight utilizza l'archivio BLOB di Azure per l'archiviazione dei dati. Questa risorsa è denominata *WASB* o *Archiviazione BLOB di Windows Azure*. WASB è l'implementazione Microsoft di HDFS sull'archivio BLOB di Azure. Per ulteriori informazioni, vedere [Utilizzo dell'archivio BLOB di Azure con HDInsight](/en-us/documentation/articles/hdinsight-use-blob-storage/).

Durante il provisioning di un cluster HDInsight, un account di archiviazione di Azure e uno specifico contenitore di archiviazione BLOB di tale account vengono designati come file system predefinito, come in HDFS. Durante il processo di provisioning è possibile aggiungere a tale account di archiviazione ulteriori account, dalla stessa sottoscrizione di Azure o da sottoscrizioni diverse. Per istruzioni sull'aggiunta di ulteriori account di archiviazione, vedere [Provisioning di cluster HDInsight](/en-us/documentation/articles/hdinsight-provision-clusters/). Per semplificare lo script di PowerShell utilizzato in questa esercitazione, tutti i file vengono archiviati nel contenitore del file system predefinito, presente in */tutorials/oozie*. Per impostazione predefinita, il nome di questo contenitore corrisponde al nome del cluster HDInsight. La sintassi WASB è la seguente:

    wasb[s]://<ContainerName>@<StorageAccountName>.blob.core.windows.net/<path>/<filename>

> [WACOM.NOTE] Nella versione 3.0 del cluster HDInsight è supportata solo la sintassi *wasb://*. La sintassi *asv://*, meno recente, è supportata nei cluster HDInsight 2.1 e 1.6, ma non è supportata nei cluster HDInsight 3.0 e non sarà supportata nelle versioni successive.

> [WACOM.NOTE] Il percorso WASB è un percorso virtuale. Per ulteriori informazioni, vedere [Utilizzo dell'archivio BLOB di Azure con HDInsight](/en-us/documentation/articles/hdinsight-use-blob-storage/).

È possibile accedere da HDInsight a un file archiviato nel contenitore del file system predefinito utilizzando uno degli URI seguenti (utilizzare come esempio workflow.xml):

    wasb://mycontainer@mystorageaccount.blob.core.windows.net/tutorials/useoozie/workflow.xml
    wasb:///tutorials/useoozie/workflow.xml
    /tutorials/useoozie/workflow.xml

Per accedere al file direttamente dall'account di archiviazione, il nome del BLOB per questo file è:

    tutorials/useoozie/workflow.xml

**Comprendere la tabella interna e la tabella esterna di Hive**

Vi sono alcune informazioni che è necessario conoscere riguardo la tabella interna ed esterna di Hive:

-   Il comando CREATE TABLE consente di creare una tabella interna, nota anche come tabella gestita. Il file di dati deve trovarsi nel contenitore predefinito.
-   Il comando CREATE TABLE consente di spostare il file di dati nella cartella /hive/warehouse/ sul contenitore predefinito.
-   Il comando CREATE EXTERNAL TABLE consente di creare una tabella esterna. Il file di dati può trovarsi all'esterno del contenitore predefinito.
-   Il comando CREATE EXTERNAL TABLE non consente di spostare il file di dati.
-   Il comando CREATE EXTERNAL TABLE non consente di creare sottocartelle nella cartella specificata nella clausola LOCATION. È per questo motivo che nell'esercitazione si esegue una copia del file sample.log.

Per ulteriori informazioni vedere l'articolo introduttivo a [HDInsight e alle tabelle interne ed esterne di Hive](http://blogs.msdn.com/b/cindygross/archive/2013/02/06/hdinsight-hive-internal-and-external-tables-intro.aspx).

**Operazioni preliminari all'esercitazione**

1.  Aprire Windows PowerShell ISE (nella schermata Start di Windows 8 digitare **PowerShell\_ISE** e quindi fare clic su **Windows PowerShell ISE**. Vedere la pagina dedicata all'[avvio di Windows PowerShell in Windows 8 e Windows](http://technet.microsoft.com/en-us/library/hh847889.aspx)).
2.  Nel riquadro inferiore eseguire il comando seguente per connettersi alla sottoscrizione di Azure:

         Add-AzureAccount

    Verrà richiesto di immettere le credenziali dell'account Azure. Questo metodo, che prevede l'aggiunta di una connessione alla sottoscrizione, scade dopo 12 ore, di conseguenza sarà necessario eseguire nuovamente il cmdlet.

    > [WACOM.NOTE] Nel caso in cui siano disponibili più sottoscrizioni di Azure e non si desideri utilizzare la sottoscrizione predefinita, utilizzare il cmdlet **Select-AzureSubscription** per selezionare la sottoscrizione corrente.

3.  Copiare lo script seguente nel riquadro di script e impostare le prime sei variabili

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

4.  Aggiungere il codice seguente allo script nel relativo riquadro:

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

5.  Fare clic su **Esegui script** o premere **F5** per eseguire lo script. L'output deve essere analogo al seguente:

    ![Output delle operazioni preliminari all'esercitazione](./media/hdinsight-use-oozie-coordinator-time/HDI.UseOozie.Preparation.Output1.png)

Esecuzione del progetto Oozie
-----------------------------

Attualmente Azure PowerShell non fornisce alcun cmdlet per la definizione dei processi Oozie. È possibile utilizzare il cmdlet Invoke-RestMethod PowerShell per richiamare i servizi Web di Oozie. L'API per servizi Web di Oozie è una API HTTP REST JSON. Per ulteriori informazioni sull'API per servizi Web di Oozie vedere la [documentazione di Apache Oozie 4.0](http://oozie.apache.org/docs/4.0.0/) (per il cluster HDInsight versione 3.0) o la [documentazione di Apache Oozie 3.3.2](http://oozie.apache.org/docs/3.3.2/) (per il cluster HDInsight versione 2.1).

**Per inviare un processo di Oozie**

1.  Aprire Windows PowerShell ISE (nella schermata Start di Windows 8 digitare **PowerShell\_ISE** e quindi fare clic su **Windows PowerShell ISE**. Vedere la pagina dedicata all'[avvio di Windows PowerShell in Windows 8 e Windows](http://technet.microsoft.com/en-us/library/hh847889.aspx)).

2.  Copiare lo script seguente nel riquadro di script e impostare le prime 14 variabili (ignorare la sesta, \$storageUri).

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
         $coordFrequency = "1440" # in minutes, 24h x 60m = 1440m
         $coordTimezone = "UTC"   #UTC/GMT

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

    $coordstart e $coordend indicano l'ora di inizio e di fine del flusso di lavoro. Per determinare l'ora UTC/GMT time, eseguire la ricerca di "ora utc" su bing.com. La variabile $coordFrequency indica la frequenza in minuti in base alla quale si desidera eseguire il flusso di lavoro.

3.  Aggiungere il codice seguente allo script. Questa parte definisce il payload di Oozie:

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
                <value>"$sqlDatabaseConnectionString"</value>
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

    > [WACOM.NOTE] La principale differenza rispetto al file di payload di invio del flusso di lavoro è la variabile **oozie.coord.application.path**. Quando si invia un processo del flusso di lavoro si utilizza invece la variabile **oozie.wf.application.path**.

4.  Aggiungere il codice seguente allo script. Questa parte consente di verificare lo stato del servizio Web Oozie:

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

5.  Aggiungere il codice seguente allo script. Questa parte consente di creare un processo Oozie:

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

    > [WACOM.NOTE] Quando si invia un processo del flusso di lavoro è necessario eseguire un'altra chiamata del servizio Web in seguito alla creazione del processo. In tal caso, il processo del coordinatore viene attivato dal tempo. Il processo verrà avviato automaticamente.

6.  Aggiungere il codice seguente allo script. Questa parte consente di verificare lo stato del processo Oozie:

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

7.  (Facoltativo) Aggiungere il codice seguente allo script.

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

8.  Aggiungere il codice seguente allo script:

         checkOozieServerStatus
         # listOozieJobs
         $oozieJobId = createOozieJob($oozieJobId)
         checkOozieJobStatus($oozieJobId)
         # ShowOozieJobLog($oozieJobId)
         # killOozieJob($oozieJobId)

    Rimuovere i segni \# se si desidera eseguire le funzioni aggiuntive.

9.  Se si utilizza la versione 2.1 del cluster HDinsight, sostituire "https://\$clusterName.azurehdinsight.net:443/oozie/v2/" con "https://\$clusterName.azurehdinsight.net:443/oozie/v1/". La versione 2.1 del cluster HDInsight non supporta la versione 2 dei servizi Web.

10. Fare clic su **Esegui script** o premere **F5** per eseguire lo script. L'output sarà analogo al seguente:

    ![Output dell'esecuzione del flusso di lavoro nell'esercitazione](./media/hdinsight-use-oozie-coordinator-time/HDI.UseOozie.RunCoord.Output.png)

11. Connettersi al database SQL per visualizzare i dati esportati.

**Per verificare il log degli errori del processo**

Per risolvere i problemi relativi a un flusso di lavoro, consultare il file di log di Oozie in C:\\apps\\dist\\oozie-3.3.2.1.3.2.0-05\\oozie-win-distro\\logs\\Oozie.log dal nodo head del cluster. Per informazioni su RDP vedere [Amministrazione di cluster HDInsight tramite il portale di gestione](/en-us/documentation/articles/hdinsight-administer-use-management-portal/).

**Per ripetere l'esecuzione dell'esercitazione**

Per eseguire nuovamente il flusso di lavoro sarà necessario eseguire i passaggi seguenti:

-   Eliminare il file di output dello script Hive
-   Eliminare i dati nella tabella log4jLogsCount

Di seguito è riportato un esempio di script di PowerShell che è possibile utilizzare:

    $storageAccountName = "<WindowsAzureStorageAccountName>"
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

Passaggi successivi
-------------------

In questa esercitazione si è appreso come definire un flusso di lavoro di Oozie e come eseguire un processo di Oozie con Azure PowerShell. Per ulteriori informazioni, vedere gli articoli seguenti:

-   [Introduzione all'utilizzo di HDInsight](/en-us/documentation/articles/hdinsight-get-started/)
-   [Introduzione a HDInsight Emulator](/en-us/documentation/articles/hdinsight-get-started-emulator/)
-   [Utilizzo dell'archivio BLOB di Azure con HDInsight](/en-us/documentation/articles/hdinsight-use-blob-storage/)
-   [Amministrazione di HDInsight tramite PowerShell](/en-us/documentation/articles/hdinsight-administer-use-powershell/)
-   [Caricamento di dati in HDInsight](/en-us/documentation/articles/hdinsight-upload-data/)
-   [Utilizzo di Sqoop con HDInsight.](../hdinsight-use-sqoop/)
-   [Utilizzo di Hive con HDInsight](/en-us/documentation/articles/hdinsight-use-hive/)
-   [Utilizzo di Pig con HDInsight](/en-us/documentation/articles/hdinsight-use-pig/)
-   [Sviluppo di programmi per la creazione di flussi Hadoop in C\# per HDInsight](/en-us/documentation/articles/hdinsight-hadoop-develop-deploy-streaming-jobs/)
-   [Sviluppo di programmi MapReduce Java per HDInsight](/en-us/documentation/articles/hdinsight-develop-deploy-java-mapreduce/)

[hdinsight-versions]:  /en-us/documentation/articles/hdinsight-component-versioning/
[hdinsight-storage]: /en-us/documentation/articles/hdinsight-use-blob-storage/
[hdinsight-get-started]: /en-us/documentation/articles/hdinsight-get-started/
[hdinsight-admin-portal]: /en-us/documentation/articles/hdinsight-administer-use-management-portal/


[hdinsight-sqoop]: ../hdinsight-use-sqoop/
[hdinsight-provision]: /en-us/documentation/articles/hdinsight-provision-clusters/

[hdinsight-admin-powershell]: /en-us/documentation/articles/hdinsight-administer-use-powershell/

[hdinsight-upload-data]: /en-us/documentation/articles/hdinsight-upload-data/

[hdinsight-mapreduce]: /en-us/documentation/articles/hdinsight-use-mapreduce/
[hdinsight-hive]: /en-us/documentation/articles/hdinsight-use-hive/

[hdinsight-pig]: /en-us/documentation/articles/hdinsight-use-pig/

[hdinsight-cmdlets-download]: http://go.microsoft.com/fwlink/?LinkID=325563
[hdinsight-storage]: /en-us/documentation/articles/hdinsight-use-blob-storage/

[hdinsight-emulator]: /en-us/documentation/articles/hdinsight-get-started-emulator/

[hdinsight-develop-streaming]: /en-us/documentation/articles/hdinsight-hadoop-develop-deploy-streaming-jobs/
[hdinsight-develop-mapreduce]: /en-us/documentation/articles/hdinsight-develop-deploy-java-mapreduce/
[hdinsight-oozie]: ../hdinsight-use-oozie/

[sqldatabase-create-configue]: ../sql-database-create-configure/
[sqldatabase-get-started]: ../sql-database-get-started/

[azure-management-portal]: https://manage.windowsazure.com/
[azure-create-storageaccount]: /en-us/manage/services/storage/how-to-create-a-storage-account/ 

[apache-hadoop]: http://hadoop.apache.org/
[apache-oozie-400]: http://oozie.apache.org/docs/4.0.0/
[apache-oozie-332]: http://oozie.apache.org/docs/3.3.2/

[powershell-download]: http://www.windowsazure.com/en-us/manage/downloads/
[powershell-about-profiles]: http://go.microsoft.com/fwlink/?LinkID=113729
[powershell-install-configure]: /en-us/manage/install-and-configure-windows-powershell/
[powershell-start]: http://technet.microsoft.com/en-us/library/hh847889.aspx
[powershell-script]: http://technet.microsoft.com/en-us/library/ee176949.aspx

[cindygross-hive-tables]: http://blogs.msdn.com/b/cindygross/archive/2013/02/06/hdinsight-hive-internal-and-external-tables-intro.aspx

[img-workflow-diagram]: ./media/hdinsight-use-oozie-coordinator-time/HDI.UseOozie.Workflow.Diagram.png
[img-preparation-output]: ./media/hdinsight-use-oozie-coordinator-time/HDI.UseOozie.Preparation.Output1.png  
[img-runworkflow-output]: ./media/hdinsight-use-oozie-coordinator-time/HDI.UseOozie.RunCoord.Output.png  

[technetwiki-hive-error]: http://social.technet.microsoft.com/wiki/contents/articles/23047.hdinsight-hive-error-unable-to-rename.aspx
