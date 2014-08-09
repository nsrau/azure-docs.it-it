<properties linkid="manage-services-hdinsight-howto-pig" urlDisplayName="Use Pig with HDInsight" pageTitle="Use Pig with HDInsight | Azure" metaKeywords="" description="Learn how to use Pig with HDInsight. Write Pig Latin statements to analyze an application log file, and run queries on the data to generate output for analysis." metaCanonical="" services="hdinsight" documentationCenter="" title="Use Pig with HDInsight" authors="jgao" solutions="" manager="paulettm" editor="cgronlun" />

Utilizzo di Pig con HDInsight
=============================

[Apache *Pig*](http://pig.apache.org/) offre un linguaggio di scripting per l'esecuzione di processi *MapReduce* in alternativa alla scrittura di codice Java. In questa esercitazione verrà utilizzato PowerShell per eseguire alcune istruzioni Pig Latin per analizzare un file di log log4j di Apache e si eseguiranno diverse query dei dati per generare l'output. Questa esercitazione mostra i vantaggi di Pig e come sia possibile utilizzarlo per semplificare i processi MapReduce.

Il linguaggio di scripting di Pig è chiamato *Pig Latin*. Le istruzioni di Pig Latin seguono questo flusso generale:

-   **Caricamento**: lettura dei dati da manipolare dal file system
-   **Trasformazione**: manipolazione dei dati
-   **Dump o archiviazione**: output dei dati sullo schermo o archiviazione per l'elaborazione

Per ulteriori informazioni su Pig Latin, vedere il [manuale di riferimento di Pig Latin 1](http://pig.apache.org/docs/r0.7.0/piglatin_ref1.html) e il [manuale di riferimento di Pig Latin 2](http://pig.apache.org/docs/r0.7.0/piglatin_ref2.html).

**Prerequisiti**

Per eseguire le procedure descritte nell'articolo è necessario:

-   Disporre di un cluster HDInsight di Azure. Per istruzioni, vedere [Introduzione all'utilizzo di Azure HDInsight](/en-us/manage/services/hdinsight/get-started-hdinsight/) o [Provisioning di cluster HDInsight](/en-us/manage/services/hdinsight/provision-hdinsight-clusters/).
-   Installare e configurare Azure PowerShell. Per istruzioni, vedere [Come installare e configurare Azure PowerShell](/it-it/documentation/articles/install-configure-powershell/).

**Tempo previsto per il completamento:** 30 minuti

Contenuto dell'articolo
-----------------------

-   [Caso di utilizzo di Pig](#usage)
-   [Caricamento di file di dati nell'archivio BLOB di Azure](#uploaddata)
-   [Informazioni su Pig Latin](#understand)
-   [Esecuzione di Pig Latin mediante PowerShell](#powershell)
-   [Passaggi successivi](#nextsteps)

Caso di utilizzo di Pig
-----------------------

I database sono ideali per piccoli set di dati e query a bassa latenza. Tuttavia, quando si tratta di gestire set di Big Data e set di dati di grandi dimensioni nell'ordine dei terabyte, i database SQL tradizionali non rappresentano la soluzione ideale. L'aumento del carico dei database e l'abbassamento delle prestazioni costringono da sempre gli amministratori di database ad acquistare hardware di dimensioni maggiori.

Tutte le applicazioni, in genere, salvano gli errori, le eccezioni e altre problematiche codificate in un file di log, in modo che gli amministratori possano esaminare i problemi o generare determinate metriche dai dati del file di log. Questi file di log solitamente raggiungono dimensioni molto grandi perché contengono una varietà di dati che devono essere elaborati e sottoposti a mining.

I file di log sono un valido esempio di Big Data. È difficile lavorare con i Big Data utilizzando database relazionali e statistiche/pacchetti di visualizzazione. A causa delle grandi quantità di dati, è spesso necessario un software parallelo in esecuzione su decine, centinaia o anche migliaia di server per poter calcolare questi dati in un tempo ragionevole. Hadoop fornisce un framework MapReduce per scrivere applicazioni che elaborano in parallelo grandi quantità di dati strutturati e non strutturati in grandi cluster di computer in modo del tutto affidabile e a tolleranza di errore.

L'utilizzo di Pig riduce il tempo necessario per scrivere programmi mapper e reducer. Non è pertanto necessario utilizzare codice Java o boilerplate. È inoltre possibile combinare il codice Java con Pig. Molti algoritmi complessi possono essere scritti in meno di cinque righe di codice Pig in un formato leggibile.

Le due figure seguenti mostrano la rappresentazione visiva di ciò che si eseguirà in questo articolo. Queste figure, che costituiscono un esempio rappresentativo del set di dati, illustrano il flusso e la trasformazione dei dati mentre si scorrono le righe di codice Pig nello script. La prima figura mostra un esempio del file log4j:

![Esempio di file completo](./media/hdinsight-use-pig/HDI.wholesamplefile.png)

La seconda figura mostra la trasformazione dei dati:

![HDI.PIG.Data.Transformation](./media/hdinsight-use-pig/HDI.DataTransformation.gif)

Caricamento del file di dati nell'archiviazione BLOB
----------------------------------------------------

HDInsight utilizza un contenitore dell'archivio BLOB di Azure come file system predefinito. Per ulteriori informazioni, vedere [Utilizzo dell'archivio BLOB di Azure con HDInsight](/en-us/manage/services/hdinsight/howto-blob-store/). In questo articolo verrà utilizzato un file log4j di esempio distribuito con il cluster HDInsight e archiviato nel percorso *\\example\\data\\sample.log*. Per informazioni sul caricamento di dati, vedere [Caricamento di dati in HDInsight](/en-us/manage/services/hdinsight/howto-upload-data-to-hdinsight/).

Per accedere ai file, utilizzare la sintassi seguente:

     wasb[s]://[<containerName>@<storageAccountName>.blob.core.windows.net]/<path>/<filename>

Ad esempio:

     wasb://mycontainer@mystorage.blob.core.windows.net/example/data/sample.log

sostituire *mycontainer* con il nome del contenitore e *mystorage* con il nome dell'account di archiviazione BLOB.

Poiché questo file è memorizzato nel file system predefinito, è possibile accedervi anche utilizzando la sintassi seguente:

     wasb:///example/data/sample.log
        /example/data/sample.log

Informazioni su Pig Latin
-------------------------

In questa sessione, si esamineranno alcune istruzioni Pig Latin e i risultati dopo l'esecuzione delle istruzioni. Nella sessione successiva, si utilizzerà PowerShell per eseguire le istruzioni Pig.

1.  Caricare i dati dal file system e quindi visualizzare i risultati

         LOGS = LOAD 'wasb:///example/data/sample.log';
         DUMP LOGS;

    L'output è simile al seguente:

         (2012-02-05 19:23:50 SampleClass5 [TRACE] verbose detail for id 313393809)
         (2012-02-05 19:23:50 SampleClass6 [DEBUG] detail for id 536603383)
         (2012-02-05 19:23:50 SampleClass9 [TRACE] verbose detail for id 564842645)
         (2012-02-05 19:23:50 SampleClass8 [TRACE] verbose detail for id 1929822199)
         (2012-02-05 19:23:50 SampleClass5 [DEBUG] detail for id 1599724386)
         (2012-02-05 19:23:50 SampleClass0 [INFO] everything normal for id 2047808796)
         (2012-02-05 19:23:50 SampleClass2 [TRACE] verbose detail for id 1774407365)
         (2012-02-05 19:23:50 SampleClass2 [TRACE] verbose detail for id 2099982986)
         (2012-02-05 19:23:50 SampleClass4 [DEBUG] detail for id 180683124)
         (2012-02-05 19:23:50 SampleClass2 [TRACE] verbose detail for id 1072988373)
         (2012-02-05 19:23:50 SampleClass9 [TRACE] verbose detail)
         ...

2.  Scorrere ogni riga del file di dati per trovare una corrispondenza nei 6 livelli del log:

         LEVELS = foreach LOGS generate REGEX_EXTRACT($0, '(TRACE|DEBUG|INFO|WARN|ERROR|FATAL)', 1)  as LOGLEVEL;

3.  Filtrare le righe senza corrispondenza. Ad esempio, le righe vuote.

         FILTEREDLEVELS = FILTER LEVELS by LOGLEVEL is not null;
         DUMP FILTEREDLEVELS;

    L'output è simile al seguente:

         (DEBUG)
         (TRACE)
         (TRACE)
         (DEBUG)
         (TRACE)
         (TRACE)
         (DEBUG)
         (TRACE)
         (TRACE)
         (DEBUG)
         (TRACE)
         ...

4.  Raggruppare tutti i livelli del log nella riga corrispondente:

         GROUPEDLEVELS = GROUP FILTEREDLEVELS by LOGLEVEL;
         DUMP GROUPEDLEVELS;

    L'output è simile al seguente:

         (TRACE),(TRACE),(TRACE),(TRACE),(TRACE),(TRACE),(TRACE),(TRACE),
         (TRACE),(TRACE),(TRACE),(TRACE),(TRACE),(TRACE),(TRACE),(TRACE),
         (TRACE),(TRACE),(TRACE),(TRACE),(TRACE),(TRACE),(TRACE),(TRACE),
         (TRACE),(TRACE),(TRACE),(TRACE),(TRACE),(TRACE),(TRACE),(TRACE),
         (TRACE),(TRACE),(TRACE),(TRACE),(TRACE),(TRACE),(TRACE),(TRACE),
         (TRACE),(TRACE),(TRACE),(TRACE),(TRACE),(TRACE),(TRACE),(TRACE),
         (TRACE),(TRACE),(TRACE),(TRACE),(TRACE),(TRACE),(TRACE),(TRACE),
         (TRACE),(TRACE),(TRACE),(TRACE),(TRACE),(TRACE),(TRACE),(TRACE),
         (TRACE),(TRACE),(TRACE),(TRACE),(TRACE),(TRACE),(TRACE),(TRACE),
         (TRACE),(TRACE),(TRACE),(TRACE),(TRACE),(TRACE),(TRACE),(TRACE),
         (TRACE), ...

5.  Per ogni gruppo, contare le occorrenze dei livelli del log. Queste sono le frequenze di ogni livello del log:

         FREQUENCIES = foreach GROUPEDLEVELS generate group as LOGLEVEL, COUNT(FILTEREDLEVELS.LOGLEVEL) as COUNT;
         DUMP FREQUENCIES;

    L'output è simile al seguente:

         (INFO,3355)
         (WARN,361)
         (DEBUG,15608)
         (ERROR,181)
         (FATAL,37)
         (TRACE,29950)

6.  Ordinare le frequenze in ordine decrescente:

         RESULT = order FREQUENCIES by COUNT desc;
         DUMP RESULT;   

    L'output è simile al seguente:

         (TRACE,29950)
         (DEBUG,15608)
         (INFO,3355)
         (WARN,361)

    (ERROR,181)

         (FATAL,37)

Esecuzione di Pig Latin mediante PowerShell
-------------------------------------------

In questa sezione sono disponibili le istruzioni per utilizzare i cmdlet di PowerShell. Prima di procedere in questa sezione, è necessario innanzitutto configurare l'ambiente locale e la connessione ad Azure. Per i dettagli, vedere [Introduzione all'utilizzo di Azure HDInsight](/en-us/manage/services/hdinsight/get-started-hdinsight/) e [Amministrazione di HDInsight tramite PowerShell](/en-use/manage/services/hdinsight/administer-hdinsight-using-powershell/).

**Per eseguire Pig Latin mediante PowerShell**

1.  Aprire una finestra della console di Azure PowerShell. Per istruzioni, vedere [Come installare e configurare Azure PowerShell](/it-it/documentation/articles/install-configure-powershell/).
2.  Eseguire il comando seguente per connettersi alla sottoscrizione di Azure:

         Add-AzureAccount

    Verrà richiesto di immettere le credenziali dell'account Azure.

3.  Impostare la variabile nello script seguente ed eseguirlo:

         $clusterName = "<HDInsightClusterName>" 

4.  Eseguire i comandi seguenti per definire la stringa di query di Pig Latin:

         # Create the Pig job definition
         $0 = '$0';
         $QueryString =  "LOGS = LOAD 'wasb:///example/data/sample.log';" +
                         "LEVELS = foreach LOGS generate REGEX_EXTRACT($0, '(TRACE|DEBUG|INFO|WARN|ERROR|FATAL)', 1)  as LOGLEVEL;" +
                         "FILTEREDLEVELS = FILTER LEVELS by LOGLEVEL is not null;" +
                         "GROUPEDLEVELS = GROUP FILTEREDLEVELS by LOGLEVEL;" +
                         "FREQUENCIES = foreach GROUPEDLEVELS generate group as LOGLEVEL, COUNT(FILTEREDLEVELS.LOGLEVEL) as COUNT;" +
                         "RESULT = order FREQUENCIES by COUNT desc;" +
                         "DUMP RESULT;" 
            
         $pigJobDefinition = New-AzureHDInsightPigJobDefinition -Query $QueryString 

    È anche possibile utilizzare l'opzione -File per specificare un file di script Pig in HDFS.

5.  Eseguire lo script seguente per inviare il processo Pig:

         # Submit the Pig job
         Select-AzureSubscription $subscriptionName
         $pigJob = Start-AzureHDInsightJob -Cluster $clusterName -JobDefinition $pigJobDefinition 

6.  Eseguire lo script seguente per attendere il completamento del processo Pig:

         # Wait for the Pig job to complete
         Wait-AzureHDInsightJob -Job $pigJob -WaitTimeoutInSeconds 3600

7.  Eseguire lo script seguente per stampare l'output del processo Pig:

         # Print the standard error and the standard output of the Pig job.
         Get-AzureHDInsightJobOutput -Cluster $clusterName -JobId $pigJob.JobId -StandardOutput

    ![HDI.Pig.PowerShell](./media/hdinsight-use-pig/hdi.pig.powershell.png)

    Il processo Pig calcola le frequenze di tipi di log diversi.

Passaggi successivi
-------------------

Oltre a Pig, che consente di eseguire l'analisi dei dati, altri linguaggi inclusi in HDInsight potrebbero risultare utili. Hive fornisce un linguaggio di query simile a SQL che consente di eseguire facilmente una query sulla base dei dati archiviati in HDInsight, mentre i processi MapReduce scritti in Java consentono di eseguire analisi di dati complesse. Per ulteriori informazioni, vedere quanto segue:

-   [Introduzione all'utilizzo di Azure HDInsight](/en-us/manage/services/hdinsight/get-started-hdinsight/)
-   [Caricamento di dati in HDInsight](/en-us/manage/services/hdinsight/howto-upload-data-to-hdinsight/)
-   [Utilizzo di Hive con HDInsight](/en-us/manage/services/hdinsight/using-hive-with-hdinsight/)

