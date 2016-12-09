---
title: Eseguire query Hive con HDInsight .NET SDK | Documentazione Microsoft
description: Informazioni su come inviare processi Hadoop ad Azure HDInsight Hadoop con HDInsight .NET SDK.
editor: cgronlun
manager: jhubbard
services: hdinsight
documentationcenter: 
tags: azure-portal
author: mumian
ms.assetid: 4e291890-f8b4-426c-b5e8-d4fd512ff042
ms.service: hdinsight
ms.workload: big-data
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 09/14/2016
ms.author: jgao
translationtype: Human Translation
ms.sourcegitcommit: 9cf1faabe3ea12af0ee5fd8a825975e30947b03a
ms.openlocfilehash: 2168147972184e14705781765b9afb3f2ee902e3


---
# <a name="run-hive-queries-using-hdinsight-net-sdk"></a>Eseguire query Hive con HDInsight .NET SDK
[!INCLUDE [hive-selector](../../includes/hdinsight-selector-use-hive.md)]

Informazioni su come inviare query Hive tramite HDInsight .NET SDK.

> [!NOTE]
> I passaggi descritti in questo articolo devono essere eseguiti da un client Windows. Per informazioni sull'uso di un client Linux, OS X o Unix con Hive, usare il selettore di schede visualizzato all'inizio dell'articolo.
> 
> 

## <a name="prerequisites"></a>Prerequisiti
Per eseguire le procedure descritte nell'articolo è necessario:

* **Un cluster Hadoop in HDInsight**. Vedere [Introduzione all'uso di Hadoop basato su Linux in HDInsight](hdinsight-use-sqoop.md#create-cluster-and-sql-database).
* **Visual Studio 2012/2013/2015**.

## <a name="submit-hive-queries-using-hdinsight-net-sdk"></a>Inviare query Hive con HDInsight .NET SDK
HDInsight .NET SDK fornisce librerie client .NET che semplificano l'uso dei cluster HDInsight da .NET. 

**Inviare i processi**

1. Creare un'applicazione console C# in Visual Studio.
2. Eseguire il comando seguente dalla console di Gestione pacchetti NuGet.
   
        Install-Package Microsoft.Azure.Management.HDInsight.Job
3. Usare il codice seguente:

    ```csharp
        using System.Collections.Generic;
        using System.IO;
        using System.Text;
        using System.Threading;
        using Microsoft.Azure.Management.HDInsight.Job;
        using Microsoft.Azure.Management.HDInsight.Job.Models;
        using Hyak.Common;
   
        namespace SubmitHDInsightJobDotNet
        {
            class Program
            {
                private static HDInsightJobManagementClient _hdiJobManagementClient;
   
                private const string ExistingClusterName = "<Your HDInsight Cluster Name>";
                private const string ExistingClusterUri = ExistingClusterName + ".azurehdinsight.net";
                private const string ExistingClusterUsername = "<Cluster Username>";
                private const string ExistingClusterPassword = "<Cluster User Password>";
   
                private const string DefaultStorageAccountName = "<Default Storage Account Name>";
                private const string DefaultStorageAccountKey = "<Default Storage Account Key>";
                private const string DefaultStorageContainerName = "<Default Blob Container Name>";
   
                static void Main(string[] args)
                {
                    System.Console.WriteLine("The application is running ...");
   
                    var clusterCredentials = new BasicAuthenticationCloudCredentials { Username = ExistingClusterUsername, Password = ExistingClusterPassword };
                    _hdiJobManagementClient = new HDInsightJobManagementClient(ExistingClusterUri, clusterCredentials);
   
                    SubmitHiveJob();
   
                    System.Console.WriteLine("Press ENTER to continue ...");
                    System.Console.ReadLine();
                }
   
                private static void SubmitHiveJob()
                {
                    Dictionary<string, string> defines = new Dictionary<string, string> { { "hive.execution.engine", "ravi" }, { "hive.exec.reducers.max", "1" } };
                    List<string> args = new List<string> { { "argA" }, { "argB" } };
                    var parameters = new HiveJobSubmissionParameters
                    {
                        Query = "SHOW TABLES",
                        Defines = defines,
                        Arguments = args
                    };
   
                    System.Console.WriteLine("Submitting the Hive job to the cluster...");
                    var jobResponse = _hdiJobManagementClient.JobManagement.SubmitHiveJob(parameters);
                    var jobId = jobResponse.JobSubmissionJsonResponse.Id;
                    System.Console.WriteLine("Response status code is " + jobResponse.StatusCode);
                    System.Console.WriteLine("JobId is " + jobId);
   
                    System.Console.WriteLine("Waiting for the job completion ...");
   
                    // Wait for job completion
                    var jobDetail = _hdiJobManagementClient.JobManagement.GetJob(jobId).JobDetail;
                    while (!jobDetail.Status.JobComplete)
                    {
                        Thread.Sleep(1000);
                        jobDetail = _hdiJobManagementClient.JobManagement.GetJob(jobId).JobDetail;
                    }
   
                    // Get job output
                    var storageAccess = new AzureStorageAccess(DefaultStorageAccountName, DefaultStorageAccountKey,
                        DefaultStorageContainerName);
                    var output = (jobDetail.ExitValue == 0)
                        ? _hdiJobManagementClient.JobManagement.GetJobOutput(jobId, storageAccess) // fetch stdout output in case of success
                        : _hdiJobManagementClient.JobManagement.GetJobErrorLogs(jobId, storageAccess); // fetch stderr output in case of failure
   
                    System.Console.WriteLine("Job output is: ");
   
                    using (var reader = new StreamReader(output, Encoding.UTF8))
                    {
                        string value = reader.ReadToEnd();
                        System.Console.WriteLine(value);
                    }
                }
            }
        }
    ```
4. Premere **F5** per eseguire l'applicazione.

## <a name="next-steps"></a>Passaggi successivi
Questo articolo ha spiegato vari modi per creare un cluster HDInsight. Per altre informazioni, vedere gli articoli seguenti:

* [Introduzione ad Azure HDInsight][hdinsight-get-started]
* [Creare cluster Hadoop in HDInsight][hdinsight-provision]
* [Gestire cluster Hadoop in HDInsight tramite il portale di Azure](hdinsight-administer-use-management-portal.md)
* [Riferimento a HDInsight .NET SDK](https://msdn.microsoft.com/library/mt271028.aspx)
* [Usare Pig con HDInsight](hdinsight-use-pig.md)
* [Usare Sqoop con Hadoop in HDInsight](hdinsight-use-sqoop-mac-linux.md)
* [Creare applicazioni .NET HDInsight di autenticazione non interattive](hdinsight-create-non-interactive-authentication-dotnet-applications.md)

[hdinsight-provision]: hdinsight-provision-clusters.md
[hdinsight-get-started]: hdinsight-hadoop-linux-tutorial-get-started.md





<!--HONumber=Nov16_HO3-->


