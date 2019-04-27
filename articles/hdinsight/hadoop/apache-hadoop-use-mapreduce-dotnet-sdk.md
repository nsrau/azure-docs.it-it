---
title: Inviare processi MapReduce mediante HDInsight .NET SDK - Azure
description: Informazioni su come inviare processi MapReduce ad Azure HDInsight Apache Hadoop con HDInsight .NET SDK.
ms.reviewer: jasonh
author: hrasheed-msft
ms.service: hdinsight
ms.custom: hdinsightactive
ms.topic: conceptual
ms.date: 05/16/2018
ms.author: hrasheed
ms.openlocfilehash: 1ac2dda20ba1219c9f62e834b5cd2cfba8a50086
ms.sourcegitcommit: 61c8de2e95011c094af18fdf679d5efe5069197b
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 04/23/2019
ms.locfileid: "62124700"
---
# <a name="run-mapreduce-jobs-using-hdinsight-net-sdk"></a>Eseguire processi MapReduce con HDInsight .NET SDK
[!INCLUDE [mapreduce-selector](../../../includes/hdinsight-selector-use-mapreduce.md)]

Informazioni su come inviare processi MapReduce con HDInsight .NET SDK. I cluster HDInsight includono un file JAR con alcuni esempi di MapReduce. Il file JAR è */example/jars/hadoop-mapreduce-examples.jar*.  Uno degli esempi è *wordcount*. Per inviare un processo wordcount, è necessario sviluppare un'applicazione console C#.  Il processo legge il file */example/data/gutenberg/davinci.txt* e restituisce i risultati in */example/data/davinciwordcount*.  Se si vuole eseguire di nuovo l'applicazione, è necessario pulire la cartella di output.

> [!NOTE]  
> I passaggi descritti in questo articolo devono essere eseguiti da un client Windows. Per informazioni sull'uso di un client Linux, OS X o Unix con Hive, usare il selettore di schede visualizzato all'inizio dell'articolo.
> 
> 

## <a name="prerequisites"></a>Prerequisiti
Per eseguire le procedure descritte nell'articolo sono necessari gli elementi seguenti:

