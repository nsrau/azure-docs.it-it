<properties linkid="manage-services-hdinsight-howto-hive" urlDisplayName="Use Hadoop Hive in HDInsight" pageTitle="Use Hadoop Hive in HDInsight | Azure" metaKeywords="" description="Learn how to use Hive with HDInsight. You'll use a log file as input into an HDInsight table, and use HiveQL to query the data and report basic statistics." metaCanonical="" services="hdinsight" documentationCenter="" title="Use Hadoop Hive in HDInsight" authors="jgao" solutions="" manager="paulettm" editor="cgronlun" />

<tags ms.service="hdinsight" ms.workload="big-data" ms.tgt_pltfrm="na" ms.devlang="na" ms.topic="article" ms.date="01/01/1900" ms.author="jgao" />

# Usare Hive con Hadoop in HDInsight

[Apache Hive][Apache Hive] fornisce un metodo per l'esecuzione di processi MapReduce mediante un linguaggio di scripting simile a SQL, denominato HiveQL, che può essere applicato per attività di riepilogo, query e analisi di volumi di dati molto elevati. In questo articolo si utilizzerà HiveQL per eseguire query sui dati in un file di log log4j di Apache e per creare report delle statistiche di base.

**Prerequisiti:**

-   È necessario avere completato il provisioning di un **cluster HDInsight**. Per una procedura dettagliata su come eseguire questa operazione con il portale di Azure, vedere [Introduzione all'utilizzo di HDInsight][Introduzione all'utilizzo di HDInsight]. Per informazioni sui vari altri metodi di creazione di tali cluster e per le relative istruzioni, vedere [Provisioning di cluster HDInsight][Provisioning di cluster HDInsight].

-   È necessario avere installato **Azure PowerShell** nella workstation. Per le relative istruzioni, vedere [Installazione e configurazione di Azure PowerShell][Installazione e configurazione di Azure PowerShell].

**Tempo previsto per il completamento:** 30 minuti

## Contenuto dell'articolo

-   [Caso di utilizzo di Hive][Caso di utilizzo di Hive]
-   [Caricamento di file di dati nell'archivio BLOB di Azure][Caricamento di file di dati nell'archivio BLOB di Azure]
-   [Esecuzione di query Hive utilizzando PowerShell][Esecuzione di query Hive utilizzando PowerShell]
-   [Passaggi successivi][Passaggi successivi]

## <span id="usage"></span></a>Caso di utilizzo di Hive

I database sono appropriati quando si gestiscono insiemi di dati di dimensioni contenute per i quali sono possibili query a bassa latenza. Tuttavia, quando si tratta di gestire set di Big Data che contengono terabyte di dati, i database SQL tradizionali spesso non rappresentano la soluzione ideale. Per gestire questi set di dati più voluminosi, gli amministratori abitualmente ricorrono alla scalabilità verticale e all'acquisto di hardware più capiente per contrastare l'aumento del carico del database e il conseguente degrado delle prestazioni.

Hive consente di risolvere i problemi associati ai Big Data, consentendo agli utenti di fruire della scalabilità orizzontale quando eseguono query su set di dati di grandi dimensioni. Hive esegue query sui dati in parallelo e su più nodi utilizzando MapReduce e distribuisce il database su un numero crescente di host con l'aumentare del carico.

Hive e HiveQL offrono inoltre un'alternativa alla scrittura di processi MapReduce in Java quando si eseguono query sui dati. Viene fornito un wrapper analogo a quello di SQL che consente la scrittura delle query in HiveQL per la successiva compilazione in MapReduce da parte di HDInsight e l'esecuzione nel cluster.

Hive consente inoltre ai programmatori che hanno familiarità con il framework MapReduce di aggiungere mapper e reducer personalizzati per eseguire analisi più sofisticate che potrebbero non essere supportate dalle funzionalità incorporate del linguaggio HiveQL.

