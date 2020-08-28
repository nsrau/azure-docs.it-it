---
title: Inviare processi MapReduce mediante HDInsight .NET SDK - Azure
description: Informazioni su come inviare processi MapReduce ad Azure HDInsight Apache Hadoop con HDInsight .NET SDK.
author: hrasheed-msft
ms.author: hrasheed
ms.reviewer: jasonh
ms.service: hdinsight
ms.topic: how-to
ms.custom: hdinsightactive, devx-track-csharp
ms.date: 01/15/2020
ms.openlocfilehash: 3de7101eb998d0f9b3b57afba6d19595738cbb63
ms.sourcegitcommit: 419cf179f9597936378ed5098ef77437dbf16295
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 08/27/2020
ms.locfileid: "89020406"
---
# <a name="run-mapreduce-jobs-using-hdinsight-net-sdk"></a>Eseguire processi MapReduce con HDInsight .NET SDK

[!INCLUDE [mapreduce-selector](../../../includes/hdinsight-selector-use-mapreduce.md)]

Informazioni su come inviare processi MapReduce con HDInsight .NET SDK. I cluster HDInsight includono un file JAR con alcuni esempi di MapReduce. Il file jar è `/example/jars/hadoop-mapreduce-examples.jar` .  Uno degli esempi è **wordcount**. Per inviare un processo wordcount, è necessario sviluppare un'applicazione console C#.  Il processo legge il `/example/data/gutenberg/davinci.txt` file e restituisce i risultati in `/example/data/davinciwordcount` .  Se si vuole eseguire di nuovo l'applicazione, è necessario pulire la cartella di output.

> [!NOTE]  
> I passaggi descritti in questo articolo devono essere eseguiti da un client Windows. Per informazioni sull'uso di un client Linux, OS X o Unix con Hive, usare il selettore di schede visualizzato all'inizio dell'articolo.

## <a name="prerequisites"></a>Prerequisiti

* Un cluster Apache Hadoop in HDInsight. Vedere [creare cluster Apache Hadoop usando il portale di Azure](../hdinsight-hadoop-create-linux-clusters-portal.md).

* [Visual Studio](https://visualstudio.microsoft.com/vs/community/).

## <a name="submit-mapreduce-jobs-using-hdinsight-net-sdk"></a>Inviare processi MapReduce mediante HDInsight .NET SDK

HDInsight .NET SDK fornisce librerie client .NET che semplificano l'uso dei cluster HDInsight da .NET.

1. Avviare Visual Studio e creare un'applicazione console C#.

1. Passare a **strumenti**  >  **Gestione pacchetti NuGet**  >  **console di gestione pacchetti** e immettere il comando seguente:

    ```   
    Install-Package Microsoft.Azure.Management.HDInsight.Job
    ```

1. Copiare il codice seguente in **Program.cs**. Modificare quindi il codice impostando i valori per: `existingClusterName` , `existingClusterPassword` , `defaultStorageAccountName` , `defaultStorageAccountKey` e `defaultStorageContainerName` .

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
            private const string existingClusterPassword = "<Cluster User Password>";
            private const string defaultStorageAccountName = "<Default Storage Account Name>"; 
            private const string defaultStorageAccountKey = "<Default Storage Account Key>";
            private const string defaultStorageContainerName = "<Default Blob Container Name>";
    
            private const string existingClusterUsername = "admin";
            private const string existingClusterUri = existingClusterName + ".azurehdinsight.net";
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

1. Premere **F5** per eseguire l'applicazione.

Per eseguire di nuovo il processo, è necessario modificare il nome della cartella di output del processo, nell'esempio `/example/data/davinciwordcount` .

Quando il processo viene completato correttamente, l'applicazione stampa il contenuto del file di output `part-r-00000` .

## <a name="next-steps"></a>Passaggi successivi

Questo articolo ha spiegato vari modi per creare un cluster HDInsight. Per altre informazioni, vedere gli articoli seguenti:

* Per inviare un processo Hive, vedere [Eseguire query Apache Hive con HDInsight .NET SDK](apache-hadoop-use-hive-dotnet-sdk.md).
* Per creare cluster HDInsight, vedere [Creare cluster Apache Hadoop basati su Linux in HDInsight](../hdinsight-hadoop-provision-linux-clusters.md).
* Per gestire cluster HDInsight, vedere [Gestire cluster Apache Hadoop in HDInsight](../hdinsight-administer-use-portal-linux.md).
* Per informazioni su HDInsight .NET SDK, vedere [HDInsight .NET SDK reference](https://docs.microsoft.com/dotnet/api/overview/azure/hdinsight) (Informazioni di riferimento su HDInsight .NET SDK).
* Per usare l'autenticazione non interattiva in Azure, vedere [Creare applicazioni .NET HDInsight di autenticazione non interattive](../hdinsight-create-non-interactive-authentication-dotnet-applications.md).
