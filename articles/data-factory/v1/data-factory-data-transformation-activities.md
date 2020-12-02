---
title: 'Trasformazione dei dati: processo & trasformare i dati '
description: Informazioni su come trasformare dati o elaborare dati in Azure Data Factory usando Hadoop, Azure Machine Learning Studio (classico) o Azure Data Lake Analytics.
services: data-factory
documentationcenter: ''
author: dcstwh
ms.author: weetok
manager: jroth
ms.reviewer: maghan
ms.service: data-factory
ms.workload: data-services
ms.topic: conceptual
ms.date: 01/10/2018
ms.openlocfilehash: 587e8eaf36a8e9d0be86237e2db72f952853a0ff
ms.sourcegitcommit: d60976768dec91724d94430fb6fc9498fdc1db37
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 12/02/2020
ms.locfileid: "96495685"
---
# <a name="transform-data-in-azure-data-factory-version-1"></a>Trasformare i dati in Azure Data Factory versione 1
> [!div class="op_single_selector"]
> * [Hive](data-factory-hive-activity.md)  
> * [Pig](data-factory-pig-activity.md)  
> * [MapReduce](data-factory-map-reduce.md)  
> * [Hadoop Streaming](data-factory-hadoop-streaming-activity.md)
> * [Azure Machine Learning Studio (versione classica)](data-factory-azure-ml-batch-execution-activity.md) 
> * [Stored procedure](data-factory-stored-proc-activity.md)
> * [Attività U-SQL di Data Lake Analytics](data-factory-usql-activity.md)
> * [Attività personalizzata .NET](data-factory-use-custom-activities.md)

## <a name="overview"></a>Panoramica
> [!NOTE]
> Le informazioni di questo articolo sono valide per la versione 1 di Data Factory. Se si usa la versione corrente del servizio Data Factory, vedere le [attività di trasformazione dei dati in Data Factory](../transform-data.md).

Questo articolo illustra le attività di trasformazione dei dati in Azure Data Factory da usare per trasformare ed elaborare i dati non elaborati in stime e informazioni dettagliate. L'attività di trasformazione viene eseguita in un ambiente di elaborazione, ad esempio cluster HDInsight di Azure o un Batch di Azure. Vengono forniti i collegamenti ad articoli con informazioni dettagliate su ciascuna attività di trasformazione.

Data Factory supporta le seguenti attività di trasformazione dei dati che possono essere aggiunte a [pipeline](data-factory-create-pipelines.md) singolarmente o con un'altra attività concatenata.

> [!NOTE]
> Per la procedura dettagliata, vedere l'articolo [Creare una pipeline con la trasformazione Hive](data-factory-build-your-first-pipeline.md) .  
> 
> 

## <a name="hdinsight-hive-activity"></a>Attività Hive di HDInsight
L'attività Hive di HDInsight in una pipeline di Data factory esegue query Hive sul proprio cluster HDInsight o sul cluster HDInsight su richiesta basato su Windows o Linux. Per informazioni dettagliate su questa attività, vedere l'articolo [attività hive](data-factory-hive-activity.md) . 

## <a name="hdinsight-pig-activity"></a>Attività Pig di HDInsight
L'attività Pig di HDInsight in una pipeline di Data factory esegue query Pig sul proprio cluster HDInsight o sul cluster HDInsight su richiesta basato su Windows o Linux. Per informazioni dettagliate su questa attività, vedere l'articolo [attività Pig](data-factory-pig-activity.md) . 

## <a name="hdinsight-mapreduce-activity"></a>Attività MapReduce di HDInsight
L'attività HDInsight MapReduce in una pipeline di Data Factory esegue i programmi di MapReduce nei cluster HDInsight personalizzati o su richiesta basati su Windows/Linux. Per informazioni dettagliate su questa attività, vedere l'articolo [attività MapReduce](data-factory-map-reduce.md) .

## <a name="hdinsight-streaming-activity"></a>Attività di streaming di HDInsight
L'attività HDInsight Streaming Activity in una pipeline di Data Factory esegue i programmi di Hadoop Streaming nei cluster HDInsight personalizzati o su richiesta basati su Windows/Linux. Vedere l' [attività di streaming di HDInsight](data-factory-hadoop-streaming-activity.md) per i dettagli.

## <a name="hdinsight-spark-activity"></a>Attività Spark di HDInsight
L'attività Spark di HDInsight in una pipeline di Data Factory esegue programmi Spark nel cluster HDInsight personale. Per conoscere i dettagli, vedere [Richiamare i programmi Spark da Azure Data Factory](data-factory-spark.md). 

