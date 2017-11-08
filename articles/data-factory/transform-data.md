---
title: Trasformare dati tramite Azure Data Factory | Microsoft Doc
description: Informazioni su come trasformare o elaborare i dati in Azure Data Factory con Hadoop, Machine Learning o Azure Data Lake Analytics.
services: data-factory
documentationcenter: 
author: sharonlo101
manager: jhubbard
editor: monicar
ms.service: data-factory
ms.workload: data-services
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 08/17/2017
ms.author: shlo
ms.openlocfilehash: 832c4f232a3821225c8086b636de713da2b967a0
ms.sourcegitcommit: 3e3a5e01a5629e017de2289a6abebbb798cec736
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 10/27/2017
---
# <a name="transform-data-in-azure-data-factory"></a>Trasformare i dati in Azure Data Factory
> [!div class="op_single_selector"]
> * [Hive](transform-data-using-hadoop-hive.md)  
> * [Pig](transform-data-using-hadoop-pig.md)  
> * [MapReduce](transform-data-using-hadoop-map-reduce.md)  
> * [Hadoop Streaming](transform-data-using-hadoop-streaming.md)
> * [Spark](transform-data-using-spark.md)
> * [Machine Learning](transform-data-using-machine-learning.md) 
> * [Stored procedure](transform-data-using-stored-procedure.md)
> * [Attività U-SQL di Data Lake Analytics](transform-data-using-data-lake-analytics.md)
> * [Attività personalizzata .NET](transform-data-using-dotnet-custom-activity.md)

## <a name="overview"></a>Panoramica
Questo articolo illustra le attività di trasformazione dei dati in Azure Data Factory da usare per trasformare ed elaborare i dati non elaborati in stime e informazioni dettagliate. L'attività di trasformazione viene eseguita in un ambiente di elaborazione, ad esempio cluster HDInsight di Azure o un Batch di Azure. Vengono forniti i collegamenti ad articoli con informazioni dettagliate su ciascuna attività di trasformazione.

Data Factory supporta le seguenti attività di trasformazione dei dati che possono essere aggiunte a [pipeline](concepts-pipelines-activities.md) singolarmente o con un'altra attività concatenata.

> [!NOTE]
> Questo articolo si applica alla versione 2 del servizio Data Factory, attualmente in versione di anteprima. Se si usa la versione 1 del servizio Data Factory, disponibile a livello generale, vedere le [attività di trasformazione in Data Factory versione 1](v1/data-factory-data-transformation-activities.md).
 

## <a name="hdinsight-hive-activity"></a>Attività Hive di HDInsight
L'attività Hive di HDInsight in una pipeline di Data factory esegue query Hive sul proprio cluster HDInsight o sul cluster HDInsight su richiesta basato su Windows o Linux. Per i dettagli su questa attività, vedere l'articolo [Attività Hive](transform-data-using-hadoop-hive.md). 

## <a name="hdinsight-pig-activity"></a>Attività Pig di HDInsight
L'attività Pig di HDInsight in una pipeline di Data factory esegue query Pig sul proprio cluster HDInsight o sul cluster HDInsight su richiesta basato su Windows o Linux. Per i dettagli su questa attività, vedere l'articolo [Attività Pig](transform-data-using-hadoop-pig.md). 

## <a name="hdinsight-mapreduce-activity"></a>Attività MapReduce di HDInsight
L'attività HDInsight MapReduce in una pipeline di Data Factory esegue i programmi di MapReduce nei cluster HDInsight personalizzati o su richiesta basati su Windows/Linux. Per i dettagli su questa attività, vedere l'articolo [Attività MapReduce](transform-data-using-hadoop-map-reduce.md).

## <a name="hdinsight-streaming-activity"></a>Attività di streaming di HDInsight
L'attività HDInsight Streaming in una pipeline di Data Factory esegue i programmi di Hadoop Streaming nei cluster HDInsight personalizzati o su richiesta basati su Windows o Linux. Vedere l' [attività di streaming di HDInsight](transform-data-using-hadoop-streaming.md) per i dettagli.

