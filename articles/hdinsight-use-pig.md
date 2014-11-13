<properties urlDisplayName="Use Hadoop Pig in HDInsight" pageTitle="Usare Pig di Hadoop in HDInsight | Azure" metaKeywords="" description="Informazioni su come usare Pig con HDInsight. Scrivere istruzioni Pig Latin per analizzare un file di registro applicazioni ed eseguire query dei dati per generare l'output da analizzare." metaCanonical="" services="hdinsight" documentationCenter="" title="Usare Pig di Hadoop in HDInsight" authors="jgao" solutions="big data" manager="paulettm" editor="cgronlun" />

<tags ms.service="hdinsight" ms.workload="big-data" ms.tgt_pltfrm="na" ms.devlang="na" ms.topic="article" ms.date="09/25/2014" ms.author="jgao" />

# Usare Pig con Hadoop in HDInsight

In questa esercitazione si apprenderà come eseguire processi [Apache Pig][Apache Pig] in HDInsight per analizzare grandi file di dati. Pig offre un linguaggio di scripting per l'esecuzione di processi *MapReduce* in alternativa alla scrittura di codice Java. Il linguaggio di scripting di Pig è chiamato *Pig Latin*.

**Prerequisiti**

-   È necessario avere completato il provisioning di un cluster HDInsight di Azure. Per istruzioni, vedere [Introduzione ad Azure HDInsight][Introduzione ad Azure HDInsight] o [Provisioning di cluster HDInsight][Provisioning di cluster HDInsight]. Per completare questa esercitazione sarà necessario il nome del cluster.

-   È necessario avere installato e configurato Azure PowerShell nella workstation. Per istruzioni, vedere [Come installare e configurare Azure PowerShell][Come installare e configurare Azure PowerShell].

**Tempo previsto per il completamento:** 30 minuti

## Contenuto dell'articolo

-   [Vantaggi offerti da Pig][Vantaggi offerti da Pig]
-   [Operazioni necessarie in questa esercitazione][Operazioni necessarie in questa esercitazione]
-   [Identificare i dati da analizzare][Identificare i dati da analizzare]
-   [Informazioni su Pig Latin][Informazioni su Pig Latin]
-   [Inviare processi Pig tramite PowerShell][Inviare processi Pig tramite PowerShell]
-   [Inviare processi Pig tramite HDInsight .NET SDK][Inviare processi Pig tramite HDInsight .NET SDK]
-   [Passaggi successivi][Passaggi successivi]

## <span id="usage"></span></a>Vantaggi offerti da Pig

È difficile lavorare con i Big Data usando database relazionali e statistiche/pacchetti di visualizzazione. A causa delle grandi quantità di dati, è spesso necessario un software parallelo in esecuzione su decine, centinaia o anche migliaia di server per poter calcolare questi dati in un tempo ragionevole. Hadoop fornisce un framework *MapReduce* per scrivere applicazioni che elaborano in parallelo grandi quantità di dati strutturati e non strutturati in grandi cluster di computer in modo del tutto affidabile e a tolleranza di errore.

![HDI.Pig.Architecture][HDI.Pig.Architecture]

[Apache *Pig*][Apache Pig] fornisce un livello di astrazione superiore al framework MapReduce basato su Java, consentendo agli utenti di analizzare i dati senza alcuna conoscenza di Java o MapReduce. Pig è una piattaforma per l'analisi di grandi set di dati per mezzo di un linguaggio di flusso dei dati facile da usare, denominato *Pig Latin*. Pig riduce il tempo necessario per scrivere programmi mapper e reducer e non richiede alcuna conoscenza di Java. È inoltre possibile combinare il codice Java con Pig. Molti algoritmi complessi possono essere scritti in meno di cinque righe di codice Pig in un formato leggibile.

Le istruzioni di Pig Latin seguono questo flusso generale:

-   **Caricamento**: lettura dei dati da manipolare dal file system
-   **Trasformazione**: manipolazione dei dati
-   **Dump o archiviazione**: output dei dati sullo schermo o archiviazione per l'elaborazione

Per altre informazioni su Pig Latin, vedere il [manuale di riferimento di Pig Latin 1][manuale di riferimento di Pig Latin 1] e il [manuale di riferimento di Pig Latin 2][manuale di riferimento di Pig Latin 2].

## <span id="what"></span></a>Operazioni necessarie in questa esercitazione

