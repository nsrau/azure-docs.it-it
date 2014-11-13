<properties urlDisplayName="Use Hadoop Hive in HDInsight" pageTitle="Usare Hive di Hadoop in HDInsight | Azure" metaKeywords="" description="Informazioni su come usare Hive con HDInsight. Si user&agrave; un file di log come input in una tabella di HDInsight e HiveQL per eseguire query sui dati e per creare report delle statistiche di base." metaCanonical="" services="hdinsight" documentationCenter="" title="Usare Hive di Hadoop in HDInsight" authors="jgao" solutions="" manager="paulettm" editor="cgronlun" />

<tags ms.service="hdinsight" ms.workload="big-data" ms.tgt_pltfrm="na" ms.devlang="na" ms.topic="article" ms.date="09/25/2014" ms.author="jgao" />

# Usare Hive con Hadoop in HDInsight

[Apache Hive][Apache Hive] fornisce un metodo per l'esecuzione di processi MapReduce mediante un linguaggio di scripting simile a SQL, denominato *HiveQL*. Hive è un sistema di data warehouse per Hadoop, che consente di eseguire attività di riepilogo, query e analisi di volumi di dati molto elevati. In questo articolo si userà HiveQL per eseguire query su un file di dati di esempio, fornito come parte del provisioning del cluster HDInsight.

**Prerequisiti:**

-   È necessario avere completato il provisioning di un **cluster HDInsight**. Per una procedura dettagliata su come eseguire questa operazione con il portale di Azure, vedere [Introduzione all'uso di HDInsight][Introduzione all'uso di HDInsight]. Per informazioni sui vari altri metodi di creazione di tali cluster e per le relative istruzioni, vedere [Provisioning di cluster HDInsight][Provisioning di cluster HDInsight].

-   È necessario avere installato **Azure PowerShell** nella workstation. Per le relative istruzioni, vedere [Installazione e configurazione di Azure PowerShell][Installazione e configurazione di Azure PowerShell].

**Tempo previsto per il completamento:** 30 minuti

## Contenuto dell'articolo

-   [Caso di utilizzo di Hive][Caso di utilizzo di Hive]
-   [Caricare i dati per le tabelle Hive][Caricare i dati per le tabelle Hive]
-   [Esecuzione di query Hive usando PowerShell][Esecuzione di query Hive usando PowerShell]
-   [Usare Tez per ottenere prestazioni migliorate][Usare Tez per ottenere prestazioni migliorate]
-   [Passaggi successivi][Passaggi successivi]

## <span id="usage"></span></a>Caso di utilizzo di Hive

![HDI.HIVE.Architecture][HDI.HIVE.Architecture]

Hive proietta una specie di struttura su un set di dati sostanzialmente non strutturato (cioè la natura stessa di Hadoop), quindi consente di eseguire query su tali dati usando HiveQL. Hive fornisce un livello di astrazione superiore al framework MapReduce basato su Java, consentendo agli utenti di eseguire query sui dati senza alcuna conoscenza di Java o MapReduce. HiveQL fornisce un wrapper analogo a quello di SQL che consente la scrittura delle query in HiveQL per la successiva compilazione in MapReduce da parte di HDInsight e l'esecuzione nel cluster. Tra gli altri vantaggi offerti da Hive sono inclusi i seguenti:

-   Hive consente ai programmatori che hanno familiarità con il framework MapReduce di aggiungere mapper e reducer personalizzati per eseguire analisi più sofisticate che potrebbero non essere supportate dalle funzionalità incorporate del linguaggio HiveQL.
-   Hive è particolarmente adatto per l'elaborazione batch di grandi quantità di dati non modificabili, ad esempio log Web. Non è appropriato per applicazioni di transazione che richiedono tempi di risposta molto rapidi, come ad esempio sistemi di gestione di database.
-   Hive è ottimizzato per la scalabilità (è possibile aggiungere più macchine in modo dinamico al cluster Hadoop), l'estensibilità (all'interno del framework MapReduce e con altre interfacce di programmazione) e la tolleranza di errore. La latenza non è una considerazione chiave di progettazione.

