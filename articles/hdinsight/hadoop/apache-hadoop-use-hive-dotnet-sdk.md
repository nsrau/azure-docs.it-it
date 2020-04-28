---
title: Eseguire query Apache Hive con HDInsight .NET SDK - Azure
description: Informazioni su come inviare processi Apache Hadoop ad Azure HDInsight Apache Hadoop con HDInsight .NET SDK.
author: hrasheed-msft
ms.author: hrasheed
ms.reviewer: jasonh
ms.service: hdinsight
ms.topic: conceptual
ms.custom: hdinsightactive
ms.date: 12/24/2019
ms.openlocfilehash: a9d71c8aebb9cc4a0adbd461aead6e2612bd13bd
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 04/28/2020
ms.locfileid: "75552492"
---
# <a name="run-apache-hive-queries-using-hdinsight-net-sdk"></a>Eseguire query Apache Hive con HDInsight .NET SDK

[!INCLUDE [hive-selector](../../../includes/hdinsight-selector-use-hive.md)]

Informazioni su come inviare query Apache Hive tramite HDInsight .NET SDK. Si scrive un programma C# per inviare una query Hive per elencare le tabelle Hive e visualizzare i risultati.

> [!NOTE]  
> I passaggi descritti in questo articolo devono essere eseguiti da un client Windows. Per informazioni sull'uso di un client Linux, OS X o Unix con Hive, usare il selettore di schede visualizzato all'inizio dell'articolo.

## <a name="prerequisites"></a>Prerequisiti

Per eseguire le procedure descritte nell'articolo sono necessari gli elementi seguenti:

* Un cluster Apache Hadoop in HDInsight. Vedere [Introduzione all'uso di Hadoop basato su Linux in HDInsight](apache-hadoop-linux-tutorial-get-started.md).

    > [!IMPORTANT]  
    > A partire dal 15 settembre 2017, HDInsight .NET SDK supporta solo i risultati della query Hive in restituzione dagli account di Archiviazione di Microsoft Azure. Se si usa questo esempio con un cluster HDInsight che usa Azure Data Lake Store come archiviazione primaria, non è possibile recuperare i risultati della ricerca tramite .NET SDK.

* [Visual Studio](https://visualstudio.microsoft.com/vs/community/) 2013 e versioni successive. È necessario installare almeno **lo sviluppo desktop .NET** del carico di lavoro.

## <a name="run-a-hive-query"></a>Eseguire una query Hive

HDInsight .NET SDK fornisce librerie client .NET che semplificano l'uso dei cluster HDInsight da .NET.

1. Creare un'applicazione console C# in Visual Studio.

1. Dalla console di Gestione pacchetti NuGet eseguire il comando seguente:

        Install-Package Microsoft.Azure.Management.HDInsight.Job

1. Modificare il codice seguente per inizializzare i valori per le `ExistingClusterName, ExistingClusterUsername, ExistingClusterPassword,DefaultStorageAccountName,DefaultStorageAccountKey,DefaultStorageContainerName`variabili:. Usare quindi il codice modificato come intero contenuto di **Program.cs** in Visual Studio.

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
            private const string ExistingClusterUsername = "<Cluster Username>";
            private const string ExistingClusterPassword = "<Cluster User Password>";

            // Only Azure Storage accounts are supported by the SDK
            private const string DefaultStorageAccountName = "<Default Storage Account Name>";
            private const string DefaultStorageAccountKey = "<Default Storage Account Key>";
            private const string DefaultStorageContainerName = "<Default Blob Container Name>";

            private const string ExistingClusterUri = ExistingClusterName + ".azurehdinsight.net";

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
                Dictionary<string, string> defines = new Dictionary<string, string> { { "hive.execution.engine", "tez" }, { "hive.exec.reducers.max", "1" } };
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

1. Premere **F5** per eseguire l'applicazione.

L'output dell'applicazione dovrebbe essere simile al seguente:

![Output processo Hive Hadoop di HDInsight](./media/apache-hadoop-use-hive-dotnet-sdk/hdinsight-hadoop-use-hive-net-sdk-output.png)

## <a name="next-steps"></a>Passaggi successivi

In questo articolo si è appreso come inviare query di Apache Hive usando HDInsight .NET SDK. Per altre informazioni, vedere gli articoli seguenti:

* [Introduzione ad Azure HDInsight](apache-hadoop-linux-tutorial-get-started.md)
* [Creare cluster Apache Hadoop in HDInsight](../hdinsight-hadoop-provision-linux-clusters.md)
* [Riferimento a HDInsight .NET SDK](https://docs.microsoft.com/dotnet/api/overview/azure/hdinsight)
* [Usare Apache Sqoop con HDInsight](apache-hadoop-use-sqoop-mac-linux.md)
* [Creare applicazioni .NET HDInsight di autenticazione non interattive](../hdinsight-create-non-interactive-authentication-dotnet-applications.md)
