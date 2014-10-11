<properties linkid="manage-services-hdinsight-howto-pig" urlDisplayName="Use Hadoop Pig in HDInsight" pageTitle="Use Hadoop Pig in HDInsight | Azure" metaKeywords="" description="Learn how to use Pig with HDInsight. Write Pig Latin statements to analyze an application log file, and run queries on the data to generate output for analysis." metaCanonical="" services="hdinsight" documentationCenter="" title="Use Hadoop Pig in HDInsight" authors="jgao" solutions="big data" manager="paulettm" editor="cgronlun" />

<tags ms.service="hdinsight" ms.workload="big-data" ms.tgt_pltfrm="na" ms.devlang="na" ms.topic="article" ms.date="01/01/1900" ms.author="jgao"></tags>

# Usare Pig con Hadoop in HDInsight

Informazioni su come eseguire processi [Apache Pig][] in HDInsight per analizzare un file di log log4j di Apache.

**Tempo previsto per il completamento:** 30 minuti

## Contenuto dell'articolo

-   [Caso di utilizzo di Pig][]
-   [Prerequisiti][]
-   [Informazioni su Pig Latin][]
-   [Inviare processi Pig tramite PowerShell][]
-   [Inviare processi Pig tramite HDInsight .NET SDK][]
-   [Passaggi successivi][]

## <span id="usage"></span></a>Caso di utilizzo di Pig

I database sono ideali per piccoli set di dati e query a bassa latenza. Tuttavia, quando si tratta di gestire set di Big Data e set di dati di grandi dimensioni nell'ordine dei terabyte, i database SQL tradizionali non rappresentano la soluzione ideale. L'aumento del carico dei database e l'abbassamento delle prestazioni costringono da sempre gli amministratori di database ad acquistare hardware di dimensioni maggiori.

Tutte le applicazioni, in genere, salvano gli errori, le eccezioni e altre problematiche codificate in un file di log, in modo che gli amministratori possano esaminare i problemi o generare determinate metriche dai dati del file di log. Questi file di log solitamente raggiungono dimensioni molto grandi perché contengono una varietà di dati che devono essere elaborati e sottoposti a mining.

I file di log sono un valido esempio di Big Data. È difficile lavorare con i Big Data utilizzando database relazionali e statistiche/pacchetti di visualizzazione. A causa delle grandi quantità di dati, è spesso necessario un software parallelo in esecuzione su decine, centinaia o anche migliaia di server per poter calcolare questi dati in un tempo ragionevole. Hadoop fornisce un framework MapReduce per scrivere applicazioni che elaborano in parallelo grandi quantità di dati strutturati e non strutturati in grandi cluster di computer in modo del tutto affidabile e a tolleranza di errore.

[Apache *Pig*][Apache Pig] offre un linguaggio di scripting per l'esecuzione di processi *MapReduce* in alternativa alla scrittura di codice Java. Il linguaggio di scripting di Pig è chiamato *Pig Latin*. Le istruzioni di Pig Latin seguono questo flusso generale:

-   **Caricamento**: lettura dei dati da manipolare dal file system
-   **Trasformazione**: manipolazione dei dati
-   **Dump o archiviazione**: output dei dati sullo schermo o archiviazione per l'elaborazione

L'utilizzo di Pig riduce il tempo necessario per scrivere programmi mapper e reducer. Non è pertanto necessario utilizzare codice Java o boilerplate. È inoltre possibile combinare il codice Java con Pig. Molti algoritmi complessi possono essere scritti in meno di cinque righe di codice Pig in un formato leggibile.

Le due figure seguenti mostrano la rappresentazione visiva di ciò che si eseguirà in questo articolo. Queste figure, che costituiscono un esempio rappresentativo del set di dati, illustrano il flusso e la trasformazione dei dati mentre si scorrono le righe di codice Pig nello script. La prima figura mostra un esempio del file log4j:

![Esempio di file completo][]

La seconda figura mostra la trasformazione dei dati:

![HDI.PIG.Data.Transformation][]

Per ulteriori informazioni su Pig Latin, vedere il [manuale di riferimento di Pig Latin 1][] e il [manuale di riferimento di Pig Latin 2][].

## <span id="prerequisites"></span></a>Prerequisiti

Per eseguire le procedure descritte nell'articolo è necessario:

