<properties 
    pageTitle="Trasformazione dei dati: elaborare e trasformare i dati | Microsoft Azure" 
    description="Informazioni su come trasformare o elaborare i dati in Azure Data Factory con Hadoop, Machine Learning o Azure Data Lake Analytics." 
    keywords="trasformazione di dati, elaborare dati, trasformare dati, attività di trasformazione"
    services="data-factory" 
    documentationCenter="" 
    authors="sharonlo101" 
    manager="jhubbard" 
    editor="monicar"/>

<tags 
    ms.service="data-factory" 
    ms.workload="data-services" 
    ms.tgt_pltfrm="na" 
    ms.devlang="na" 
    ms.topic="article" 
    ms.date="09/23/2016" 
    ms.author="shlo"/>


# <a name="transform-data-in-azure-data-factory"></a>Trasformare i dati in Azure Data Factory
> [AZURE.SELECTOR]
[Hive](data-factory-hive-activity.md)  
[Pig](data-factory-pig-activity.md)  
[MapReduce](data-factory-map-reduce.md)  
[Hadoop Streaming](data-factory-hadoop-streaming-activity.md)
[Machine Learning](data-factory-azure-ml-batch-execution-activity.md) 
[Stored procedure](data-factory-stored-proc-activity.md)
[U-SQL per Data Lake Analytics](data-factory-usql-activity.md)
[.NET personalizzato](data-factory-use-custom-activities.md)
   

## <a name="overview"></a>Panoramica 
Questo articolo illustra le attività di trasformazione dei dati in Azure Data Factory da usare per trasformare ed elaborare i dati non elaborati in stime e informazioni dettagliate. L'attività di trasformazione viene eseguita in un ambiente di elaborazione, ad esempio cluster HDInsight di Azure o un Batch di Azure. Vengono forniti i collegamenti ad articoli con informazioni dettagliate su ciascuna attività di trasformazione.
 
Data Factory supporta le seguenti attività di trasformazione dei dati che possono essere aggiunte a [pipeline](data-factory-create-pipelines.md) singolarmente o con un'altra attività concatenata.

> [AZURE.NOTE] Per la procedura dettagliata, vedere l'articolo [Creare una pipeline con la trasformazione Hive](data-factory-build-your-first-pipeline.md) .  

## <a name="hdinsight-hive-activity"></a>Attività Hive di HDInsight
L'attività Hive di HDInsight in una pipeline di Data factory esegue query Hive sul proprio cluster HDInsight o sul cluster HDInsight su richiesta basato su Windows o Linux. Vedere l'articolo [Attività Hive](data-factory-hive-activity.md) per i dettagli. 

## <a name="hdinsight-pig-activity"></a>Attività Pig di HDInsight
L'attività Pig di HDInsight in una pipeline di Data factory esegue query Pig sul proprio cluster HDInsight o sul cluster HDInsight su richiesta basato su Windows o Linux. Vedere l'articolo [Attività Pig](data-factory-pig-activity.md) per i dettagli. 

## <a name="hdinsight-mapreduce-activity"></a>Attività MapReduce di HDInsight
L'attività HDInsight MapReduce in una pipeline di Data Factory esegue i programmi di MapReduce nei cluster HDInsight personalizzati o su richiesta basati su Windows/Linux. Vedere l'articolo [Attività MapReduce](data-factory-map-reduce.md) per i dettagli.

È possibile usare l'attività MapReduce per eseguire i programmi Spark nel cluster HDInsight Spark. Per i dettagli, vedere [Chiamare i programmi Spark da Azure Data Factory](data-factory-spark.md) .

## <a name="hdinsight-streaming-activity"></a>Attività di streaming di HDInsight
L'attività HDInsight Streaming Activity in una pipeline di Data Factory esegue i programmi di Hadoop Streaming nei cluster HDInsight personalizzati o su richiesta basati su Windows/Linux. Vedere l' [attività di streaming di HDInsight](data-factory-hadoop-streaming-activity.md) per i dettagli.

## <a name="machine-learning-activities"></a>Attività di Machine Learning
Azure Data Factory consente di creare facilmente pipeline che usano un servizio Web pubblicato di Azure Machine Learning per l'analisi predittiva. Con [Attività di esecuzione Batch](data-factory-azure-ml-batch-execution-activity.md#invoking-a-web-service-using-batch-execution-activity) in una pipeline di Azure Data Factory è possibile richiamare un servizio Web di Machine Learning per eseguire stime dei dati in batch.

