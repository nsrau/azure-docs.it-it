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
	ms.date="10/30/2015"
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

- **Un cluster HDInsight di Azure**. Per istruzioni, vedere [Introduzione all'uso di HDInsight][hdinsight-get-started] o [Creare cluster Hadoop in HDInsight][hdinsight-provision].
- **Workstation con Azure PowerShell**. Vedere [Installare Azure PowerShell 1.0 e versioni successive](hdinsight-administer-use-powershell.md#install-azure-powershell-10-and-greater).

##Invio di processi MapReduce tramite PowerShell

Vedere [esempi di MapReduce di Hadoop in HDInsight basato su Windows](hdinsight-run-samples.md).

##Invio di processi Hive tramite PowerShell

Vedere [Esecuzione di query Hive tramite PowerShell](hdinsight-hadoop-use-hive-powershell.md)

## Inviare processi Hive mediante Visual Studio

Vedere [Introduzione all'uso di HDInsight Tools per Visual Studio][hdinsight-visual-studio-tools].

##Invio di processi Sqoop tramite PowerShell

Vedere [Usare Sqoop con Hadoop in HDInsight][hdinsight-use-sqoop].

##Invio di processi Hive/Pig/Sqoop tramite HDInsight .NET SDK
HDInsight .NET SDK fornisce librerie client .NET che semplificano l'uso dei cluster HDInsight da .NET.

**Inviare i processi**

1. Creare un'applicazione console C# in Visual Studio.
2. Eseguire il comando seguente dalla console di Gestione pacchetti NuGet.

		Install-Package Microsoft.Azure.Common.Authentication -pre
		Install-Package Microsoft.Azure.Management.HDInsight -Pre
		Install-Package Microsoft.Azure.Management.HDInsight.Job -Pre
2. Usare il codice seguente:

		using System;
		using System.Collections.Generic;
		using System.Linq;
		using System.Security;
		
		using Microsoft.Azure;
		using Microsoft.Azure.Common.Authentication;
		using Microsoft.Azure.Common.Authentication.Factories;
		using Microsoft.Azure.Common.Authentication.Models;
		using Microsoft.Azure.Management.HDInsight;
		using Microsoft.Azure.Management.HDInsight.Job;
		using Microsoft.Azure.Management.HDInsight.Job.Models;
		using Hyak.Common;
		
		namespace SubmitHDInsightJobDotNet
		{
			class Program
			{
				private static HDInsightManagementClient _hdiManagementClient;
				private static HDInsightJobManagementClient _hdiJobManagementClient;
		
				private static Guid SubscriptionId = new Guid("<Your Subscription ID>");
				private const string ResourceGroupName = "<Your Resource Group Name>";
		
				private const string ExistingClusterName = "<Your HDInsight Cluster Name>";
				private const string ExistingClusterUri = ExistingClusterName + ".azurehdinsight.net";
				private const string ExistingClusterUsername = "admin";
				private const string ExistingClusterPassword = "**********";
		
				static void Main(string[] args)
				{
					System.Console.WriteLine("Running");
		
					var tokenCreds = GetTokenCloudCredentials();
					var subCloudCredentials = GetSubscriptionCloudCredentials(tokenCreds, SubscriptionId);
		
					_hdiManagementClient = new HDInsightManagementClient(subCloudCredentials);
		
					var clusterCredentials = new BasicAuthenticationCloudCredentials { Username = ExistingClusterUsername, Password = ExistingClusterPassword };
					_hdiJobManagementClient = new HDInsightJobManagementClient(ExistingClusterUri, clusterCredentials);
		
					SubmitHiveJob();
					SubmitPigJob();
					SubmitSqoopJob();
				}
		
				public static TokenCloudCredentials GetTokenCloudCredentials(string username = null, SecureString password = null)
				{
					var authFactory = new AuthenticationFactory();
		
					var account = new AzureAccount { Type = AzureAccount.AccountType.User };
		
					if (username != null && password != null)
						account.Id = username;
		
					var env = AzureEnvironment.PublicEnvironments[EnvironmentName.AzureCloud];
		
					var accessToken =
						authFactory.Authenticate(account, env, AuthenticationFactory.CommonAdTenant, password, ShowDialog.Auto)
							.AccessToken;
		
					return new TokenCloudCredentials(accessToken);
				}
		
				public static SubscriptionCloudCredentials GetSubscriptionCloudCredentials(TokenCloudCredentials creds, Guid subId)
				{
					return new TokenCloudCredentials(subId.ToString(), creds.Token);
				}
		
				private static void SubmitPigJob()
				{
					var parameters = new PigJobSubmissionParameters
					{
						UserName = ExistingClusterUsername,
						Query = @"LOGS = LOAD 'wasb:///example/data/sample.log';
							LEVELS = foreach LOGS generate REGEX_EXTRACT($0, '(TRACE|DEBUG|INFO|WARN|ERROR|FATAL)', 1)  as LOGLEVEL;
							FILTEREDLEVELS = FILTER LEVELS by LOGLEVEL is not null;
							GROUPEDLEVELS = GROUP FILTEREDLEVELS by LOGLEVEL;
							FREQUENCIES = foreach GROUPEDLEVELS generate group as LOGLEVEL, COUNT(FILTEREDLEVELS.LOGLEVEL) as COUNT;
							RESULT = order FREQUENCIES by COUNT desc;
							DUMP RESULT;"
					};
		
					System.Console.WriteLine("Submitting the Pig job to the cluster...");
					var response = _hdiJobManagementClient.JobManagement.SubmitPigJob(parameters);
					System.Console.WriteLine("Validating that the response is as expected...");
					System.Console.WriteLine("Response status code is " + response.StatusCode);
					System.Console.WriteLine("Validating the response object...");
					System.Console.WriteLine("JobId is " + response.JobSubmissionJsonResponse.Id);
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
				}
		
				private static void SubmitSqoopJob()
				{
					var sqlDatabaseServerName = "<SQLDatabaseServerName>";
					var sqlDatabaseLogin = "<SQLDatabaseLogin>";
					var sqlDatabaseLoginPassword = "<SQLDatabaseLoginPassword>";
					var sqlDatabaseDatabaseName = "hdisqoop";
		
					var tableName = "log4jlogs";
					var exportDir = "/tutorials/usesqoop/data";
		
					// Connection string for using Azure SQL Database.
					// Comment if using SQL Server
					var connectionString = "jdbc:sqlserver://" + sqlDatabaseServerName + ".database.windows.net;user=" + sqlDatabaseLogin + "@" + sqlDatabaseServerName + ";password=" + sqlDatabaseLoginPassword + ";database=" + sqlDatabaseDatabaseName;
					// Connection string for using SQL Server.
					// Uncomment if using SQL Server
					//var connectionString = "jdbc:sqlserver://" + sqlDatabaseServerName + ";user=" + sqlDatabaseLogin + ";password=" + sqlDatabaseLoginPassword + ";database=" + sqlDatabaseDatabaseName;
		
					var parameters = new SqoopJobSubmissionParameters
					{
						UserName = ExistingClusterUsername,
						Command = "export --connect " + connectionString + " --table " + tableName + "_mobile --export-dir " + exportDir + "_mobile --fields-terminated-by \\t -m 1"
					};
		
					System.Console.WriteLine("Submitting the Sqoop job to the cluster...");
					var response = _hdiJobManagementClient.JobManagement.SubmitSqoopJob(parameters);
					System.Console.WriteLine("Validating that the response is as expected...");
					System.Console.WriteLine("Response status code is " + response.StatusCode);
					System.Console.WriteLine("Validating the response object...");
					System.Console.WriteLine("JobId is " + response.JobSubmissionJsonResponse.Id);
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

5. Premere **F5** per eseguire l'applicazione.

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

<!---HONumber=AcomDC_1203_2015-->