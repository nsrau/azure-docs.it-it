<properties 
	pageTitle="Eseguire un processo Hadoop usando DocumentDB e HDInsight | Microsoft Azure" 
	description="Informazioni su come eseguire un semplice processo Hive, Pig e MapReduce con DocumentDB e Azure HDInsight."
	services="documentdb" 
	authors="AndrewHoh" 
	manager="jhubbard" 
	editor="mimig"
	documentationCenter=""/>


<tags 
	ms.service="documentdb" 
	ms.workload="data-services" 
	ms.tgt_pltfrm="na" 
	ms.devlang="java" 
	ms.topic="article" 
	ms.date="10/28/2015" 
	ms.author="anhoh"/>

#<a name="DocumentDB-HDInsight"></a>Eseguire un processo Hadoop usando DocumentDB e HDInsight

Questa esercitazione illustra come eseguire processi [Apache Hive][apache-hive], [Apache Pig][apache-pig] e [Apache Hadoop][apache-hadoop] MapReduce in Azure HDInsight con il connettore Hadoop di DocumentDB. Il connettore Hadoop di DocumentDB consente a quest'ultimo di fungere da origine e sink per i processi Hive, Pig e MapReduce. Questa esercitazione userà DocumentDB come origine e destinazione dati per i processi Hadoop.

Dopo aver completato questa esercitazione, si potrà rispondere alle domande seguenti:

- Come si caricano i dati da DocumentDB usando un processo Hive, Pig o MapReduce?
- Come si archiviano i dati in DocumentDB usando un processo Hive, Pig o MapReduce?

Si consiglia di iniziare guardando il video seguente, dove viene illustrato un processo Hive usando DocumentDB e HDInsight.

> [AZURE.VIDEO use-azure-documentdb-hadoop-connector-with-azure-hdinsight]

Quindi, tornare a questo articolo, dove si riceveranno tutti i dettagli su come eseguire i processi di analisi sui dati di DocumentDB.

> [AZURE.TIP] Questa esercitazione presume che l'utente abbia una precedente esperienza nell'uso di Apache Hadoop, Hive e/o Pig. Se non si conoscono Apache Hadoop, Hive e Pig, si consiglia di visitare la pagina relativa alla [documentazione di Apache Hadoop][apache-hadoop-doc]. Questa esercitazione presume inoltre che si abbia precedente esperienza con DocumentDB e si disponga di un account DocumentDB. Se non si conosce DocumentDB o non si dispone di un account DocumentDB, visitare la pagina [Per iniziare][getting-started].

Se non si ha tempo di completare l'esercitazione e si vogliono ottenere gli script PowerShell completi di esempio per Hive, Pig e MapReduce, È possibile ottenerli [qui][documentdb-hdinsight-samples]. Il download contiene anche i file hql, pig e java per questi esempi.

## <a name="NewestVersion"></a>Versione più recente

<table border='1'>
	<tr><th>Versione di Hadoop Connector</th>
		<td>1.1.0</td></tr>
	<tr><th>URI script</th>
		<td>https://portalcontent.blob.core.windows.net/scriptaction/documentdb-hadoop-installer-v03.ps1</td></tr>
	<tr><th>Data ultima modifica</th>
		<td>07/20/2015</td></tr>
	<tr><th>Versioni supportate di HDInsight</th>
		<td>3.1, 3.2.</td></tr>
	<tr><th>Registro modifiche</th>
		<td>Aggiornamento di DocumentDB Java SDK alla versione 1.1.0</br>
			Rimozione del parametro di output aggiuntivo per i percorsi di indicizzazione personalizzati</br>
			Aggiunta del parametro facoltativo per la precisione delle stringhe personalizzate (-1 per impostazione predefinita)</br>
			6/11/2015</br>
			Risolta compatibilità del connettore con <a href="https://www.microsoft.com/download/details.aspx?id=40886">driver Microsoft Hive ODBC</a></br>
			Ulteriore possibilità di cambiare tipo di offerta di output insieme (offerta S3 per impostazione predefinita)</br>
		</td></tr>
</table>

## <a name="Prerequisites"></a>Prerequisiti
Prima di seguire le istruzioni di questa esercitazione, verificare che siano disponibili gli elementi seguenti:

- Un DocumentDB, un database e una raccolta di documenti all'interno. Per altre informazioni, vedere [Introduzione a DocumentDB][getting-started]. Importare dati di esempio nell'account di DocumentDB con lo [strumento di importazione DocumentDB][documentdb-import-data].
- Velocità effettiva. Le letture e le scritture da HDInsight verranno conteggiate rispetto alle unità di richiesta dell'unità di capacità. Per altre informazioni, vedere [Velocità effettiva con provisioning, unità di richiesta e operazioni di database][documentdb-manage-throughput].
- Capacità di una stored procedure aggiuntiva all'interno di ciascun insieme di output. Le stored procedure vengono usate per trasferire i documenti risultanti. Per altre informazioni, vedere [Raccolte e velocità effettiva con provisioning][documentdb-manage-document-storage].
- Capacità per i documenti risultanti da processi Hive, Pig o MapReduce. Per altre informazioni, vedere [Gestire la capacità e le prestazioni di DocumentDB][documentdb-manage-collections].
- *Facoltativo* Capacità per una raccolta aggiuntiva. Per altre informazioni, vedere [Archiviazione documenti con provisioning e sovraccarico dell'indice][documentdb-manage-document-storage].
	
> [AZURE.WARNING] Per evitare la creazione di una nuova raccolta durante uno qualsiasi dei processi è possibile stampare i risultati in stdout, salvare l'output nel contenitore WASB oppure specificare una raccolta già esistente. Nel caso in cui venga specificata una raccolta esistente, verranno creati nuovi documenti all'interno della raccolta, mentre i documenti esistenti verranno interessati solo se si verifica un conflitto tra gli *ids*. **Il connettore sovrascriverà automaticamente i documenti esistenti con conflitti tra ID**. È possibile disattivare questa funzionalità impostando l'opzione upsert su false. Se upsert è impostato su false e si verifica un conflitto, il processo Hadoop avrà esito negativo, indicando un errore di conflitto tra ID.

## <a name="CreateStorage"></a>Passaggio 1: creare un account di archiviazione di Azure

> [AZURE.IMPORTANT] Se si ha **già** un account di archiviazione di Azure e si vuole creare un nuovo contenitore BLOB all'interno di tale account, passare direttamente al [Passaggio 2: creare un cluster HDInsight personalizzato](#ProvisionHDInsight).

Azure HDInsight usa l'archiviazione BLOB di Azure per l'archiviazione dei dati. Questa risorsa è denominata *WASB* o *Archiviazione BLOB di Azure*. WASB è l'implementazione Microsoft di HDFS sull'archivio BLOB di Azure. Per altre informazioni, vedere [Usare l'archivio BLOB di Azure con HDInsight][hdinsight-storage].

Quando si effettua il provisioning di un cluster HDInsight, si specifica un account di archiviazione di Azure. Un contenitore di archiviazione BLOB specifico dell'account è designato come file system predefinito, come in HDFS. Per impostazione predefinita, il provisioning del cluster HDInsight è eseguito nello stesso data center che include l'account di archiviazione specificato.

**Per creare un account di archiviazione di Azure**

1. Accedere al [portale di Azure classico][azure-classic-portal].
	
	> [AZURE.NOTE] Azure HDInsight è attualmente supportato nel portale di Azure classico, mentre Azure DocumentDB esiste solo nel portale di Microsoft  Azure.

2. Fare clic su **NUOVO** nell'angolo inferiore sinistro, selezionare **SERVIZI DATI**, quindi **ARCHIVIAZIONE** e infine **CREAZIONE RAPIDA**.
	![Portale di Azure classico dove è possibile usare la funzione di creazione rapida per configurare un nuovo account di archiviazione.][image-storageaccount-quickcreate]

3. Immettere l'**URL**, selezionare i valori **PERCORSO** e **REPLICA**, quindi fare clic su **CREA ACCOUNT DI ARCHIVIAZIONE**. I gruppi di affinità non sono supportati.
	
	Il nuovo account di archiviazione verrà incluso nell'elenco di archiviazione.

	> [AZURE.IMPORTANT] Per ottenere le migliori prestazioni, assicurarsi che l'account di archiviazione, il cluster HDInsight e l'account di DocumentDB si trovino nella stessa area di Azure. Aree di Azure che supportano tutti i tre servizi sono: **Asia orientale**, **sud-est asiatico**, **Europa settentrionale**, **Europa occidentale**, **Stati Uniti orientali** e **Stati Uniti occidentali**.

4. Attendere che il valore di **STATUS** per il nuovo account di archiviazione venga modificato in **Online**.

## <a name="ProvisionHDInsight"></a>Passaggio 2: creare un cluster HDInsight personalizzato
Questa esercitazione usa l'Azione script dal portale di Azure classico per personalizzare il cluster HDInsight. In questa esercitazione si userà il portale di Azure classico per creare il cluster personalizzato. Per istruzioni su come usare i cmdlet di PowerShell oppure HDInsight .NET SDK, leggere l'articolo [Personalizzare cluster HDInsight mediante Azione script][hdinsight-custom-provision].

1. Accedere al [portale di Azure classico][azure-classic-portal]. È possibile che sia già stato eseguito l'accesso al passaggio precedente.

2. Fare clic su **+ NUOVO** nella parte inferiore della pagina, quindi su **SERVIZI DATI**, **HDINSIGHT** e infine su **CREAZIONE PERSONALIZZATA**.

3. Nella pagina **Dettagli cluster** digitare o scegliere i valori seguenti:

	![Fornire i dettagli di iniziale del cluster Hadoop HDInsight][image-customprovision-page1]

	<table border='1'>
		<tr><th>Proprietà</th><th>Valore</th></tr>
		<tr><td>Nome del cluster</td><td>Assegnare un nome al cluster.<br/>
			Il nome DNS deve iniziare e finire con un carattere alfanumerico e può contenere trattini.<br/>
			Il campo deve essere una stringa contenente da 3 a 63 caratteri.</td></tr>
		<tr><td>Subscription Name</td>
			<td>Se si hanno più sottoscrizioni di Azure, selezionare la sottoscrizione corrispondente all'account di archiviazione nel <strong>passaggio 1</strong>. </td></tr>
		<tr><td>Tipo di cluster</td>
			<td>Per il tipo di cluster selezionare <strong>Hadoop</strong>.</td></tr>
		<tr><td>Sistema operativo</td>
			<td>Selezionare <strong>Windows Server 2012 R2 Datacenter</strong> come sistema operativo.</td></tr>
		<tr><td>Versione HDInsight</td>
			<td>Scegliere la versione. </br>Selezionare <Strong>HDInsight versione 3.1</Strong>.</td></tr>
		</table>

	<p>Immettere o selezionare i valori come illustrato nella tabella, quindi fare clic sulla freccia destra.</p>

4. Nella pagina **Configura cluster** immettere o selezionare i valori seguenti:

	<table border="1">
	<tr><th>Nome</th><th>Valore</th></tr>
	<tr><td>Nodi di dati</td><td>Numero di nodi di dati che si vuole distribuire. </br>Notare che i nodi di dati di HDInsight sono associati a prestazioni e prezzi.</td></tr>
	<tr><td>Area/Rete virtuale</td><td>Scegliere la stessa area dell'<strong>account di archiviazione</strong> appena creato e dell'<strong>account di DocumentDB</strong>. </br> HDInsight richiede che l'account di archiviazione si trovi nella stessa area geografica. Più avanti nella configurazione sarà possibile selezionare un account di archiviazione situato nella stessa area geografica specificata qui.</td></tr>
	</table>
	
    Fare clic sulla freccia destra.

5. Nella pagina **Configura utente cluster** digitare o scegliere il valore seguente:

    <table border='1'>
		<tr><th>Proprietà</th><th>Valore</th></tr>
		<tr><td>Nome utente</td>
			<td>Specificare il nome utente del cluster HDInsight.</td></tr>
		<tr><td>Password/Conferma password</td>
			<td>Specificare la password utente del cluster HDInsight.</td></tr>
	</table>
	
    Fare clic sulla freccia destra.
    
6. Nella pagina **Account di archiviazione** specificare i valori seguenti:

	![Specificare l'account di archiviazione per il cluster Hadoop HDInsight][image-customprovision-page4]

	<table border='1'>
		<tr><th>Proprietà</th><th>Valore</th></tr>
		<tr><td>Account di archiviazione</td>
			<td>Specificare l'account di archiviazione di Azure che verrà usato come file system predefinito per il cluster HDInsight. È possibile scegliere una delle tre opzioni: utilizzare el’archiviazione esistente, creare nuove risorse di archiviazione o archiviazione utilizzato da un'altra sottoscrizione</br></br>
			Selezionare <strong>Utilizzare l'archiviazione esistente</strong>.
			</td>
			</td></tr>
		<tr><td>Nome account</td>
			<td>
			In <strong>Nome account</strong>, selezionare l'account creato al <strong>Passaggio 1</strong>. Nella casella di riepilogo vengono elencati solo gli account di archiviazione della stessa sottoscrizione di Azure che si trovano nello stesso data center in cui si è scelto di effettuare il provisioning del cluster.
			</td></tr>
		<tr><td>Contenitore predefinito</td>
			<td>Consente di specificare il contenitore predefinito nell'account di archiviazione viene usato come file system predefinito per il cluster HDInsight. Se si sceglie <strong>Utilizzare l'archiviazione esistente</strong> per il campo <strong>Account di archiviazione</strong> e non ci sono contenitori in tale account, il contenitore verrà creato per impostazione predefinita con lo stesso nome del cluster. Se esiste già un contenitore con il nome del cluster, al nome del contenitore verrà aggiunto un numero di sequenza.
        </td></tr>
		<tr><td>Account di archiviazione aggiuntivi</td>
			<td>HDInsight supporta più account di archiviazione. Un cluster può usare un numero illimitato di account di archiviazione aggiuntivi. Se tuttavia si crea un cluster tramite il portale di Azure, esiste un limite di sette dovuto ai vincoli dell'interfaccia utente. Per ogni account di archiviazione aggiuntivo specificato viene aggiunta un'ulteriore pagina Account di archiviazione alla procedura guidata, in cui è possibile specificare le informazioni account.</td></tr>
	</table>

	Fare clic sulla freccia destra.

7. Nella pagina **Azioni script** fare clic su **aggiungi azione script** per specificare i dettagli relativi allo script di PowerShell da eseguire per personalizzare un cluster, nel momento in cui viene creato. Lo script di PowerShell installerà il connettore Hadoop di DocumentDB nei cluster HDInsight durante la creazione del cluster.
	
	![Configurare l'azione di script per personalizzare un cluster HDInsight][image-customprovision-page5]

	<table border='1'>
		<tr><th>Proprietà</th><th>Valore</th></tr>
		<tr><td>Nome</td>
			<td>Specificare un nome per l'azione script.</td></tr>
		<tr><td>URI script</td>
			<td>Specificare l'URI dello script da richiamare per personalizzare il cluster.</br></br>
			Immettere: </br> <strong>https://portalcontent.blob.core.windows.net/scriptaction/documentdb-hadoop-installer-v03.ps1</strong>.</td></tr>
		<tr><td>Tipo di nodo</td>
			<td>Specifica i nodi in cui viene eseguito lo script di personalizzazione. È possibile scegliere <b>Tutti i nodi</b>, <b>Solo nodi head</b> o <b>Solo nodi di lavoro</b>.</br></br>
			Selezionare <strong>Tutti i nodi</strong>.</td></tr>
		<tr><td>Parametri</td>
			<td>Specificare i parametri, se richiesti dallo script.</br></br>
			<strong>Nessun parametro necessario</strong>.</td></tr>
	</table>

	Fare clic sul segno di spunta per completare la creazione del cluster.

## <a name="InstallCmdlets"></a>Passaggio 3: Installare e configurare Azure PowerShell.

1. Installare Azure PowerShell. Le istruzioni sono consultabili [qui][powershell-install-configure].

	> [AZURE.NOTE] In alternativa, solo per le query Hive, è possibile usare l'Editor Hive online di HDInsight. A tale scopo, accedere al [portale di Azure classico][azure-classic-portal], fare clic su **HDInsight** nel riquadro sinistro per visualizzare un elenco dei cluster HDInsight. Fare clic sul cluster che sul quale si vogliono eseguire le query Hive e quindi fare clic su **Console Query**.

2. Aprire Integrated Scripting Environment di Azure PowerShell:
	- In un computer che esegue Windows 8 o Windows Server 2012 o versione successiva, è possibile usare la funzionalità di ricerca incorporata. Dalla schermata Start digitare **powershell ise** e fare clic su **Invio**. 
	- In un computer che esegue una versione di Windows precedente a Windows 8 o Windows Server 2012 è possibile usare il menu Start. Dal menu Start, digitare **Prompt dei comandi** nella casella di ricerca, quindi nell'elenco dei risultati, fare clic su **Prompt dei comandi**. Al prompt dei comandi, digitare **powershell\_ise** e fare clic su **Invio**.

3. Aggiungere il proprio Account Azure.
	1. Nel riquadro console digitare **Add-AzureAccount** e fare clic su **Invio**. 
	2. Digitare l'indirizzo di posta elettronica associato alla sottoscrizione di Azure e fare clic su **Continua**. 
	3. Digitare la password per la sottoscrizione di Azure. 
	4. Fare clic su **Accedi**.

4. Il diagramma seguente identifica le parti importanti dell'ambiente di Scripting PowerShell di Azure.

	![Diagramma per Azure PowerShell][azure-powershell-diagram]

## <a name="RunHive"></a>Passaggio 4: Eseguire un processo Hive usando DocumentDB e HDInsight

> [AZURE.IMPORTANT] Tutte le variabili indicate da < > devono essere compilate usando le proprie impostazioni di configurazione.

1. Impostare le variabili seguenti nel riquadro di script di PowerShell.

		# Provide Azure subscription name, the Azure Storage account and container that is used for the default HDInsight file system.
		$subscriptionName = "<SubscriptionName>"
		$storageAccountName = "<AzureStorageAccountName>"
		$containerName = "<AzureStorageContainerName>"

		# Provide the HDInsight cluster name where you want to run the Hive job.
		$clusterName = "<HDInsightClusterName>"

2. 
	<p>Si inizia a costruire la stringa di query. Verrà scritta una query Hive che accetta i timestamp generati dal sistema di tutti i documenti (_ts) e ID univoci (_rid) da una raccolta di DocumentDB, calcola tutti i documenti in base al minuto e quindi archivia i risultati in una nuova raccolta di DocumentDB. </p>

    <p>In primo luogo, creare una tabella Hive dalla raccolta DocumentDB. Aggiungere il seguente frammento di codice nel riquadro di script di PowerShell <strong>dopo</strong> il frammento di codice da #1. Assicurarsi di includere il parametro DocumentDB.query facoltativo per ridurre i documenti semplicemente a _ts e _rid. </p>

    > [AZURE.NOTE] **La denominazione DocumentDB.inputCollections non è stata un errore.** Sì, si consente l'aggiunta di più raccolte come input: </br>
    '*DocumentDB.inputCollections*' = '*<Nome raccolta di input di DocumentDB 1 >*,*<Nome raccolta di input di DocumentDB 2> *' </br> I nomi di raccolta sono separati senza spazi, mediante una singola virgola.


		# Create a Hive table using data from DocumentDB. Pass DocumentDB the query to filter transferred data to _rid and _ts.
		$queryStringPart1 = "drop table DocumentDB_timestamps; "  + 
                            "create external table DocumentDB_timestamps(id string, ts BIGINT) "  +
                            "stored by 'com.microsoft.azure.documentdb.hive.DocumentDBStorageHandler' "  +
                            "tblproperties ( " + 
                                "'DocumentDB.endpoint' = '<DocumentDB Endpoint>', " +
                                "'DocumentDB.key' = '<DocumentDB Primary Key>', " +
                                "'DocumentDB.db' = '<DocumentDB Database Name>', " +
                                "'DocumentDB.inputCollections' = '<DocumentDB Input Collection Name>', " +
                                "'DocumentDB.query' = 'SELECT r._rid AS id, r._ts AS ts FROM root r' ); "
 
3.  Successivamente, si passa alla creazione di una tabella Hive per la raccolta di output. Le proprietà del documento di output saranno mese, giorno, ora, minuti e numero totale di occorrenze.

	> [AZURE.NOTE] **Ancora una volta, la denominazione di DocumentDB.outputCollections non è un errore.** Sì, si consente l'aggiunta di più raccolte come output: </br>
    '*DocumentDB.outputCollections*' = '*<Nome raccolta di output di DocumentDB 1>*,*<Nome raccolta di output di DocumentDB 2>*' </br> I nomi di raccolta sono separati senza spazi, mediante una singola virgola. </br></br>
    Verrà eseguita la distribuzione round robin dei documenti tra più raccolte. Un batch di documenti verrà archiviato in una raccolta, quindi un secondo batch dei documenti verrà archiviato nella raccolta successiva e così via.

		# Create a Hive table for the output data to DocumentDB.
	    $queryStringPart2 = "drop table DocumentDB_analytics; " +
                              "create external table DocumentDB_analytics(Month INT, Day INT, Hour INT, Minute INT, Total INT) " +
                              "stored by 'com.microsoft.azure.documentdb.hive.DocumentDBStorageHandler' " + 
                              "tblproperties ( " + 
                                  "'DocumentDB.endpoint' = '<DocumentDB Endpoint>', " +
                                  "'DocumentDB.key' = '<DocumentDB Primary Key>', " +  
                                  "'DocumentDB.db' = '<DocumentDB Database Name>', " +
                                  "'DocumentDB.outputCollections' = '<DocumentDB Output Collection Name>' ); "

4. Infine, si calcoleranno i documenti per mese, giorno, ora e minuto e si inseriranno i risultati nuovamente nell'output della tabella Hive.

		# GROUP BY minute, COUNT entries for each, INSERT INTO output Hive table.
        $queryStringPart3 = "INSERT INTO table DocumentDB_analytics " +
                              "SELECT month(from_unixtime(ts)) as Month, day(from_unixtime(ts)) as Day, " +
                              "hour(from_unixtime(ts)) as Hour, minute(from_unixtime(ts)) as Minute, " +
                              "COUNT(*) AS Total " +
                              "FROM DocumentDB_timestamps " +
                              "GROUP BY month(from_unixtime(ts)), day(from_unixtime(ts)), " +
                              "hour(from_unixtime(ts)) , minute(from_unixtime(ts)); "

5. Aggiungere il frammento di script seguente per creare una definizione processo Hive dalla query precedente.

		# Create a Hive job definition.
		$queryString = $queryStringPart1 + $queryStringPart2 + $queryStringPart3
		$hiveJobDefinition = New-AzureHDInsightHiveJobDefinition -Query $queryString

	È anche possibile usare l'opzione -File per specificare un file di script HiveQL in HDFS.

6. Aggiungere il frammento seguente per salvare l'ora di inizio e inviare il processo Hive.

		# Save the start time and submit the job to the cluster.
		$startTime = Get-Date
		Select-AzureSubscription $subscriptionName
		$hiveJob = Start-AzureHDInsightJob -Cluster $clusterName -JobDefinition $hiveJobDefinition

7. Aggiungere quanto segue per attendere il completamento del processo Hive.

		# Wait for the Hive job to complete.
		Wait-AzureHDInsightJob -Job $hiveJob -WaitTimeoutInSeconds 3600

8. Aggiungere il comando seguente per stampare l'output standard e l'ora di inizio e fine.

		# Print the standard error, the standard output of the Hive job, and the start and end time.
		$endTime = Get-Date
		Get-AzureHDInsightJobOutput -Cluster $clusterName -JobId $hiveJob.JobId -StandardOutput
		Write-Host "Start: " $startTime ", End: " $endTime -ForegroundColor Green

9. **Eseguire** il nuovo script. **Fare clic** sul pulsante di esecuzione verde.

10. Verificare i risultati. Accedere al [portale di Azure][azure-portal].
	1. Fare clic su <strong>Sfoglia</strong> nel riquadro a sinistra. </br>
	2. Fare clic su <strong>tutto</strong> in alto a destra del riquadro di esplorazione. </br>
	3. Individuare e fare clic su <strong>Account DocumentDB</strong>. </br>
	4. Successivamente, individuare il proprio <strong>Account DocumentDB</strong>, quindi il <strong>Database DocumentDB</strong> e la <strong>Raccolta DocumentDB</strong> associati alla raccolta di output specificata nella query Hive.</br>
	5. Infine, fare clic su <strong>Esplora documenti</strong> sotto <strong>Strumenti di sviluppo</strong>.</br></p>

	Verranno visualizzati i risultati della query Hive.

	![Risultati della query relativa alla tabella][image-hive-query-results]

## <a name="RunPig"></a>Passaggio 5: Eseguire un processo Pig usando DocumentDB e HDInsight

> [AZURE.IMPORTANT] Tutte le variabili indicate da < > devono essere compilate usando le proprie impostazioni di configurazione.

1. Impostare le variabili seguenti nel riquadro di script di PowerShell.

        # Provide Azure subscription name.
        $subscriptionName = "Azure Subscription Name"

        # Provide HDInsight cluster name where you want to run the Pig job.
        $clusterName = "Azure HDInsight Cluster Name"

2. <p>Si inizia a costruire la stringa di query. Verrà scritta una query Pig che accetta i timestamp generati dal sistema di tutti i documenti (_ts) e ID univoci (_rid) da una raccolta di DocumentDB, calcola tutti i documenti in base al minuto e quindi archivia i risultati in una nuova raccolta di DocumentDB.</p>
    <p>In primo luogo, caricare i documenti da DocumentDB in HDInsight. Aggiungere il seguente frammento di codice nel riquadro di script di PowerShell <strong>dopo</strong> il frammento di codice da #1. Assicurarsi di aggiungere una query di DocumentDB al parametro di query DocumentDB facoltativo per ridurre i documenti semplicemente a _ts e _rid.</p>

    > [AZURE.NOTE] Sì, si consente l'aggiunta di più raccolte come input: </br>
    '*<Nome raccolta di input di DocumentDB 1>*,*<Nome raccolta di input di DocumentDB 2>*'</br> I nomi di raccolta sono separati senza spazi, mediante una singola virgola. </b>

	Verrà eseguita la distribuzione round robin dei documenti tra più raccolte. Un batch di documenti verrà archiviato in una raccolta, quindi un secondo batch dei documenti verrà archiviato nella raccolta successiva e così via.

		# Load data from DocumentDB. Pass DocumentDB query to filter transferred data to _rid and _ts.
        $queryStringPart1 = "DocumentDB_timestamps = LOAD '<DocumentDB Endpoint>' USING com.microsoft.azure.documentdb.pig.DocumentDBLoader( " +
                                                        "'<DocumentDB Primary Key>', " +
                                                        "'<DocumentDB Database Name>', " +
                                                        "'<DocumentDB Input Collection Name>', " +
                                                        "'SELECT r._rid AS id, r._ts AS ts FROM root r' ); "

3.  Successivamente, verranno calcolati i documenti in base a mese, giorno, ora, minuti e numero totale di occorrenze.

		# GROUP BY minute and COUNT entries for each.
        $queryStringPart2 = "timestamp_record = FOREACH DocumentDB_timestamps GENERATE `$0#'id' as id:int, ToDate((long)(`$0#'ts') * 1000) as timestamp:datetime; " +
                            "by_minute = GROUP timestamp_record BY (GetYear(timestamp), GetMonth(timestamp), GetDay(timestamp), GetHour(timestamp), GetMinute(timestamp)); " +
                            "by_minute_count = FOREACH by_minute GENERATE FLATTEN(group) as (Year:int, Month:int, Day:int, Hour:int, Minute:int), COUNT(timestamp_record) as Total:int; "

4. Infine, si archivieranno i risultati nella nuova raccolta di output.

    > [AZURE.NOTE] Sì, si consente l'aggiunta di più raccolte come output: </br>
    '\<Nome raccolta di output di DocumentDB 1\>,\<Nome raccolta di output di DocumentDB 2\>'</br> I nomi di raccolta sono separati senza spazi, mediante una singola virgola.</br>
    Verrà eseguita la distribuzione round robin dei documenti tra più raccolte. Un batch di documenti verrà archiviato in una raccolta, quindi un secondo batch dei documenti verrà archiviato nella raccolta successiva e così via.

		# Store output data to DocumentDB.
        $queryStringPart3 = "STORE by_minute_count INTO '<DocumentDB Endpoint>' " +
                            "USING com.microsoft.azure.documentdb.pig.DocumentDBStorage( " +
                                "'<DocumentDB Primary Key>', " +
                                "'<DocumentDB Database Name>', " +
                                "'<DocumentDB Output Collection Name>'); "

5. Aggiungere il frammento di script seguente per creare una definizione processo Pig dalla query precedente.

        # Create a Pig job definition.
        $queryString = $queryStringPart1 + $queryStringPart2 + $queryStringPart3
        $pigJobDefinition = New-AzureHDInsightPigJobDefinition -Query $queryString -StatusFolder $statusFolder

	È anche possibile usare l'opzione -File per specificare un file di script Pig in HDFS.

6. Aggiungere il frammento seguente per salvare l'ora di inizio e inviare il processo Pig.

		# Save the start time and submit the job to the cluster.
		$startTime = Get-Date
		Select-AzureSubscription $subscriptionName
		$pigJob = Start-AzureHDInsightJob -Cluster $clusterName -JobDefinition $pigJobDefinition

7. Aggiungere quanto segue per attendere il completamento del processo Pig.

		# Wait for the Pig job to complete.
		Wait-AzureHDInsightJob -Job $pigJob -WaitTimeoutInSeconds 3600

8. Aggiungere il comando seguente per stampare l'output standard e l'ora di inizio e fine.

		# Print the standard error, the standard output of the Hive job, and the start and end time.
		$endTime = Get-Date
		Get-AzureHDInsightJobOutput -Cluster $clusterName -JobId $pigJob.JobId -StandardOutput
		Write-Host "Start: " $startTime ", End: " $endTime -ForegroundColor Green
		
9. **Eseguire** il nuovo script. **Fare clic** sul pulsante di esecuzione verde.

10. Verificare i risultati. Accedere al [portale di Azure][azure-portal].
	1. Fare clic su <strong>Sfoglia</strong> nel riquadro a sinistra. </br>
	2. Fare clic su <strong>tutto</strong> in alto a destra del riquadro di esplorazione. </br>
	3. Individuare e fare clic su <strong>Account DocumentDB</strong>. </br>
	4. Successivamente, individuare il proprio <strong>Account DocumentDB</strong>, quindi il <strong>Database DocumentDB</strong> e la <strong>Raccolta DocumentDB</strong> associati alla raccolta di output specificata nella query Pig.</br>
	5. Infine, fare clic su <strong>Esplora documenti</strong> sotto <strong>Strumenti di sviluppo</strong>.</br></p>

	Verranno visualizzati i risultati della query Pig.

	![Risultati della query SQL][image-pig-query-results]

## <a name="RunMapReduce"></a>Passaggio 6: Eseguire un processo MapReduce usando DocumentDB e HDInsight

1. Impostare le variabili seguenti nel riquadro di script di PowerShell.
		
		$subscriptionName = "<SubscriptionName>"   # Azure subscription name
		$clusterName = "<ClusterName>"             # HDInsight cluster name
		
2. È possibile eseguire un processo MapReduce che calcola il numero di occorrenze per ogni proprietà di documento dalla raccolta DocumentDB. Aggiungere questo frammento di script **dopo** il frammento di codice riportato sopra.

		# Define the MapReduce job.
		$TallyPropertiesJobDefinition = New-AzureHDInsightMapReduceJobDefinition -JarFile "wasb:///example/jars/TallyProperties-v01.jar" -ClassName "TallyProperties" -Arguments "<DocumentDB Endpoint>","<DocumentDB Primary Key>", "<DocumentDB Database Name>","<DocumentDB Input Collection Name>","<DocumentDB Output Collection Name>","<[Optional] DocumentDB Query>"

	> [AZURE.NOTE] Il file TallyProperties-v01.jar viene fornito con l'installazione personalizzata del connettore Hadoop di DocumentDB.

3. Per inviare il processo MapReduce, aggiungere il comando seguente.

		# Save the start time and submit the job.
		$startTime = Get-Date
		Select-AzureSubscription $subscriptionName
		$TallyPropertiesJob = Start-AzureHDInsightJob -Cluster $clusterName -JobDefinition $TallyPropertiesJobDefinition | Wait-AzureHDInsightJob -WaitTimeoutInSeconds 3600  

	Oltre alla definizione del processo MapReduce, è necessario specificare anche il nome del cluster HDInsight in cui si desidera eseguire il processo MapReduce e le credenziali. Start-AzureHDInsightJob è una chiamata non sincronizzata. Per verificare il completamento del processo, usare il cmdlet *Wait-AzureHDInsightJob*.

4. Per controllare se si sono verificati errori nel processo MapReduce, aggiungere il comando seguente:
	
		# Get the job output and print the start and end time.
		$endTime = Get-Date
		Get-AzureHDInsightJobOutput -Cluster $clusterName -JobId $TallyPropertiesJob.JobId -StandardError
		Write-Host "Start: " $startTime ", End: " $endTime -ForegroundColor Green 

5. **Eseguire** il nuovo script. **Fare clic** sul pulsante di esecuzione verde.

6. Verificare i risultati. Accedere al [portale di Azure][azure-portal].
	1. Fare clic su <strong>Sfoglia</strong> nel riquadro a sinistra.
	2. Fare clic su <strong>tutto</strong> in alto a destra del riquadro di esplorazione.
	3. Individuare e fare clic su <strong>Account DocumentDB</strong>.
	4. Successivamente, individuare il proprio <strong>Account DocumentDB</strong>, quindi il <strong>Database DocumentDB</strong> e la <strong>Raccolta DocumentDB</strong> associati alla raccolta di output specificata nel processo MapReduce.
	5. Infine, fare clic su <strong>Esplora documenti</strong> sotto <strong>Strumenti di sviluppo</strong>.

	Verranno visualizzati i risultati del processo MapReduce.

	![Risultati della query MapReduce][image-mapreduce-query-results]

## <a name="NextSteps"></a>Passaggi successivi

Congratulazioni. Sono stati eseguiti i primi processi Hive, Pig e MapReduce usando Azure DocumentDB e HDInsight.

Abbiamo reso Open Source il connettore Hadoop. Se si è interessati, è possibile contribuire in [GitHub][documentdb-github].

Per altre informazioni, vedere gli articoli seguenti:

- [Sviluppare un'applicazione Java con Documentdb][documentdb-java-application]
- [Sviluppare programmi MapReduce Java per Hadoop in HDInsight][hdinsight-develop-deploy-java-mapreduce]
- [Introduzione all'uso di Hadoop con Hive in HDInsight per analizzare l'uso di telefoni cellulari][hdinsight-get-started]
- [Usare MapReduce con HDInsight][hdinsight-use-mapreduce]
- [Usare Hive con HDInsight][hdinsight-use-hive]
- [Usare Pig con HDInsight][hdinsight-use-pig]
- [Personalizzare cluster HDInsight mediante le azioni script][hdinsight-hadoop-customize-cluster]

[apache-hadoop]: http://hadoop.apache.org/
[apache-hadoop-doc]: http://hadoop.apache.org/docs/current/
[apache-hive]: http://hive.apache.org/
[apache-pig]: http://pig.apache.org/
[getting-started]: documentdb-get-started.md

[azure-classic-portal]: https://manage.windowsazure.com/
[azure-powershell-diagram]: ./media/documentdb-run-hadoop-with-hdinsight/azurepowershell-diagram-med.png
[azure-portal]: https://portal.azure.com/

[documentdb-hdinsight-samples]: http://portalcontent.blob.core.windows.net/samples/documentdb-hdinsight-samples.zip
[documentdb-github]: https://github.com/Azure/azure-documentdb-hadoop
[documentdb-java-application]: documentdb-java-application.md
[documentdb-manage-collections]: documentdb-manage.md#Collections
[documentdb-manage-document-storage]: documentdb-manage.md#IndexOverhead
[documentdb-manage-throughput]: documentdb-manage.md#ProvThroughput
[documentdb-import-data]: documentdb-import-data.md

[hdinsight-custom-provision]: ../hdinsight/hdinsight-provision-clusters.md#powershell
[hdinsight-develop-deploy-java-mapreduce]: ../hdinsight/hdinsight-develop-deploy-java-mapreduce.md
[hdinsight-hadoop-customize-cluster]: ../hdinsight/hdinsight-hadoop-customize-cluster.md
[hdinsight-get-started]: ../hdinsight-get-started.md
[hdinsight-storage]: ../hdinsight-use-blob-storage.md
[hdinsight-use-hive]: ../hdinsight/hdinsight-use-hive.md
[hdinsight-use-mapreduce]: ../hdinsight/hdinsight-use-mapreduce.md
[hdinsight-use-pig]: ../hdinsight/hdinsight-use-pig.md

[image-customprovision-page1]: ./media/documentdb-run-hadoop-with-hdinsight/customprovision-page1.png
[image-customprovision-page4]: ./media/documentdb-run-hadoop-with-hdinsight/customprovision-page4.png
[image-customprovision-page5]: ./media/documentdb-run-hadoop-with-hdinsight/customprovision-page5.png
[image-storageaccount-quickcreate]: ./media/documentdb-run-hadoop-with-hdinsight/storagequickcreate.png
[image-hive-query-results]: ./media/documentdb-run-hadoop-with-hdinsight/hivequeryresults.PNG
[image-mapreduce-query-results]: ./media/documentdb-run-hadoop-with-hdinsight/mapreducequeryresults.PNG
[image-pig-query-results]: ./media/documentdb-run-hadoop-with-hdinsight/pigqueryresults.PNG

[powershell-install-configure]: ../install-configure-powershell.md
 

<!------HONumber=AcomDC_1203_2015-->