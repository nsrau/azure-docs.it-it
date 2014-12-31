<properties urlDisplayName="Use Hadoop Hive in HDInsight" pageTitle="Usare Hive di Hadoop in HDInsight | Azure" metaKeywords="" description="Learn how to use Hive with HDInsight. You'll use a log file as input into an HDInsight table, and use HiveQL to query the data and report basic statistics." metaCanonical="" services="hdinsight" documentationCenter="" title="Use Hadoop Hive in HDInsight" authors="jgao" solutions="" manager="paulettm" editor="cgronlun" videoId="" scriptId="" />

<tags ms.service="hdinsight" ms.workload="big-data" ms.tgt_pltfrm="na" ms.devlang="na" ms.topic="article" ms.date="09/25/2014" ms.author="jgao" />

# Usare Hive con Hadoop in HDInsight

[Apache Hive][apache-hive] fornisce un metodo per l'esecuzione di processi MapReduce mediante un linguaggio di scripting simile a SQL, denominato *HiveQL*. Hive è un sistema di data warehouse per Hadoop, che consente di eseguire attività di riepilogo, query e analisi di volumi di dati molto elevati. In questo articolo si userà HiveQL per eseguire query su un file di dati di esempio, fornito come parte del provisioning del cluster HDInsight.


**Prerequisiti:**

