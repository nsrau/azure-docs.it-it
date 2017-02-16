---
title: Usare Pig di Hadoop con .NET in HDInsight | Documentazione Microsoft
description: Informazioni su come usare .NET SDK per Hadoop per inviare processi Pig a Hadoop in HDInsight.
services: hdinsight
documentationcenter: .net
author: Blackmist
manager: jhubbard
editor: cgronlun
tags: azure-portal
ms.assetid: fa11d49a-328c-47e7-b16d-e7ed2a453195
ms.service: hdinsight
ms.devlang: dotnet
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: big-data
ms.date: 10/17/2016
ms.author: larryfr
translationtype: Human Translation
ms.sourcegitcommit: 8c07f0da21eab0c90ad9608dfaeb29dd4a01a6b7
ms.openlocfilehash: bd1c31c669bacf9e44b13d5a676dffc8a9176046


---
# <a name="run-pig-jobs-using-the-net-sdk-for-hadoop-in-hdinsight"></a>Esecuzione di processi Pig con .NET SDK per Hadoop in HDInsight
[!INCLUDE [pig-selector](../../includes/hdinsight-selector-use-pig.md)]

Questo documento fornisce un esempio d'uso di .NET SDK per Hadoop per inviare i processi Pig a un cluster Hadoop in HDInsight.

HDInsight .NET SDK fornisce librerie client .NET che semplificano l'utilizzo dei cluster HDInsight da .NET. Pig consente di creare operazioni MapReduce modellando una serie di trasformazioni di dati. In questo articolo si apprenderà come usare un'applicazione di base C# per inviare un processo Pig a un cluster HDInsight.

## <a name="prerequisites"></a>Prerequisiti
Per seguire la procedura descritta in questo articolo, è necessario quanto segue:

* Un cluster Azure HDInsight (Hadoop in HDInsight) basato su Windows o su Linux.

  > [!IMPORTANT]
  > Linux è l'unico sistema operativo usato in HDInsight versione 3.4 o successiva. Per altre informazioni, vedere [HDInsight deprecato in Windows](hdinsight-component-versioning.md#hdi-version-32-and-33-nearing-deprecation-date).

* Visual Studio 2012, 2013 o 2015.

## <a name="create-the-application"></a>Creazione dell'applicazione
HDInsight .NET SDK fornisce librerie client .NET che semplificano l'uso dei cluster HDInsight da .NET. 

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
5. Selezionare **Library Package Manager** (Gestione pacchetti libreria) o **Gestione pacchetti NuGet** dal menu **Strumenti** e quindi scegliere **Console di Gestione pacchetti**.
6. Usare il seguente comando nella console per installare i pacchetti di .NET SDK.
   
        Install-Package Microsoft.Azure.Management.HDInsight.Job
7. In Esplora soluzioni fare doppio clic su **Program.cs** per aprirlo. Replace Sostituire il codice esistente con il seguente.
   
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
   
                static void Main(string[] args)
                {
                    System.Console.WriteLine("The application is running ...");
   
                    var clusterCredentials = new BasicAuthenticationCloudCredentials { Username = ExistingClusterUsername, Password = ExistingClusterPassword };
                    _hdiJobManagementClient = new HDInsightJobManagementClient(ExistingClusterUri, clusterCredentials);
   
                    SubmitPigJob();
   
                    System.Console.WriteLine("Press ENTER to continue ...");
                    System.Console.ReadLine();
                }
   
                private static void SubmitPigJob()
                {
                    var parameters = new PigJobSubmissionParameters
                    {
                        Query = @"LOGS = LOAD 'wasbs:///example/data/sample.log';
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
            }
        }
8. Premere **F5** per avviare l'applicazione.
9. Premere **INVIO** per uscire dall'applicazione.

## <a name="summary"></a>Riepilogo
Come si può notare, .NET SDK per Hadoop consente di creare applicazioni .NET che inviano processi Pig a un cluster HDInsight e di monitorare lo stato del processo.

## <a name="next-steps"></a>Passaggi successivi
Per informazioni generali su Pig in HDInsight.

* [Usare Pig con Hadoop in HDInsight](hdinsight-use-pig.md)

Per informazioni su altre modalità d'uso di Hadoop in HDInsight.

* [Usare Hive con Hadoop in HDInsight](hdinsight-use-hive.md)
* [Usare MapReduce con Hadoop in HDInsight](hdinsight-use-mapreduce.md)

[preview-portal]: https://portal.azure.com/



<!--HONumber=Jan17_HO3-->


