---
title: Eseguire processi Apache Sqoop usando .NET e HDInsight - Azure
description: Informazioni su come usare HDInsight .NET SDK per eseguire l'importazione e l'esportazione di Apache Sqoop tra un cluster Apache Hadoop e un database SQL di Azure.Learn how to use the HDInsight .NET SDK to run Apache Sqoop import and export between an Apache Hadoop cluster and an Azure SQL Database.
author: hrasheed-msft
ms.author: hrasheed
ms.reviewer: jasonh
ms.service: hdinsight
ms.topic: conceptual
ms.custom: hdinsightactive,hdiseo17may2017
ms.date: 01/14/2020
ms.openlocfilehash: f0f767273a40bc91b1d49477c896b0b157623106
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/27/2020
ms.locfileid: "76157067"
---
# <a name="run-apache-sqoop-jobs-by-using-net-sdk-for-apache-hadoop-in-hdinsight"></a>Eseguire processi Apache Sqoop con .NET SDK per Apache Hadoop in HDInsight

[!INCLUDE [sqoop-selector](../../../includes/hdinsight-selector-use-sqoop.md)]

Informazioni su come usare azure HDInsight .NET SDK per eseguire processi Apache Sqoop in HDInsight per importare ed esportare tra un cluster HDInsight e un database SQL di Azure o un database di SQL Server.Learn how to use the Azure HDInsight .NET SDK to run Apache Sqoop jobs in HDInsight to import and export between an HDInsight cluster and an Azure SQL Database or SQL Server database.

## <a name="prerequisites"></a>Prerequisiti

