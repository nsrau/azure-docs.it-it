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
   ms.date="02/04/2016"
   ms.author="larryfr"/>

#Esecuzione di processi Pig con .NET SDK per Hadoop in HDInsight

[AZURE.INCLUDE [pig-selector](../../includes/hdinsight-selector-use-pig.md)]

Questo documento fornisce un esempio d'uso di .NET SDK per Hadoop per inviare i processi Pig a un cluster Hadoop in HDInsight.

HDInsight .NET SDK fornisce librerie client .NET che semplificano l'utilizzo dei cluster HDInsight da .NET. Pig consente di creare operazioni MapReduce modellando una serie di trasformazioni di dati. In questo articolo si apprenderà come usare un'applicazione di base C# per inviare un processo Pig a un cluster HDInsight.

[AZURE.INCLUDE [azure-preview-portal](../../includes/hdinsight-azure-portal.md)]

* [Esecuzione di processi Pig con .NET SDK per Hadoop in HDInsight](hdinsight-hadoop-use-pig-dotnet-sdk.md)

##<a id="prereq"></a>Prerequisiti

Per seguire la procedura descritta in questo articolo, è necessario quanto segue:

* Un cluster Azure HDInsight (Hadoop in HDInsight) (basato su Windows o su Linux)

* Visual Studio 2012 o 2013

##<a id="certificate"></a>Creazione di un certificato di gestione

Per autenticare l'applicazione in Azure HDInsight, è necessario creare un certificato autofirmato, installarlo nella workstation e caricarlo nella sottoscrizione Azure.

Per istruzioni su come eseguire questa operazione, vedere [Creare un certificato autofirmato](http://go.microsoft.com/fwlink/?LinkId=511138).

> [AZURE.NOTE] Quando si crea il certificato, assicurarsi di prendere nota del nome descrittivo usato, in quanto sarà utile in un secondo momento.

##<a id="subscriptionid"></a>Trovare l'ID sottoscrizione

Ogni sottoscrizione Azure è identificata da un valore GUID, noto come ID sottoscrizione. Per trovare questo valore, seguire questa procedura.

1. Visitare la pagina relativa alla [Console di gestione di Azure](https://manage.windowsazure.com/).

2. Dalla barra sinistra del portale, selezionare **Impostazioni**.

3. Nelle informazioni presentate nella parte destra della pagina trovare la sottoscrizione che si desidera usare e prendere nota del valore nella colonna **ID sottoscrizione**.

Salvare l'ID sottoscrizione, che verrà usato in un secondo momento.

##<a id="create"></a>Creare l'applicazione

1. Aprire Visual Studio 2012 o 2013 o 2015.

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

		Install-Package Microsoft.Azure.Management.HDInsight.Job -Pre

7. In Esplora soluzioni fare doppio clic su **Program.cs** per aprirlo. Replace Sostituire il codice esistente con il seguente.

		using System;
		using Microsoft.Azure.Management.HDInsight.Job;
		using Microsoft.Azure.Management.HDInsight.Job.Models;
		using Hyak.Common;
		
		namespace HDInsightSubmitPigJobsDotNet
		{
		    class Program
		    {
		        static void Main(string[] args)
		        {
					var ExistingClusterName = "<HDInsightClusterName>";
					var ExistingClusterUri = ExistingClusterName + ".azurehdinsight.net";
					var ExistingClusterUsername = "<HDInsightClusterHttpUsername>";
					var ExistingClusterPassword = "<HDInsightClusterHttpUserPassword>";
		
		            // The Pig Latin statements to run
		            string queryString = "LOGS = LOAD 'wasb:///example/data/sample.log';" +
		                "LEVELS = foreach LOGS generate REGEX_EXTRACT($0, '(TRACE|DEBUG|INFO|WARN|ERROR|FATAL)', 1)  as LOGLEVEL;" +
		                "FILTEREDLEVELS = FILTER LEVELS by LOGLEVEL is not null;" +
		                "GROUPEDLEVELS = GROUP FILTEREDLEVELS by LOGLEVEL;" +
		                "FREQUENCIES = foreach GROUPEDLEVELS generate group as LOGLEVEL, COUNT(FILTEREDLEVELS.LOGLEVEL) as COUNT;" +
		                "RESULT = order FREQUENCIES by COUNT desc;" +
		                "DUMP RESULT;";
		
		
		            HDInsightJobManagementClient _hdiJobManagementClient;
		            var clusterCredentials = new BasicAuthenticationCloudCredentials { Username = ExistingClusterUsername, Password = ExistingClusterPassword };
		            _hdiJobManagementClient = new HDInsightJobManagementClient(ExistingClusterUri, clusterCredentials);
		
		            // Define the Pig job
		            var parameters = new PigJobSubmissionParameters()
		            {
		                Query = queryString,
		            };
		
		            System.Console.WriteLine("Submitting the Pig job to the cluster...");
		            var response = _hdiJobManagementClient.JobManagement.SubmitPigJob(parameters);
		            System.Console.WriteLine("Validating that the response is as expected...");
		            System.Console.WriteLine("Response status code is " + response.StatusCode);
		            System.Console.WriteLine("Validating the response object...");
		            System.Console.WriteLine("JobId is " + response.JobSubmissionJsonResponse.Id);
		            Console.WriteLine("Press ENTER to continue ...");
		            Console.ReadLine();
		        }
		    }
		}

7. Premere **F5** per avviare l'applicazione.
8. Premere **INVIO** per uscire dall'applicazione.

##<a id="summary"></a>Riepilogo

Come si può notare, .NET SDK per Hadoop consente di creare applicazioni .NET che inviano processi Pig a un cluster HDInsight, monitorare lo stato del processo e recuperare l'output.

##<a id="nextsteps"></a>Passaggi successivi

Per informazioni generali su Pig in HDInsight.

* [Usare Pig con Hadoop in HDInsight](hdinsight-use-pig.md)

Per informazioni su altre modalità d'uso di Hadoop in HDInsight.

* [Usare Hive con Hadoop in HDInsight](hdinsight-use-hive.md)

* [Usare MapReduce con Hadoop in HDInsight](hdinsight-use-mapreduce.md)

<!---HONumber=AcomDC_0323_2016-->