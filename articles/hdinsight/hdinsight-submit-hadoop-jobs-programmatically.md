<properties
	pageTitle="Inviare processi Hadoop in HDInsight | Microsoft Azure"
	description="Informazioni su come inviare processi Hadoop ad Azure HDInsight Hadoop."
	editor="cgronlun"
	manager="paulettm"
	services="hdinsight"
	documentationCenter=""
	tags="azure-portal"
	authors="mumian"/>

<tags
	ms.service="hdinsight"
	ms.workload="big-data"
	ms.tgt_pltfrm="na"
	ms.devlang="na"
	ms.topic="article"
	ms.date="07/28/2015"
	ms.author="jgao"/>

# Inviare processi Hadoop in HDInsight

Informazioni su come usare Azure PowerShell per inviare processi MapReduce e Hive e su come usare HDInsight .NET SDK per inviare flussi MapReduce e Hadoop e processi Hive.

> [AZURE.NOTE]I passaggi descritti in questo articolo devono essere eseguiti da un client Windows. Per informazioni sull'uso di un client Linux, OS X o Unix per operare con MapReduce, Hive o Pig in HDInsight, vedere gli articoli seguenti e selezionare i collegamenti **SSH** o **Curl** in ciascuno di essi:
>
> - [Usare Hive con HDInsight](hdinsight-use-hive.md)
> - [Usare Pig con HDInsight](hdinsight-use-pig.md)
> - [Usare MapReduce con HDInsight](hdinsight-use-mapreduce.md)

##Prerequisiti

Per eseguire le procedure descritte nell'articolo è necessario:

* **Un cluster HDInsight di Azure**. Per istruzioni, vedere [Introduzione all'uso di HDInsight][hdinsight-get-started] o [Effettuare il provisioning di cluster HDInsight][hdinsight-provision].
- **Workstation con Azure PowerShell**. Vedere [Installare e usare Azure PowerShell](http://azure.microsoft.com/documentation/videos/install-and-use-azure-powershell/).



##Inviare processi MapReduce mediante Azure PowerShell
Azure PowerShell è un ambiente di scripting potente che può essere usato per controllare e automatizzare la distribuzione e la gestione dei carichi di lavoro in Azure. Per altre informazioni sull'uso di Azure PowerShell con HDInsight, vedere [Gestire cluster Hadoop in HDInsight tramite Azure PowerShell][hdinsight-admin-powershell].

Hadoop MapReduce è un framework software per la scrittura di applicazioni in grado di elaborare ingenti quantità di dati. I cluster HDInsight includono un file con estensione jar, situato nella cartella *\\example\\jars\\hadoop-mapreduce-examples.jar*, che contiene numerosi esempi di MapReduce.

Uno degli esempi è relativo al conteggio della frequenza delle parole nei file di origine. In questa sessione si apprenderà come usare Azure PowerShell da una workstation per eseguire l'esempio relativo al conteggio di parole. Per altre informazioni sullo sviluppo e l'esecuzione di processi MapReduce, vedere [Usare Hadoop MapReduce in HDInsight][hdinsight-use-mapreduce].

**Per eseguire il programma MapReduce di conteggio delle parole mediante Azure PowerShell**

1.	Aprire **Azure PowerShell**. Per istruzioni sull'apertura della finestra della console di Azure PowerShell, vedere [Come installare e configurare Azure PowerShell][powershell-install-configure].

3. Impostare le variabili necessarie eseguendo i comandi di Azure PowerShell riportati di seguito:

		$subscriptionName = "<SubscriptionName>"
		$clusterName = "<HDInsightClusterName>"

	Il nome della sottoscrizione è quello usato per creare il cluster HDInsight. Il cluster HDInsight è quello da usare per eseguire il processo MapReduce.

5. Per creare una definizione del processo MapReduce, eseguire i comandi seguenti:

		# Define the word count MapReduce job
		$wordCountJobDefinition = New-AzureHDInsightMapReduceJobDefinition -JarFile "wasb:///example/jars/hadoop-mapreduce-examples.jar" -ClassName "wordcount" -Arguments "wasb:///example/data/gutenberg/davinci.txt", "wasb:///example/data/WordCountOutput"

	Vengono usati due argomenti. Il primo argomento è il nome del file di origine, il secondo è il percorso del file di output. Per altre informazioni sul prefisso wasb://, vedere l'articolo relativo all'[uso dell'archivio BLOB di Azure con HDInsight][hdinsight-storage].

6. Per eseguire il processo MapReduce, usare il comando seguente:

		# Submit the MapReduce job
		Select-AzureSubscription $subscriptionName
		$wordCountJob = Start-AzureHDInsightJob -Cluster $clusterName -JobDefinition $wordCountJobDefinition

	Oltre alla definizione del processo MapReduce, è necessario specificare il nome del cluster HDInsight in cui si desidera eseguire il processo MapReduce.

7. Per verificare il completamento del processo MapReduce, eseguire il comando seguente:

		# Wait for the job to complete
		Wait-AzureHDInsightJob -Job $wordCountJob -WaitTimeoutInSeconds 3600


8. Per verificare se si sono verificati errori nel processo MapReduce, eseguire il comando seguente:

		# Get the job standard error output
		Get-AzureHDInsightJobOutput -Cluster $clusterName -JobId $wordCountJob.JobId -StandardError

	Nella schermata seguente viene mostrato l'output di un'esecuzione completata correttamente. In caso di esito negativo, verranno visualizzati alcuni messaggi di errore.

	![HDI.GettingStarted.RunMRJob][image-hdi-gettingstarted-runmrjob]


**Per recuperare i risultati del processo MapReduce**

1. Aprire **Azure PowerShell**.
2. Impostare le variabili necessarie eseguendo i comandi di Azure PowerShell riportati di seguito:

		$subscriptionName = "<SubscriptionName>"
		$storageAccountName = "<StorageAccountName>"
		$containerName = "<ContainerName>"

	Il nome dell'account di archiviazione di Azure è quello specificato durante il provisioning del cluster HDInsight. L'account di archiviazione viene usato per l'hosting del contenitore BLOB usato come file system predefinito per il cluster HDInsight. Il nome del contenitore corrisponde in genere al nome del cluster HDInsight, a meno che non venga specificato un nome diverso durante il provisioning del cluster.

3. Per creare un oggetto contesto dell'archivio BLOB di Azure, eseguire i comandi seguenti:

		# Create the storage account context object
		Select-AzureSubscription $subscriptionName
		$storageAccountKey = Get-AzureStorageKey $storageAccountName | %{ $_.Primary }
		$storageContext = New-AzureStorageContext -StorageAccountName $storageAccountName -StorageAccountKey $storageAccountKey  

	Il comando **Select-AzureSubscription** consente di impostare la sottoscrizione corrente se sono disponibili più sottoscrizioni e non si desidera usare la sottoscrizione predefinita.

4. Per scaricare l'output del processo MapReduce dal contenitore BLOB alla workstation, eseguire il comando seguente:

		# Get the blob content
		Get-AzureStorageBlobContent -Container $ContainerName -Blob example/data/WordCountOutput/part-r-00000 -Context $storageContext -Force

	La cartella *example/data/WordCountOutput* è la cartella di output specificata durante l'esecuzione del processo MapReduce. Il valore *part-r-00000* corrisponde al nome file predefinito per l'output del processo MapReduce. Il file verrà scaricato nella stessa struttura di cartelle nella cartella locale. Ad esempio, nella schermata seguente la cartella corrente è la cartella radice C:. Il file verrà scaricato nella cartella:

**C:\\example\\data\\WordCountOutput*

5. Per stampare il file di output del processo MapReduce, eseguire il comando seguente:

		cat ./example/data/WordCountOutput/part-r-00000 | findstr "there"

	![HDI.GettingStarted.MRJobOutput][image-hdi-gettingstarted-mrjoboutput]

	Il processo MapReduce produce un file denominato *part-r-00000*, che include le parole e i conteggi. Lo script usa il comando **findstr** per elencare tutte le parole contenenti "there".


> [AZURE.NOTE]Se si apre ./example/data/WordCountOutput/part-r-00000, un output multilinea dal processo MapReduce, nel Blocco note, si noterà che il rendering delle interruzioni di riga non è corretto. Si tratta di un comportamento previsto.









































































































































##Inviare processi Hive mediante Azure PowerShell
[Apache Hive][apache-hive] fornisce un metodo per l'esecuzione di processi MapReduce mediante un linguaggio di scripting simile a SQL, denominato *HiveQL*, che può essere usato per attività di riepilogo, query e analisi di volumi di dati molto elevati.

I cluster HDInsight includono una tabella Hive di esempio denominata *hivesampletable*. In questa sessione si userà Azure PowerShell per eseguire un processo Hive che consente di ottenere un elenco di dati dalla tabella Hive.

**Per eseguire un processo Hive mediante Azure PowerShell**

1.	Aprire **Azure PowerShell**. Per istruzioni sull'apertura della finestra della console di Azure PowerShell, vedere [Come installare e configurare Azure PowerShell][powershell-install-configure].

2. Impostare le prime due variabili necessarie nei comandi seguenti, quindi eseguirli:

		$subscriptionName = "<SubscriptionName>"
		$clusterName = "<HDInsightClusterName>"
		$querystring = "SELECT * FROM hivesampletable WHERE Country='United Kingdom';"

	$querystring corrisponde alla query HiveQL.

3. Eseguire i comandi seguenti per selezionare la sottoscrizione di Azure e il cluster per eseguire il processo Hive:

		Select-AzureSubscription -SubscriptionName $subscriptionName

4. Eseguire i comandi seguenti per inviare il processo Hive:

		Use-AzureHDInsightCluster $clusterName
		Invoke-Hive -Query $queryString

	È possibile usare l'opzione **-File** per specificare un file di script HiveQL nel file system distribuito Hadoop (HDFS).

Per altre informazioni su Hive, vedere [Usare Hive con HDInsight][hdinsight-use-hive].


## Inviare processi Hive mediante Visual Studio

Vedere [Introduzione all'uso di HDInsight Tools per Visual Studio][hdinsight-visual-studio-tools].

##Inviare processi Sqoop mediante Azure PowerShell

Vedere [Usare Sqoop con Hadoop in HDInsight][hdinsight-use-sqoop].

##Inviare processi MapReduce mediante HDInsight .NET SDK
HDInsight .NET SDK fornisce librerie client .NET che semplificano l'utilizzo dei cluster HDInsight da .NET. I cluster HDInsight includono un file con estensione jar, situato nella cartella *\\example\\jars\\hadoop-mapreduce-examples.jar*, che contiene numerosi esempi di MapReduce. Uno degli esempi è relativo al conteggio della frequenza delle parole nei file di origine. In questa sessione si apprenderà come creare un'applicazione .NET per eseguire l'esempio relativo al conteggio di parole. Per altre informazioni sullo sviluppo e l'esecuzione di processi MapReduce, vedere [Usare Hadoop MapReduce in HDInsight][hdinsight-use-mapreduce].


Le procedure seguenti sono necessarie per effettuare il provisioning di un cluster HDInsight usando l'SDK:

- Installare HDInsight .NET SDK
- Creare un'applicazione console
- Eseguire l'applicazione


**Per installare HDInsight .NET SDK** È possibile installare l'ultima build pubblicata dell'SDK da [NuGet](http://nuget.codeplex.com/wikipage?title=Getting%20Started). Le istruzioni verranno illustrate nella procedura successiva.

**Per creare un'applicazione console di Visual Studio**

1. Aprire Visual Studio.

2. Scegliere **Nuovo** dal menu **File** e quindi fare clic su **Progetto**.

3. In **Nuovo progetto** digitare o selezionare i valori seguenti:

	<table style="border-color: #c6c6c6; border-width: 2px; border-style: solid; border-collapse: collapse;">
<tr>
<th style="border-color: #c6c6c6; border-width: 2px; border-style: solid; border-collapse: collapse; width:90px; padding-left:5px; padding-right:5px;">Proprietà</th>
<th style="border-color: #c6c6c6; border-width: 2px; border-style: solid; border-collapse: collapse; width:90px; padding-left:5px; padding-right:5px;">Valore</th></tr>
<tr>
<td style="border-color: #c6c6c6; border-width: 2px; border-style: solid; border-collapse: collapse; padding-left:5px;">Categoria</td>
<td style="border-color: #c6c6c6; border-width: 2px; border-style: solid; border-collapse: collapse; padding-left:5px; padding-right:5px;">Templates/Visual C#/Windows</td></tr>
<tr>
<td style="border-color: #c6c6c6; border-width: 2px; border-style: solid; border-collapse: collapse; padding-left:5px;">Modello</td>
<td style="border-color: #c6c6c6; border-width: 2px; border-style: solid; border-collapse: collapse; padding-left:5px;">Applicazione console</td></tr>
<tr>
<td style="border-color: #c6c6c6; border-width: 2px; border-style: solid; border-collapse: collapse; padding-left:5px;">Nome</td>
<td style="border-color: #c6c6c6; border-width: 2px; border-style: solid; border-collapse: collapse; padding-left:5px;">SubmitMapReduceJob</td></tr>
</table>

4. Fare clic su **OK** per creare il progetto.


5. Scegliere **Gestione pacchetti libreria** dal menu **Strumenti** e quindi fare clic su **Console di Gestione pacchetti**.

6. Eseguire i seguenti comandi nella console per installare i pacchetti.

		Install-Package Microsoft.WindowsAzure.Management.HDInsight


	Questo comando aggiunge librerie .NET e riferimenti ad esse nel progetto corrente di Visual Studio. È richiesta la versione 0.11.0.1 o successiva.

7. In **Esplora soluzioni** fare doppio clic su **Program.cs** per aprirlo.

8. Aggiungere le istruzioni using seguenti all'inizio del file:

		using System.IO;
		using System.Threading;
		using System.Security.Cryptography.X509Certificates;

		using Microsoft.WindowsAzure.Storage;
		using Microsoft.WindowsAzure.Storage.Blob;

		using Microsoft.WindowsAzure.Management.HDInsight;
		using Microsoft.Hadoop.Client;

9. Aggiungere alla classe la definizione di funzione seguente. Questa funzione viene usata per attendere il completamento di un processo Hadoop.

        private static void WaitForJobCompletion(JobCreationResults jobResults, IJobSubmissionClient client)
        {
            JobDetails jobInProgress = client.GetJob(jobResults.JobId);
            while (jobInProgress.StatusCode != JobStatusCode.Completed && jobInProgress.StatusCode != JobStatusCode.Failed)
            {
                jobInProgress = client.GetJob(jobInProgress.JobId);
                Thread.Sleep(TimeSpan.FromSeconds(10));
            }
        }

10. Nella funzione **Main()** incollare il codice seguente:

		// Set the variables
		string subscriptionID = "<Azure subscription ID>";
		string certFriendlyName = "<certificate friendly name>";

		string clusterName = "<HDInsight cluster name>";

		string storageAccountName = "<Azure storage account name>";
		string storageAccountKey = "<Azure storage account key>";
		string containerName = "<Blob container name>";


	Queste sono tutte le variabili che è necessario impostare per il programma. È possibile ottenere il nome della sottoscrizione di Azure dal [portale di anteprima di Azure][azure-management-portal].

	Per informazioni sul certificato, vedere [Creare e caricare un certificato di gestione per Azure][azure-certificate]. Un modo semplice per configurare il certificato consiste nell'eseguire i cmdlet di Azure PowerShell **Get-AzurePublishSettingsFile** e **Import-AzurePublishSettingsFile**, che creeranno e gestiranno automaticamente il certificato di gestione. Dopo aver eseguito questi cmdlet, è possibile aprire **certmgr.msc** dalla workstation e individuare il certificato espandendo **Personal** > **Certificates**. Il certificato creato dai cmdlet di PowerShell conterrà l'indicazione Strumenti di Azure in entrambi in campi **Rilasciato a** e **Rilasciato da**.

	Il nome dell'account di archiviazione di Azure è quello specificato al momento del provisioning del cluster HDInsight. Il nome predefinito del contenitore corrisponde al nome del cluster HDInsight.

11. Nella funzione **Main()** aggiungere il codice seguente per definire il processo MapReduce:


        // Define the MapReduce job
        MapReduceJobCreateParameters mrJobDefinition = new MapReduceJobCreateParameters()
        {
            JarFile = "wasb:///example/jars/hadoop-mapreduce-examples.jar",
            ClassName = "wordcount"
        };

        mrJobDefinition.Arguments.Add("wasb:///example/data/gutenberg/davinci.txt");
        mrJobDefinition.Arguments.Add("wasb:///example/data/WordCountOutput");

	Vengono usati due argomenti. Il primo argomento è il nome del file di origine, il secondo è il percorso del file di output. Per altre informazioni sul prefisso wasb://, vedere l'articolo relativo all'[uso dell'archivio BLOB di Azure con HDInsight][hdinsight-storage].

12. Nella funzione **Main()** aggiungere il codice seguente per creare un oggetto JobSubmissionCertificateCredential:

        // Get the certificate object from certificate store using the friendly name to identify it
        X509Store store = new X509Store();
        store.Open(OpenFlags.ReadOnly);
        X509Certificate2 cert = store.Certificates.Cast<X509Certificate2>().First(item => item.FriendlyName == certFriendlyName);
        JobSubmissionCertificateCredential creds = new JobSubmissionCertificateCredential(new Guid(subscriptionID), cert, clusterName);

13. Nella funzione **Main()** aggiungere il codice seguente per eseguire il processo e attenderne il completamento:

        // Create a hadoop client to connect to HDInsight
        var jobClient = JobSubmissionClientFactory.Connect(creds);

        // Run the MapReduce job
        JobCreationResults mrJobResults = jobClient.CreateMapReduceJob(mrJobDefinition);

        // Wait for the job to complete
        WaitForJobCompletion(mrJobResults, jobClient);

14. Nella funzione **Main()** aggiungere il codice seguente per stampare l'output del processo MapReduce:

		// Print the MapReduce job output
		Stream stream = new MemoryStream();

		CloudStorageAccount storageAccount = CloudStorageAccount.Parse("DefaultEndpointsProtocol=https;AccountName=" + storageAccountName + ";AccountKey=" + storageAccountKey);
		CloudBlobClient blobClient = storageAccount.CreateCloudBlobClient();
		CloudBlobContainer blobContainer = blobClient.GetContainerReference(containerName);
		CloudBlockBlob blockBlob = blobContainer.GetBlockBlobReference("example/data/WordCountOutput/part-r-00000");

		blockBlob.DownloadToStream(stream);
		stream.Position = 0;

		StreamReader reader = new StreamReader(stream);
		Console.WriteLine(reader.ReadToEnd());

        Console.WriteLine("Press ENTER to continue.");
		Console.ReadLine();

	La cartella di output viene specificata al momento della definizione del processo MapReduce. Il nome file predefinito è **part-r-00000**.

**Per eseguire l'applicazione**

Mentre l'applicazione è aperta in Visual Studio, premere **F5** per eseguirla. Verrà aperta una finestra della console in cui sono visualizzati lo stato e l'output dell'applicazione.

##Inviare processi MapReduce di flussi Hadoop tramite HDInsight .NET SDK
I cluster HDInsight includono un programma di flussi Hadoop per il conteggio di parole sviluppato in C\#. Il programma per il mapping è */example/apps/cat.exe* e il programma per la riduzione è */example/apps/wc.exe*. In questa sessione si apprenderà come creare un'applicazione .NET per eseguire l'esempio relativo al conteggio di parole.

Per informazioni dettagliate sulla creazione di un'applicazione .NET per l'invio di processi MapReduce, vedere [Inviare processi MapReduce tramite HDInsight .NET SDK](#mapreduce-sdk).

Per altre informazioni sullo sviluppo e sulla distribuzione di processi di flussi di Hadoop, vedere [Sviluppare programmi per la creazione di flussi Hadoop in C#per HDInsight][hdinsight-develop-streaming-jobs].

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

	namespace SubmitStreamingJob
	{
	    class Program
	    {
	        static void Main(string[] args)
	        {

				// Set the variables
				string subscriptionID = "<Azure subscription ID>";
				string certFriendlyName = "<certificate friendly name>";

				string clusterName = "<HDInsight cluster name>";
				string statusFolderName = @"/tutorials/wordcountstreaming/status";

	            // Define the Hadoop streaming MapReduce job
	            StreamingMapReduceJobCreateParameters myJobDefinition = new StreamingMapReduceJobCreateParameters()
	            {
	                JobName = "my word counting job",
	                StatusFolder = statusFolderName,
	                Input = "/example/data/gutenberg/davinci.txt",
	                Output = "/tutorials/wordcountstreaming/output",
	                Reducer = "wc.exe",
	                Mapper = "cat.exe"
	            };

	            myJobDefinition.Files.Add("/example/apps/wc.exe");
	            myJobDefinition.Files.Add("/example/apps/cat.exe");

	            // Get the certificate object from certificate store using the friendly name to identify it
	            X509Store store = new X509Store();
	            store.Open(OpenFlags.ReadOnly);
	            X509Certificate2 cert = store.Certificates.Cast<X509Certificate2>().First(item => item.FriendlyName == certFriendlyName);

	            JobSubmissionCertificateCredential creds = new JobSubmissionCertificateCredential(new Guid(subscriptionID), cert, clusterName);

	            // Create a hadoop client to connect to HDInsight
	            var jobClient = JobSubmissionClientFactory.Connect(creds);

	            // Run the MapReduce job
	            Console.WriteLine("----- Submit the Hadoop streaming job ...");
	            JobCreationResults mrJobResults = jobClient.CreateStreamingJob(myJobDefinition);

	            // Wait for the job to complete
	            Console.WriteLine("----- Wait for the Hadoop streaming job to complete ...");
	            WaitForJobCompletion(mrJobResults, jobClient);

	            // Display the error log
	            Console.WriteLine("----- The hadoop streaming job error log.");
	            using (Stream stream = jobClient.GetJobErrorLogs(mrJobResults.JobId))
	            {
	                var reader = new StreamReader(stream);
	                Console.WriteLine(reader.ReadToEnd());
	            }

	            // Display the output log
	            Console.WriteLine("----- The hadoop streaming job output log.");
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






##Inviare processi Hive mediante HDInsight .NET SDK
I cluster HDInsight includono una tabella Hive di esempio denominata *hivesampletable*. In questa sessione si creerà un'applicazione .NET per eseguire un processo Hive che elenca le tabelle Hive create in un cluster HDInsight. Per altre informazioni sull'uso di Hive, vedere [Usare Hive con Hadoop in HDInsight][hdinsight-use-hive].

Le procedure seguenti sono necessarie per effettuare il provisioning di un cluster HDInsight usando l'SDK:

- Installare HDInsight .NET SDK
- Creare un'applicazione console
- Eseguire l'applicazione


**Per installare HDInsight .NET SDK** È possibile installare l'ultima build pubblicata dell'SDK da [NuGet](http://nuget.codeplex.com/wikipage?title=Getting%20Started). Le istruzioni verranno illustrate nella procedura successiva.

**Per creare un'applicazione console di Visual Studio**

1. Aprire Visual Studio.

2. Scegliere **Nuovo** dal menu **File** e quindi fare clic su **Progetto**.

3. In **Nuovo progetto** digitare o selezionare i valori seguenti:

	<table style="border-color: #c6c6c6; border-width: 2px; border-style: solid; border-collapse: collapse;">
<tr>
<th style="border-color: #c6c6c6; border-width: 2px; border-style: solid; border-collapse: collapse; width:90px; padding-left:5px; padding-right:5px;">Proprietà</th>
<th style="border-color: #c6c6c6; border-width: 2px; border-style: solid; border-collapse: collapse; width:90px; padding-left:5px; padding-right:5px;">Valore</th></tr>
<tr>
<td style="border-color: #c6c6c6; border-width: 2px; border-style: solid; border-collapse: collapse; padding-left:5px;">Categoria</td>
<td style="border-color: #c6c6c6; border-width: 2px; border-style: solid; border-collapse: collapse; padding-left:5px; padding-right:5px;">Templates/Visual C#/Windows</td></tr>
<tr>
<td style="border-color: #c6c6c6; border-width: 2px; border-style: solid; border-collapse: collapse; padding-left:5px;">Modello</td>
<td style="border-color: #c6c6c6; border-width: 2px; border-style: solid; border-collapse: collapse; padding-left:5px;">Applicazione console</td></tr>
<tr>
<td style="border-color: #c6c6c6; border-width: 2px; border-style: solid; border-collapse: collapse; padding-left:5px;">Nome</td>
<td style="border-color: #c6c6c6; border-width: 2px; border-style: solid; border-collapse: collapse; padding-left:5px;">SubmitHiveJob</td></tr>
</table>

4. Fare clic su **OK** per creare il progetto.


5. Scegliere **Gestione pacchetti libreria** dal menu **Strumenti** e quindi fare clic su **Console di Gestione pacchetti**.

6. Eseguire il comando seguente nella console per installare il pacchetto:

		Install-Package Microsoft.WindowsAzure.Management.HDInsight


	Questo comando aggiunge librerie .NET e riferimenti ad esse nel progetto corrente di Visual Studio.

7. In **Esplora soluzioni** fare doppio clic su **Program.cs** per aprirlo.

8. Aggiungere le istruzioni **using** seguenti all'inizio del file.

		using System.IO;
		using System.Threading;
		using System.Security.Cryptography.X509Certificates;

		using Microsoft.WindowsAzure.Management.HDInsight;
		using Microsoft.Hadoop.Client;

9. Aggiungere alla classe la definizione di funzione seguente. Questa funzione viene usata per attendere il completamento di un processo Hadoop.

        private static void WaitForJobCompletion(JobCreationResults jobResults, IJobSubmissionClient client)
        {
            JobDetails jobInProgress = client.GetJob(jobResults.JobId);
            while (jobInProgress.StatusCode != JobStatusCode.Completed && jobInProgress.StatusCode != JobStatusCode.Failed)
            {
                jobInProgress = client.GetJob(jobInProgress.JobId);
                Thread.Sleep(TimeSpan.FromSeconds(10));
            }
        }

10. Nella funzione **Main()** incollare il codice seguente:

		// Set the variables
		string subscriptionID = "<Azure subscription ID>";
		string clusterName = "<HDInsight cluster name>";
		string certFriendlyName = "<certificate friendly name>";


	Queste sono tutte le variabili che è necessario impostare per il programma. È possibile ottenere l'ID della sottoscrizione di Azure dall'amministratore di sistema.

	Per informazioni sul certificato, vedere [Creare e caricare un certificato di gestione per Azure][azure-certificate]. Un modo semplice per configurare il certificato consiste nell'eseguire i cmdlet di Azure PowerShell **Get-AzurePublishSettingsFile** e **Import-AzurePublishSettingsFile**, che creeranno e gestiranno automaticamente il certificato di gestione. Dopo aver eseguito questi cmdlet, è possibile aprire **certmgr.msc** dalla workstation e individuare il certificato espandendo **Personal** > **Certificates**. Il certificato creato dai cmdlet di Azure PowerShell conterrà l'indicazione Strumenti di Azure in entrambi i campi **Rilasciato a** e **Rilasciato da**.

11. Nella funzione **Main()** aggiungere il codice seguente per definire il processo Hive:

        // define the Hive job
        HiveJobCreateParameters hiveJobDefinition = new HiveJobCreateParameters()
        {
            JobName = "show tables job",
            StatusFolder = "/ShowTableStatusFolder",
            Query = "show tables;"
        };

	È inoltre possibile usare il parametro **File** per specificare un file di script HiveQL in HDFS, ad esempio:

        // define the Hive job
        HiveJobCreateParameters hiveJobDefinition = new HiveJobCreateParameters()
        {
            JobName = "show tables job",
            StatusFolder = "/ShowTableStatusFolder",
            File = "/user/admin/showtables.hql"
        };


12. Nella funzione **Main()** aggiungere il codice seguente per creare un oggetto **JobSubmissionCertificateCredential**:

        // Get the certificate object from certificate store using the friendly name to identify it
        X509Store store = new X509Store();
        store.Open(OpenFlags.ReadOnly);
        X509Certificate2 cert = store.Certificates.Cast<X509Certificate2>().First(item => item.FriendlyName == certFriendlyName);
        JobSubmissionCertificateCredential creds = new JobSubmissionCertificateCredential(new Guid(subscriptionID), cert, clusterName);

13. Nella funzione **Main()** aggiungere il codice seguente per eseguire il processo e attenderne il completamento:

        // Submit the Hive job
        var jobClient = JobSubmissionClientFactory.Connect(creds);
        JobCreationResults jobResults = jobClient.CreateHiveJob(hiveJobDefinition);

        // Wait for the job to complete
        WaitForJobCompletion(jobResults, jobClient);

14. Nella funzione **Main()** aggiungere il codice seguente per stampare l'output del processo Hive:

        // Print the Hive job output
        System.IO.Stream stream = jobClient.GetJobOutput(jobResults.JobId);

        StreamReader reader = new StreamReader(stream);
        Console.WriteLine(reader.ReadToEnd());

        Console.WriteLine("Press ENTER to continue.");
        Console.ReadLine();

**Per eseguire l'applicazione**

Mentre l'applicazione è aperta in Visual Studio, premere **F5** per eseguirla. Verrà aperta una finestra della console in cui è visualizzato lo stato dell'applicazione. L'output dovrebbe essere analogo al seguente:

	hivesampletable

##Invio di processi tramite gli strumenti di HDInsight per Visual Studio

Utilizzando gli strumenti di HDInsight per Visual Studio, è possibile eseguire query Hive e Pig script. Vedere [Introduzione all'uso di Hadoop Tools per Visual Studio per HDInsight](hdinsight-hadoop-visual-studio-tools-get-started.md).


##Passaggi successivi
In questo articolo si sono appresi vari modi per effettuare il provisioning di un cluster HDInsight. Per altre informazioni, vedere gli articoli seguenti:

* [Introduzione all'uso di Azure HDInsight][hdinsight-get-started]
* [Effettuare il provisioning di cluster HDInsight][hdinsight-provision]
* [Gestire cluster Hadoop in HDInsight tramite Azure PowerShell][hdinsight-admin-powershell]
* [Documentazione di riferimento dei cmdlet di HDInsight][hdinsight-powershell-reference]
* [Usare Hive con HDInsight][hdinsight-use-hive]
* [Usare Pig con HDInsight][hdinsight-use-pig]


[azure-certificate]: http://msdn.microsoft.com/library/windowsazure/gg551722.aspx
[azure-management-portal]: https://portal.azure.com/

[hdinsight-visual-studio-tools]: ../HDInsight/hdinsight-hadoop-visual-studio-tools-get-started.md
[hdinsight-use-sqoop]: hdinsight-use-sqoop.md
[hdinsight-provision]: hdinsight-provision-clusters.md
[hdinsight-use-mapreduce]: hdinsight-use-mapreduce.md
[hdinsight-use-hive]: hdinsight-use-hive.md
[hdinsight-use-pig]: hdinsight-use-pig.md
[hdinsight-get-started]: ../hdinsight-get-started.md
[hdinsight-storage]: ../hdinsight-use-blob-storage.md
[hdinsight-admin-powershell]: hdinsight-administer-use-powershell.md
[hdinsight-develop-streaming-jobs]: hdinsight-hadoop-develop-deploy-streaming-jobs.md

[hdinsight-powershell-reference]: https://msdn.microsoft.com/library/dn858087.aspx

[powershell-install-configure]: ../install-configure-powershell.md

[image-hdi-gettingstarted-runmrjob]: ./media/hdinsight-submit-hadoop-jobs-programmatically/HDI.GettingStarted.RunMRJob.png
[image-hdi-gettingstarted-mrjoboutput]: ./media/hdinsight-submit-hadoop-jobs-programmatically/HDI.GettingStarted.MRJobOutput.png

[apache-hive]: http://hive.apache.org/

<!---HONumber=August15_HO8-->