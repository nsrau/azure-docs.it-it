<properties
   pageTitle="Usare Pig di Hadoop con .NET in HDInsight | Microsoft Azure"
   description="Informazioni su come usare .NET SDK per Hadoop per inviare processi Pig a Hadoop in HDInsight."
   services="hdinsight"
   documentationCenter=".net"
   authors="Blackmist"
   manager="paulettm"
   editor="cgronlun"/>

<tags
   ms.service="hdinsight"
   ms.devlang="dotnet"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="big-data"
   ms.date="07/06/2015"
   ms.author="larryfr"/>

#Esecuzione di processi Pig con .NET SDK per Hadoop in HDInsight

[AZURE.INCLUDE [pig-selector](../../includes/hdinsight-selector-use-pig.md)]

Questo documento fornisce un esempio d'uso di .NET SDK per Hadoop per inviare i processi Pig a un cluster Hadoop in HDInsight.

HDInsight .NET SDK fornisce librerie client .NET che semplificano l'utilizzo dei cluster HDInsight da .NET. Pig consente di creare operazioni MapReduce modellando una serie di trasformazioni di dati. In questo articolo si apprenderà come usare un'applicazione di base C# per inviare un processo Pig a un cluster HDInsight.

##<a id="prereq"></a>Prerequisiti

Per seguire la procedura descritta in questo articolo, è necessario quanto segue:

* Un cluster Azure HDInsight (Hadoop in HDInsight) (basato su Windows o su Linux)

* Visual Studio 2012 o 2013

##<a id="certificate"></a>Creazione di un certificato di gestione

Per autenticare l'applicazione in Azure HDInsight, è necessario creare un certificato autofirmato, installarlo nella workstation e caricarlo nella sottoscrizione Azure.

Per istruzioni su come eseguire questa operazione, vedere [Creare un certificato autofirmato](http://go.microsoft.com/fwlink/?LinkId=511138).

> [AZURE.NOTE]Quando si crea il certificato, assicurarsi di prendere nota del nome descrittivo usato, in quanto sarà utile in un secondo momento.

##<a id="subscriptionid"></a>Trovare l'ID sottoscrizione

Ogni sottoscrizione Azure è identificata da un valore GUID, noto come ID sottoscrizione. Per trovare questo valore, seguire questa procedura.

1. Visitare la pagina relativa alla [Console di gestione di Azure](https://manage.windowsazure.com/).

2. Dalla barra sinistra del portale, selezionare **Impostazioni**.

3. Nelle informazioni presentate nella parte destra della pagina trovare la sottoscrizione che si desidera usare e prendere nota del valore nella colonna **ID sottoscrizione**.

Salvare l'ID sottoscrizione, che verrà usato in un secondo momento.

##<a id="create"></a>Creare l'applicazione

1. Aprire Visual Studio 2012 o 2013.

2. Scegliere **Nuovo** dal menu **File**, quindi fare clic su **Progetto**.

3. Nella finestra di dialogo Nuovo progetto digitare o selezionare i seguenti valori:

	<table>
<tr>
<th>Proprietà</th>
<th>Valore</th>
</tr>
<tr>
<th>Categoria</th>
<th>Templates/Visual C#/Windows</th>
</tr>
<tr>
<th>Modello</th>
<th>Applicazione console</th>
</tr>
<tr>
<th>Nome</th>
<th>SubmitPigJob</th>
</tr>
</table>

4. Fare clic su **OK** per creare il progetto.

5. Selezionare **Gestione pacchetti libreria** o **Gestione pacchetti NuGet** dal menu **Strumenti** e quindi scegliere **Console di Gestione pacchetti**.

6. Usare il seguente comando nella console per installare i pacchetti di .NET SDK.

		Install-Package Microsoft.Windowsazure.Management.HDInsight

7. In Esplora soluzioni fare doppio clic su **Program.cs** per aprirlo. Replace Sostituire il codice esistente con il seguente.

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

		namespace SubmitPigJob
		{
		    class Program
		    {
		        static void Main(string[] args)
		        {
		            // Get the subscription ID
		            string subscriptionID = PromptForInput("Enter your Azure Subscription ID:");

		            // Get the certificate name
		            string certFriendlyName = PromptForInput("Enter the management certificate name:");

		            // Get the cluster name
		            string clusterName = PromptForInput("Enter the HDInsight cluster name:");

		            // Set the folder that job status is written to
		            string statusFolderName = @"/tutorials/usepig/status";

		            // The Pig Latin statements to run
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
		            JobCreationResults mrJobResults = jobClient.CreatePigJob		(myJobDefinition);

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

		        private static string PromptForInput(string message)
		        {
		            Console.WriteLine(message);
		            return Console.ReadLine();
		        }
		    }
		}


7. Salvare il file.

##<a id="run"></a>Eseguire l'applicazione

Usare **F5** per avviare l'applicazione. Quando richiesto, immettere i valori relativi a **ID sottoscrizione**, **nome descrittivo del certificato** e **nome del cluster HDInsight**. L'applicazione genererà diverse righe di informazioni durante l'esecuzione, terminando con qualcosa di simile al seguente.

```
----- The Pig job output log.
(TRACE,816)
(DEBUG,434)
(INFO,96)
(WARN,11)
(ERROR,6)
(FATAL,2)

----- Press ENTER to continue.
```

Premere **INVIO** per uscire dall'applicazione.

##<a id="summary"></a>Riepilogo

Come si può notare, .NET SDK per Hadoop consente di creare applicazioni .NET che inviano processi Pig a un cluster HDInsight, monitorare lo stato del processo e recuperare l'output.

##<a id="nextsteps"></a>Passaggi successivi

Per informazioni generali su Pig in HDInsight.

* [Usare Pig con Hadoop in HDInsight](hdinsight-use-pig.md)

Per informazioni su altre modalità d'uso di Hadoop in HDInsight.

* [Usare Hive con Hadoop in HDInsight](hdinsight-use-hive.md)

* [Usare MapReduce con Hadoop in HDInsight](hdinsight-use-mapreduce.md)

<!---HONumber=July15_HO2-->