## <span id="uploaddata"></span></a>Caricare i dati per le tabelle Hive

HDInsight usa il contenitore dell'archivio BLOB di Azure come file system predefinito per i cluster Hadoop. Alcuni file di dati di esempio vengono aggiunti all'archivio BLOB come parte del provisioning del cluster ed è possibile usarli per eseguire query Hive sul cluster. Volendo è anche possibile caricare il proprio file di dati nell'account di archiviazione BLOB associato al cluster. Per istruzioni, vedere [Caricamento di dati in HDInsight][Caricamento di dati in HDInsight]. Per altre informazioni sul modo in cui HDInsight usa l'archivio BLOB di Azure, vedere [Usare l'archivio BLOB di Azure con HDInsight][Usare l'archivio BLOB di Azure con HDInsight].

In questo articolo viene usato un file *log4j* di esempio distribuito con il cluster HDInsight e archiviato nel percorso *\\example\\data\\sample.log* nel contenitore di archiviazione BLOB. È anche possibile generare i propri file log4j usando l'utilità di registrazione [Apache Log4j][Apache Log4j] e quindi caricandoli nel contenitore BLOB. Ogni log all'interno del file è costituito da una riga di campi che contiene un campo `[LOG LEVEL]` per visualizzare il tipo e la gravità. Ad esempio:

    2012-02-03 20:26:41 SampleClass3 [ERROR] verbose detail for id 1527353937 

Nell'esempio precedente, il livello log è ERROR.

È possibile accedere ai file usando la sintassi seguente dall'applicazione stessa:

    wasb://<containerName>@<AzureStorageName>.blob.core.windows.net

Quindi, per accedere al file sample.log, usare la sintassi seguente:

    wasb://mycontainer@mystorage.blob.core.windows.net/example/data/sample.log

Sostituire *mycontainer* con il nome del contenitore BLOB di Azure e *mystorage* con il nome dell'account di archiviazione di Azure.

Poiché questo file è memorizzato nel file system predefinito, è possibile accedervi anche usando la sintassi seguente:

    wasb:///example/data/sample.log
    /example/data/sample.log

## <span id="runhivequeries"></span></a> Esecuzione di query Hive usando PowerShell

Nell'ultima sezione è stato identificato un file *sample.log* che verrà usato per eseguire le query Hive. In questa sezione si eseguirà HiveQL per creare una tabella Hive, caricarvi i dati di esempio e infine eseguire query sui dati per scoprire quanti log di errore sono presenti nel file.

In questo articolo vengono fornite le istruzioni per usare i cmdlet di Azure PowerShell per eseguire una query Hive. Prima di procedere in questa sezione, è necessario innanzitutto configurare l'ambiente locale e la connessione ad Azure come spiegato nella sezione **Prerequisiti** all'inizio di questo argomento.

Le query Hive possono essere eseguite in PowerShell usando il cmdlet **Start-AzureHDInsightJob** o il cmdlet **Invoke-Hive**.

**Per eseguire query Hive usando Start-AzureHDInsightJob**

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

4.  Eseguire lo script seguente per definire le query HiveQL. È possibile scegliere di creare una tabella Hive *interna* o *esterna*.

    -   Con le tabelle interne, i dati di esempio usati vengono spostati dal percorso esistente alla cartella \\hive\\warehouse\\\<*nometabella\>*. Dunque, quando si rimuove una cartella interna verranno eliminati anche i dati a essa associati. Se si usano le tabelle interne e si vuole ripetere l'esecuzione dello script, è necessario caricare nuovamente il file *sample.log* nell'archivio BLOB.
    -   Con le tabelle esterne i dati non vengono spostati dal percorso originale. È anche possibile usare la tabella esterna per situazioni in cui il file di dati si trova in un contenitore o account di archiviazione diverso.

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

    Il comando DROP TABLE consente di eliminare la tabella e il file di dati, nel caso in cui la tabella esista già. Il comando LOAD DATA HiveQL consente di spostare il file di dati dal percorso \\example\\data alla cartella \\hive\\warehouse\\\<*nometabella\>*.