In questa esercitazione verrà analizzato un file di log di Apache (*sample.log*) per determinare il numero dei diversi livelli di log, ad esempio INFO, DEBUG, TRACE, ecc. Le due figure seguenti mostrano la rappresentazione visiva di ciò che si eseguirà in questo articolo. La prima figura mostra un frammento del file sample.log:

![Esempio di file completo][Esempio di file completo]

La seconda figura illustra il flusso e la trasformazione dei dati mentre si scorrono le righe di codice Pig nello script:

![HDI.PIG.Data.Transformation][HDI.PIG.Data.Transformation]

Il processo Pig creato in questa esercitazione segue lo stesso flusso.

## <span id="data"></span></a>Identificare i dati da analizzare

HDInsight usa il contenitore dell'archivio BLOB di Azure come file system predefinito per i cluster Hadoop. Alcuni file di dati di esempio vengono aggiunti all'archivio BLOB come parte del provisioning del cluster ed è possibile usarli per eseguire query Hive sul cluster. Volendo è anche possibile caricare il proprio file di dati nell'account di archiviazione BLOB associato al cluster. Per istruzioni, vedere [Caricamento di dati in HDInsight][Caricamento di dati in HDInsight]. Per altre informazioni sul modo in cui HDInsight usa l'archivio BLOB di Azure, vedere [Usare l'archivio BLOB di Azure con HDInsight][Usare l'archivio BLOB di Azure con HDInsight].

La sintassi per accedere ai file nell'archivio BLOB è la seguente:

    wasb[s]://<ContainerName>@<StorageAccountName>.blob.core.windows.net/<path>/<filename>

> [WACOM.NOTE] Nella versione 3.0 del cluster HDInsight è supportata solo la sintassi *wasb://*. La sintassi *asv://*, meno recente, è supportata nei cluster HDInsight 2.1 e 1.6, ma non è supportata nei cluster HDInsight 3.0 e non sarà supportata nelle versioni successive.