## <a name="hdinsight-spark-activity"></a>Attività HDInsight Spark
L'attività Spark di HDInsight in una pipeline di Data Factory esegue programmi Spark nel cluster HDInsight personale. Per conoscere i dettagli, vedere [Richiamare i programmi Spark da Azure Data Factory](transform-data-using-spark.md). 

## <a name="machine-learning-activities"></a>Attività di Machine Learning
Azure Data Factory consente di creare facilmente pipeline che usano un servizio Web pubblicato di Azure Machine Learning per l'analisi predittiva. Con l'[attività Esecuzione batch](transform-data-using-machine-learning.md) in una pipeline di Azure Data Factory è possibile eseguire stime dei dati in batch richiamando un servizio Web di Machine Learning.

Nel corso del tempo è necessario ripetere il training dei modelli predittivi negli esperimenti di assegnazione dei punteggi di Machine Learning usando nuovi set di dati di input. Una volta ripetuto il training, aggiornare il servizio Web di assegnazione dei punteggi con il modello Machine Learning di cui è stato ripetuto il training. È possibile usare l'[attività Aggiorna risorsa](update-machine-learning-models.md) per aggiornare il servizio Web con il nuovo modello con training.  

Vedere [Usare le attività Machine Learning](transform-data-using-machine-learning.md) per i relativi dettagli. 

## <a name="stored-procedure-activity"></a>Attività stored procedure
È possibile usare l'attività stored procedure di SQL Server in una pipeline di Data Factory per richiamare una stored procedure in uo dei seguenti archivi dati: database SQL di Azure, Azure SQL Data Warehouse, database di SQL Server in azienda o in una VM. Per i dettagli, vedere l'articolo [Attività stored procedure](transform-data-using-stored-procedure.md).  

## <a name="data-lake-analytics-u-sql-activity"></a>Attività U-SQL di Data Lake Analytics
L'attività U-SQL di Data Lake Analytics esegue uno script U-SQL in un cluster Azure Data Lake Analytics. Per i dettagli, vedere l'articolo [Attività U-SQL di Analisi dei dati](transform-data-using-data-lake-analytics.md). 

## <a name="net-custom-activity"></a>Attività personalizzata .NET
Se è necessario trasformare i dati in una modalità non supportata da Data Factory, è possibile creare un'attività personalizzata contenente la logica di elaborazione dei dati richiesta e usarla nella pipeline. È possibile configurare l'attività .NET personalizzata da eseguire usando il servizio Azure Batch o un cluster Azure HDInsight. Vedere l'articolo [Usare le attività personalizzate](transform-data-using-dotnet-custom-activity.md) per i dettagli. 

È possibile creare un'attività personalizzata per eseguire gli script R nel cluster HDInsight con R installato. Vedere [RunRScriptUsingADFSample](https://github.com/Azure/Azure-DataFactory/tree/master/Samples/RunRScriptUsingADFSample)(Esempio relativo all'esecuzione di script R con Azure Data Factory). 

## <a name="compute-environments"></a>Ambienti di calcolo
Creare un servizio collegato per l'ambiente di calcolo e quindi usare il servizio collegato quando si definisce un'attività di trasformazione. Esistono due tipi di ambienti di calcolo supportati da Data factory. 

- **On Demand**: in questo caso, l'ambiente informatico è completamente gestito da Data factory. Viene automaticamente creato dal servizio Data Factory prima che un processo venga inviato per l’elaborazione dati e rimosso quando il processo viene completato. È possibile configurare e controllare le impostazioni granulari dell'ambiente di elaborazione su richiesta per l'esecuzione del processo, la gestione del cluster e azioni di avvio automatico. 
- **Bring Your Own**: In questo caso, è possibile registrare il proprio ambiente di elaborazione (ad esempio cluster HDInsight) come servizio collegato in Data factory. L'ambiente di elaborazione viene gestito dall'utente e il servizio Data factory viene usato per eseguire le attività. 

Per informazioni sui servizi di calcolo supportati da Data factory, vedere l'articolo [Servizi collegati di calcolo](compute-linked-services.md) . 

## <a name="next-steps"></a>Passaggi successivi
Per un esempio di uso di un'attività di trasformazione, vedere l'esercitazione seguente: [Tutorial: transform data using Spark](tutorial-transform-data-spark-powershell.md) (Esercitazione: Trasformare dati tramite Spark)