5.  Eseguire lo script seguente per creare una definizione processo Hive:

        # Create a Hive job definition 
        $hiveJobDefinition = New-AzureHDInsightHiveJobDefinition -Query $queryString 

    È anche possibile usare l'opzione -File per specificare un file di script HiveQL in HDFS.

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

    Il risultato è il seguente:

        [ERROR] 3

    Ciò significa che il file *sample.log* conteneva tre istanze di log ERROR.

Se necessario, è anche possibile importare l'output delle query in Microsoft Excel per ulteriori analisi. Per istruzioni, vedere [Connettere Excel a Hadoop mediante Power Query][Connettere Excel a Hadoop mediante Power Query].

**Per inviare query Hive usando Invoke-Hive**

1.  Aprire una finestra della console di Azure PowerShell.
2.  Eseguire il comando seguente per connettersi alla sottoscrizione di Azure:

        Add-AzureAccount

    Verrà richiesto di immettere le credenziali dell'account Azure.

3.  Impostare le variabili nello script seguente ed eseguirlo:

        # Provide Azure subscription name, Azure Storage account, and container.
        $subscriptionName = "<SubscriptionName>"
        $storageAccountName = "<AzureStorageAccountName>"
        $containerName = "<AzureStorageContainerName>"

        # Provide HDInsight cluster name Where you want to run the Hive job
        $clusterName = "<HDInsightClusterName>"

4.  Connettersi al cluster HDInsight.

        # Connect to the cluster
        Use-AzureHDInsightCluster $clusterName

5.  Eseguire lo script seguente per richiamare le query HiveQL:

        # Run the query to create an internal Hive table, load sample data
        $response = Invoke-Hive -Query @"
        >> DROP TABLE log4jLogs;
        >> CREATE TABLE log4jLogs(t1 string, t2 string, t3 string, t4 string, t5 string, t6 string, t7 string) ROW   
            FORMAT DELIMITED FIELDS TERMINATED BY ' ';
        >> LOAD DATA INPATH 'wasb://$containerName@$storageAccountName.blob.core.windows.net/example/data/
            sample.log' OVERWRITE INTO TABLE log4jLogs;
        >> SELECT t4 AS sev, COUNT(*) AS cnt FROM log4jLogs WHERE t4 = '[ERROR]' GROUP BY t4; 
        >> "@

6.  Stampare l'output nella console.

        # print the output on the console
        Write-Host $response

    L'output è simile al seguente:

    ![Output di PowerShell Invoke-Hive][Output di PowerShell Invoke-Hive]

    Per query HiveQL più lunghe, è possibile usare stringhe Here di PowerShell o un file di script HiveQL. Nel frammento di codice seguente viene illustrato come usare il cmdlet *Invoke-Hive* per eseguire un file di script HiveQL. Il file di script HiveQL deve essere caricato in WASB.

        Invoke-Hive -File "wasb://<ContainerName>@<StorageAccountName>/<Path>/query.hql"

    Per altre informazioni sulle stringhe Here, vedere [Uso delle stringhe Here di PowerShell][Uso delle stringhe Here di PowerShell].

Se necessario, è anche possibile importare l'output delle query in Microsoft Excel per ulteriori analisi. Per istruzioni, vedere [Connettere Excel a Hadoop mediante Power Query][Connettere Excel a Hadoop mediante Power Query].

## <span id="usetez"></span></a>Usare Tez per ottenere prestazioni migliorate

[Apache Tez][Apache Tez] è un framework che consente di eseguire le applicazioni come Hive, che richiedono un uso elevato di dati, in modo molto più efficiente e scalabile. Nella versione più recente di HDInsight, Hive ora supporta l'esecuzione su Tez. Questa funzionalità è attualmente disattivata per impostazione predefinita ed è necessario attivarla. Nelle future versioni dei cluster verrà impostata come attiva per impostazione predefinita. Per poter sfruttare Tez, è necessario impostare il valore seguente per una query Hive:

        set hive.execution.engine=tez;
        

