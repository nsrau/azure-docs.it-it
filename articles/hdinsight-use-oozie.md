<properties linkid="hdinsight-use-hadoop-oozie-in-hdinsight" urlDisplayName="Use Hadoop Oozie in HDInsight" pageTitle="Use Hadoop Oozie in HDInsight | Azure" metaKeywords="" description="Use Hadoop Oozie in HDInsight, a big data solution. Learn how to define an Oozie workflow, and submit an Oozie job." metaCanonical="" services="hdinsight" documentationCenter="" title="Use Hadop Oozie in HDInsight" authors="jgao" solutions="" manager="paulettm" editor="cgronlun" />

<tags ms.service="hdinsight" ms.workload="big-data" ms.tgt_pltfrm="na" ms.devlang="na" ms.topic="article" ms.date="01/01/1900" ms.author="jgao"></tags>

# Usare Oozie con Hadoop in HDInsight

Di seguito sono disponibili le informazioni necessarie per definire un flusso di lavoro ed eseguirlo in HDInsight. Per informazioni sul coordinatore Oozie, vedere [Usare il coordinatore Oozie basato sul tempo con HDInsight][].

**Tempo previsto per il completamento:** 40 minuti

## Contenuto dell'articolo

1.  [Informazioni su Oozie][]
2.  [Prerequisiti][]
3.  [Definizione del file del flusso di lavoro di Oozie][]
4.  [Distribuzione del progetto Oozie e operazioni preliminari all'esercitazione][]
5.  [Esecuzione del flusso di lavoro][]
6.  [Passaggi successivi][]

## <span id="whatisoozie"></span></a>Informazioni su Oozie

Apache Oozie è un sistema di flusso di lavoro/coordinamento che consente di gestire i processi Hadoop. È integrato nello stack di Hadoop e supporta i processi Hadoop per Apache MapReduce, Apache Pig, Apache Hive e Apache Sqoop. Può anche essere utilizzato per pianificare processi specifici di un sistema, come i programmi Java o gli script della shell.

Il flusso di lavoro che verrà implementato contiene due azioni:

![Diagramma del flusso di lavoro][]

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

    Per altre informazioni su Hive, vedere [Usare Hive con HDInsight][].

2.  Un'azione di Sqoop esporta l'output dell'azione di HiveQL in una tabella nel database SQL di Azure. Per altre informazioni su Sqoop, vedere [Usare Sqoop con HDInsight][].

> [WACOM.NOTE] Per informazioni sulle versioni di Oozie supportate nei cluster HDInsight, vedere [Novità delle versioni cluster incluse con HDInsight][].

> [WACOM.NOTE] In questa esercitazione viene illustrato l'utilizzo di cluster HDInsight versione 2.1 e 3.0. Il contenuto di questo articolo non è stato testato su HDInsight Emulator.

## <span id="prerequisites"></span></a>Prerequisiti

Prima di iniziare questa esercitazione, è necessario disporre di quanto segue:

-   **Una workstation** in cui sia stato installato e configurato Azure PowerShell. Per istruzioni, vedere [Come installare e configurare Azure PowerShell][]. Per eseguire script di PowerShell, è necessario eseguire Azure PowerShell come amministratore e impostare i criteri di esecuzione su *RemoteSigned*. Vedere la pagina relativa all'[esecuzione di script di Windows PowerShell][].
-   **Un cluster HDInsight**. Per informazioni sulla creazione di un cluster HDInsight, vedere [Provisioning di cluster HDInsight][] o [Introduzione all'utilizzo di HDInsight][]. Per completare l'esercitazione sono necessari i dati seguenti:

    <table>
    <colgroup>
    <col width="25%" />
    <col width="25%" />
    <col width="25%" />
    <col width="25%" />
    </colgroup>
    <thead>
    <tr class="header">
    <th align="left">Proprietà del cluster</th>
    <th align="left">Nome variabile di PowerShell</th>
    <th align="left">Valore</th>
    <th align="left">Descrizione</th>
    </tr>
    </thead>
    <tbody>
    <tr class="odd">
    <td align="left">Nome del cluster HDInsight</td>
    <td align="left">$clusterName</td>
    <td align="left"></td>
    <td align="left">Cluster HDInsight su cui si eseguirà questa esercitazione.</td>
    </tr>
    <tr class="even">
    <td align="left">Nome utente del cluster HDInsight</td>
    <td align="left">$clusterUsername</td>
    <td align="left"></td>
    <td align="left">Il nome utente del cluster HDInsight.</td>
    </tr>
    <tr class="odd">
    <td align="left">Password utente del cluster HDInsight</td>
    <td align="left">$clusterPassword</td>
    <td align="left"></td>
    <td align="left">La password utente del cluster HDInsight.</td>
    </tr>
    <tr class="even">
    <td align="left">Nome dell'account di archiviazione di Azure</td>
    <td align="left">$storageAccountName</td>
    <td align="left"></td>
    <td align="left">Un account di archiviazione di Azure disponibile per il cluster HDInsight. Ai fini di questa esercitazione, utilizzare l'account di archiviazione predefinito, specificato durante il processo di provisioning del cluster.</td>
    </tr>
    <tr class="odd">
    <td align="left">Nome del contenitore BLOB di Azure</td>
    <td align="left">$containerName</td>
    <td align="left"></td>
    <td align="left">Per questo esempio, utilizzare il contenitore di archiviazione BLOB di Azure utilizzato come file system predefinito del cluster HDInsight. Per impostazione predefinita, il nome del contenitore corrisponde al nome del cluster HDInsight.</td>
    </tr>
    </tbody>
    </table>

-   **Un database SQL di Azure**. È necessario configurare una regola del firewall per il server di database SQL per consentire l'accesso dalla workstation. Per istruzioni sulla creazione di un database SQL e sulla configurazione del firewall, vedere l'[introduzione all'utilizzo del database SQL di Azure][]. Questo articolo fornisce uno script PowerShell per la creazione della tabella di database SQL necessaria per questa esercitazione.

    <table>
    <colgroup>
    <col width="25%" />
    <col width="25%" />
    <col width="25%" />
    <col width="25%" />
    </colgroup>
    <thead>
    <tr class="header">
    <th align="left">Proprietà del database SQL</th>
    <th align="left">Nome variabile di PowerShell</th>
    <th align="left">Valore</th>
    <th align="left">Descrizione</th>
    </tr>
    </thead>
    <tbody>
    <tr class="odd">
    <td align="left">Nome del server di database SQL</td>
    <td align="left">$sqlDatabaseServer</td>
    <td align="left"></td>
    <td align="left">Il server di database SQL nel quale Sqoop esporterà i dati.</td>
    </tr>
    <tr class="even">
    <td align="left">Nome di accesso al database SQL</td>
    <td align="left">$sqlDatabaseLogin</td>
    <td align="left"></td>
    <td align="left">Il nome di accesso al database SQL.</td>
    </tr>
    <tr class="odd">
    <td align="left">Password di accesso al database SQL</td>
    <td align="left">$sqlDatabaseLoginPassword</td>
    <td align="left"></td>
    <td align="left">La password di accesso al database SQL.</td>
    </tr>
    <tr class="even">
    <td align="left">Nome del database SQL</td>
    <td align="left">$sqlDatabaseName</td>
    <td align="left"></td>
    <td align="left">Il server di database SQL di Azure nel quale Sqoop esporterà i dati.</td>
    </tr>
    </tbody>
    </table>

    > [WACOM.NOTE] Per impostazione predefinita un database SQL di Azure consente connessioni da servizi di Azure, ad esempio Azure HDinsight. Se questa impostazione del firewall è disabilitata, sarà necessario abilitarla nel portale di gestione di Azure. Per istruzioni sulla creazione di un database SQL e sulla configurazione di regole del firewall, vedere [Come creare e configurare database SQL][].

> [WACOM.NOTE] L'inserimento dei valori nelle tabelle potrà essere utile per completare questa esercitazione.

## <span id="defineworkflow"></span></a>Definizione del flusso di lavoro di Oozie e del relativo script HiveQL

Le definizioni dei flussi di lavoro di Oozie sono scritte in linguaggio hPDL (XML Process Definition Language). Il nome del file del flusso di lavoro predefinito è *workflow.xml*. Il file del flusso di lavoro verrà salvato a livello locale e distribuito nel cluster HDInsight con Azure PowerShell più avanti in questa esercitazione.

L'azione di Hive nel flusso di lavoro chiama un file di script HiveQL che contiene tre istruzioni HiveQL:

1.  **L'istruzione DROP TABLE** consente di eliminare la tabella Hive log4j, se esistente.
2.  **L'istruzione CREATE TABLE** permette di creare una tabella esterna Hive log4j che punti al percorso del file di log log4j. Il delimitatore di campo è ",". Il delimitatore di riga predefinito è "\\n". La tabella esterna di Hive viene utilizzata per evitare che il file di dati sia rimosso dal percorso originale, nel caso in cui si desideri eseguire il flusso di lavoro di Oozie più volte.
3.  **L'istruzione INSERT OVERWRITE** conta le occorrenze di ciascun tipo di livello di log nella tabella Hive log4j e salva l'output in un percorso di archiviazione BLOB di Azure (WASB).

Il percorso di Hive è caratterizzato da un problema noto che si verifica durante l'invio di un processo Oozie. Le istruzioni per la risoluzione del problema sono disponibili nella [Wiki di TechNet][].

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

    | Variabili del flusso di lavoro | Descrizione                                                                                                                                                                                       |
    |--------------------------------|---------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|
    | ${jobTracker}                  | Specificare l'URL dell'analisi dei processi Hadoop. Utilizzare **jobtrackerhost:9010** sul cluster HDInsight versione 2.0 e 3.0.                                                                  |
    | ${nameNode}                    | Specificare l'URL del namenode di Hadoop. Utilizzare l'indirizzo WASB predefinito del file system. Ad esempio, *wasb://\<nomecontenitore\>@\<nomeaccountdiarchiviazione\>.blob.core.windows.net*. |
    | ${queueName}                   | Consente di specificare il queuename al quale verrà inviato il processo. Utilizzare **default**.                                                                                                  |

    | Variabile azione Hive | Descrizione                                                          |
    |-----------------------|----------------------------------------------------------------------|
    | ${hiveDataFolder}     | La directory di origine per il comando Hive Create Table.            |
    | ${hiveOutputFolder}   | La cartella di output per l'istruzione INSERT OVERWRITE.             |
    | ${hiveTableName}      | Il nome della tabella Hive che fa riferimento ai file di dati log4j. |

    | Variabile azione Sqoop         | Descrizione                                                                                                                      |
    |--------------------------------|----------------------------------------------------------------------------------------------------------------------------------|
    | ${sqlDatabaseConnectionString} | Stringa di connessione del database SQL.                                                                                         |
    | ${sqlDatabaseTableName}        | La tabella del database SQL in cui verranno esportati i dati.                                                                    |
    | ${hiveOutputFolder}            | La cartella di output per l'istruzione INSERT OVERWRITE di Hive. È la stessa cartella dell'argomento export-dir di Sqoop Export. |

    Per ulteriori informazioni sul flusso di lavoro di Oozie e l'utilizzo di azioni del flusso di lavoro vedere la [documentazione di Apache Oozie 4.0][] (per il cluster HDInsight versione 3.0) o la [documentazione di Apache Oozie 3.3.2][] (per il cluster HDInsight versione 2.1).

2.  Salvare il file come **C:\\Tutorials\\UseOozie\\workflow.xml** utilizzando la codifica ANSI (ASCII) (utilizzare il Blocco note se l'editor di testo in uso non offre questa opzione).

## <span id="deploy"></span></a>Distribuzione del progetto Oozie e operazioni preliminari all'esercitazione

Eseguire uno script di Azure PowerShell per completare le seguenti attività:

-   Copiare lo script di HiveQL (useoozie.hql) nell'archivio BLOB di Azure, wasb:///tutorials/useoozie/useoozie.hql.
-   Copiare il file workflow.xml in wasb:///tutorials/useoozie/workflow.xml.
-   Copiare il file di dati (/example/data/sample.log) in wasb:///tutorials/useoozie/data/sample.log.
-   Creare un database SQL per l'archiviazione dei dati di esportazione Sqoop. Il nome della tabella è *log4jLogCount*.

**Informazioni sull'archiviazione in HDInsight**

HDInsight utilizza l'archivio BLOB di Azure per l'archiviazione dei dati. Questa risorsa è denominata *WASB* o *Archiviazione BLOB di Azure*. WASB è l'implementazione Microsoft di HDFS sull'archivio BLOB di Azure. Per altre informazioni, vedere [Usare l'archivio BLOB di Azure con HDInsight][].

Durante il provisioning di un cluster HDInsight, un account di archiviazione di Azure e uno specifico contenitore di archiviazione BLOB di tale account vengono designati come file system predefinito, come in HDFS. Durante il processo di provisioning è possibile aggiungere a tale account di archiviazione ulteriori account, dalla stessa sottoscrizione di Azure o da sottoscrizioni diverse. Per istruzioni sull'aggiunta di ulteriori account di archiviazione, vedere [Provisioning di cluster HDInsight][]. Per semplificare lo script di PowerShell utilizzato in questa esercitazione, tutti i file vengono archiviati nel contenitore del file system predefinito, presente in */tutorials/oozie*. Per impostazione predefinita, il nome di questo contenitore corrisponde al nome del cluster HDInsight.
La sintassi per WASB è la seguente:

    wasb[s]://<ContainerName>@<StorageAccountName>.blob.core.windows.net/<path>/<filename>

> [WACOM.NOTE] Nella versione 3.0 del cluster HDInsight è supportata solo la sintassi *wasb://*. La sintassi *asv://*, meno recente, è supportata nei cluster HDInsight 2.1 e 1.6, ma non è supportata nei cluster HDInsight 3.0 e non sarà supportata nelle versioni successive.

> [WACOM.NOTE] Il percorso WASB è un percorso virtuale. Per altre informazioni, vedere [Usare l'archivio BLOB di Azure con HDInsight][].

È possibile accedere da HDInsight a un file archiviato nel contenitore del file system predefinito utilizzando uno degli URI seguenti (utilizzare come esempio workflow.xml):

    wasb://mycontainer@mystorageaccount.blob.core.windows.net/tutorials/useoozie/workflow.xml
    wasb:///tutorials/useoozie/workflow.xml
    /tutorials/useoozie/workflow.xml

Per accedere al file direttamente dall'account di archiviazione, il nome del BLOB per questo file è:

    tutorials/useoozie/workflow.xml

**Comprendere la tabella interna e la tabella esterna di Hive**

Vi sono alcune informazioni che è necessario conoscere riguardo la tabella interna ed esterna di Hive:

-   Il comando CREATE TABLE consente di creare una tabella interna, nota anche come tabella gestita. Il file di dati deve trovarsi nel contenitore predefinito.
-   Il comando CREATE TABLE consente di spostare il file di dati nella cartella /hive/warehouse/<tablename> sul contenitore predefinito.
-   Il comando CREATE EXTERNAL TABLE consente di creare una tabella esterna. Il file di dati può trovarsi all'esterno del contenitore predefinito.
-   Il comando CREATE EXTERNAL TABLE non consente di spostare il file di dati.
-   Il comando CREATE EXTERNAL TABLE non consente di creare sottocartelle nella cartella specificata nella clausola LOCATION. È per questo motivo che nell'esercitazione si esegue una copia del file sample.log.

Per ulteriori informazioni vedere l'articolo introduttivo a [HDInsight e alle tabelle interne ed esterne di Hive][].

**Operazioni preliminari all'esercitazione**

1.  Aprire Windows PowerShell ISE (nella schermata Start di Windows 8 digitare **PowerShell\_ISE** e quindi fare clic su **Windows PowerShell ISE**. Vedere la pagina dedicata all'[avvio di Windows PowerShell in Windows 8 e Windows][]).
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
        $workflowDefinition = "C:\Tutorials\UseOozie\workflow.xml"
        $hiveQLScript = "C:\Tutorials\UseOozie\useooziewf.hql"

        # WASB folder for storing the Oozie tutorial files.
        $destFolder = "tutorials/useoozie"  # Do NOT use the long path here

    Per altre descrizioni di queste variabili, vedere la sezione [Prerequisiti][] di questa esercitazione.

4.  Aggiungere il codice seguente allo script nel relativo riquadro:

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

5.  Fare clic su **Esegui script** o premere **F5** per eseguire lo script. L'output deve essere analogo al seguente:

    ![Output delle operazioni preliminari all'esercitazione][]

## <span id="run"></span></a>Esecuzione del progetto Oozie

Attualmente Azure PowerShell non fornisce alcun cmdlet per la definizione dei processi Oozie. È possibile usare
il cmdlet Invoke-RestMethod di PowerShell per richiamare i servizi Web di Oozie. L'API per servizi Web di Oozie è una API HTTP REST JSON. Per ulteriori informazioni sull'API per servizi Web di Oozie vedere la [documentazione di Apache Oozie 4.0][] (per il cluster HDInsight versione 3.0) o la [documentazione di Apache Oozie 3.3.2][] (per il cluster HDInsight versione 2.1).

**Per inviare un processo di Oozie**

1.  Aprire Windows PowerShell ISE (nella schermata Start di Windows 8 digitare **PowerShell\_ISE** e quindi fare clic su **Windows PowerShell ISE**. Vedere la pagina dedicata all'[avvio di Windows PowerShell in Windows 8 e Windows][]).

2.  Copiare lo script seguente nel riquadro di script e impostare le prime 10 variabili (ignorare la sesta, $storageUri).

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

    Per altre descrizioni di queste variabili, vedere la sezione [Prerequisiti][] di questa esercitazione.

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
               <value>$clusterUsername</value>
           </property>

           <property>
               <name>oozie.wf.application.path</name>
               <value>$oozieWFPath</value>
           </property>

        </configuration>
        "@

4.  Aggiungere il codice seguente allo script. Questa parte consente di verificare lo stato del servizio Web Oozie:

        Write-Host "Checking Oozie server status..." -ForegroundColor Green
        $clusterUriStatus = "https://$clusterName.azurehdinsight.net:443/oozie/v2/admin/status"
        $response = Invoke-RestMethod -Method Get -Uri $clusterUriStatus -Credential $creds -OutVariable $OozieServerStatus 

        $jsonResponse = ConvertFrom-Json (ConvertTo-Json -InputObject $response)
        $oozieServerSatus = $jsonResponse[0].("systemMode")
        Write-Host "Oozie server status is $oozieServerSatus..."

5.  Aggiungere il codice seguente allo script. Questa parte consente di creare e avviare un processo Oozie:

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

6.  Aggiungere il codice seguente allo script. Questa parte consente di verificare lo stato del processo Oozie:

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

7.  Se si usa la versione 2.1 del cluster HDinsight, sostituire "https://$clusterName.azurehdinsight.net:443/oozie/v2/" con "https://$clusterName.azurehdinsight.net:443/oozie/v1/". La versione 2.1 del cluster HDInsight non supporta la versione 2 dei servizi Web.

8.  Fare clic su **Esegui script** o premere **F5** per eseguire lo script. L'output sarà analogo al seguente:

    ![Output dell'esecuzione del flusso di lavoro nell'esercitazione][]

9.  Connettersi al database SQL per visualizzare i dati esportati.

**Per verificare il log degli errori del processo**

Per risolvere i problemi relativi a un flusso di lavoro, consultare il file di log di Oozie in
*C:\\apps\\dist\\oozie-3.3.2.1.3.2.0-05\\oozie-win-distro\\logs\\Oozie.log* or
C:\\apps\\dist\\oozie-4.0.0.2.0.7.0-1528\\oozie-win-distro\\logs\\Oozie.log dal nodo head del cluster. Per informazioni su RDP vedere [Amministrazione di cluster HDInsight tramite il portale di gestione][].

**Per ripetere l'esecuzione dell'esercitazione**

Per eseguire nuovamente il flusso di lavoro sarà necessario eseguire i passaggi seguenti:

-   Eliminare il file di output dello script Hive
-   Eliminare i dati nella tabella log4jLogsCount

Di seguito è riportato un esempio di script di PowerShell che è possibile utilizzare:

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

## <span id="nextsteps"></span></a>Passaggi successivi

In questa esercitazione si è appreso come definire un flusso di lavoro di Oozie e come eseguire un processo di Oozie con Azure PowerShell. Per ulteriori informazioni, vedere gli articoli seguenti:

-   [Usare il coordinatore Oozie basato sul tempo con HDInsight][]
-   [Introduzione all'utilizzo di HDInsight][]
-   [Introduzione all'utilizzo di HDInsight Emulator][]
-   [Usare l'archivio BLOB di Azure con HDInsight][]
-   [Amministrazione di HDInsight tramite PowerShell][]
-   [Caricare i dati in HDInsight][]
-   [Usare Sqoop con HDInsight][]
-   [Usare Hive con HDInsight][]
-   [Usare Pig con HDInsight][]
-   [Sviluppare programmi per la creazione di flussi Hadoop in C# per HDInsight][]
-   [Sviluppare programmi MapReduce Java per HDInsight][]

  [Usare il coordinatore Oozie basato sul tempo con HDInsight]: ../hdinsight-use-oozie-coordinator-time/
  [Informazioni su Oozie]: #whatisoozie
  [Prerequisiti]: #prerequisites
  [Definizione del file del flusso di lavoro di Oozie]: #defineworkflow
  [Distribuzione del progetto Oozie e operazioni preliminari all'esercitazione]: #deploy
  [Esecuzione del flusso di lavoro]: #run
  [Passaggi successivi]: #nextsteps
  [Diagramma del flusso di lavoro]: ./media/hdinsight-use-oozie/HDI.UseOozie.Workflow.Diagram.png
  [Usare Hive con HDInsight]: ../hdinsight-use-hive/
  [Usare Sqoop con HDInsight]: ../hdinsight-use-sqoop/
  [Novità delle versioni cluster incluse con HDInsight]: ../hdinsight-component-versioning/
  [Come installare e configurare Azure PowerShell]: ../install-and-configure-powershell/
  [esecuzione di script di Windows PowerShell]: http://technet.microsoft.com/en-us/library/ee176949.aspx
  [Provisioning di cluster HDInsight]: ../hdinsight-provision-clusters/
  [Introduzione all'utilizzo di HDInsight]: ../hdinsight-get-started/
  [introduzione all'utilizzo del database SQL di Azure]: ../sql-database-get-started/
  [Come creare e configurare database SQL]: ../sql-database-create-configure/
  [Wiki di TechNet]: http://social.technet.microsoft.com/wiki/contents/articles/23047.hdinsight-hive-error-unable-to-rename.aspx
  [documentazione di Apache Oozie 4.0]: http://oozie.apache.org/docs/4.0.0/
  [documentazione di Apache Oozie 3.3.2]: http://oozie.apache.org/docs/3.3.2/
  [Usare l'archivio BLOB di Azure con HDInsight]: ../hdinsight-use-blob-storage/
  [HDInsight e alle tabelle interne ed esterne di Hive]: http://blogs.msdn.com/b/cindygross/archive/2013/02/06/hdinsight-hive-internal-and-external-tables-intro.aspx
  [avvio di Windows PowerShell in Windows 8 e Windows]: http://technet.microsoft.com/en-us/library/hh847889.aspx
  [Output delle operazioni preliminari all'esercitazione]: ./media/hdinsight-use-oozie/HDI.UseOozie.Preparation.Output1.png
  [Output dell'esecuzione del flusso di lavoro nell'esercitazione]: ./media/hdinsight-use-oozie/HDI.UseOozie.RunWF.Output.png
  [Amministrazione di cluster HDInsight tramite il portale di gestione]: ../hdinsight-administer-use-management-portal/
  [Introduzione all'utilizzo di HDInsight Emulator]: ../hdinsight-get-started-emulator/
  [Amministrazione di HDInsight tramite PowerShell]: ../hdinsight-administer-use-powershell/
  [Caricare i dati in HDInsight]: ../hdinsight-upload-data/
  [Usare Pig con HDInsight]: ../hdinsight-use-pig/
  [Sviluppare programmi per la creazione di flussi Hadoop in C# per HDInsight]: ../hdinsight-hadoop-develop-deploy-streaming-jobs/
  [Sviluppare programmi MapReduce Java per HDInsight]: ../hdinsight-develop-deploy-java-mapreduce/