* Completamento di [Set up test environment](./hdinsight-use-sqoop.md#create-cluster-and-sql-database) da Use [Apache Sqoop with Hadoop in HDInsight](./hdinsight-use-sqoop.md).

* [Visual Studio](https://visualstudio.microsoft.com/vs/community/).

* Familiarità con Sqoop. Per ulteriori informazioni, consultate [Guida per l'utente di Sqoop.](https://sqoop.apache.org/docs/1.4.7/SqoopUserGuide.html)

## <a name="use-sqoop-on-hdinsight-clusters-with-the-net-sdk"></a>Usare Sqoop nei cluster HDInsight con .NET SDK

HDInsight .NET SDK fornisce librerie client .NET che semplificano l'uso dei cluster HDInsight da .NET. In questa sezione viene creata un'applicazione `hivesampletable` console c'è per esportare la tabella di database SQL di Azure creata dai prerequisiti.

## <a name="set-up"></a>Configurare

1. Avviare Visual Studio e creare un'applicazione console di C.

1. Passare a **Strumenti** > NuGet Gestione pacchetti Console di gestione pacchetti ed eseguire il comando seguente:Navigate to Tools**NuGet Package Manager** > Package Manager**Console** and run the following command:

    ```
    Install-Package Microsoft.Azure.Management.HDInsight.Job
    ```

## <a name="sqoop-export"></a>Esportazione con Sqoop

Da Hive a SQL Server.  In questo esempio vengono `hivesampletable` esportati `mobiledata` i dati dalla tabella Hive alla tabella in Database SQL.

1. Utilizzare il codice seguente nel file di Program.cs. Modificare il codice per `ExistingClusterName`impostare `ExistingClusterPassword`i valori per e .

    ```csharp
    using Microsoft.Azure.Management.HDInsight.Job;
    using Microsoft.Azure.Management.HDInsight.Job.Models;
    using Hyak.Common;
    
    namespace SubmitHDInsightJobDotNet
    {
        class Program
        {
            private static HDInsightJobManagementClient _hdiJobManagementClient;
    
            private const string ExistingClusterName = "<Your HDInsight Cluster Name>";
            private const string ExistingClusterPassword = "<Cluster User Password>";
            private const string ExistingClusterUri = ExistingClusterName + ".azurehdinsight.net";
            private const string ExistingClusterUsername = "admin";
    
            static void Main(string[] args)
            {
                System.Console.WriteLine("The application is running ...");
    
                var clusterCredentials = new BasicAuthenticationCloudCredentials { Username = ExistingClusterUsername, Password = ExistingClusterPassword };
                _hdiJobManagementClient = new HDInsightJobManagementClient(ExistingClusterUri, clusterCredentials);
    
                SubmitSqoopJob();
    
                System.Console.WriteLine("Press ENTER to continue ...");
                System.Console.ReadLine();
            }
    
            private static void SubmitSqoopJob()
            {
                var sqlDatabaseServerName = ExistingClusterName + "dbserver";
                var sqlDatabaseLogin = "sqluser";
                var sqlDatabaseLoginPassword = ExistingClusterPassword;
                var sqlDatabaseDatabaseName = ExistingClusterName + "db";
    
                // Connection string for using Azure SQL Database; Comment if using SQL Server
                var connectionString = "jdbc:sqlserver://" + sqlDatabaseServerName + ".database.windows.net;user=" + sqlDatabaseLogin + "@" + sqlDatabaseServerName + ";password=" + sqlDatabaseLoginPassword + ";database=" + sqlDatabaseDatabaseName;
    
                // Connection string for using SQL Server; Uncomment if using SQL Server
                // var connectionString = "jdbc:sqlserver://" + sqlDatabaseServerName + ";user=" + sqlDatabaseLogin + ";password=" + sqlDatabaseLoginPassword + ";database=" + sqlDatabaseDatabaseName;
    
                //sqoop start
                var tableName = "mobiledata";
    
                var parameters = new SqoopJobSubmissionParameters
                {
                     Command = "export --connect " + connectionString + " --table " + tableName + " --hcatalog-table hivesampletable"
                };
                //sqoop end
    
                System.Console.WriteLine("Submitting the Sqoop job to the cluster...");
                var response = _hdiJobManagementClient.JobManagement.SubmitSqoopJob(parameters);
                System.Console.WriteLine("Validating that the response is as expected...");
                System.Console.WriteLine("Response status code is " + response.StatusCode);
                System.Console.WriteLine("Validating the response object...");
                System.Console.WriteLine("JobId is " + response.JobSubmissionJsonResponse.Id);
            }
        }
    }
    ```

1. Per eseguire il programma, premere **F5**.

## <a name="sqoop-import"></a>Importazione con Sqoop

Da SQL Server ad Archiviazione di Azure.From SQL Server to Azure Storage. Questo esempio dipende dall'esportazione precedente eseguita.  In questo esempio vengono `mobiledata` importati i dati `wasb:///tutorials/usesqoop/importeddata` dalla tabella nel database SQL nella directory dell'account di archiviazione predefinito del cluster.

1. Sostituire il codice `//sqoop start //sqoop end` precedente nel blocco con il codice seguente:

    ```csharp
    var tableName = "mobiledata";
    var exportDir = "/tutorials/usesqoop/importeddata";
    
    var parameters = new SqoopJobSubmissionParameters
    {
        Command = "import --connect " + connectionString + " --table " + tableName + " --target-dir " +  exportDir + " --fields-terminated-by \\t --lines-terminated-by \\n -m 1"
    };
    ```

1. Per eseguire il programma, premere **F5**.

## <a name="limitations"></a>Limitazioni

HDInsight basato su Linux prevede le limitazioni seguenti:

* Esportazione in blocco: il connettore Sqoop utilizzato per esportare i dati in Microsoft SQL Server o nel database SQL di Azure non supporta attualmente gli inserimenti bulk.

* Batching: utilizzando `-batch` l'opzione, Sqoop esegue più inserimenti anziché l'invio in batch delle operazioni di inserimento.

## <a name="next-steps"></a>Passaggi successivi

Ora hai imparato a usare Sqoop. Per altre informazioni, vedere:

* [Usare Apache Oozie con HDInsight:](../hdinsight-use-oozie-linux-mac.md)usare l'azione Sqoop in un flusso di lavoro di Oozie.Use Apache Oozie with HDInsight : Use Sqoop action in an Oozie workflow.
* [Caricare i dati in HDInsight](../hdinsight-upload-data.md): trovare altri metodi per caricare i dati in HDInsight o in Archiviazione BLOB di Azure.