- È necessario avere completato il provisioning di un **cluster HDInsight**. Per una procedura dettagliata su come eseguire questa operazione con il portale di Azure, vedere [Introduzione all'uso di HDInsight][hdinsight-get-started]. Per informazioni sui vari altri metodi di creazione di tali cluster e per le relative istruzioni, vedere [Provisioning di cluster HDInsight][hdinsight-provision].

- È necessario avere installato **Azure PowerShell** nella workstation. Per le relative istruzioni, vedere [Installazione e configurazione di Azure PowerShell][powershell-install-configure].

##Contenuto dell'articolo

* [Caso di uso di Hive](#usage)
* [Caricare i dati per le tabelle Hive](#uploaddata)
* [Eseguire query Hive usando PowerShell](#runhivequeries)
* [Eseguire query Hive usando gli strumenti di HDInsight per Visual Studio](#runhivefromvs)
* [Usare Tez per ottenere prestazioni migliorate](#usetez)
* [Passaggi successivi](#nextsteps)

##<a id="usage"></a>Caso di uso di Hive

![HDI.HIVE.Architecture][image-hdi-hive-architecture]

Hive applica una struttura a dati complessivamente non strutturati e quindi permette di eseguire query su tali dati. Hive fornisce un livello di astrazione superiore al framework MapReduce basato su Java, consentendo agli utenti di eseguire query sui dati senza alcuna conoscenza di Java o MapReduce. HiveQL, il linguaggio di query di Hive, permette di scrivere query con istruzioni simili a quelle di T-SQL. Le query HiveQL vengono compilate automaticamente in MapReduce da HDInsight e vengono eseguite nel cluster. Tra gli altri vantaggi offerti da Hive sono inclusi i seguenti:

- Hive consente ai programmatori che hanno familiarità con il framework MapReduce di aggiungere mapper e reducer personalizzati per eseguire analisi più sofisticate che potrebbero non essere supportate dalle funzionalità incorporate del linguaggio HiveQL.
- Hive è particolarmente adatto per l'elaborazione batch di grandi quantità di dati non modificabili, ad esempio log Web. Non è appropriato per applicazioni di transazione che richiedono tempi di risposta molto rapidi, come ad esempio sistemi di gestione di database.
- Hive è ottimizzato per la scalabilità (è possibile aggiungere più macchine in modo dinamico al cluster Hadoop), l'estensibilità (all'interno del framework MapReduce e con altre interfacce di programmazione) e la tolleranza di errore. La latenza non è una considerazione chiave di progettazione.

##<a id="uploaddata"></a>Caricare i dati per le tabelle Hive

HDInsight usa un contenitore dell'archiviazione BLOB di Azure come file system predefinito per i cluster Hadoop. Alcuni file di dati di esempio vengono aggiunti all'archivio BLOB come parte del provisioning del cluster Questo articolo usa un file *log4j* di esempio distribuito con il cluster HDInsight e archiviato nel percorso **/example/data/sample.log** nel contenitore di archiviazione BLOB. Ogni log all'interno del file è costituito da una riga di campi che contiene un campo `[LOG LEVEL]` per visualizzare il tipo e la gravità. Ad esempio:

	2012-02-03 20:26:41 SampleClass3 [ERROR] verbose detail for id 1527353937

Nell'esempio precedente, il livello log è ERROR.

> [AZURE.NOTE] È anche possibile generare i propri file log4j usando l'utilità di registrazione [Apache Log4j][apache-log4j] e quindi caricandoli nel contenitore BLOB. Per istruzioni, vedere [Caricare i dati in HDInsight][hdinsight-upload-data]. Per altre informazioni sul modo in cui HDInsight usa l'archiviazione BLOB di Azure, vedere [Usare l'archiviazione BLOB di Azure con HDInsight][hdinsight-storage].

HDInsight può accedere ai file archiviati nell'archiviazione BLOB tramite il prefisso **wasb**. Ad esempio, per accedere al file sample.log, usare la sintassi seguente:

	wasb:///example/data/sample.log

Poiché WASB è la risorsa di archiviazione predefinita per HDInsight, è anche possibile accedere al file usando **/example/data/sample.log**.

> [AZURE.NOTE] La sintassi precedente, **wasb:///**, permette di accedere ai file archiviati nel contenitore di archiviazione predefinito per il cluster HDInsight. Se durante il provisioning del cluster sono stati specificati account di archiviazione aggiuntivi e si vuole accedere ai file archiviati in tali account, è possibile accedere ai dati specificando il nome del contenitore e l'indirizzo dell'account di archiviazione. Ad esempio, **wasb://mycontainer@mystorage.blob.core.windows.net/example/data/sample.log**.

##<a id="runhivequeries"></a> Eseguire query Hive usando PowerShell

Le query Hive possono essere eseguite in PowerShell usando il cmdlet **Start-AzureHDInsightJob** o il cmdlet **Invoke-Hive**.

* **Start-AzureHDInsightJob** è un esecutore di processi generici, usato per avviare processi Hive, Pig e MapReduce in un cluster HDInsight. **Start-AzureHDInsightJob** è asincrono e viene restituito prima del completamento del processo. Vengono restituite informazioni sul processo, che possono essere usate con cmdlet quali **Wait-AzureHDInsightJob**, **Stop-AzureHDInsightJob** e **Get-AzureHDInsightJobOutput**.  **È necessario usare Get-AzureHDInsightJobOutput** per recuperare le informazioni scritte in **STDOUT** o **STDERR** dal processo.

* **Invoke-Hive** esegue una query Hive, ne attende il completamento e recupera l'output della query in un'unica azione.

1. Aprire una finestra della console di Azure PowerShell. Per istruzioni, vedere [Come installare e configurare Azure PowerShell][powershell-install-configure].
2. Eseguire il comando seguente per connettersi alla sottoscrizione di Azure:

		Add-AzureAccount

	Verrà richiesto di immettere le credenziali dell'account Azure.

2. Impostare le variabili nello script seguente ed eseguirlo.

		# Provide Azure subscription name, and the Azure Storage account and container that is used for the default HDInsight file system.
		$subscriptionName = "<SubscriptionName>"
		$storageAccountName = "<AzureStorageAccountName>"
		$containerName = "<AzureStorageContainerName>"

		# Provide HDInsight cluster name Where you want to run the Hive job
		$clusterName = "<HDInsightClusterName>"

3. Eseguire lo script seguente per creare una nuova tabella denominata **log4jLogs** usando i dati di esempio.

		# HiveQL
		# Create an EXTERNAL table
		$queryString = "DROP TABLE log4jLogs;" +
		               "CREATE EXTERNAL TABLE log4jLogs(t1 string, t2 string, t3 string, t4 string, t5 string, t6 string, t7 string) ROW FORMAT DELIMITED FIELDS TERMINATED BY ' ' STORED AS TEXTFILE LOCATION 'wasb:///example/data/';" +
		               "SELECT t4 AS sev, COUNT(*) AS cnt FROM log4jLogs WHERE t4 = '[ERROR]' GROUP BY t4;"

	Le istruzioni HiveQL eseguono le azioni seguenti:

	* **DROP TABLE**: elimina la tabella e il file di dati, nel caso in cui la tabella esista già.
	* **CREATE EXTERNAL TABLE**: crea una nuova tabella 'esterna' in Hive. Le tabelle esterne archiviano solo la definizione della tabella in Hive. I dati rimangono nella posizione originale.
	* **ROW FORMAT**: indica a Hive il modo in cui sono formattati i dati. In questo caso i campi di ogni log sono separati da uno spazio.
	* **STORED AS TEXTFILE LOCATION**: indica a Hive dove sono archiviati i dati (directory example/data) e specifica che i dati sono archiviati come testo.
	* **SELECT**: seleziona un numero di tutte le righe in cui la colonna **t4** include il valore **[ERROR]**. Dovrebbe restituire un valore pari a **3**, poiché sono presenti tre righe contenenti questo valore.

	> [AZURE.NOTE] È consigliabile usare le tabelle esterne quando si prevede che i dati sottostanti vengano aggiornati da un'origine esterna, ad esempio un processo automatico di caricamento dei dati, oppure da un'altra operazione MapReduce, ma si vuole che le query Hive usino sempre i dati più recenti.
	>
	> L'eliminazione di una tabella esterna **non** comporta anche l'eliminazione dei dati. Viene eliminata solo la definizione della tabella.


4. Eseguire lo script seguente per creare una definizione processo Hive dalla query precedente.

		# Create a Hive job definition
		$hiveJobDefinition = New-AzureHDInsightHiveJobDefinition -Query $queryString

	È anche possibile usare l'opzione -File per specificare un file di script HiveQL in HDFS.

5. Eseguire lo script seguente per inviare il processo Hive:

		# Submit the job to the cluster
		Select-AzureSubscription $subscriptionName
		$hiveJob = Start-AzureHDInsightJob -Cluster $clusterName -JobDefinition $hiveJobDefinition

6. Eseguire lo script seguente per attendere il completamento del processo Hive:

		# Wait for the Hive job to complete
		Wait-AzureHDInsightJob -Job $hiveJob -WaitTimeoutInSeconds 3600

7. Eseguire lo script seguente per stampare l'output standard:

		# Print the standard error and the standard output of the Hive job.
		Get-AzureHDInsightJobOutput -Cluster $clusterName -JobId $hiveJob.JobId -StandardOutput


 	![HDI.HIVE.PowerShell][image-hdi-hive-powershell]

	Il risultato è il seguente:

		[ERROR] 3

	Ciò significa che il file *sample.log* conteneva tre istanze di log ERROR.

4. Per usare **Invoke-Hive**, è prima di tutto necessario configurare il cluster da usare.

		# Connect to the cluster
		Use-AzureHDInsightCluster $clusterName

4. Usare lo script seguente per creare una nuova tabella 'interna' denominata **errorLogs** usando il cmdlet **Invoke-Hive**.

		# Run a query to create an 'internal' Hive table
		$response = Invoke-Hive -Query @"
		CREATE TABLE IF NOT EXISTS errorLogs (t1 string, t2 string, t3 string, t4 string, t5 string, t6 string, t7 string) STORED AS ORC;
		INSERT OVERWRITE TABLE errorLogs SELECT t1, t2, t3, t4, t5, t6, t7 FROM log4jLogs WHERE t4 = '[ERROR]';
		"@
		# print the output on the console
		Write-Host $response

	Queste istruzioni eseguono le azioni seguenti.

	* **CREATE TABLE IF NOT EXISTS**: crea una tabella, se non esiste già. Poiché non viene usata la parola chiave **EXTERNAL**, si tratta di una tabella 'interna', che viene archiviata nel data warehouse di Hive e viene gestita completamente da Hive.
	* **STORED AS ORC**: archivia i dati nel formato ORC (Optimized Row Columnar). Si tratta di un formato di file altamente ottimizzato per l'archiviazione di dati Hive.
	* **INSERT OVERWRITE ... SELECT**: seleziona dalla tabella **log4jLogs** le righe che includono **[ERROR]**, quindi inserisce i dati nella tabella **errorLogs**.

	> [AZURE.NOTE] A differenza delle tabelle **EXTERNAL**, se si elimina una tabella interna verranno eliminati anche i dati sottostanti.

	L'output sarà simile al seguente:

	![PowerShell Invoke-Hive output][img-hdi-hive-powershell-output]

	> [AZURE.NOTE] Per query HiveQL più lunghe, è possibile usare stringhe Here di PowerShell o un file di script HiveQL. Il frammento di codice seguente illustra come usare il cmdlet *Invoke-Hive* per eseguire un file di script HiveQL. Il file di script HiveQL deve essere caricato in WASB.
	>
	> `Invoke-Hive -File "wasb://<ContainerName>@<StorageAccountName>/<Path>/query.hql"`
	>
	> Per altre informazioni sulle stringhe Here, vedere [Uso delle stringhe Here di PowerShell][powershell-here-strings].

5. Per verificare che solo le righe contenenti **[ERROR]** nella colonna t4 siano state archiviate nella tabella **errorLogs**, usare l'istruzione seguente per restituire tutte le righe da **errorLogs**.

		#Select all rows
		$response = Invoke-Hive -Query "SELECT * from errorLogs;"
		Write-Host $response

	Dovrebbero essere restituite tre righe di dati, tutte contenenti **[ERROR]** nella colonna t4.


> [AZURE.NOTE] Se necessario, è anche possibile importare l'output delle query in Microsoft Excel per ulteriori analisi. Per istruzioni, vedere [Connettere Excel a Hadoop mediante Power Query][import-to-excel].

##<a id="runhivefromvs"></a>Eseguire query Hive usando Visual Studio
HDInsight Tools per Visual Studio è disponibile in Azure SDK per .NET versione 2.5 o versione successiva.  Usando gli strumenti da Visual Studio, sarà possibile connettersi al cluster HDInsight, creare tabelle Hive ed eseguire query Hive.  Per altre informazioni, vedere [Introduzione all'uso di HDInsight Hadoop Tools per Visual Studio][1].



##<a id="usetez"></a>Usare Tez per ottenere prestazioni migliorate

[Apache Tez][apache-tez] è un framework che consente di eseguire le applicazioni come Hive, che richiedono un uso elevato di dati, in modo molto più efficiente e scalabile. Nella versione più recente di HDInsight, Hive ora supporta l'esecuzione su Tez.  Questa funzionalità è attualmente disattivata per impostazione predefinita ed è necessario attivarla.  Nelle future versioni dei cluster verrà impostata come attiva per impostazione predefinita. Per poter sfruttare Tez, è necessario impostare il valore seguente per una query Hive:

		set hive.execution.engine=tez;

È possibile inviarlo in ogni query inserendolo all'inizio della stessa.  È anche possibile impostarlo come valore predefinito su un cluster scegliendo il valore di configurazione al momento della creazione del cluster.  Per informazioni più dettagliate, vedere [Provisioning di cluster HDInsight][hdinsight-provision].

La [documentazione sulla progettazione di Hive su Tez][hive-on-tez-wiki] contiene una serie di informazioni dettagliate sulle scelte di implementazione e l'ottimizzazione delle configurazioni.


##<a id="nextsteps"></a>Passaggi successivi

Mentre Hive semplifica l'interrogazione dei dati usando un linguaggio di query simile a SQL, altri componenti disponibili con HDInsight forniscono funzionalità complementari quali lo spostamento e la trasformazione dei dati. Per altre informazioni, vedere gli articoli seguenti:

* [Introduzione all'uso di HDInsight Hadoop Tools per Visual Studio][1]
* [Usare Oozie con HDInsight][hdinsight-use-oozie]
* [Inviare processi Hadoop a livello di codice][hdinsight-submit-jobs]
* [Usare Pig con HDInsight](../hdinsight-use-pig/)
* [Analizzare i dati sui ritardi dei voli usando HDInsight][hdinsight-analyze-flight-data]
* [Documentazione di Azure HDInsight SDK][hdinsight-sdk-documentation]
* [Caricare i dati in HDInsight][hdinsight-upload-data]
* [Introduzione ad Azure HDInsight](../hdinsight-get-started/)


[1]: ../hdinsight-hadoop-visual-studio-tools-get-started/

[hdinsight-sdk-documentation]: http://msdnstage.redmond.corp.microsoft.com/it-it/library/dn479185.aspx

[azure-purchase-options]: http://azure.microsoft.com/it-it/pricing/purchase-options/
[azure-member-offers]: http://azure.microsoft.com/it-it/pricing/member-offers/
[azure-free-trial]: http://azure.microsoft.com/it-it/pricing/free-trial/

[apache-tez]: http://tez.apache.org
[apache-hive]: http://hive.apache.org/
[apache-log4j]: http://en.wikipedia.org/wiki/Log4j
[hive-on-tez-wiki]: https://cwiki.apache.org/confluence/display/Hive/Hive+on+Tez
[import-to-excel]: http://azure.microsoft.com/it-it/documentation/articles/hdinsight-connect-excel-power-query/


[hdinsight-use-oozie]: ../hdinsight-use-oozie/
[hdinsight-analyze-flight-data]: ../hdinsight-analyze-flight-delay-data/



[hdinsight-storage]: ../hdinsight-use-blob-storage

[hdinsight-provision]: ../hdinsight-provision-clusters/
[hdinsight-submit-jobs]: ../hdinsight-submit-hadoop-jobs-programmatically/
[hdinsight-upload-data]: ../hdinsight-upload-data/
[hdinsight-get-started]: ../hdinsight-get-started/

[Powershell-install-configure]: ../install-configure-powershell/
[powershell-here-strings]: http://technet.microsoft.com/it-it/library/ee692792.aspx

[image-hdi-hive-powershell]: ./media/hdinsight-use-hive/HDI.HIVE.PowerShell.png
[img-hdi-hive-powershell-output]: ./media/hdinsight-use-hive/HDI.Hive.PowerShell.Output.png
[image-hdi-hive-architecture]: ./media/hdinsight-use-hive/HDI.Hive.Architecture.png

<!--HONumber=35_1-->