È possibile accedere da HDInsight a un file archiviato nel contenitore del file system predefinito anche usando uno degli URI seguenti (usare come esempio sample.log. Si tratta del file di dati usato nell'esercitazione):

    wasb://mycontainer@mystorageaccount.blob.core.windows.net/example/data/sample.log
    wasb:///example/data/sample.log
    /example/data/sample.log

Per accedere al file direttamente dall'account di archiviazione, il nome del BLOB per questo file è:

    example/data/sample.log

In questo articolo sarà usato un file *log4j* di esempio incluso nel cluster HDInsight e archiviato nel percorso *\\example\\data\\sample.log*. Per informazioni sul caricamento di file di dati personalizzati, vedere [Caricare i dati in HDInsight][Caricamento di dati in HDInsight].

## <span id="understand"></span></a>Informazioni su Pig Latin

In questa sezione, si esamineranno singolarmente alcune istruzioni Pig Latin e i risultati dopo l'esecuzione delle istruzioni. Nella sezione successiva, si userà PowerShell per eseguire assieme le istruzioni Pig allo scopo di analizzare il file di log di esempio. Le singole istruzioni Pig Latin devono essere eseguite direttamente sul cluster HDInsight.

1.  Abilitare Desktop remoto per il cluster HDInsight seguendo le istruzioni fornite in [Connessione a cluster HDInsight tramite RDP][Connessione a cluster HDInsight tramite RDP]. Accedere al nodo del cluster e, dal desktop, fare clic su **Hadoop Command Line**.

2.  Dalla riga di comando passare alla directory in cui è installato **Pig**. Digitare:

        C:\apps\dist\hadoop-<version>> cd %pig_home%\bin

3.  Al prompt dei comandi digitare *pig* e premere INVIO per ottenere la shell *grunt*.

        C:\apps\dist\pig-<version>\bin>pig
        ...
        grunt>  

4.  Immettere quanto segue per caricare il file di esempio nel file system e quindi visualizzare i risultati:

        grunt> LOGS = LOAD 'wasb:///example/data/sample.log';
        grunt> DUMP LOGS;

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

5.  Scorrere ogni riga del file di dati per trovare una corrispondenza nei 6 livelli del log:

        grunt> LEVELS = foreach LOGS generate REGEX_EXTRACT($0, '(TRACE|DEBUG|INFO|WARN|ERROR|FATAL)', 1)  as LOGLEVEL;

6.  Filtrare le righe senza corrispondenza e visualizzare il risultato. In tal modo vengono eliminate le righe vuote.

        grunt> FILTEREDLEVELS = FILTER LEVELS by LOGLEVEL is not null;
        grunt> DUMP FILTEREDLEVELS;

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

7.  Raggruppare tutti i livelli del log nella riga corrispondente e visualizzare il risultato:

        grunt> GROUPEDLEVELS = GROUP FILTEREDLEVELS by LOGLEVEL;
        grunt> DUMP GROUPEDLEVELS;

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

8.  Per ogni gruppo, contare le occorrenze dei livelli del log e visualizzare il risultato:

        grunt> FREQUENCIES = foreach GROUPEDLEVELS generate group as LOGLEVEL, COUNT(FILTEREDLEVELS.LOGLEVEL) as COUNT;
        grunt> DUMP FREQUENCIES;

    L'output è simile al seguente:

        (INFO,96)
        (WARN,11)
        (DEBUG,434)
        (ERROR,6)
        (FATAL,2)
        (TRACE,816)

9.  Ordinare le frequenze in ordine decrescente e visualizzare il risultato:

        grunt> RESULT = order FREQUENCIES by COUNT desc;
        grunt> DUMP RESULT;   

    L'output è simile al seguente:

        (TRACE,816)
        (DEBUG,434)
        (INFO,96)
        (WARN,11)
        (ERROR,6)
        (FATAL,2)

## <span id="powershell"></span></a>Inviare processi Pig tramite PowerShell

In questa sezione sono disponibili le istruzioni per usare i cmdlet di PowerShell. Prima di procedere in questa sezione, è necessario innanzitutto configurare l'ambiente locale e la connessione ad Azure. Per i dettagli, vedere [Introduzione ad Azure HDInsight][Introduzione ad Azure HDInsight] e [Amministrazione di HDInsight tramite PowerShell][Amministrazione di HDInsight tramite PowerShell].

**Per eseguire Pig Latin mediante PowerShell**

1.  Aprire Windows PowerShell ISE. Nella schermata Start di Windows 8 digitare **PowerShell\_ISE** e quindi fare clic su **Windows PowerShell ISE**. Per altre informazioni, vedere la pagina dedicata all'[avvio di Windows PowerShell in Windows 8 e Windows][avvio di Windows PowerShell in Windows 8 e Windows].

2.  Nel riquadro inferiore eseguire il comando seguente per connettersi alla sottoscrizione di Azure:

        Add-AzureAccount

    Verrà richiesto di immettere le credenziali dell'account Azure. Questo metodo, che prevede l'aggiunta di una connessione alla sottoscrizione, scade dopo 12 ore, di conseguenza sarà necessario eseguire nuovamente il cmdlet.

    > [WACOM.NOTE] Nel caso in cui siano disponibili più sottoscrizioni di Azure e non si desideri usare la sottoscrizione predefinita, usare il cmdlet **Select-AzureSubscription** per selezionare la sottoscrizione corrente.

3.  Copiare e incollare le righe seguenti nel riquadro dello script.

        $clusterName = "<HDInsightClusterName>"     #Specify the cluster name
        $statusFolder = "/tutorials/usepig/status"  #Specify the folder to dump results

    Se la cartella di stato specificata non esiste già, verrà creata dallo script.

4.  Aggiungere le righe seguenti nel riquadro di script. Queste righe definiscono la stringa di query di Pig Latin e creano una definizione del processo Pig:

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

    È anche possibile usare l'opzione **-File** per specificare un file di script Pig in HDFS. L'opzione **-StatusFolder** inserisce il registro errori standard e il file di output standard nella cartella.

5.  Aggiungere le righe seguenti per inviare il processo Pig:

        # Submit the Pig job
        Write-Host "Submit the Pig job ..." -ForegroundColor Green
        $pigJob = Start-AzureHDInsightJob -Cluster $clusterName -JobDefinition $pigJobDefinition  

6.  Aggiungere le righe seguenti per attendere il completamento del processo Pig:

        # Wait for the Pig job to complete
        Write-Host "Wait for the Pig job to complete ..." -ForegroundColor Green
        Wait-AzureHDInsightJob -Job $pigJob -WaitTimeoutInSeconds 3600

7.  Aggiungere le righe seguenti per stampare l'output del processo Pig:

        # Print the standard error and the standard output of the Pig job.
        Write-Host "Display the standard output ..." -ForegroundColor Green
        Get-AzureHDInsightJobOutput -Cluster $clusterName -JobId $pigJob.JobId -StandardOutput

    > [WACOM.NOTE] Uno dei cmdlet Get-AzureHDInsightJobOut è commentare per abbreviare l'output nella schermata seguente.

8.  Premere **F5** per eseguire lo script:

    ![HDI.Pig.PowerShell][HDI.Pig.PowerShell]

    Il processo Pig calcola le frequenze di tipi di log diversi.

## <span id="sdk"></span></a>Inviare processi Pig tramite HDInsight .NET SDK

Attenersi alla procedura riportata di seguito per inviare un processo Pig con un'applicazione C#. Per istruzioni sulla creazione di un'applicazione C# per l'invio di processi Hadoop, vedere [Inviare processi Hadoop a livello di codice][Inviare processi Hadoop a livello di codice].

1.  Creare un certificato autofirmato, installarlo nella workstation e caricarlo nella sottoscrizione di Azure. Per ottenere istruzioni, vedere [Creare un certificato autofirmato][Creare un certificato autofirmato].

2.  Creare l'applicazione console di Visual Studio e installare il pacchetto HDInsight. Dal menu Strumenti fare clic su **Gestione pacchetti NuGet**, quindi su **Console di Gestione pacchetti**. Al prompt, immettere il codice seguente:

        Install-Package Microsoft.WindowsAzure.Management.HDInsight

3.  Fare doppio clic su Program.cs e incollare il codice seguente per inviare un processo Pig. Fornire i valori alle variabili.

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

Oltre a Pig, che consente di eseguire l'analisi dei dati, altri linguaggi inclusi in HDInsight potrebbero risultare utili. Hive fornisce un linguaggio di query simile a SQL che consente di eseguire facilmente una query sulla base dei dati archiviati in HDInsight, mentre i processi MapReduce scritti in Java consentono di eseguire analisi di dati complesse. Per altre informazioni, vedere quanto segue:

-   [Introduzione all'utilizzo di Azure HDInsight][Introduzione ad Azure HDInsight]
-   [Caricare i dati in HDInsight][Caricamento di dati in HDInsight]
-   [Inviare processi Hadoop a livello di codice][Inviare processi Hadoop a livello di codice]
-   [Usare Hive con HDInsight][Usare Hive con HDInsight]

  [Apache Pig]: http://pig.apache.org/
  [Introduzione ad Azure HDInsight]: ../hdinsight-get-started/
  [Provisioning di cluster HDInsight]: ../hdinsight-provision-clusters/
  [Come installare e configurare Azure PowerShell]: ../install-configure-powershell/
  [Vantaggi offerti da Pig]: #usage
  [Operazioni necessarie in questa esercitazione]: #what
  [Identificare i dati da analizzare]: #data
  [Informazioni su Pig Latin]: #understand
  [Inviare processi Pig tramite PowerShell]: #powershell
  [Inviare processi Pig tramite HDInsight .NET SDK]: #sdk
  [Passaggi successivi]: #nextsteps
  [HDI.Pig.Architecture]: ./media/hdinsight-use-pig/HDI.Pig.Architecture.png
  [manuale di riferimento di Pig Latin 1]: http://pig.apache.org/docs/r0.7.0/piglatin_ref1.html
  [manuale di riferimento di Pig Latin 2]: http://pig.apache.org/docs/r0.7.0/piglatin_ref2.html
  [Esempio di file completo]: ./media/hdinsight-use-pig/HDI.wholesamplefile.png
  [HDI.PIG.Data.Transformation]: ./media/hdinsight-use-pig/HDI.DataTransformation.gif
  [Caricamento di dati in HDInsight]: ../hdinsight-upload-data/
  [Usare l'archivio BLOB di Azure con HDInsight]: ../hdinsight-use-blob-storage/
  [Connessione a cluster HDInsight tramite RDP]: http://azure.microsoft.com/it-it/documentation/articles/hdinsight-administer-use-management-portal/#rdp
  [Amministrazione di HDInsight tramite PowerShell]: ../hdinsight-administer-use-powershell/
  [avvio di Windows PowerShell in Windows 8 e Windows]: http://technet.microsoft.com/it-it/library/hh847889.aspx
  [HDI.Pig.PowerShell]: ./media/hdinsight-use-pig/hdi.pig.powershell.png
  [Inviare processi Hadoop a livello di codice]: ../hdinsight-submit-hadoop-jobs-programmatically/#mapreduce-sdk
  [Creare un certificato autofirmato]: http://go.microsoft.com/fwlink/?LinkId=511138
  [Usare Hive con HDInsight]: ../hdinsight-use-hive/
