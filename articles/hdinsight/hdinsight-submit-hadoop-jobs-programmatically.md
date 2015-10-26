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
	ms.date="10/02/2015"
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

* **Un cluster HDInsight di Azure**. Per istruzioni, vedere [Introduzione all'uso di HDInsight][hdinsight-get-started] o [Creare cluster Hadoop in HDInsight][hdinsight-provision].
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

	Il nome dell'account di archiviazione di Azure è quello specificato durante la creazione del cluster HDInsight. L'account di archiviazione viene usato per l'hosting del contenitore BLOB usato come file system predefinito per il cluster HDInsight. Il nome del contenitore corrisponde in genere al nome del cluster HDInsight, a meno che non venga specificato un nome diverso durante la creazione del cluster.

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
HDInsight .NET SDK fornisce librerie client .NET che semplificano l'uso dei cluster HDInsight da .NET. I cluster HDInsight includono un file con estensione jar, situato nella cartella *\\example\\jars\\hadoop-mapreduce-examples.jar*, che contiene numerosi esempi di MapReduce. Uno degli esempi è relativo al conteggio della frequenza delle parole nei file di origine. In questa sessione si apprenderà come creare un'applicazione .NET per eseguire l'esempio relativo al conteggio di parole. Per altre informazioni sullo sviluppo e l'esecuzione di processi MapReduce, vedere [Usare Hadoop MapReduce in HDInsight][hdinsight-use-mapreduce].

**Per inoltrare il processo MapReduce di conteggio parole**

1. Creare un'applicazione console C# in Visual Studio.
2. Eseguire il comando seguente dalla console di Gestione pacchetti NuGet.

		Install-Package Microsoft.Azure.Management.HDInsight.Job -Pre

2. Nel file Program.cs usare le istruzioni using seguenti:

		using System;
		using System.Collections.Generic;
		using Microsoft.Azure.Management.HDInsight.Job;
		using Microsoft.Azure.Management.HDInsight.Job.Models;
		using Hyak.Common;

3. Nella funzione Main() aggiungere il codice seguente.

		var ExistingClusterName = "<HDInsightClusterName>";
		var ExistingClusterUri = ExistingClusterName + ".azurehdinsight.net";
		var ExistingClusterUsername = "<HDInsightClusterHttpUsername>";
		var ExistingClusterPassword = "<HDInsightClusterHttpUserPassword>";
	    HDInsightJobManagementClient _hdiJobManagementClient;

	    List<string> arguments = new List<string> { { "wasb:///example/data/gutenberg/davinci.txt" }, { "wasb:///example/data/WordCountOutput" } };
	
	    var clusterCredentials = new BasicAuthenticationCloudCredentials { Username = ExistingClusterUsername, Password = ExistingClusterPassword };
	    _hdiJobManagementClient = new HDInsightJobManagementClient(ExistingClusterUri, clusterCredentials);
	
	    var parameters = new MapReduceJobSubmissionParameters
	    {
	        UserName = ExistingClusterUsername,
	        JarFile = "wasb:///example/jars/hadoop-mapreduce-examples.jar",
	        JarClass = "wordcount",
	        Arguments = ConvertArgsToString(arguments)
	    };
	
	    System.Console.WriteLine("Submitting the MapReduce job to the cluster...");
	    var response = _hdiJobManagementClient.JobManagement.SubmitMapReduceJob(parameters);
	    System.Console.WriteLine("Validating that the response is as expected...");
	    System.Console.WriteLine("Response status code is " + response.StatusCode);
	    System.Console.WriteLine("Validating the response object...");
	    System.Console.WriteLine("JobId is " + response.JobSubmissionJsonResponse.Id);
	    Console.WriteLine("Press ENTER to continue ...");
	    Console.ReadLine();

4. Aggiungere alla classe la funzione helper seguente.

        private static string ConvertArgsToString(List<string> args)
        {
            if (args.Count == 0)
            {
                return null;
            }

            return string.Join("&arg=", args.ToArray());
        }

5. Premere **F5** per eseguire l'applicazione.










##Inviare processi di flusso MapReduce Hadoop tramite HDInsight .NET SDK
I cluster HDInsight includono un programma di flussi Hadoop per il conteggio di parole sviluppato in C#. Il programma per il mapping è */example/apps/cat.exe* e il programma per la riduzione è */example/apps/wc.exe*. In questa sessione si apprenderà come creare un'applicazione .NET per eseguire l'esempio relativo al conteggio di parole.

Per informazioni dettagliate sulla creazione di un'applicazione .NET per l'invio di processi MapReduce, vedere [Inviare processi MapReduce tramite HDInsight .NET SDK](#mapreduce-sdk).

Per altre informazioni sullo sviluppo e sulla distribuzione di processi di flusso Hadoop, vedere [Sviluppare programmi per la creazione di flussi Hadoop in C# per HDInsight][hdinsight-develop-streaming-jobs].

La procedura seguente funziona solo nei cluster HDInsight in Windows. Lo streaming C# non è ancora supportato nei cluster Linux. Tuttavia, è possibile usare il programma .NET per inoltrare il processo di flusso scritto in altri linguaggi di programmazione supportati dai cluster Linux, ad esempio Python. Per un esempio di streaming Python, vedere [Sviluppare programmi di streaming Python per HDInsight](hdinsight-hadoop-streaming-python.md).

**Per inoltrare il processo MapReduce di conteggio parole**

1. Dalla Console di gestione pacchetti di Visual Studio, eseguire il comando NuGet seguente per importare il pacchetto.

		Install-Package Microsoft.Azure.Management.HDInsight.Job -Pre

2. Nel file Program.cs usare le istruzioni using seguenti:

		using System;
		using System.Collections.Generic;
		using Microsoft.Azure.Management.HDInsight.Job;
		using Microsoft.Azure.Management.HDInsight.Job.Models;
		using Hyak.Common;

3. Nella funzione Main() aggiungere il codice seguente.

		var ExistingClusterName = "<HDInsightClusterName>";
		var ExistingClusterUri = ExistingClusterName + ".azurehdinsight.net";
		var ExistingClusterUsername = "<HDInsightClusterHttpUsername>";
		var ExistingClusterPassword = "<HDInsightClusterHttpUserPassword>";

        List<string> arguments = new List<string> { { "/example/apps/cat.exe" }, { "/example/apps/wc.exe" } };

        HDInsightJobManagementClient _hdiJobManagementClient;
        var clusterCredentials = new BasicAuthenticationCloudCredentials { Username = ExistingClusterUsername, Password = ExistingClusterPassword };
        _hdiJobManagementClient = new HDInsightJobManagementClient(ExistingClusterUri, clusterCredentials);

        var parameters = new MapReduceStreamingJobSubmissionParameters
        {
            UserName = ExistingClusterUsername,
            File = ConvertArgsToString(arguments),
            Mapper = "cat.exe",
            Reducer = "wc.exe",
            Input = "/example/data/gutenberg/davinci.txt",
            Output = "/tutorials/wordcountstreaming/output"
        };

        System.Console.WriteLine("Submitting the MapReduce job to the cluster...");
        var response = _hdiJobManagementClient.JobManagement.SubmitMapReduceStreamingJob(parameters);
        System.Console.WriteLine("Validating that the response is as expected...");
        System.Console.WriteLine("Response status code is " + response.StatusCode);
        System.Console.WriteLine("Validating the response object...");
        System.Console.WriteLine("JobId is " + response.JobSubmissionJsonResponse.Id);
        Console.WriteLine("Press ENTER to continue ...");
        Console.ReadLine();

4. Aggiungere una funzione help alla classe.

        private static string ConvertArgsToString(List<string> args)
        {
            if (args.Count == 0)
            {
                return null;
            }

            return string.Join("&arg=", args.ToArray());
        }

5. Premere **F5** per eseguire l'applicazione.

##Inviare processi Hive mediante HDInsight .NET SDK
I cluster HDInsight includono una tabella Hive di esempio denominata *hivesampletable*. In questa sessione si creerà un'applicazione .NET per eseguire un processo Hive che elenca le tabelle Hive create in un cluster HDInsight. Per altre informazioni sull'uso di Hive, vedere [Usare Hive con Hadoop in HDInsight][hdinsight-use-hive].

Le procedure seguenti sono necessarie per creare un cluster HDInsight usando l'SDK:

- Installare HDInsight .NET SDK
- Creare un'applicazione console
- Eseguire l'applicazione


**Per installare HDInsight .NET SDK** È possibile installare l'ultima build pubblicata dell'SDK da [NuGet](http://nuget.codeplex.com/wikipage?title=Getting%20Started). Le istruzioni verranno illustrate nella procedura successiva.

**Per creare un'applicazione console di Visual Studio**

1. Aprire Visual Studio 2013 o 2015.

2. Creare un nuovo progetto con le impostazioni seguenti:

	|Proprietà|Valore|
	|--------|-----|
	|Modello|Templates/Visual C#/Windows/Console Application|
	|Nome|SubmitHiveJob|

3. Dal menu **Strumenti** fare clic su **Gestione pacchetti NuGet**, quindi su **Console di Gestione pacchetti**.
4. Eseguire il comando seguente nella console per installare i pacchetti:

		Install-Package Microsoft.Azure.Common.Authentication -pre
		Install-Package Microsoft.Azure.Management.HDInsight -Pre
		Install-Package Microsoft.Azure.Management.HDInsight.Job -Pre

	Questi comandi aggiungono librerie .NET e riferimenti ad esse nel progetto corrente di Visual Studio.

5. Da Esplora soluzioni fare doppio clic su **Program.cs** per aprirlo, incollare il codice seguente e indicare i valori per le variabili:

		using System.Collections.Generic;
		using System.Linq;
		using Microsoft.Azure.Management.HDInsight.Job;
		using Microsoft.Azure.Management.HDInsight.Job.Models;
		using Hyak.Common;
		
		namespace SubmitHiveJob
		{
		    class Program
		    {
		        private static HDInsightJobManagementClient _hdiJobManagementClient;
		
		        private const string ExistingClusterName = "<HDINSIGHT CLUSTER NAME>";
		        private const string ExistingClusterUri = ExistingClusterName + ".azurehdinsight.net";
		
		        private const string ExistingClusterUsername = "<HDINSIGHT HTTP USER NAME>";  //The default name is admin.
		        private const string ExistingClusterPassword = "<HDINSIGHT HTTP USER PASSWORD>";
		
		        private static void Main(string[] args)
		        {
		
		            var clusterCredentials = new BasicAuthenticationCloudCredentials { Username = ExistingClusterUsername, Password = ExistingClusterPassword };
		            _hdiJobManagementClient = new HDInsightJobManagementClient(ExistingClusterUri, clusterCredentials);
		
		            SubmitHiveJob();
		        }
		
		        private static void SubmitHiveJob()
		        {
		            Dictionary<string, string> defines = new Dictionary<string, string> { { "hive.execution.engine", "ravi" }, { "hive.exec.reducers.max", "1" } };
		            List<string> args = new List<string> { { "argA" }, { "argB" } };
		            var parameters = new HiveJobSubmissionParameters
		            {
		                UserName = ExistingClusterUsername,
		                Query = "SHOW TABLES",
		                Defines = ConvertDefinesToString(defines),
		                Arguments = ConvertArgsToString(args)
		            };
		
		            System.Console.WriteLine("Submitting the Hive job to the cluster...");
		            var response = _hdiJobManagementClient.JobManagement.SubmitHiveJob(parameters);
		            System.Console.WriteLine("Validating that the response is as expected...");
		            System.Console.WriteLine("Response status code is " + response.StatusCode);
		            System.Console.WriteLine("Validating the response object...");
		            System.Console.WriteLine("JobId is " + response.JobSubmissionJsonResponse.Id);
		            System.Console.WriteLine("Press ENTER to continue ...");
		            System.Console.ReadLine();
		        }
		
		        private static string ConvertDefinesToString(Dictionary<string, string> defines)
		        {
		            if (defines.Count == 0)
		            {
		                return null;
		            }
		
		            return string.Join("&define=", defines.Select(x => x.Key + "%3D" + x.Value).ToArray());
		        }
		        private static string ConvertArgsToString(List<string> args)
		        {
		            if (args.Count == 0)
		            {
		                return null;
		            }
		
		            return string.Join("&arg=", args.ToArray());
		        }
		    }
		}

6. Premere **F5** per eseguire l'applicazione.

##Invio di processi tramite gli strumenti di HDInsight per Visual Studio

Usando gli strumenti di HDInsight per Visual Studio, è possibile eseguire query Hive e Pig script. Vedere [Introduzione all'uso di Hadoop Tools per Visual Studio per HDInsight](hdinsight-hadoop-visual-studio-tools-get-started.md).


##Passaggi successivi
Questo articolo ha spiegato vari modi per creare un cluster HDInsight. Per altre informazioni, vedere gli articoli seguenti:

* [Introduzione all'uso di Azure HDInsight][hdinsight-get-started]
* [Creare cluster Hadoop in HDInsight][hdinsight-provision]
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

<!---HONumber=Oct15_HO3-->