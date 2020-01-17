---
title: Eseguire processi Apache Sqoop usando .NET e HDInsight - Azure
description: Informazioni su come usare HDInsight .NET SDK per eseguire l'importazione e l'esportazione di Apache Sqoop tra un cluster Apache Hadoop e un database SQL di Azure.
author: hrasheed-msft
ms.author: hrasheed
ms.reviewer: jasonh
ms.service: hdinsight
ms.topic: conceptual
ms.custom: hdinsightactive,hdiseo17may2017
ms.date: 01/14/2020
ms.openlocfilehash: f0f767273a40bc91b1d49477c896b0b157623106
ms.sourcegitcommit: 276c1c79b814ecc9d6c1997d92a93d07aed06b84
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 01/16/2020
ms.locfileid: "76157067"
---
# <a name="run-apache-sqoop-jobs-by-using-net-sdk-for-apache-hadoop-in-hdinsight"></a>Eseguire processi Apache Sqoop con .NET SDK per Apache Hadoop in HDInsight

[!INCLUDE [sqoop-selector](../../../includes/hdinsight-selector-use-sqoop.md)]

Informazioni su come usare Azure HDInsight .NET SDK per eseguire processi Apache Sqoop in HDInsight per l'importazione e l'esportazione tra un cluster HDInsight e un database SQL di Azure o un database SQL Server.

## <a name="prerequisites"></a>Prerequisiti

* Il completamento della [configurazione dell'ambiente di test](./hdinsight-use-sqoop.md#create-cluster-and-sql-database) da [usare Apache Sqoop con Hadoop in HDInsight](./hdinsight-use-sqoop.md).

* [Visual Studio](https://visualstudio.microsoft.com/vs/community/).

* Familiarità con Sqoop. Per ulteriori informazioni, vedere la [Guida dell'utente di Sqoop](https://sqoop.apache.org/docs/1.4.7/SqoopUserGuide.html).

## <a name="use-sqoop-on-hdinsight-clusters-with-the-net-sdk"></a>Usare Sqoop nei cluster HDInsight con .NET SDK

HDInsight .NET SDK fornisce librerie client .NET che semplificano l'uso dei cluster HDInsight da .NET. In questa sezione viene creata un' C# applicazione console per esportare il `hivesampletable` nella tabella del database SQL di Azure creata dai prerequisiti.

## <a name="set-up"></a>Configurare

1. Avviare Visual Studio e creare un' C# applicazione console.

1. Passare a **strumenti** > **gestione pacchetti NuGet** > **console di gestione pacchetti** ed eseguire il comando seguente:

    ```
    Install-Package Microsoft.Azure.Management.HDInsight.Job
    ```

## <a name="sqoop-export"></a>Esportazione con Sqoop

Da hive a SQL Server.  Questo esempio Mostra come esportare i dati dalla tabella hive `hivesampletable` alla tabella `mobiledata` del database SQL.

1. Usare il codice seguente nel file Program.cs. Modificare il codice per impostare i valori per `ExistingClusterName`e `ExistingClusterPassword`.

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

Da SQL Server ad archiviazione di Azure. Questo esempio dipende dall'esportazione precedente eseguita.  Questo esempio importa i dati dalla tabella `mobiledata` nel database SQL alla directory `wasb:///tutorials/usesqoop/importeddata` nell'account di archiviazione predefinito del cluster.

1. Sostituire il codice precedente nel blocco `//sqoop start //sqoop end` con il codice seguente:

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

* Esportazione in blocco: il connettore Sqoop usato per esportare i dati in Microsoft SQL Server o nel database SQL di Azure attualmente non supporta gli inserimenti bulk.

* Invio in batch: usando l'opzione `-batch`, Sqoop esegue più inserimenti invece di suddividere in batch le operazioni di inserimento.

## <a name="next-steps"></a>Passaggi successivi

A questo punto si è appreso come usare Sqoop. Per altre informazioni, vedere:

* [Usare Apache oozie con HDInsight](../hdinsight-use-oozie-linux-mac.md): usare l'azione Sqoop in un flusso di lavoro oozie.
* [Caricare i dati in HDInsight](../hdinsight-upload-data.md): trovare altri metodi per caricare i dati in HDInsight o in Archiviazione BLOB di Azure.