-   Disporre di un cluster HDInsight di Azure. Per istruzioni, vedere [Introduzione ad Azure HDInsight][] o [Provisioning di cluster HDInsight][]. Per completare l'esercitazione sono necessari i dati seguenti:

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
    </tbody>
    </table>

-   Installare e configurare Azure PowerShell. Per istruzioni, vedere [Come installare e configurare Azure PowerShell][].

**Informazioni sull'archiviazione in HDInsight**

HDInsight utilizza l'archivio BLOB di Azure per l'archiviazione dei dati. Questa risorsa è denominata *WASB* o *Archiviazione BLOB di Azure*. WASB è l'implementazione Microsoft di HDFS sull'archivio BLOB di Azure. Per altre informazioni, vedere [Usare l'archivio BLOB di Azure con HDInsight][].

Durante il provisioning di un cluster HDInsight, un account di archiviazione di Azure e uno specifico contenitore di archiviazione BLOB di tale account sono designati come file system predefinito, come in HDFS. Durante il processo di provisioning è possibile aggiungere a tale account di archiviazione altri account, dalla stessa sottoscrizione di Azure o da sottoscrizioni diverse. Per istruzioni sull'aggiunta di ulteriori account di archiviazione, vedere [Provisioning di cluster HDInsight][]. Per semplificare lo script di PowerShell usato in questa esercitazione, tutti i file vengono archiviati nel contenitore del file system predefinito, presente in */tutorials/usepig*. Per impostazione predefinita, il nome di questo contenitore corrisponde al nome del cluster HDInsight.
La sintassi per WASB è la seguente:

    wasb[s]://<ContainerName>@<StorageAccountName>.blob.core.windows.net/<path>/<filename>

> [WACOM.NOTE] Nella versione 3.0 del cluster HDInsight è supportata solo la sintassi *wasb://*. La sintassi *asv://*, meno recente, è supportata nei cluster HDInsight 2.1 e 1.6, ma non è supportata nei cluster HDInsight 3.0 e non sarà supportata nelle versioni successive.

> [WACOM.NOTE] Il percorso WASB è un percorso virtuale. Per altre informazioni, vedere [Usare l'archivio BLOB di Azure con HDInsight][].

È possibile accedere da HDInsight a un file archiviato nel contenitore del file system predefinito usando uno degli URI seguenti (usare come esempio sample.log. Si tratta del file di dati usato nell'esercitazione):

    wasb://mycontainer@mystorageaccount.blob.core.windows.net/example/data/sample.log
    wasb:///example/data/sample.log
    /example/data/sample.log

Per accedere al file direttamente dall'account di archiviazione, il nome del BLOB per questo file è:

    example/data/sample.log

In questo articolo sarà usato un file log4j di esempio incluso nel cluster HDInsight e archiviato nel percorso *\\example\\data\\sample.log*. Per informazioni sul caricamento di file di dati personalizzati, vedere [Caricare i dati in HDInsight][].

## <span id="understand"></span></a>Informazioni su Pig Latin

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

## <span id="powershell"></span></a>Inviare processi Pig tramite PowerShell

In questa sezione sono disponibili le istruzioni per utilizzare i cmdlet di PowerShell. Prima di procedere in questa sezione, è necessario innanzitutto configurare l'ambiente locale e la connessione ad Azure. Per i dettagli, vedere [Introduzione ad Azure HDInsight][] e [Amministrazione di HDInsight tramite PowerShell][].

**Per eseguire Pig Latin mediante PowerShell**

1.  Aprire Windows PowerShell ISE (nella schermata Start di Windows 8 digitare **PowerShell\_ISE** e quindi fare clic su **Windows PowerShell ISE**. Vedere la pagina dedicata all'[avvio di Windows PowerShell in Windows 8 e Windows][]).
2.  Nel riquadro inferiore eseguire il comando seguente per connettersi alla sottoscrizione di Azure:

        Add-AzureAccount

    Verrà richiesto di immettere le credenziali dell'account Azure. Questo metodo, che prevede l'aggiunta di una connessione alla sottoscrizione, scade dopo 12 ore, di conseguenza sarà necessario eseguire nuovamente il cmdlet.

    > [WACOM.NOTE] Nel caso in cui siano disponibili più sottoscrizioni di Azure e non si desideri utilizzare la sottoscrizione predefinita, utilizzare il cmdlet **Select-AzureSubscription** per selezionare la sottoscrizione corrente.

3.  Copiare e incollare le righe seguenti nel riquadro dello script.

        $clusterName = "<HDInsightClusterName>" 
        $statusFolder = "/tutorials/usepig/status"

4.  Impostare la variabile $clusterName.

5.  Aggiungere le righe seguenti nel riquadro di script. Queste righe definiscono la stringa di query di Pig Latin e creano una definizione del processo Pig:

        # Create the Pig job definition
        $0 = '$0';
        $QueryString =  "LOGS = LOAD 'wasb:///example/data/sample.log';" +
                        "LEVELS = foreach LOGS generate REGEX_EXTRACT($0, '(TRACE|DEBUG|INFO|WARN|ERROR|FATAL)', 1)  as LOGLEVEL;" +
                        "FILTEREDLEVELS = FILTER LEVELS by LOGLEVEL is not null;" +
                        "GROUPEDLEVELS = GROUP FILTEREDLEVELS by LOGLEVEL;" +
                        "FREQUENCIES = foreach GROUPEDLEVELS generate group as LOGLEVEL, COUNT(FILTEREDLEVELS.LOGLEVEL) as COUNT;" +
                        "RESULT = order FREQUENCIES by COUNT desc;" +
                        "DUMP RESULT;" 

        $pigJobDefinition = New-AzureHDInsightPigJobDefinition -Query $QueryString -StatusFolder $statusFolder 

    È anche possibile utilizzare l'opzione -File per specificare un file di script Pig in HDFS. L'opzione -StatusFolder inserisce il registro errori standard e il file di output standard nella cartella.

6.  Aggiungere le righe seguenti per inviare il processo Pig:

        # Submit the Pig job
        Write-Host "Submit the Pig job ..." -ForegroundColor Green
        $pigJob = Start-AzureHDInsightJob -Cluster $clusterName -JobDefinition $pigJobDefinition  

7.  Aggiungere le righe seguenti per attendere il completamento del processo Pig:

        # Wait for the Pig job to complete
        Write-Host "Wait for the Pig job to complete ..." -ForegroundColor Green
        Wait-AzureHDInsightJob -Job $pigJob -WaitTimeoutInSeconds 3600

8.  Aggiungere le righe seguenti per stampare l'output del processo Pig:

        # Print the standard error and the standard output of the Pig job.
        #Write-Host "Display the standard error log ..." -ForegroundColor Green
        #Get-AzureHDInsightJobOutput -Cluster $clusterName -JobId $pigJob.JobId -StandardError

        Write-Host "Display the standard output ..." -ForegroundColor Green
        Get-AzureHDInsightJobOutput -Cluster $clusterName -JobId $pigJob.JobId -StandardOutput

    > [WACOM.NOTE] Uno dei cmdlet Get-AzureHDInsightJobOut è commentare per abbreviare l'output nella schermata seguente.

9.  Premere **F5** per eseguire lo script:
    ![HDI.Pig.PowerShell][]

    Il processo Pig calcola le frequenze di tipi di log diversi.

## <span id="sdk"></span></a>Inviare processi Pig tramite HDInsight .NET SDK

Il seguente è un esempio di invio di un processo Pig tramite HDInsight .NET SDK. Per istruzioni sulla creazione di un'applicazione C# per l'invio di processi Hadoop, vedere [Inviare processi Hadoop a livello di codice][].

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

    namespace SubmitPigJobs
    {
        class Program
        {
            static void Main(string[] args)
            {
                // Set the variables
                string subscriptionID = "<Azure subscription ID>";
                string certFriendlyName = "<certificate friendly name>";
        
                string clusterName = "<HDInsight cluster name>";
                string statusFolderName = @"/tutorials/usepig/status";

                string queryString = "LOGS = LOAD 'wasb:///example/data/sample.log';" +
                    "LEVELS = foreach LOGS generate REGEX_EXTRACT($0, '(TRACE|DEBUG|INFO|WARN|ERROR|FATAL)', 1)  as LOGLEVEL;" +
                    "FILTEREDLEVELS = FILTER LEVELS by LOGLEVEL is not null;" +
                    "GROUPEDLEVELS = GROUP FILTEREDLEVELS by LOGLEVEL;" +
                    "FREQUENCIES = foreach GROUPEDLEVELS generate group as LOGLEVEL, COUNT(FILTEREDLEVELS.LOGLEVEL) as COUNT;" +
                    "RESULT = order FREQUENCIES by COUNT desc;" +
                    "DUMP RESULT;";

                // Define the Pig job
                PigJobCreateParameters myJobDefinition = new PigJobCreateParameters()
                {
                    Query = queryString,
                    StatusFolder = statusFolderName
                };

                // Get the certificate object from certificate store using the friendly name to identify it
                X509Store store = new X509Store();
                store.Open(OpenFlags.ReadOnly);
                X509Certificate2 cert = store.Certificates.Cast<X509Certificate2>().First(item => item.FriendlyName == certFriendlyName);

                JobSubmissionCertificateCredential creds = new JobSubmissionCertificateCredential(new Guid(subscriptionID), cert, clusterName);

                // Create a hadoop client to connect to HDInsight
                var jobClient = JobSubmissionClientFactory.Connect(creds);

                // Run the MapReduce job
                Console.WriteLine("----- Submit the Pig job ...");
                JobCreationResults mrJobResults = jobClient.CreatePigJob(myJobDefinition);

                // Wait for the job to complete
                Console.WriteLine("----- Wait for the Pig job to complete ...");
                WaitForJobCompletion(mrJobResults, jobClient);

                // Display the error log
                Console.WriteLine("----- The Pig job error log.");
                using (Stream stream = jobClient.GetJobErrorLogs(mrJobResults.JobId))
                {
                    var reader = new StreamReader(stream);
                    Console.WriteLine(reader.ReadToEnd());
                }

                // Display the output log
                Console.WriteLine("----- The Pig job output log.");
                using (Stream stream = jobClient.GetJobOutput(mrJobResults.JobId))
                {
                    var reader = new StreamReader(stream);
                    Console.WriteLine(reader.ReadToEnd());
                }

                Console.WriteLine("----- Press ENTER to continue.");
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

## <span id="nextsteps"></span></a>Passaggi successivi

Oltre a Pig, che consente di eseguire l'analisi dei dati, altri linguaggi inclusi in HDInsight potrebbero risultare utili. Hive fornisce un linguaggio di query simile a SQL che consente di eseguire facilmente una query sulla base dei dati archiviati in HDInsight, mentre i processi MapReduce scritti in Java consentono di eseguire analisi di dati complesse. Per ulteriori informazioni, vedere quanto segue:

-   [Introduzione all'utilizzo di Azure HDInsight][Introduzione ad Azure HDInsight]
-   [Caricare i dati in HDInsight][]
-   [Inviare processi Hadoop a livello di codice][]
-   [Usare Hive con HDInsight][]

  [Apache Pig]: http://pig.apache.org/
  [Caso di utilizzo di Pig]: #usage
  [Prerequisiti]: #prerequisites
  [Informazioni su Pig Latin]: #understand
  [Inviare processi Pig tramite PowerShell]: #powershell
  [Inviare processi Pig tramite HDInsight .NET SDK]: #sdk
  [Passaggi successivi]: #nextsteps
  [Esempio di file completo]: ./media/hdinsight-use-pig/HDI.wholesamplefile.png
  [HDI.PIG.Data.Transformation]: ./media/hdinsight-use-pig/HDI.DataTransformation.gif
  [manuale di riferimento di Pig Latin 1]: http://pig.apache.org/docs/r0.7.0/piglatin_ref1.html
  [manuale di riferimento di Pig Latin 2]: http://pig.apache.org/docs/r0.7.0/piglatin_ref2.html
  [Introduzione ad Azure HDInsight]: ../hdinsight-get-started/
  [Provisioning di cluster HDInsight]: ../hdinsight-provision-clusters/
  [Come installare e configurare Azure PowerShell]: ../install-configure-powershell/
  [Usare l'archivio BLOB di Azure con HDInsight]: ../hdinsight-use-blob-storage/
  [Caricare i dati in HDInsight]: ../hdinsight-upload-data/
  [Amministrazione di HDInsight tramite PowerShell]: ../hdinsight-administer-use-powershell/
  [avvio di Windows PowerShell in Windows 8 e Windows]: http://technet.microsoft.com/en-us/library/hh847889.aspx
  [HDI.Pig.PowerShell]: ./media/hdinsight-use-pig/hdi.pig.powershell.png
  [Inviare processi Hadoop a livello di codice]: ../hdinsight-submit-hadoop-jobs-programmatically/#mapreduce-sdk
  [Usare Hive con HDInsight]: ../hdinsight-use-hive/