È possibile inviarlo in ogni query inserendolo all'inizio della stessa. È anche possibile impostarlo come valore predefinito su un cluster scegliendo il valore di configurazione al momento della creazione del cluster. Per informazioni più dettagliate, vedere [Provisioning di cluster HDInsight][Provisioning di cluster HDInsight].

La [documentazione sulla progettazione di Hive su Tez][documentazione sulla progettazione di Hive su Tez] contiene una serie di informazioni dettagliate sulle scelte di implementazione e l'ottimizzazione delle configurazioni.

## <span id="nextsteps"></span></a>Passaggi successivi

Mentre Hive semplifica l'interrogazione dei dati usando un linguaggio di query simile a SQL, altri componenti disponibili con HDInsight forniscono funzionalità complementari quali lo spostamento e la trasformazione dei dati. Per altre informazioni, vedere gli articoli seguenti:

-   [Usare Oozie con HDInsight][Usare Oozie con HDInsight]
-   [Inviare processi Hadoop a livello di codice][Inviare processi Hadoop a livello di codice]
-   [Usare Pig con HDInsight][Usare Pig con HDInsight]
-   [Analizzare i dati sui ritardi dei voli usando HDInsight][Analizzare i dati sui ritardi dei voli usando HDInsight]
-   [Documentazione di Azure HDInsight SDK][Documentazione di Azure HDInsight SDK]
-   [Caricare i dati in HDInsight][Caricamento di dati in HDInsight]
-   [Introduzione all'utilizzo di Azure HDInsight][Introduzione all'uso di HDInsight]

  [Apache Hive]: http://hive.apache.org/
  [Introduzione all'uso di HDInsight]: ../hdinsight-get-started/
  [Provisioning di cluster HDInsight]: ../hdinsight-provision-clusters/
  [Installazione e configurazione di Azure PowerShell]: ../install-configure-powershell/
  [Caso di utilizzo di Hive]: #usage
  [Caricare i dati per le tabelle Hive]: #uploaddata
  [Esecuzione di query Hive usando PowerShell]: #runhivequeries
  [Usare Tez per ottenere prestazioni migliorate]: #usetez
  [Passaggi successivi]: #nextsteps
  [HDI.HIVE.Architecture]: ./media/hdinsight-use-hive/HDI.Hive.Architecture.png
  [Caricamento di dati in HDInsight]: ../hdinsight-upload-data/
  [Usare l'archivio BLOB di Azure con HDInsight]: ../hdinsight-use-blob-storage
  [Apache Log4j]: http://en.wikipedia.org/wiki/Log4j
  [HDI.HIVE.PowerShell]: ./media/hdinsight-use-hive/HDI.HIVE.PowerShell.png
  [Connettere Excel a Hadoop mediante Power Query]: http://azure.microsoft.com/it-it/documentation/articles/hdinsight-connect-excel-power-query/
  [Output di PowerShell Invoke-Hive]: ./media/hdinsight-use-hive/HDI.Hive.PowerShell.Output.png
  [Uso delle stringhe Here di PowerShell]: http://technet.microsoft.com/it-it/library/ee692792.aspx
  [Apache Tez]: http://tez.apache.org
  [documentazione sulla progettazione di Hive su Tez]: https://cwiki.apache.org/confluence/display/Hive/Hive+on+Tez
  [Usare Oozie con HDInsight]: ../hdinsight-use-oozie/
  [Inviare processi Hadoop a livello di codice]: ../hdinsight-submit-hadoop-jobs-programmatically/
  [Usare Pig con HDInsight]: ../hdinsight-use-pig/
  [Analizzare i dati sui ritardi dei voli usando HDInsight]: ../hdinsight-analyze-flight-delay-data/
  [Documentazione di Azure HDInsight SDK]: http://msdnstage.redmond.corp.microsoft.com/it-it/library/dn479185.aspx
