<properties urlDisplayName="HDInsight Administration" pageTitle="Inviare processi Hadoop in HDInsight | Azure" metaKeywords="hdinsight, hdinsight administration, hdinsight administration azure, hive, mapreduce, HDInsight .NET SDK, powershell, submit mapreduce jobs, submit hive jobs, development, hadoop, apache" description="Learn how to submit Hadoop jobs to Azure HDInsight Hadoop." umbracoNaviHide="0" disqusComments="1" editor="cgronlun" manager="paulettm" services="hdinsight" documentationCenter="" title="Submit  Hadoop jobs in HDInsight" authors="jgao" />

<tags ms.service="hdinsight" ms.workload="big-data" ms.tgt_pltfrm="na" ms.devlang="na" ms.topic="article" ms.date="11/12/2014" ms.author="jgao" />

# Inviare processi Hadoop in HDInsight

In questo articolo verrà illustrato come inviare processi MapReduce e Hive tramite PowerShell e HDInsight .NET SDK.

**Prerequisiti:**

Per eseguire le procedure descritte nell'articolo è necessario:

* Disporre di un cluster HDInsight di Azure. Per istruzioni, vedere [Introduzione all'uso di HDInsight][hdinsight-get-started] o [Provisioning di cluster HDInsight][hdinsight-provision].
* Installare e configurare Azure PowerShell. Per istruzioni, vedere [Come installare e configurare Azure PowerShell][powershell-install-configure].


##Contenuto dell'articolo

* [Inviare processi MapReduce tramite PowerShell](#mapreduce-powershell)
* [Inviare processi Hive tramite PowerShell](#hive-powershell)
* [Inviare processi Sqoop tramite PowerShell](#sqoop-powershell)
* [Inviare processi MapReduce tramite HDInsight .NET SDK](#mapreduce-sdk)
* [Inviare processi MapReduce di flussi Hadoop tramite HDInsight .NET SDK](#streaming-sdk)
* [Inviare processi Hive tramite HDInsight .NET SDK](#hive-sdk)
* [Passaggi successivi](#nextsteps)

##<a id="mapreduce-powershell"></a> Inviare processi MapReduce tramite PowerShell
Azure PowerShell è un ambiente di scripting potente che può essere usato per controllare e automatizzare la distribuzione e la gestione dei carichi di lavoro in Azure. Per altre informazioni sull'uso di PowerShell con HDInsight, vedere [Amministrazione di HDInsight tramite PowerShell][hdinsight-admin-powershell].

Hadoop MapReduce è un framework software per la scrittura di applicazioni in grado di elaborare quantità elevate di dati. I cluster HDInsight includono un file con estensione jar, situato nella cartella *\example\jars\hadoop-examples.jar*, che contiene svariati esempi di MapReduce. Nella versione 3.0 dei cluster HDInsight, il file è stato rinominato in hadoop-mapreduce-examples.jar. Uno degli esempi è relativo al conteggio della frequenza delle parole nei file di origine. In questa sessione si apprenderà come usare PowerShell da una workstation per eseguire l'esempio relativo al conteggio di parole. Per altre informazioni sullo sviluppo e l'esecuzione di processi MapReduce, vedere [Usare MapReduce con HDInsight][hdinsight-use-mapreduce].

**Per eseguire il programma MapReduce di conteggio delle parole usando PowerShell**

1.	Aprire **Azure PowerShell**. Per istruzioni sull'apertura della finestra della console Azure PowerShell, vedere [Come installare e configurare Azure PowerShell][powershell-install-configure].

3. Impostare le due variabili necessarie eseguendo i comandi di PowerShell seguenti:
		
		$subscriptionName = "<SubscriptionName>"   
		$clusterName = "<HDInsightClusterName>"    

	La sottoscrizione è quella usata per creare il cluster HDInsight. Il cluster HDInsight è quello da usare per eseguire il processo MapReduce.
	
5. Per creare una definizione del processo MapReduce, eseguire i comandi seguenti:

		# Define the word count MapReduce job
		$wordCountJobDefinition = New-AzureHDInsightMapReduceJobDefinition -JarFile "wasb:///example/jars/hadoop-mapreduce-examples.jar" -ClassName "wordcount" -Arguments "wasb:///example/data/gutenberg/davinci.txt", "wasb:///example/data/WordCountOutput"

	Vengono usati due argomenti. Il primo argomento è il nome del file di origine, il secondo è il percorso del file di output. Per altre informazioni sul prefisso wasb, vedere [Usare l'archiviazione BLOB di Azure con HDInsight][hdinsight-storage].

6. Per eseguire il processo MapReduce, eseguire il comando seguente:

		# Submit the MapReduce job
		Select-AzureSubscription $subscriptionName
		$wordCountJob = Start-AzureHDInsightJob -Cluster $clusterName -JobDefinition $wordCountJobDefinition 

	Oltre alla definizione del processo MapReduce, è necessario specificare anche il nome del cluster HDInsight in cui si desidera eseguire il processo MapReduce. 

7. Per verificare il completamento del processo MapReduce, eseguire il comando seguente:

		# Wait for the job to complete
		Wait-AzureHDInsightJob -Job $wordCountJob -WaitTimeoutInSeconds 3600 
		

8. Per verificare se si sono verificati errori nel processo MapReduce, eseguire il comando seguente:	

		# Get the job standard error output
		Get-AzureHDInsightJobOutput -Cluster $clusterName -JobId $wordCountJob.JobId -StandardError 
					
	La schermata seguente mostra l'output di un'esecuzione completata correttamente. In caso di esito negativo, verranno visualizzati alcuni messaggi di errore.

	![HDI.GettingStarted.RunMRJob][image-hdi-gettingstarted-runmrjob]

		
**Per recuperare i risultati del processo MapReduce**

1. Aprire **Azure PowerShell**.
2. Impostare le tre variabili necessarie eseguendo i comandi di PowerShell seguenti:

		$subscriptionName = "<SubscriptionName>"       
		$storageAccountName = "<StorageAccountName>"
		$containerName = "<ContainerName>"			

	L'account di archiviazione di Azure è quello specificato durante il provisioning del cluster HDInsight. L'account di archiviazione viene usato per l'hosting del contenitore BLOB usato come file system predefinito per il cluster HDInsight.  Il nome del contenitore di archiviazione BLOB corrisponde in genere al nome del cluster HDInsight, a meno che non venga specificato un nome diverso durante il provisioning del cluster.

3. Per creare un oggetto contesto archiviazione di Azure, eseguire i comandi seguenti:

		# Create the storage account context object
		Select-AzureSubscription $subscriptionName
		$storageAccountKey = Get-AzureStorageKey $storageAccountName | %{ $_.Primary }
		$storageContext = New-AzureStorageContext -StorageAccountName $storageAccountName -StorageAccountKey $storageAccountKey  

	Il comando Select-AzureSubscription consente di impostare la sottoscrizione corrente, nel caso in cui siano disponibili più sottoscrizioni e non si desideri usare la sottoscrizione predefinita. 

4. Per scaricare l'output del processo MapReduce dal contenitore BLOB alla workstation, eseguire il comando seguente:

		# Get the blob content
		Get-AzureStorageBlobContent -Container $ContainerName -Blob example/data/WordCountOutput/part-r-00000 -Context $storageContext -Force

	Esempio *example/data/WordCountOutput* è la cartella di output specificata quando si esegue il processo MapReduce. *part-r-00000* è il nome file predefinito per l'output del processo MapReduce.  Il file verrà scaricato nella stessa struttura di cartelle nella cartella locale. Ad esempio, nella schermata seguente la cartella corrente è la cartella radice C.  Il file verrà scaricato nella cartella *C:\example\data\WordCountOutput\* .

5. Per stampare il file di output del processo MapReduce, eseguire il comando seguente:

		cat ./example/data/WordCountOutput/part-r-00000 | findstr "there"

	![HDI.GettingStarted.MRJobOutput][image-hdi-gettingstarted-mrjoboutput]

	Il processo MapReduce produce un file denominato *part-r-00000*, che include le parole e i conteggi.  Lo script usa il comando findstr per elencare tutte le parole contenenti "there".


> [WACOM.NOTE] Se si apre ./example/data/WordCountOutput/part-r-00000, un output multilinea dal processo MapReduce, nel Blocco note, si noterà che il rendering delle interruzioni di riga non è corretto. Si tratta di un comportamento previsto.









































































































































##<a id="hive-powershell"></a> Inviare processi Hive tramite PowerShell
Apache [hdinsight-use-hive][apache-hive] fornisce un metodo per l'esecuzione di processi MapReduce mediante un linguaggio di scripting simile a SQL, denominato *HiveQL*, che può essere applicato per attività di riepilogo, query e analisi di volumi di dati molto elevati. 

I cluster HDInsight includono una tabella Hive di esempio denominata *hivesampletable*. In questa sessione si utilizzerà PowerShell per eseguire un processo Hive per ottenere un elenco di dati dalla tabella Hive. 

**Per eseguire un processo Hive usando PowerShell**

1.	Aprire **Azure PowerShell**. Per istruzioni sull'apertura della finestra della console Azure PowerShell, vedere [Come installare e configurare Azure PowerShell][powershell-install-configure].

2. Impostare le prime due variabili necessarie nei comandi seguenti, quindi eseguirli:
		
		$subscriptionName = "<SubscriptionName>"   
		$clusterName = "<HDInsightClusterName>"             
		$querystring = "SELECT * FROM hivesampletable WHERE Country='United Kingdom';"

	$querystring corrisponde alla query HiveQL.

3. Eseguire i seguenti comandi per selezionare la sottoscrizione di Azure e il cluster per eseguire il processo Hive:

		Select-AzureSubscription -SubscriptionName $subscriptionName

4. Inviare il processo Hive:

		Use-AzureHDInsightCluster $clusterName
		Invoke-Hive -Query $queryString

	È possibile usare l'opzione -File per specificare un file di script HiveQL in HDFS.

Per altre informazioni su Hive, vedere [Usare Hive con HDInsight][hdinsight-use-hive].

##<a id="sqoop-powershell"></a>Inviare processi Sqoop tramite PowerShell

Vedere [Usare Sqoop con HDInsight][hdinsight-use-sqoop].

##<a id="mapreduce-sdk"></a> Inviare processi MapReduce tramite HDInsight .NET SDK
HDInsight .NET SDK fornisce librerie client .NET che semplificano l'uso dei cluster HDInsight da .NET. I cluster HDInsight includono un file con estensione jar, situato nella cartella *\example\jars\hadoop-examples.jar*, che contiene svariati esempi di MapReduce. Uno degli esempi è relativo al conteggio della frequenza delle parole nei file di origine. In questa sessione si apprenderà come creare un'applicazione .NET per eseguire l'esempio relativo al conteggio di parole. Per altre informazioni sullo sviluppo e l'esecuzione di processi MapReduce, vedere [Usare MapReduce con HDInsight][hdinsight-use-mapreduce].


Le procedure seguenti sono necessarie per eseguire il provisioning di un cluster HDInsight usando l'SDK:

- Installazione di HDInsight .NET SDK
- Creazione di un'applicazione console
- Esecuzione dell'applicazione


**Per installare .NET SDK per HDInsight**
È possibile installare l'ultima build pubblicata dell'SDK [NuGet](http://nuget.codeplex.com/wikipage?title=Getting%20Started). Le istruzioni verranno illustrate nella procedura successiva.

**Per creare l'applicazione console di Visual Studio**

1. Aprire Visual Studio 2012.

2. Scegliere **Nuovo** dal menu File, quindi fare clic su **Progetto**.

3. In Nuovo progetto digitare o selezionare i valori seguenti:

	<table style="border-color: #c6c6c6; border-width: 2px; border-style: solid; border-collapse: collapse;">
	<tr>
	<th style="border-color: #c6c6c6; border-width: 2px; border-style: solid; border-collapse: collapse; width:90px; padding-left:5px; padding-right:5px;">Property</th>
	<th style="border-color: #c6c6c6; border-width: 2px; border-style: solid; border-collapse: collapse; width:90px; padding-left:5px; padding-right:5px;">Value</th></tr>
	<tr>
	<td style="border-color: #c6c6c6; border-width: 2px; border-style: solid; border-collapse: collapse; padding-left:5px;">Category</td>
	<td style="border-color: #c6c6c6; border-width: 2px; border-style: solid; border-collapse: collapse; padding-left:5px; padding-right:5px;">Templates/Visual C#/Windows</td></tr>
	<tr>
	<td style="border-color: #c6c6c6; border-width: 2px; border-style: solid; border-collapse: collapse; padding-left:5px;">Template</td>
	<td style="border-color: #c6c6c6; border-width: 2px; border-style: solid; border-collapse: collapse; padding-left:5px;">Console Application</td></tr>
	<tr>
	<td style="border-color: #c6c6c6; border-width: 2px; border-style: solid; border-collapse: collapse; padding-left:5px;">Name</td>
	<td style="border-color: #c6c6c6; border-width: 2px; border-style: solid; border-collapse: collapse; padding-left:5px;">SubmitMapReduceJob</td></tr>
	</table>

4. Fare clic su **OK** per creare il progetto.


5. Scegliere **Gestione pacchetti libreria** dal menu **Strumenti**, quindi fare clic su **Console di Gestione pacchetti**.

6. Eseguire i seguenti comandi nella console per installare i pacchetti.

		Install-Package Microsoft.WindowsAzure.Management.HDInsight


	Questo comando aggiunge librerie .NET e riferimenti ad esse nel progetto corrente di Visual Studio. La versione sarà 0.11.0.1 o successiva.

7. In Esplora soluzioni fare doppio clic su **Program.cs** per aprirlo.

8. Aggiungere le istruzioni using seguenti all'inizio del file:

		using System.IO;
		using System.Threading;
		using System.Security.Cryptography.X509Certificates;
		
		using Microsoft.WindowsAzure.Storage;
		using Microsoft.WindowsAzure.Storage.Blob;
		
		using Microsoft.WindowsAzure.Management.HDInsight;
		using Microsoft.Hadoop.Client;

9. Aggiungere la definizione di funzione seguente alla classe. Questa funzione viene usata per attendere il completamento di un processo Hadoop.

        private static void WaitForJobCompletion(JobCreationResults jobResults, IJobSubmissionClient client)
        {
            JobDetails jobInProgress = client.GetJob(jobResults.JobId);
            while (jobInProgress.StatusCode != JobStatusCode.Completed && jobInProgress.StatusCode != JobStatusCode.Failed)
            {
                jobInProgress = client.GetJob(jobInProgress.JobId);
                Thread.Sleep(TimeSpan.FromSeconds(10));
            }
        }
	
10. Nella funzione Main() copiare e incollare il codice seguente:
		
		// Set the variables
		string subscriptionID = "<Azure subscription ID>";
		string certFriendlyName = "<certificate friendly name>";

		string clusterName = "<HDInsight cluster name>";
		
		string storageAccountName = "<Azure storage account name>";
		string storageAccountKey = "<Azure storage account key>";
		string containerName = "<Blob container name>";
		
	
	Queste sono tutte le variabili che è necessario impostare per il programma. È possibile ottenere il nome della sottoscrizione di Azure dal [portale di gestione di Azure][azure-management-portal]. 

	Per informazioni sul certificato, vedere [Creare e caricare un certificato di gestione per Azure][azure-certificate]. Un modo semplice per configurare il certificato consiste nell'eseguire i cmdlet di PowerShell *Get-AzurePublishSettingsFile* e *Import-AzurePublishSettingsFile*, che creeranno e gestiranno automaticamente il certificato di gestione. Dopo aver eseguito i cmdlet di PowerShell, è possibile aprire *certmgr.msc* dalla workstation e individuare il certificato espandendo *Personale/Certificati*. Il certificato creato dai cmdlet di PowerShell conterrà l'indicazione *Strumenti di Azure* in entrambi in campi *Rilasciato a* e *Rilasciato da*.

	Il nome dell'account di archiviazione di Azure è quello specificato al momento del provisioning del cluster HDInsight. Il nome predefinito del contenitore corrisponde al nome del cluster HDInsight.
	
11. Nella funzione Main() aggiungere il codice seguente per definire il processo MapReduce:


        // Define the MapReduce job
        MapReduceJobCreateParameters mrJobDefinition = new MapReduceJobCreateParameters()
        {
            JarFile = "wasb:///example/jars/hadoop-examples.jar",
            ClassName = "wordcount"
        };

        mrJobDefinition.Arguments.Add("wasb:///example/data/gutenberg/davinci.txt");
        mrJobDefinition.Arguments.Add("wasb:///example/data/WordCountOutput");

	Vengono usati due argomenti. Il primo argomento è il nome del file di origine, il secondo è il percorso del file di output. Per altre informazioni sul prefisso wasb, vedere [Usare l'archiviazione BLOB di Azure con HDInsight][hdinsight-storage].
		
12. Nella funzione Main() aggiungere il codice seguente per creare un oggetto JobSubmissionCertificateCredential:

        // Get the certificate object from certificate store using the friendly name to identify it
        X509Store store = new X509Store();
        store.Open(OpenFlags.ReadOnly);
        X509Certificate2 cert = store.Certificates.Cast<X509Certificate2>().First(item => item.FriendlyName == certFriendlyName);
        JobSubmissionCertificateCredential creds = new JobSubmissionCertificateCredential(new Guid(subscriptionID), cert, clusterName);
		
13. Nella funzione Main() aggiungere il codice seguente per eseguire il processo e attenderne il completamento:

        // Create a hadoop client to connect to HDInsight
        var jobClient = JobSubmissionClientFactory.Connect(creds);

        // Run the MapReduce job
        JobCreationResults mrJobResults = jobClient.CreateMapReduceJob(mrJobDefinition);

        // Wait for the job to complete
        WaitForJobCompletion(mrJobResults, jobClient);

14. Nella funzione Main() aggiungere il codice seguente per stampare l'output del processo MapReduce:

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

	La cartella di output viene specificata al momento della definizione del processo MapReduce. Il nome file predefinito è *part-r-00000*.

**Per eseguire l'applicazione**

Mentre l'applicazione è aperta in Visual Studio, premere **F5** per eseguirla. Verrà aperta una finestra della console in cui sono visualizzati lo stato e l'output dell'applicazione. 

##<a id="streaming-sdk"></a> Inviare processi MapReduce di flussi Hadoop tramite HDInsight .NET SDK
I cluster HDInsight includono un programma di flussi Hadoop per il conteggio di parole sviluppato in C#. Il programma mapper è */example/apps/cat.exe* e il programma reducer è */example/apps/wc.exe*. In questa sessione si apprenderà come creare un'applicazione .NET per eseguire l'esempio relativo al conteggio di parole. 

Per informazioni dettagliate sulla creazione di un'applicazione .NET per l'invio di processi MapReduce, vedere [Inviare processi MapReduce tramite HDInsight .NET SDK](#mapreduce-sdk).

Per altre informazioni sullo sviluppo e sulla distribuzione di processi di flussi di Hadoop, vedere [Sviluppare programmi per la creazione di flussi Hadoop in C# per HDInsight][hdinsight-develop-streaming-jobs].

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






##<a id="hive-sdk"></a> Inviare processi Hive tramite HDInsight .NET SDK 
I cluster HDInsight includono una tabella Hive di esempio denominata *hivesampletable*. In questa sessione si creerà un'applicazione .NET per eseguire un processo Hive che elenca le tabelle Hive create nel cluster HDInsight. Per altre informazioni sull'uso di Hive, vedere [Usare Hive con HDInsight][hdinsight-use-hive].

Le procedure seguenti sono necessarie per eseguire il provisioning di un cluster HDInsight usando l'SDK:

- Installazione di HDInsight .NET SDK
- Creazione di un'applicazione console
- Esecuzione dell'applicazione


**Per installare .NET SDK per HDInsight**
È possibile installare l'ultima build pubblicata dell'SDK [NuGet](http://nuget.codeplex.com/wikipage?title=Getting%20Started). Le istruzioni verranno illustrate nella procedura successiva.

**Per creare l'applicazione console di Visual Studio**

1. Aprire Visual Studio 2012.

2. Scegliere **Nuovo** dal menu File, quindi fare clic su **Progetto**.

3. In Nuovo progetto digitare o selezionare i valori seguenti:

	<table style="border-color: #c6c6c6; border-width: 2px; border-style: solid; border-collapse: collapse;">
	<tr>
	<th style="border-color: #c6c6c6; border-width: 2px; border-style: solid; border-collapse: collapse; width:90px; padding-left:5px; padding-right:5px;">Property</th>
	<th style="border-color: #c6c6c6; border-width: 2px; border-style: solid; border-collapse: collapse; width:90px; padding-left:5px; padding-right:5px;">Value</th></tr>
	<tr>
	<td style="border-color: #c6c6c6; border-width: 2px; border-style: solid; border-collapse: collapse; padding-left:5px;">Category</td>
	<td style="border-color: #c6c6c6; border-width: 2px; border-style: solid; border-collapse: collapse; padding-left:5px; padding-right:5px;">Templates/Visual C#/Windows</td></tr>
	<tr>
	<td style="border-color: #c6c6c6; border-width: 2px; border-style: solid; border-collapse: collapse; padding-left:5px;">Template</td>
	<td style="border-color: #c6c6c6; border-width: 2px; border-style: solid; border-collapse: collapse; padding-left:5px;">Console Application</td></tr>
	<tr>
	<td style="border-color: #c6c6c6; border-width: 2px; border-style: solid; border-collapse: collapse; padding-left:5px;">Name</td>
	<td style="border-color: #c6c6c6; border-width: 2px; border-style: solid; border-collapse: collapse; padding-left:5px;">SubmitHiveJob</td></tr>
	</table>

4. Fare clic su **OK** per creare il progetto.


5. Scegliere **Gestione pacchetti libreria** dal menu **Strumenti**, quindi fare clic su **Console di Gestione pacchetti**.

6. Eseguire i seguenti comandi nella console per installare i pacchetti.

		Install-Package Microsoft.WindowsAzure.Management.HDInsight


	Questo comando aggiunge librerie .NET e riferimenti ad esse nel progetto corrente di Visual Studio.

7. In Esplora soluzioni fare doppio clic su **Program.cs** per aprirlo.

8. Aggiungere le istruzioni using seguenti all'inizio del file:

		using System.IO;
		using System.Threading;
		using System.Security.Cryptography.X509Certificates;

		using Microsoft.WindowsAzure.Management.HDInsight;
		using Microsoft.Hadoop.Client;

9. Aggiungere la definizione di funzione seguente alla classe. Questa funzione viene usata per attendere il completamento di un processo Hadoop.

        private static void WaitForJobCompletion(JobCreationResults jobResults, IJobSubmissionClient client)
        {
            JobDetails jobInProgress = client.GetJob(jobResults.JobId);
            while (jobInProgress.StatusCode != JobStatusCode.Completed && jobInProgress.StatusCode != JobStatusCode.Failed)
            {
                jobInProgress = client.GetJob(jobInProgress.JobId);
                Thread.Sleep(TimeSpan.FromSeconds(10));
            }
        }
	
10. Nella funzione Main() copiare e incollare il codice seguente:
		
		// Set the variables
		string subscriptionID = "<Azure subscription ID>";
		string clusterName = "<HDInsight cluster name>";
		string certFriendlyName = "<certificate friendly name>";		
		
	
	Queste sono tutte le variabili che è necessario impostare per il programma. È possibile ottenere l'ID della sottoscrizione di Azure dall'amministratore di sistema. 

	Per informazioni sul certificato, vedere [Creare e caricare un certificato di gestione per Azure][azure-certificate]. Un modo semplice per configurare il certificato consiste nell'eseguire i cmdlet di PowerShell *Get-AzurePublishSettingsFile* e *Import-AzurePublishSettingsFile* . che creeranno e gestiranno automaticamente il certificato di gestione. Dopo aver eseguito i cmdlet di PowerShell, è possibile aprire *certmgr.msc* dalla workstation e individuare il certificato espandendo *Personale/Certificati*. Il certificato creato dai cmdlet di PowerShell conterrà l'indicazione *Strumenti di Azure* in entrambi in campi *Rilasciato a* e *Rilasciato da* .
	
11. Nella funzione Main() aggiungere il codice seguente per definire il processo Hive:

        // define the Hive job
        HiveJobCreateParameters hiveJobDefinition = new HiveJobCreateParameters()
        {
            JobName = "show tables job",
            StatusFolder = "/ShowTableStatusFolder",
            Query = "show tables;"
        };

	È inoltre possibile usare il parametro File per specificare un file di script HiveQL in HDFS. Ad esempio

        // define the Hive job
        HiveJobCreateParameters hiveJobDefinition = new HiveJobCreateParameters()
        {
            JobName = "show tables job",
            StatusFolder = "/ShowTableStatusFolder",
            File = "/user/admin/showtables.hql"
        };

		
12. Nella funzione Main() aggiungere il codice seguente per creare un oggetto JobSubmissionCertificateCredential:
	
        // Get the certificate object from certificate store using the friendly name to identify it
        X509Store store = new X509Store();
        store.Open(OpenFlags.ReadOnly);
        X509Certificate2 cert = store.Certificates.Cast<X509Certificate2>().First(item => item.FriendlyName == certFriendlyName);
        JobSubmissionCertificateCredential creds = new JobSubmissionCertificateCredential(new Guid(subscriptionID), cert, clusterName);
		
13. Nella funzione Main() aggiungere il codice seguente per eseguire il processo e attenderne il completamento:

        // Submit the Hive job
        var jobClient = JobSubmissionClientFactory.Connect(creds);
        JobCreationResults jobResults = jobClient.CreateHiveJob(hiveJobDefinition);

        // Wait for the job to complete
        WaitForJobCompletion(jobResults, jobClient);
		
14. Nella funzione Main() aggiungere il codice seguente per stampare l'output del processo Hive:

        // Print the Hive job output
        System.IO.Stream stream = jobClient.GetJobOutput(jobResults.JobId);

        StreamReader reader = new StreamReader(stream);
        Console.WriteLine(reader.ReadToEnd());

        Console.WriteLine("Press ENTER to continue.");
        Console.ReadLine();

**Per eseguire l'applicazione**

Mentre l'applicazione è aperta in Visual Studio, premere **F5** per eseguirla. Verrà aperta una finestra della console in cui è visualizzato lo stato dell'applicazione. L'output sarà il seguente:

	hivesampletable




##<a id="nextsteps"></a> Passaggi successivi
In questo articolo si sono appresi vari modi per eseguire il provisioning di un cluster HDInsight. Per altre informazioni, vedere gli articoli seguenti:

* [Introduzione ad Azure HDInsight][hdinsight-get-started]
* [Effettuare il provisioning di cluster HDInsight][hdinsight-provision]
* [Amministrare HDInsight tramite PowerShell][hdinsight-admin-powershell]
* [Documentazione di riferimento relativa ai cmdlet di HDInsight][hdinsight-powershell-reference]
* [Usare Hive con HDInsight][hdinsight-use-hive]
* [Usare Pig con HDInsight][hdinsight-use-pig]


[azure-certificate]: http://msdn.microsoft.com/it-it/library/windowsazure/gg551722.aspx
[azure-management-portal]: http://manage.windowsazure.com/

[hdinsight-use-sqoop]: ../hdinsight-use-sqoop/
[hdinsight-provision]: ../hdinsight-provision-clusters/
[hdinsight-use-mapreduce]: ../hdinsight-use-mapreduce/
[hdinsight-use-hive]: ../hdinsight-use-hive/
[hdinsight-use-pig]: ../hdinsight-use-pig/
[hdinsight-get-started]: ../hdinsight-get-started/
[hdinsight-storage]: ../hdinsight-use-blob-storage/
[hdinsight-admin-powershell]: ../hdinsight-administer-use-powershell/
[hdinsight-develop-streaming-jobs]: ../hdinsight-hadoop-develop-deploy-streaming-jobs/

[hdinsight-powershell-reference]: http://msdn.microsoft.com/it-it/library/windowsazure/dn479228.aspx

[Powershell-install-configure]: ../install-configure-powershell/

[image-hdi-gettingstarted-runmrjob]: ./media/hdinsight-submit-hadoop-jobs-programmatically/HDI.GettingStarted.RunMRJob.png 
[image-hdi-gettingstarted-mrjoboutput]: ./media/hdinsight-submit-hadoop-jobs-programmatically/HDI.GettingStarted.MRJobOutput.png

[apache-hive]: http://hive.apache.org/

<!--HONumber=35.1-->