Nel corso del tempo è necessario ripetere il training dei modelli predittivi negli esperimenti di assegnazione dei punteggi di Machine Learning usando nuovi set di dati di input. Una volta ripetuto il training, aggiornare il servizio Web di assegnazione dei punteggi con il modello Machine Learning di cui è stato ripetuto il training. È possibile usare l' [attività di aggiornamento risorse](data-factory-azure-ml-batch-execution-activity.md#updating-models-using-update-resource-activity) per aggiornare il servizio Web con il nuovo modello sottoposto a training.  

Vedere [Usare le attività Machine Learning](data-factory-azure-ml-batch-execution-activity.md) per i relativi dettagli. 

## <a name="stored-procedure-activity"></a>Attività stored procedure
È possibile usare l'attività stored procedure di SQL Server in una pipeline di Data Factory per richiamare una stored procedure in uo dei seguenti archivi dati: database SQL di Azure, Azure SQL Data Warehouse, database di SQL Server in azienda o in una VM. Vedere l'articolo [Attività stored procedure](data-factory-stored-proc-activity.md) per i dettagli.  

## <a name="data-lake-analytics-u-sql-activity"></a>Attività U-SQL di Data Lake Analytics
L'attività U-SQL di Data Lake Analytics esegue uno script U-SQL in un cluster di Azure Data Lake Analytics. Vedere l'articolo [Attività di U-SQL di Data Analytics](data-factory-usql-activity.md) per i dettagli. 

## <a name=".net-custom-activity"></a>Attività personalizzata .NET
Se è necessario trasformare i dati in una modalità non supportata da Data Factory, è possibile creare un'attività personalizzata contenente la logica di elaborazione dei dati richiesta e usarla nella pipeline. È possibile configurare l'attività .NET personalizzata da eseguire usando il servizio Azure Batch o un cluster Azure HDInsight. Vedere l'articolo [Usare le attività personalizzate](data-factory-use-custom-activities.md) per i dettagli. 

È possibile creare un'attività personalizzata per eseguire gli script R nel cluster HDInsight con R installato. Vedere [RunRScriptUsingADFSample](https://github.com/Azure/Azure-DataFactory/tree/master/Samples/RunRScriptUsingADFSample)(Esempio relativo all'esecuzione di script R con Azure Data Factory). 

## <a name="compute-environments"></a>Ambienti di calcolo
Creare un servizio collegato per l'ambiente di calcolo e quindi usare il servizio collegato quando si definisce un'attività di trasformazione. Esistono due tipi di ambienti di calcolo supportati da Data factory. 

1. **On Demand**: in questo caso, l'ambiente informatico è completamente gestito da Data factory. Viene automaticamente creato dal servizio Data Factory prima che un processo venga inviato per l’elaborazione dati e rimosso quando il processo viene completato. È possibile configurare e controllare le impostazioni granulari dell'ambiente di elaborazione su richiesta per l'esecuzione del processo, la gestione del cluster e azioni di avvio automatico. 
2. **Bring Your Own**: In questo caso, è possibile registrare il proprio ambiente di elaborazione (ad esempio cluster HDInsight) come servizio collegato in Data factory. L'ambiente di elaborazione viene gestito dall'utente e il servizio Data factory viene usato per eseguire le attività. 

Per informazioni sui servizi di calcolo supportati da Data factory, vedere l'articolo [Servizi collegati di calcolo](data-factory-compute-linked-services.md) . 


## <a name="summary"></a>Riepilogo
Azure Data Factory supporta le seguenti attività di trasformazione di dati e i seguenti ambienti di calcolo per le attività. Le attività di trasformazione possono essere aggiunte alla pipeline singolarmente o con un'altra attività concatenata.

Attività di trasformazione dei dati |  Ambiente di calcolo 
:----------------------- | :--------------------
[Hive](data-factory-hive-activity.md) | HDInsight [Hadoop] 
[Pig](data-factory-pig-activity.md) | HDInsight [Hadoop]  
[MapReduce](data-factory-map-reduce.md) | HDInsight [Hadoop]  
[Hadoop Streaming](data-factory-hadoop-streaming-activity.md) | HDInsight [Hadoop]
[Attività di Machine Learning: esecuzione batch e aggiornamento risorse](data-factory-azure-ml-batch-execution-activity.md) | Macchina virtuale di Azure 
[Stored procedure](data-factory-stored-proc-activity.md) | Azure SQL, Azure SQL Data Warehouse o SQL Server |
[Attività U-SQL di Data Lake Analytics](data-factory-usql-activity.md) | Azure Data Lake Analytics. 
[DotNet](data-factory-use-custom-activities.md) | HDInsight [Hadoop] o Batch di Azure
   




<!--HONumber=Oct16_HO2-->