Hive è particolarmente adatto per l'elaborazione batch di grandi quantità di dati non modificabili, ad esempio log Web. Non è appropriato per applicazioni di transazione che richiedono tempi di risposta molto rapidi, come ad esempio sistemi di gestione di database. Hive è ottimizzato per la scalabilità (è possibile aggiungere più macchine in modo dinamico al cluster Hadoop), l'estensibilità (all'interno del framework MapReduce e con altre interfacce di programmazione) e la tolleranza di errore. La latenza non è una considerazione chiave di progettazione.

In generale le applicazioni salvano errori, eccezioni e altri problemi codificati in un file di log, quindi gli amministratori possono utilizzare i dati nei file di log per esaminare i problemi che possono sorgere e generare metriche relative a errori o ad altri problemi, ad esempio quelli correlati alle prestazioni. Questi file di log solitamente raggiungono dimensioni molto grandi e contengono una varietà di dati che devono essere elaborati e sottoposti a mining per l'estrazione di informazioni utili per l'applicazione.

I file di log sono quindi un esempio paradigmatico di Big Data. HDInsight fornisce un sistema di data warehouse Hive che semplifica il riepilogo dei dati, le query ad hoc e l'analisi di questi grandi dataset memorizzati in file system compatibili con Hadoop, ad esempio l'archivio BLOB di Azure.

## <span id="uploaddata"></span></a>Caricamento di file di dati nell'archiviazione BLOB

HDInsight utilizza il contenitore dell'archivio BLOB di Azure come file system predefinito. Per altre informazioni, vedere [Usare l'archivio BLOB di Azure con HDInsight][Usare l'archivio BLOB di Azure con HDInsight].

In questo articolo viene utilizzato un file log4j di esempio distribuito con il cluster HDInsight e archiviato nel percorso *\\example\\data\\sample.log*. Ogni log all'interno del file è costituito da una riga di campi che contiene un campo `[LOG LEVEL]` per visualizzare il tipo e la gravità. Ad esempio:

    2012-02-03 20:26:41 SampleClass3 [ERROR] verbose detail for id 1527353937 

Per accedere ai file, utilizzare la sintassi seguente:

    wasb://<containerName>@<AzureStorageName>.blob.core.windows.net

Ad esempio:

    wasb://mycontainer@mystorage.blob.core.windows.net/example/data/sample.log

sostituire *mycontainer* con il nome del contenitore e *mystorage* con il nome dell'account di archiviazione BLOB.

Poiché questo file è memorizzato nel file system predefinito, è possibile accedervi anche utilizzando la sintassi seguente:

    wasb:///example/data/sample.log
    /example/data/sample.log

Per generare i propri file log4j, utilizzare l'utilità di registrazione [Apache Log4j][Apache Log4j]. Per informazioni sul caricamento di dati dall'archivio BLOB di Azure, vedere [Caricare i dati in HDInsight][Caricare i dati in HDInsight].

## <span id="runhivequeries"></span></a> Esecuzione di query Hive utilizzando PowerShell

Nella sezione precedente si è caricato un file log4j denominato sample.log nel contenitore del file system predefinito. In questa sezione si eseguirà HiveQL per creare una tabella Hive, caricarvi i dati e infine eseguire query sui dati per scoprire quanti log di errore erano presenti.

In questo articolo vengono fornite le istruzioni per utilizzare i cmdlet di Azure PowerShell per eseguire una query Hive. Prima di procedere in questa sezione, è necessario innanzitutto configurare l'ambiente locale e la connessione ad Azure come spiegato nella sezione **Prerequisiti** all'inizio di questo argomento.

Le query Hive possono essere eseguite in PowerShell utilizzando il cmdlet **Start-AzureHDInsightJob** o il cmdlet **Invoke-Hive**.

**Per eseguire query Hive utilizzando Start-AzureHDInsightJob**

1.  Aprire una finestra della console di Azure PowerShell. Per istruzioni, vedere [Come installare e configurare Azure PowerShell][Installazione e configurazione di Azure PowerShell].
2.  Eseguire il comando seguente per connettersi alla sottoscrizione di Azure:

        Add-AzureAccount

    Verrà richiesto di immettere le credenziali dell'account Azure.

3.  Impostare le variabili nello script seguente ed eseguirlo:

        # Provide Azure subscription name, and the Azure Storage account and container that is used for the default HDInsight file system.
        $subscriptionName = "<SubscriptionName>"
        $storageAccountName = "<AzureStorageAccountName>"
        $containerName = "<AzureStorageContainerName>"

        # Provide HDInsight cluster name Where you want to run the Hive job
        $clusterName = "<HDInsightClusterName>"

4.  Eseguire lo script seguente per definire le query HiveQL:

        # HiveQL queries
        # Use the internal table option. 
        $queryString = "DROP TABLE log4jLogs;" +
                       "CREATE TABLE log4jLogs(t1 string, t2 string, t3 string, t4 string, t5 string, t6 string, t7 string) ROW FORMAT DELIMITED FIELDS TERMINATED BY ' ';" +
                       "LOAD DATA INPATH 'wasb://$containerName@$storageAccountName.blob.core.windows.net/example/data/sample.log' OVERWRITE INTO TABLE log4jLogs;" +
                       "SELECT t4 AS sev, COUNT(*) AS cnt FROM log4jLogs WHERE t4 = '[ERROR]' GROUP BY t4;"

        # Use the external table option. 
        $queryString = "DROP TABLE log4jLogs;" +
                        "CREATE EXTERNAL TABLE log4jLogs(t1 string, t2 string, t3 string, t4 string, t5 string, t6 string, t7 string) ROW FORMAT DELIMITED FIELDS TERMINATED BY ' ' STORED AS TEXTFILE LOCATION 'wasb://$containerName@$storageAccountName.blob.core.windows.net/example/data/';" +
                        "SELECT t4 AS sev, COUNT(*) AS cnt FROM log4jLogs WHERE t4 = '[ERROR]' GROUP BY t4;"

    The LOAD DATA HiveQL command will result in moving the data file to the \\hive\\warehouse\\ folder. Il comando DROP TABLE elimina la tabella e il file di dati. Se si utilizza l'opzione tabella interna e si desidera eseguire nuovamente lo script, è necessario caricare nuovamente il file sample.log. Se si desidera mantenere il file di dati, è necessario utilizzare il comando CREATE EXTERNAL TABLE come mostrato nello script.

    È anche possibile utilizzare la tabella esterna per situazioni in cui il file di dati si trova in un contenitore o account di archiviazione diverso.

    Utilizzare l'istruzione DROP TABLE per prima nel caso in cui si esegua lo script di nuovo e la tabella log4jlogs esista già.

5.  Eseguire lo script seguente per creare una definizione processo Hive:

        # Create a Hive job definition 
        $hiveJobDefinition = New-AzureHDInsightHiveJobDefinition -Query $queryString 

    È anche possibile utilizzare l'opzione -File per specificare un file di script HiveQL in HDFS.

6.  Eseguire lo script seguente per inviare il processo Hive:

        # Submit the job to the cluster 
        Select-AzureSubscription $subscriptionName
        $hiveJob = Start-AzureHDInsightJob -Cluster $clusterName -JobDefinition $hiveJobDefinition

7.  Eseguire lo script seguente per attendere il completamento del processo Hive:

        # Wait for the Hive job to complete
        Wait-AzureHDInsightJob -Job $hiveJob -WaitTimeoutInSeconds 3600

8.  Eseguire lo script seguente per stampare l'output standard:

		# Print the standard error and the standard output of the Hive job.
        Get-AzureHDInsightJobOutput -Cluster $clusterName -JobId $hiveJob.JobId -StandardOutput

    ![HDI.HIVE.PowerShell][HDI.HIVE.PowerShell]

    Il risultato è:

        [ERROR] 3

**Per inviare query Hive utilizzando Invoke-Hive**

1.  Aprire una finestra della console di Azure PowerShell.
2.  Eseguire il comando seguente per connettersi alla sottoscrizione di Azure:

        Add-AzureAccount

    Verrà richiesto di immettere le credenziali dell'account Azure.

3.  Impostare la variabile, e procedere all'esecuzione:

        $clusterName = "<HDInsightClusterName>"

4.  Eseguire lo script seguente per richiamare le query HiveQL:

        Use-AzureHDInsightCluster $clusterName 
        $response = Invoke-Hive -Query @"
            SELECT * FROM hivesampletable
                WHERE devicemake LIKE "HTC%"
                LIMIT 10; 
        "@

        Write-Host $response

    L'output è:

    ![Output di PowerShell Invoke-Hive][Output di PowerShell Invoke-Hive]

    Per query HiveQL più lunghe, è consigliabile utilizzare stringhe Here di PowerShell o un file di script HiveQL. Negli esempi seguenti viene illustrato come utilizzare il cmdlet Invoke-Hive per eseguire un file di script HiveQL. Il file di script HiveQL deve essere caricato in WASB.

        Invoke-Hive -File "wasb://<ContainerName>@<StorageAccountName>/<Path>/query.hql"

    Per ulteriori informazioni sulle stringhe Here, vedere [Utilizzo delle stringhe Here di PowerShell][Utilizzo delle stringhe Here di PowerShell].

## <span id="nextsteps"></span></a>Passaggi successivi

Mentre Hive semplifica l'interrogazione dei dati utilizzando un linguaggio di query simile a SQL, altri componenti disponibili con HDInsight forniscono funzionalità complementari quali lo spostamento e la trasformazione dei dati. Per ulteriori informazioni, vedere gli articoli seguenti:

-   [Introduzione all'utilizzo di Azure HDInsight][Introduzione all'utilizzo di HDInsight]
-   [Analizzare i dati sui ritardi dei voli utilizzando HDInsight][Analizzare i dati sui ritardi dei voli utilizzando HDInsight]
-   [Usare Oozie con HDInsight][Usare Oozie con HDInsight]
-   [Inviare processi Hadoop a livello di codice][Inviare processi Hadoop a livello di codice]
-   [Caricare i dati in HDInsight][Caricare i dati in HDInsight]
-   [Usare Pig con HDInsight][Usare Pig con HDInsight]
-   [Documentazione di Azure HDInsight SDK][Documentazione di Azure HDInsight SDK]

  [Apache Hive]: http://hive.apache.org/
  [Introduzione all'utilizzo di HDInsight]: ../hdinsight-get-started/
  [Provisioning di cluster HDInsight]: ../hdinsight-provision-clusters/
  [Installazione e configurazione di Azure PowerShell]: ../install-configure-powershell/
  [Caso di utilizzo di Hive]: #usage
  [Caricamento di file di dati nell'archivio BLOB di Azure]: #uploaddata
  [Esecuzione di query Hive utilizzando PowerShell]: #runhivequeries
  [Passaggi successivi]: #nextsteps
  [Usare l'archivio BLOB di Azure con HDInsight]: ../hdinsight-use-blob-storage
  [Apache Log4j]: http://en.wikipedia.org/wiki/Log4j
  [Caricare i dati in HDInsight]: ../hdinsight-upload-data/
  [HDI.HIVE.PowerShell]: ./media/hdinsight-use-hive/HDI.HIVE.PowerShell.png
  [Output di PowerShell Invoke-Hive]: ./media/hdinsight-use-hive/HDI.Hive.PowerShell.Output.png
  [Utilizzo delle stringhe Here di PowerShell]: http://technet.microsoft.com/it-it/library/ee692792.aspx
  [Analizzare i dati sui ritardi dei voli utilizzando HDInsight]: ../hdinsight-analyze-flight-delay-data/
  [Usare Oozie con HDInsight]: ../hdinsight-use-oozie/
  [Inviare processi Hadoop a livello di codice]: ../hdinsight-submit-hadoop-jobs-programmatically/
  [Usare Pig con HDInsight]: ../hdinsight-use-pig/
  [Documentazione di Azure HDInsight SDK]: http://msdnstage.redmond.corp.microsoft.com/it-it/library/dn479185.aspx