## <a name="azure-machine-learning-studio-classic-activities"></a>Attività Azure Machine Learning Studio (classiche)
Azure Data Factory consente di creare facilmente pipeline che usano un servizio Web pubblicato Azure Machine Learning Studio (classico) per l'analisi predittiva. Utilizzando l' [attività di esecuzione batch](data-factory-azure-ml-batch-execution-activity.md#invoking-a-web-service-using-batch-execution-activity) in una pipeline di Azure Data Factory, è possibile richiamare un servizio Web Studio (classico) per eseguire stime sui dati in batch.

Nel corso del tempo, è necessario ripetere il training dei modelli predittivi negli esperimenti di assegnazione dei punteggi in studio (classico) usando nuovi set di dati di input. Al termine della ripetizione del training, si vuole aggiornare il servizio Web di assegnazione dei punteggi con il modello di apprendimento automatico di cui è stato ripetuto il training. È possibile utilizzare l' [attività Aggiorna risorsa](data-factory-azure-ml-batch-execution-activity.md#updating-models-using-update-resource-activity) per aggiornare il servizio Web con il nuovo modello sottoposto a training.  

Per informazioni dettagliate su queste attività di studio (classiche), vedere [usare le attività Azure Machine Learning Studio (classiche)](data-factory-azure-ml-batch-execution-activity.md) . 

## <a name="stored-procedure-activity"></a>Attività stored procedure
È possibile usare l'attività SQL Server stored procedure in una pipeline Data Factory per richiamare un stored procedure in uno dei seguenti archivi dati: database SQL di Azure, analisi sinapsi di Azure, database di SQL Server nell'azienda o in una VM di Azure. Per informazioni dettagliate, vedere l'articolo [attività stored procedure](data-factory-stored-proc-activity.md) .  

## <a name="data-lake-analytics-u-sql-activity"></a>Attività U-SQL di Data Lake Analytics
L'attività U-SQL di Data Lake Analytics esegue uno script U-SQL in un cluster di Azure Data Lake Analytics. Per informazioni dettagliate, vedere l'articolo [attività U-SQL di analisi dei dati](data-factory-usql-activity.md) . 

## <a name="net-custom-activity"></a>Attività personalizzata .NET
Se è necessario trasformare i dati in una modalità non supportata da Data Factory, è possibile creare un'attività personalizzata contenente la logica di elaborazione dei dati richiesta e usarla nella pipeline. È possibile configurare l'attività .NET personalizzata da eseguire usando il servizio Azure Batch o un cluster Azure HDInsight. Vedere l'articolo [Usare le attività personalizzate](data-factory-use-custom-activities.md) per i dettagli. 

È possibile creare un'attività personalizzata per eseguire gli script R nel cluster HDInsight con R installato. Vedere [RunRScriptUsingADFSample](https://github.com/Azure/Azure-DataFactory/tree/master/SamplesV1/RunRScriptUsingADFSample)(Esempio relativo all'esecuzione di script R con Azure Data Factory). 

## <a name="compute-environments"></a>Ambienti di calcolo
Creare un servizio collegato per l'ambiente di calcolo e quindi usare il servizio collegato quando si definisce un'attività di trasformazione. Esistono due tipi di ambienti di calcolo supportati da Data factory. 

1. **On Demand**: in questo caso, l'ambiente informatico è completamente gestito da Data factory. Viene automaticamente creato dal servizio Data Factory prima che un processo venga inviato per l’elaborazione dati e rimosso quando il processo viene completato. È possibile configurare e controllare le impostazioni granulari dell'ambiente di elaborazione su richiesta per l'esecuzione del processo, la gestione del cluster e azioni di avvio automatico. 
2. **Bring Your Own**: In questo caso, è possibile registrare il proprio ambiente di elaborazione (ad esempio cluster HDInsight) come servizio collegato in Data factory. L'ambiente di elaborazione viene gestito dall'utente e il servizio Data factory viene usato per eseguire le attività. 

Per informazioni sui servizi di calcolo supportati da Data factory, vedere l'articolo [Servizi collegati di calcolo](data-factory-compute-linked-services.md) . 

## <a name="summary"></a>Riepilogo
Azure Data Factory supporta le seguenti attività di trasformazione di dati e i seguenti ambienti di calcolo per le attività. Le attività di trasformazione possono essere aggiunte alla pipeline singolarmente o con un'altra attività concatenata.

| Attività di trasformazione dei dati | Ambiente di calcolo |
|:--- |:--- |
| [Hive](data-factory-hive-activity.md) |HDInsight [Hadoop] |
| [Pig](data-factory-pig-activity.md) |HDInsight [Hadoop] |
| [MapReduce](data-factory-map-reduce.md) |HDInsight [Hadoop] |
| [Hadoop Streaming](data-factory-hadoop-streaming-activity.md) |HDInsight [Hadoop] |
| [Attività di Azure Machine Learning Studio (versione classica): Esecuzione batch e Aggiorna risorsa](data-factory-azure-ml-batch-execution-activity.md) |Macchina virtuale di Azure |
| [Stored procedure](data-factory-stored-proc-activity.md) |Azure SQL, Azure Synapse Analytics o SQL Server |
| [Attività U-SQL di Data Lake Analytics](data-factory-usql-activity.md) |Azure Data Lake Analytics. |
| [DotNet](data-factory-use-custom-activities.md) |HDInsight [Hadoop] o Batch di Azure |