* **Un cluster Hadoop in HDInsight**. Vedere [Introduzione all'uso di Apache Hadoop basato su Linux in HDInsight](apache-hadoop-linux-tutorial-get-started.md).
* **Visual Studio 2013/2015/2017**.

## <a name="submit-mapreduce-jobs-using-hdinsight-net-sdk"></a>Inviare processi MapReduce mediante HDInsight .NET SDK
HDInsight .NET SDK fornisce librerie client .NET che semplificano l'uso dei cluster HDInsight da .NET. 

**Inviare i processi**

1. Creare un'applicazione console C# in Visual Studio.
2. Eseguire il comando seguente dalla console di Gestione pacchetti NuGet:

    ```   
    Install-Package Microsoft.Azure.Management.HDInsight.Job
    ```
3. Usare il codice seguente:

    ```csharp
    using System.Collections.Generic;
    using System.IO;
    using System.Text;
    using System.Threading;
    using Microsoft.Azure.Management.HDInsight.Job;
    using Microsoft.Azure.Management.HDInsight.Job.Models;
    using Hyak.Common;
    using Microsoft.WindowsAzure.Storage;
    using Microsoft.WindowsAzure.Storage.Blob;

    namespace SubmitHDInsightJobDotNet
    {
        class Program
        {
            private static HDInsightJobManagementClient _hdiJobManagementClient;

            private const string existingClusterName = "<Your HDInsight Cluster Name>";
            private const string existingClusterUri = existingClusterName + ".azurehdinsight.net";
            private const string existingClusterUsername = "<Cluster Username>";
            private const string existingClusterPassword = "<Cluster User Password>";

            private const string defaultStorageAccountName = "<Default Storage Account Name>"; //<StorageAccountName>.blob.core.windows.net
            private const string defaultStorageAccountKey = "<Default Storage Account Key>";
            private const string defaultStorageContainerName = "<Default Blob Container Name>";

            private const string sourceFile = "/example/data/gutenberg/davinci.txt";  
            private const string outputFolder = "/example/data/davinciwordcount";

            static void Main(string[] args)
            {
                System.Console.WriteLine("The application is running ...");

                var clusterCredentials = new BasicAuthenticationCloudCredentials { Username = existingClusterUsername, Password = existingClusterPassword };
                _hdiJobManagementClient = new HDInsightJobManagementClient(existingClusterUri, clusterCredentials);

                SubmitMRJob();

                System.Console.WriteLine("Press ENTER to continue ...");
                System.Console.ReadLine();
            }

            private static void SubmitMRJob()
            {
                List<string> args = new List<string> { { "/example/data/gutenberg/davinci.txt" }, { "/example/data/davinciwordcount" } };

                var paras = new MapReduceJobSubmissionParameters
                {
                    JarFile = @"/example/jars/hadoop-mapreduce-examples.jar",
                    JarClass = "wordcount",
                    Arguments = args
                };

                System.Console.WriteLine("Submitting the MR job to the cluster...");
                var jobResponse = _hdiJobManagementClient.JobManagement.SubmitMapReduceJob(paras);
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
                System.Console.WriteLine("Job output is: ");
                var storageAccess = new AzureStorageAccess(defaultStorageAccountName, defaultStorageAccountKey,
                    defaultStorageContainerName);
    
                if (jobDetail.ExitValue == 0)
                {
                    // Create the storage account object
                    CloudStorageAccount storageAccount = CloudStorageAccount.Parse("DefaultEndpointsProtocol=https;AccountName=" + 
                        defaultStorageAccountName + 
                        ";AccountKey=" + defaultStorageAccountKey);
    
                    // Create the blob client.
                    CloudBlobClient blobClient = storageAccount.CreateCloudBlobClient();
    
                    // Retrieve reference to a previously created container.
                    CloudBlobContainer container = blobClient.GetContainerReference(defaultStorageContainerName);
    
                    CloudBlockBlob blockBlob = container.GetBlockBlobReference(outputFolder.Substring(1) + "/part-r-00000");
    
                    using (var stream = blockBlob.OpenRead())
                    {
                        using (StreamReader reader = new StreamReader(stream))
                        {
                            while (!reader.EndOfStream)
                            {
                                System.Console.WriteLine(reader.ReadLine());
                            }
                        }
                    }
                }
                else
                {
                    // fetch stderr output in case of failure
                    var output = _hdiJobManagementClient.JobManagement.GetJobErrorLogs(jobId, storageAccess); 
    
                    using (var reader = new StreamReader(output, Encoding.UTF8))
                    {
                        string value = reader.ReadToEnd();
                        System.Console.WriteLine(value);
                    }
    
                }
            }
        }
    }
    ```

4. Premere **F5** per eseguire l'applicazione.

Per eseguire nuovamente il processo, è necessario cambiare il nome della cartella di output del processo, che nell'esempio è: "/example/data/davinciwordcount".

Al completamento del processo, l'applicazione stampa il contenuto del file di output "part-r-00000".

## <a name="next-steps"></a>Passaggi successivi
Questo articolo ha spiegato vari modi per creare un cluster HDInsight. Per altre informazioni, vedere gli articoli seguenti:

* Per inviare un processo Hive, vedere [Eseguire query Apache Hive con HDInsight .NET SDK](apache-hadoop-use-hive-dotnet-sdk.md).
* Per creare cluster HDInsight, vedere [Creare cluster Apache Hadoop basati su Linux in HDInsight](../hdinsight-hadoop-provision-linux-clusters.md).
* Per gestire cluster HDInsight, vedere [Gestire cluster Apache Hadoop in HDInsight](../hdinsight-administer-use-portal-linux.md).
* Per informazioni su HDInsight .NET SDK, vedere [HDInsight .NET SDK reference](https://docs.microsoft.com/dotnet/api/overview/azure/hdinsight) (Informazioni di riferimento su HDInsight .NET SDK).
* Per usare l'autenticazione non interattiva in Azure, vedere [Creare applicazioni .NET HDInsight di autenticazione non interattive](../hdinsight-create-non-interactive-authentication-dotnet-applications.md).

